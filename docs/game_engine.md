
Part 4: Game Engine
===================

Entity Component System (ECS) is our software architectural pattern aka our game engine.
ECS entities are composed from components of data, with systems which operate on components of entities. Every entity is defined by the components that are associated with it. Systems act globally over all entities which have the required components.


Entity: An entity represents a general-purpose object. In a game engine context, for example, every coarse game object is represented as an entity. Usually, it only consists of a unique id. Implementations typically use a plain integer for this.

Component: A component labels an entity as possessing a particular aspect and binary signature, and holds the data needed to model that aspect. For example, every game object that can take damage might have a RigidBody component associated with its entity. Implementations typically use structs, classes, or associative arrays.

System: A system is a process which acts on all entities who's has the same binary signature (register by the component) as him. For example a physics system may query for entities having mass, velocity and position components, and iterate over the results doing physics calculations on the sets of components for each entity. 


The Entity Manager is in charge of distributing entity IDs and keeping record of which IDs are in use and which are not.
EntityManager.hpp
```Cpp
    class EntityManager
    {
    private:
        std::queue<Entity> mAvailableEntities;
        std::array<Signature, MAX_ENTITIES> mSignature;
        int mLivingEntityCount = 0;
    public:
        EntityManager() {
            ...
        }
        Entity CreateEntity() {
            ...
        }
        void DestroyEntity(Entity entt) {
            ...
        }
        void SetSignature(Entity entt, Signature signature) {
            ...
        }
        Signature GetSignature(Entity entt) const {
            ...
        }
        std::array<Signature, MAX_ENTITIES> GetAllSignatures()
        {
            ...
        }
    };
```

The component will add a unique binary signature to an entity with all it's component.



The Component Manager, which is in charge of talking to all of the different Component Arrays when a component needs to be added or removed.
ComponentManager.hpp
```Cpp
    class ComponentManager
    {
    private:
        std::unordered_map<const char *, ComponentType> mComponentTypes;
        std::unordered_map<const char *,std::shared_ptr<IComponentArray>> mComponentArrays;
        ComponentType mNextComponentType;
        template<typename T> std::shared_ptr<ComponentArray<T>> GetComponentArray() {
            const char *TypeName = typeid(T).name();
            std::string tmp(TypeName);
            assert(this->mComponentTypes.find(TypeName) != this->mComponentTypes.end() && "Component not registered before use.");
            return std::static_pointer_cast<ComponentArray<T>>(this->mComponentArrays[TypeName]);
        }
    public:
        template<typename T> void RegisterComponent() {
            const char * TypeName = typeid(T).name();
            assert(this->mComponentTypes.find(TypeName) == this->mComponentTypes.end() && "Registering component type more than once.");
            this->mComponentTypes.insert({TypeName, this->mNextComponentType});
            this->mComponentArrays.insert({TypeName, std::make_shared<ComponentArray<T>>()});
            this->mNextComponentType++;
        }
        template<typename T> ComponentType GetComponentType() {
            const char *TypeName = typeid(T).name();
            assert(this->mComponentTypes.find(TypeName) != this->mComponentTypes.end() && "Component not registered before use.");
            return this->mComponentTypes[TypeName];
        }
        template<typename T> void AddComponent(Entity entt, T component) {
            this->GetComponentArray<T>()->InsertData(entt, component);
        }
        template<typename T> void RemoveComponent(Entity entt) {
            this->GetComponentArray<T>()->RemoveData(entt);
        }
        template<typename T> T &GetComponent(Entity entt) {
            return this->GetComponentArray<T>()->GetData(entt);
        }
        void EntityDestroyed(Entity entt) {
            for (auto &pair : this->mComponentArrays) {
                auto component = pair.second;
                component->EntityDestroyed(entt);
            }
        }
    };
```



The System Manager is in charge of maintaining a record of registered systems and their signatures.
SystemManager.hpp
```Cpp
    class SystemManager
    {
    private:
        std::unordered_map<const char *, Signature> mSignatures;

        std::unordered_map<const char *, std::shared_ptr<System>> mSystems;

    public:
        template<typename T> std::shared_ptr<T> RegisterSystem() {
            ...
        }

        template<typename T>void SetSignature(Signature signature) {
            ...
        }

        void EntityDestroyed(Entity entt) {
            ...
        }

        void EntitySignatureChanged(Entity entt, Signature enttsignature) {
            ...
        }
        
    };
```

The system get the signature of the components in order to modify the entities' components by their signature.

We have entities which are managed by an Entity Manager. We have components which are managed by a Component Manager. And we have systems which are managed by a System Manager. These three managers also need to talk to each other.

There are a few ways of accomplishing that, such as having them all be globals, or using an event system, we opted to instead bundle them into a single class called Coordinator that acts as a mediator. This allows us to have a single instance of the coordinator, and we can use it to interface with all of the managers.
Coordinator.hpp
```Cpp
   class Coordinator
    {
    private:
        std::unique_ptr<ComponentManager> mComponentManager;
        std::unique_ptr<EntityManager> mEntityManager;
        std::unique_ptr<SystemManager> mSystemManager;

    public:
        Coordinator() {
            this->mComponentManager = std::make_unique<ComponentManager>();
            this->mEntityManager    = std::make_unique<EntityManager>();
            this->mSystemManager    = std::make_unique<SystemManager>();
        }
        void Init() {
            this->mComponentManager = std::make_unique<ComponentManager>();
            this->mEntityManager    = std::make_unique<EntityManager>();
            this->mSystemManager    = std::make_unique<SystemManager>();
        }
        Entity CreateEntity() {
            return this->mEntityManager->CreateEntity();
        }
        void DestroyedEntity(Entity entt) {
            this->mEntityManager->DestroyEntity(entt);
            this->mComponentManager->EntityDestroyed(entt);
            this->mSystemManager->EntityDestroyed(entt);
        }

        void print_available_signatures()
        {
            std::array<Signature, MAX_ENTITIES> tmp = this->mEntityManager->GetAllSignatures();
            for(const Signature bitset : tmp)
                std::cout << bitset << std::endl;

        }

        template<typename T> void RegisterComponent() {
            this->mComponentManager->RegisterComponent<T>();
        }

        template<typename T> void AddComponent(Entity entt, T component) {
            this->mComponentManager->AddComponent<T>(entt, component);

            Signature signature = this->mEntityManager->GetSignature(entt);
            signature.set(this->mComponentManager->GetComponentType<T>(), true);
            this->mEntityManager->SetSignature(entt, signature);

            this->mSystemManager->EntitySignatureChanged(entt, signature);
        }

        template<typename T> void RemoveComponent(Entity entt) {
            this->mComponentManager->RemoveComponent<T>(entt);

            Signature signature = this->mEntityManager->GetSignature(entt);
            signature.set(this->mComponentManager->GetComponentType<T>(), false);
            this->mEntityManager->SetSignature(entt, signature);

            this->mSystemManager->EntitySignatureChanged(entt, signature);
        }

        template<typename T> T &GetComponent(Entity entt) {
            return this->mComponentManager->GetComponent<T>(entt);
        }

        template<typename T> ComponentType GetComponentType() {
            return this->mComponentManager->GetComponentType<T>();
        }

        template<typename T> std::shared_ptr<T> RegisterSystem() {
            return this->mSystemManager->RegisterSystem<T>();
        }

        template<typename T> void SetSystemSignature(Signature signature) {
            this->mSystemManager->SetSignature<T>(signature);
        }
    };
```


ECS architecture :
![](/assets/ECS/system.png)
