# BackgroundExecutor for Blazor

A lightweight background task execution service for Blazor applications that processes queued actions sequentially while keeping the UI responsive.

## Features

- Process tasks asynchronously in the background
- Sequential execution of queued tasks
- Notification system integration with FluentUI
- Responsive UI during task execution
- Thread-safe implementation with error handling

## Installation

1. Add the BackgroundExecutor service to your DI container in `Program.cs`:

```csharp
builder.Services.AddScoped<BackgroundExecutor.Services.BackgroundExecutor>();
```

2. Make sure you have the FluentUI Blazor package installed:

```
dotnet add package Microsoft.FluentUI.AspNetCore.Components
```

## Usage

### Basic Example

```csharp
@inject BackgroundExecutor.Services.BackgroundExecutor Executor

<FluentButton OnClick="QueueSomeAction">Queue Action</FluentButton>

@code {
    private void QueueSomeAction()
    {
        Executor.QueueAction(async () => {
            // Long-running operation
            await Task.Delay(2000);
            // Do work here
        });
    }
}
```

### Notification Center

The BackgroundExecutor integrates with FluentUI's notification system. Add this to your `MainLayout.razor`:

```razor
<FluentCounterBadge Count="@MessageService.Count(BackgroundExecutor.NotificationSectionName)"
                    ShowWhen="@((i)=> MessageService.Count(BackgroundExecutor.NotificationSectionName) > 0)"
                    BackgroundColor="@Color.Error"
                    Color="Color.Fill">
    <ChildContent>
        <FluentButton OnClick="OpenNotificationCenter">
            <FluentIcon Value="@(new Icons.Regular.Size20.Alert())" />
        </FluentButton>
    </ChildContent>
</FluentCounterBadge>
```

## How It Works

The BackgroundExecutor uses a producer-consumer pattern with a `ConcurrentQueue` and `SemaphoreSlim` to efficiently manage task execution:

1. Actions are added to a thread-safe queue
2. A semaphore signals when new actions are available
3. A background thread processes actions sequentially
4. Notifications are displayed for errors or task status updates

## License

MIT