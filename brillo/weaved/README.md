weaved
========================================

* system/weaved

This directory contains the a Brillo service for registering a device and
sending/receiving remote commands.

Diagraming
----------------------------------------

http://www.processon.com/diagraming/58101768e4b0bf457c943022

```
   DBusDaemon: bus_ = dbus_connection_.Connect()
       +
       |             service_name_     object_manager_path_
DBusServiceDaemon("com.android.Weave", "com/android/Weave"): object_manager_ -> ExportedObjectManager(bus_, object_manager_path_)
       +
       |
     weaved
```

Flowchart
----------------------------------------

```
main
 |
brillo::Daemon::Run()
 |
buffet::Daemon::OnInit()
 |
brillo::DBusServiceDaemon::OnInit()
 |
 +-> brillo::DBusDaemon::OnInit()
 |
 +-> buffet::Daemon::RegisterDBusObjectsAsync()
      |
     buffet::Manager::Start()
      |
     buffet::Manager::RestartWeave()
      |
     buffet::Manager::CreateDevice()
      |
     weave::Device::Create()
      |
     weave::DeviceManager::DeviceManager()
      |
      +-> weave::DeviceRegistrationInfo::Start()
      |
      +-> weave::DeviceManager::StartPrivet()
          |
          +-> weave::privet::Manager::Start()
```
