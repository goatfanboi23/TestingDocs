## How to log Commands
- When creating a standard command extend `LoggableCommand`
- When creating a `CommandGroup` extend the corresponding Logged version
  - `ParallelCommandGroup` :material-arrow-right: `LoggableParallelCommandGroup`
  - `SequentialCommandGroup` :material-arrow-right: `LoggableSequentialCommandGroup`
  - `ParallelRaceGroup` :material-arrow-right: `LoggableRaceCommandGroup`
  - `ParallelDeadlineGroup` :material-arrow-right: `LoggableDeadlineCommandGroup`
- When using a command provided by another library, you must wrap the command using <br>
`LoggableCommandWrapper(Command commandToWrap)`

### Useful Built-in Commands
  - `LoggableWaitCommand`: does nothing for some amount of time
  - `DoNothingCommand`: Does nothing and finishes in one tick (Useful as a placeholder)

!!! warning
    Right now, command compositions do not work. Each command must be declared in a file. In other words, you can not call `.andThen` or `.withTimeout`.
## How Command Logging works
!!! note
    You saidly might have to switch to light theme for the diagram to be readable.
``` mermaid

classDiagram
  Loggable <|-- LoggableCommand
  Loggable <|-- LoggableParallelCommandGroup
  Loggable <|-- LoggableWaitCommand
  LoggableCommand <|-- DoNothingCommand
  Command <|-- WaitCommand
  Command <|-- LoggableCommand
  WaitCommand <|-- LoggableWaitCommand
  ParallelCommandGroup <|-- LoggableParallelCommandGroup
  class Loggable {
    <<interface>>
    +getBasicName() String
    +setParent(Command loggable) void
  }
  class LoggableCommand{
    -String basicName
    -Command parent
    +withBasicName(String name) LoggableCommand
  }
  class LoggableWaitCommand {
    -String basicName
    -Command parent
    +withBasicName(String name) LoggableCommand
  }
  class LoggableParallelCommandGroup{
    -String basicName
    -Command parent
    +withBasicName(String name) LoggableCommand
    +LoggableParallelCommandGroup~T extends Command & Loggable~(T...commands)
  }
  class Command
  class WaitCommand
  class ParallelCommandGroup
  class DoNothingCommand
  
  note for Command "Provided my WPILib" 
  note for WaitCommand "Provided my WPILib" 
  note for ParallelCommandGroup "Provided my WPILib" 
```
Let's unpack this:
