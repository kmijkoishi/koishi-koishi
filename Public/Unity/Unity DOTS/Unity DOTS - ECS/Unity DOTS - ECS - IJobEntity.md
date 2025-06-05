유니티 [[Unity DOTS - ECS|ECS]]에서 [[Unity DOTS - Job System|Job System]]을 이용하기 위한 인터페이스.

예시:
```Csharp
[BurstCompile]
partial struct ExampleSystem : ISystem
{
	[BurstCompile]
	public void OnUpdate(ref SystemState state)
	{
		EntityCommandBuffer.ParallelWriter ecb = GetEntityCommandBuffer(ref state);

		new ExampleJob
		{
			Ecb = ecb,
			Foo = 10
		}.ScheduleParallel();
	}
}

[BurstCompile]
public partial struct ExampleJob : IJobEntity
{
	public EntityCommandBuffer.ParallelWriter Ecb;
	public int Foo;

	private void Execute([ChunkIndexInQuery] int chunkIndex, ref ExampleComponent component)
	{
		// ...
	}
}
```

- IJobEntity 인터페이스를 가진 구조는 partial 로 선언해야 한다. (유니티 ECS에서 자동으로 추가 코드를 생성하기에 partial이 필요)
- IJobEntity 인터페이스에서의 Execute() 메소드는,
  메소드 내의 파라미터를 검사하여 자동으로 필요한 엔티티만을 쿼리하여 동작함.
  (Execute()의 파라미터로써 A, B 컴포넌트를 요구할 경우 유니티ECS 에서 자동으로 A, B컴포넌트를 모두 가진 엔티티들에 대해서만 Execute()메소드를 실행함.)

%% # 해설
## - partial struct ExampleSystem : ISystem
ECS 내에서 System을 담당하고 있는 예시 구조.
### -- OnUpdate()
유니티에서 업데이트를 할 때 마다 자동으로 호출되는 메소드.


### Execute()
 %%