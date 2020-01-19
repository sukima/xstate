# xstate

## 5.0.0-next.0

### Major Changes

- e09efc7: Removed third parameter (context) from Machine's transition method. If you want to transition with a particular context value you should create appropriate `State` using `State.from`. So instead of this - `machine.transition('green', 'TIMER', { elapsed: 100 })`, you should do this - `machine.transition(State.from('green', { elapsed: 100 }), 'TIMER')`.
- 97b0569: Simplified invoke definition: the `invoke` property of a state definition will now only accept an `InvokeCreator`, which is a function that takes in context, event, and meta (parent, id, etc.) and returns an `Actor`.

  ```diff
  - invoke: someMachine
  + invoke: spawnMachine(someMachine)

  - invoke: (ctx, e) => somePromise
  + invoke: spawnPromise((ctx, e) => somePromise)

  - invoke: (ctx, e) => (cb, receive) => { ... }
  + invoke: spawnCallback((ctx, e) => (cb, receive) => { ... })

  - invoke: (ctx, e) => someObservable$
  + invoke: spawnObservable((ctx, e) => someObservable$)
  ```

  This also includes a helper function for spawning activities:

  ```diff
  - activity: (ctx, e) => { ... }
  + invoke: spawnActivity((ctx, e) => { ... })
  ```

- 025a2d6: - Breaking: activities removed (can be invoked)

  Since activities can be considered invoked services, they can be implemented as such. Activities are services that do not send any events back to the parent machine, nor do they receive any events, other than a "stop" signal when the parent changes to a state where the activity is no longer active. This is modeled the same way as a callback service is modeled.- e09efc7: Removed previously deprecated config properties: `onEntry`, `onExit`, `parallel` and `forward`.

### Patch Changes

- c8db035: The `escalate()` action can now take in an expression, which will be evaluated against the `context`, `event`, and `meta` to return the error data.
- 2a3fea1: The typings for the raise() action have been fixed to allow any event to be raised. This typed behavior will be refined in version 5, to limit raised events to those that the machine accepts.

## 4.7.6

### Patch Changes

- dae8818: Typestates are now propagated to interpreted services.

## 4.7.5

### Patch Changes

- 6b3d767: Fixed issue with delayed transitions scheduling a delayed event for each transition defined for a single delay.

## 4.7.4

### Patch Changes

- 9b043cd: The initial state is now cached inside of the service instance instead of the machine, which was the previous (faulty) strategy. This will prevent entry actions on initial states from being called more than once, which is important for ensuring that actors are not spawned more than once.

## 4.7.3

### Patch Changes

- 2b134eee: Fixed issue with events being forwarded to children after being processed by the current machine. Events are now always forwarded first.
- 2b134eee: Fixed issue with not being able to spawn an actor when processing an event batch.
