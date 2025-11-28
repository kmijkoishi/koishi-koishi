

## ISystem vs SystemBase

| ISystem                               | SystemBase                        |
| ------------------------------------- | --------------------------------- |
| Can use Burst, only unmanaged allowed | Cannot use Burst, managed allowed |

### ISystem (Example)

```c#
[BurstCompile]  
partial struct MinerSystem : ISystem  
{  
    public void OnCreate(ref SystemState state)  
    {  
        throw new System.NotImplementedException();  
    }  
  
    public void OnDestroy(ref SystemState state)  
    {  
        throw new System.NotImplementedException();  
    }  
  
    [BurstCompile]  
    public void OnUpdate(ref SystemState state)  
    {  
        EntityCommandBuffer.ParallelWriter ecb = SystemAPI.GetSingleton<BeginSimulationEntityCommandBufferSystem.Singleton>()  
            .CreateCommandBuffer(state.WorldUnmanaged).AsParallelWriter();  
  
        new MinerJob  
        {  
            Ecb = ecb,  
            deltaTime = Time.deltaTime  
        }.ScheduleParallel();  
          
        var minerEntity = SystemAPI.QueryBuilder()  
            .WithAll<MinerComponent>()  
            .Build();  
    }  
}  
  
[BurstCompile]  
public partial struct MinerJob : IJobEntity  
{  
    public EntityCommandBuffer.ParallelWriter Ecb;  
    public float deltaTime;  
  
    private void Execute([ChunkIndexInQuery] int chunkIndex, ref MinerComponent miner)  
    {  
        if (!miner.IsMining)  
        {  
            //TODO: Check Ore Patch/Electricity and start run  
            return;  
        }  
  
        if (miner.RemainTime <= 0)  
        {  
            //TODO: Generate Ore items  
            miner.RemainTime = 1 / miner.Speed;  
        }  
        else  
        {  
            miner.RemainTime -= deltaTime;  
        }  
    }  
}
```

### SystemBase (Example)
```C#
public partial class GameObjectSetupSystem : SystemBase  
{  
    protected override void OnUpdate()  
    {  
        var newGOComponents = SystemAPI.QueryBuilder()  
            .WithAll<GameObjectRefFlag>()  
            .WithNone<Transform>()  
            .Build();  
  
        foreach (var entity in newGOComponents.ToEntityArray(Allocator.Temp))  
        {  
            GameObject go = new GameObject(EntityManager.GetName(entity));  
            EntityLink entityLink = go.AddComponent<EntityLink>();  
            entityLink.Initialize(Unity.Entities.World.DefaultGameObjectInjectionWorld, entity);  
            EntityManager.AddComponentObject(entity, go.transform);  
            Debug.Log($"object added to {EntityManager.GetName(entity)}.");  
        }  
    }  
}
```

## ComponentLookUp vs SystemAPI.QueryBuilder

| ComponentLookUp                                 | SystemAPI.QueryBuilder  |
| ----------------------------------------------- | ----------------------- |
| Good for getting few entities from reference(?) | Good for fetching large |
### SystemAPI.QueryBuiler
can be used SystemAPI.Query<RefRW<A> RefRO<B>>() instead for static.
