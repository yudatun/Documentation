weaved
========================================

* system/weaved

This directory contains the a Brillo service for registering a device and
sending/receiving remote commands.

Summary
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
