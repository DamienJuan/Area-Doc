
Part 3: Game Engine
===================

Entity Component System (ECS) is our software architectural pattern aka our game engine.
ECS entities are composed from components of data, with systems which operate on components of entities. Every entity is defined by the components that are associated with it. Systems act globally over all entities which have the required components.


Entity: An entity represents a general-purpose object. In a game engine context, for example, every coarse game object is represented as an entity. Usually, it only consists of a unique id. Implementations typically use a plain integer for this.

Component: A component labels an entity as possessing a particular aspect, and holds the data needed to model that aspect. For example, every game object that can take damage might have a Health component associated with its entity. Implementations typically use structs, classes, or associative arrays.

System: A system is a process which acts on all entities with the desired components. For example a physics system may query for entities having mass, velocity and position components, and iterate over the results doing physics calculations on the sets of components for each entity. 


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

The component will add a unique binary signature to an entity.



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
