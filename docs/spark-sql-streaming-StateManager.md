== [[StateManager]] StateManager Contract -- State Managers for Arbitrary Stateful Streaming Aggregation

`StateManager` is the <<contract, abstraction>> of <<implementations, state managers>> that act as _middlemen_ between <<spark-sql-streaming-StateStore.md#, state stores>> and the [FlatMapGroupsWithStateExec](physical-operators/FlatMapGroupsWithStateExec.md) physical operator used in [Arbitrary Stateful Streaming Aggregation](arbitrary-stateful-streaming-aggregation.md).

[[contract]]
.StateManager Contract
[cols="30m,70",options="header",width="100%"]
|===
| Method
| Description

| getAllState
a| [[getAllState]]

[source, scala]
----
getAllState(store: StateStore): Iterator[StateData]
----

Retrieves all state data (for all keys) from the <<spark-sql-streaming-StateStore.md#, StateStore>>

Used when `InputProcessor` is requested to [processTimedOutState](InputProcessor.md#processTimedOutState)

| getState
a| [[getState]]

[source, scala]
----
getState(
  store: StateStore,
  keyRow: UnsafeRow): StateData
----

Gets the state data for the key from the <<spark-sql-streaming-StateStore.md#, StateStore>>

Used exclusively when `InputProcessor` is requested to [processNewData](InputProcessor.md#processNewData)

| putState
a| [[putState]]

[source, scala]
----
putState(
  store: StateStore,
  keyRow: UnsafeRow,
  state: Any,
  timeoutTimestamp: Long): Unit
----

Persists (_puts_) the state value for the key in the <<spark-sql-streaming-StateStore.md#, StateStore>>

Used exclusively when `InputProcessor` is requested to [callFunctionAndUpdateState](InputProcessor.md#callFunctionAndUpdateState) ([right after all rows have been processed](InputProcessor.md#onIteratorCompletion))

| removeState
a| [[removeState]]

[source, scala]
----
removeState(
  store: StateStore,
  keyRow: UnsafeRow): Unit
----

Removes the state for the key from the <<spark-sql-streaming-StateStore.md#, StateStore>>

Used exclusively when `InputProcessor` is requested to [callFunctionAndUpdateState](InputProcessor.md#callFunctionAndUpdateState) ([right after all rows have been processed](InputProcessor.md#onIteratorCompletion))

| stateSchema
a| [[stateSchema]]

[source, scala]
----
stateSchema: StructType
----

*State schema*

NOTE: <<spark-sql-streaming-StateStoreOps.md#mapPartitionsWithStateStore, It looks like>> (in <<FlatMapGroupsWithStateExec.md#stateManager, StateManager>> of the [FlatMapGroupsWithStateExec](physical-operators/FlatMapGroupsWithStateExec.md) physical operator) `stateSchema` is used for the schema of state value objects (not state keys as they are described by the grouping attributes instead).

Used when:

* [FlatMapGroupsWithStateExec](physical-operators/FlatMapGroupsWithStateExec.md) physical operator is executed

* `StateManagerImplBase` is requested for the <<spark-sql-streaming-StateManagerImplBase.md#stateDeserializerFunc, stateDeserializerFunc>>

|===

[[implementations]]
NOTE: <<spark-sql-streaming-StateManagerImplBase.md#, StateManagerImplBase>> is the one and only known direct implementation of the <<contract, StateManager Contract>> in Spark Structured Streaming.

NOTE: `StateManager` is a Scala *sealed trait* which means that all the <<implementations, implementations>> are in the same compilation unit (a single file).
