
Part 4: Game Engine
===================

Entity Component System (ECS) is our software architectural pattern aka our game engine.
ECS entities are composed from components of data, with systems which operate on components of entities. Every entity is defined by the components that are associated with it. Systems act globally over all entities which have the required components.


Entity: An entity represents a general-purpose object. In a game engine context, for example, every coarse game object is represented as an entity. Usually, it only consists of a unique id. Implementations typically use a plain integer for this.

Component: A component labels an entity as possessing a particular aspect and binary signature, and holds the data needed to model that aspect. For example, every game object that can take damage might have a RigidBody component associated with its entity. Implementations typically use structs, classes, or associative arrays.

System: A system is a process which acts on all entities who's has the same binary signature (register by the component) as him. For example a physics system may query for entities having mass, velocity and position components, and iterate over the results doing physics calculations on the sets of components for each entity. 


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

ECS architecture :
![](/assets/ECS/system.png)
