Daemon
========================================

Daemon is a simple base class for system daemons. It provides a
lot of useful facilities such as a message loop, handling of
SIGTERM, SIGINT, and SIGHUP system signals. You can use this
class directly to implement your daemon or you can specialize it
by creating your own class and deriving it from `brillo::Daemon`.
Override some of the virtual methods provide to fine-tune its
behavior to suit your daemon's needs.

brillo::Daemon
----------------------------------------

### Class

path: external/libbrillo/brillo/deamons/daemon.h

```
namespace brillo {
class BRILLO_EXPORT Daemon : public AsynchronousSignalHandlerInterface {
};
}
```

### Construct

path: external/libbrillo/brillo/deamons/daemon.cc

```
Daemon::Daemon() : exit_code_{EX_OK} {
  message_loop_.SetAsCurrent();
}
```

* message_loop_

```
  // The brillo wrapper for the base message loop.
  BaseMessageLoop message_loop_;
```

### Run

```
int Daemon::Run() {
  int exit_code = OnInit();
  if (exit_code != EX_OK)
    return exit_code;

  message_loop_.Run();

  OnShutdown(&exit_code_);

  // base::RunLoop::QuitClosure() causes the message loop to quit
  // immediately, even if pending tasks are still queued.
  // Run a secondary loop to make sure all those are processed.
  // This becomes important when working with D-Bus since dbus::Bus does
  // a bunch of clean-up tasks asynchronously when shutting down.
  while (message_loop_.RunOnce(false /* may_block */)) {}

  return exit_code_;
}
```

brillo::BaseMessageLoop
----------------------------------------

### Class

```
namespace brillo {

class BRILLO_EXPORT BaseMessageLoop : public MessageLoop {
...
};

}  // namespace brillo
```

### Construct

```
BaseMessageLoop::BaseMessageLoop() {
  CHECK(!base::MessageLoop::current())
      << "You can't create a base::MessageLoopForIO when another "
         "base::MessageLoop is already created for this thread.";
  owned_base_loop_.reset(new base::MessageLoopForIO);
  base_loop_ = owned_base_loop_.get();
}
```

### Run

```
void BaseMessageLoop::Run() {
  base::RunLoop run_loop;  // Uses the base::MessageLoopForIO implicitly.
  base_run_loop_ = &run_loop;
  run_loop.Run();
  base_run_loop_ = nullptr;
}
```

brillo::MessageLoop
----------------------------------------

### Class

```
namespace brillo {

class BRILLO_EXPORT MessageLoop {
...
};
}
```

### SetAsCurrent

```
void MessageLoop::SetAsCurrent() {
  DCHECK(lazy_tls_ptr.Pointer()->Get() == nullptr) <<
      "There's already a MessageLoop for this thread.";
  lazy_tls_ptr.Pointer()->Set(this);
}
```

### lazy_tls_ptr

```
namespace {

// A lazily created thread local storage for quick access to a thread's message
// loop, if one exists.  This should be safe and free of static constructors.
base::LazyInstance<base::ThreadLocalPointer<MessageLoop> >::Leaky lazy_tls_ptr =
    LAZY_INSTANCE_INITIALIZER;

}  // namespace
```

base::RunLoop
----------------------------------------

Helper class to Run a nested MessageLoop. Please do not use nested
MessageLoops in production code! If you must, use this class
instead of calling MessageLoop::Run/Quit directly. RunLoop::Run
can only be called once per RunLoop lifetime. Create a RunLoop
on the stack and call Run/Quit to run a nested MessageLoop.

### Class

```
namespace base {

class BASE_EXPORT RunLoop {
...
};
}
```

### Construct

```
namespace base {

RunLoop::RunLoop()
    : loop_(MessageLoop::current()),
      previous_run_loop_(NULL),
      run_depth_(0),
      run_called_(false),
      quit_called_(false),
      running_(false),
      quit_when_idle_received_(false),
      weak_factory_(this) {
#if defined(OS_WIN)
   dispatcher_ = NULL;
#endif
}
...
}
```

* loop_

```
  MessageLoop* loop_;
```

### Run

```
void RunLoop::Run() {
  if (!BeforeRun())
    return;

  // Use task stopwatch to exclude the loop run time from the current task, if
  // any.
  tracked_objects::TaskStopwatch stopwatch;
  stopwatch.Start();
  loop_->RunHandler();
  stopwatch.Stop();

  AfterRun();
}
```

base::MessageLoop
----------------------------------------

A MessageLoop is used to process events for a particular thread.
There is at most one MessageLoop instance per thread.
Events include at a minimum Task instances submitted to
PostTask and its variants.  Depending on the type of message
pump used by the `MessageLoop` other events such as UI messages
may be processed.  On Windows APC calls (as time permits) and
signals sent to a registered set of HANDLEs may also be processed.

**NOTE**: Unless otherwise specified, a MessageLoop's methods
may only be called on the thread where the MessageLoop's Run
method executes.

**NOTE**: MessageLoop has task reentrancy protection.  This means
that if a task is being processed, a second task cannot start
until the first task is finished.  Reentrancy can happen when
processing a task, and an inner message pump is created.  That
inner pump then processes native messages which could implicitly
start an inner task.  Inner message pumps are created with
dialogs (DialogBox), common dialogs (GetOpenFileName), OLE
functions (DoDragDrop), printer functions (StartDoc) and *many*
others.

Sample workaround when inner task processing is needed:

```
HRESULT hr;
{
  MessageLoop::ScopedNestableTaskAllower allow(MessageLoop::current());
  hr = DoDragDrop(...); // Implicitly runs a modal message loop.
}
```
Process |hr| (the result returned by DoDragDrop()).
Please be SURE your task is reentrant (nestable) and all global
variables are stable and accessible before calling
SetNestableTasksAllowed(true).

### Class

```
class BASE_EXPORT MessageLoop : public MessagePump::Delegate {
...
};
```

### Construct

```
MessageLoop::MessageLoop(Type type)
    : MessageLoop(type, MessagePumpFactoryCallback()) {
  BindToCurrentThread();
}
```

### BindToCurrentThread

```
void MessageLoop::BindToCurrentThread() {
  DCHECK(!pump_);
  if (!pump_factory_.is_null())
    pump_ = pump_factory_.Run();
  else
    pump_ = CreateMessagePumpForType(type_);

  DCHECK(!current()) << "should only have one message loop per thread";
  lazy_tls_ptr.Pointer()->Set(this);

  incoming_task_queue_->StartScheduling();
  unbound_task_runner_->BindToCurrentThread();
  unbound_task_runner_ = nullptr;
  SetThreadTaskRunnerHandle();
}
```

### current

```
// static
MessageLoop* MessageLoop::current() {
  // TODO(darin): sadly, we cannot enable this yet since people call us even
  // when they have no intention of using us.
  // DCHECK(loop) << "Ouch, did you forget to initialize me?";
  return lazy_tls_ptr.Pointer()->Get();
}
```

### lazy_tls_ptr

```
// A lazily created thread local storage for quick access to a thread's message
// loop, if one exists.  This should be safe and free of static constructors.
LazyInstance<base::ThreadLocalPointer<MessageLoop> >::Leaky lazy_tls_ptr =
    LAZY_INSTANCE_INITIALIZER;
```

### RunHandler

```
void MessageLoop::RunHandler() {
  DCHECK_EQ(this, current());

  StartHistogrammer();

#if defined(OS_WIN)
  if (run_loop_->dispatcher_ && type() == TYPE_UI) {
    static_cast<MessagePumpForUI*>(pump_.get())->
        RunWithDispatcher(this, run_loop_->dispatcher_);
    return;
  }
#endif

  pump_->Run(this);
}
```