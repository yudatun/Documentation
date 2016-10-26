weaved
========================================

* system/weaved

This directory contains the a Brillo service for registering a device and
sending/receiving remote commands.

Summary
----------------------------------------

```
buffet::Daemon::Run
 |
brillo::Daemon::Run
 |
buffet::Daemon::OnInit
 |
brillo::DBusServiceDaemon::OnInit
 |
brillo::DBusDaemon::OnInit
 |
buffet::Daemon::RegisterDBusObjectsAsync
 |
buffet::Manager::Start
```

main
----------------------------------------

path: system/weaved/buffet/main.cc

```
int main(int argc, char* argv[]) {
  ...
  buffet::Daemon daemon{options};
  return daemon.Run();
}
```

buffet::Daemon
----------------------------------------

path: system/weaved/buffet/main.cc

```
namespace buffet {

class Daemon final : public DBusServiceDaemon {
 public:
  explicit Daemon(const Manager::Options& options)
      : DBusServiceDaemon(kServiceName, kRootServicePath), options_{options} {}

 protected:
  int OnInit() override {
    android::BinderWrapper::Create();
    if (!binder_watcher_.Init())
      return EX_OSERR;

    return brillo::DBusServiceDaemon::OnInit();
  }

  void RegisterDBusObjectsAsync(AsyncEventSequencer* sequencer) override {
    manager_ = new Manager{options_, bus_};
    android::BinderWrapper::Get()->RegisterService(
        weaved::binder::kWeaveServiceName,
        android::IInterface::asBinder(manager_));
    manager_->Start(sequencer);
  }

  void OnShutdown(int* return_code) override { manager_->Stop(); }

 private:
  Manager::Options options_;
  brillo::BinderWatcher binder_watcher_;
  android::sp<buffet::Manager> manager_;

  DISALLOW_COPY_AND_ASSIGN(Daemon);
};

}  // namespace buffet
```

brillo::DBusServiceDaemon
----------------------------------------

path: external/libbrillo/brillo/dbus_daemon.h

### Class

```
// DBusServiceDaemon adds D-Bus service support to DBusDaemon.
// Derive your daemon from this class if your daemon exposes D-Bus objects.
// Provides an ExportedObjectManager to announce your object/interface creation
// and destruction.
class BRILLO_EXPORT DBusServiceDaemon : public DBusDaemon {
};
```

### Construct

path: external/libbrillo/brillo/dbus_daemon.cc

```
DBusServiceDaemon::DBusServiceDaemon(const std::string& service_name)
    : service_name_(service_name) {
}

DBusServiceDaemon::DBusServiceDaemon(
    const std::string& service_name,
    const dbus::ObjectPath& object_manager_path)
    : service_name_(service_name), object_manager_path_(object_manager_path) {
}

DBusServiceDaemon::DBusServiceDaemon(const std::string& service_name,
                                     base::StringPiece object_manager_path)
    : DBusServiceDaemon(service_name,
                        dbus::ObjectPath(object_manager_path.as_string())) {
}
```

### OnInit

```
int DBusServiceDaemon::OnInit() {
  int exit_code = DBusDaemon::OnInit();
  if (exit_code != EX_OK)
    return exit_code;

  scoped_refptr<AsyncEventSequencer> sequencer(new AsyncEventSequencer());
  if (object_manager_path_.IsValid()) {
    object_manager_.reset(
        new ExportedObjectManager(bus_, object_manager_path_));
    object_manager_->RegisterAsync(
        sequencer->GetHandler("ObjectManager.RegisterAsync() failed.", true));
  }
  RegisterDBusObjectsAsync(sequencer.get());
  sequencer->OnAllTasksCompletedCall({
      base::Bind(&DBusServiceDaemon::TakeServiceOwnership,
                 base::Unretained(this))
  });
  return EX_OK;
}
```

brillo::DBusDaemon
----------------------------------------

path: external/libbrillo/brillo/dbus_daemon.cc

### Class

```
// DBusDaemon adds D-Bus support to Daemon.
// Derive your daemon from this class if you want D-Bus client services in your
// daemon (consuming other D-Bus objects). Currently uses a SYSTEM bus.
class BRILLO_EXPORT DBusDaemon : public Daemon {
  ...
};
```

### OnInit

```
int DBusDaemon::OnInit() {
  int exit_code = Daemon::OnInit();
  if (exit_code != EX_OK)
    return exit_code;

  bus_ = dbus_connection_.Connect();
  CHECK(bus_);

  return exit_code;
}
```

brillo::Daemon
----------------------------------------

### Class

```
class BRILLO_EXPORT Daemon : public AsynchronousSignalHandlerInterface {
...
};
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