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
 |
buffet::Manager::RestartWeave
 |
buffet::Manager::CreateDevice
 |
weave::Device::Create
```
