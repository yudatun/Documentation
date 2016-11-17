weaved
========================================

* system/weaved

This directory contains the a Brillo service for registering a device and
sending/receiving remote commands.

Diagraming
----------------------------------------

http://www.processon.com/diagraming/58101768e4b0bf457c943022

Flowchart
----------------------------------------

```
   DBusDaemon:
       |
DBusServiceDaemon(): service_name_("com.android.Weave"), object_manager_path_("com/android/Weave")
       |
     weaved
       |
brillo::Daemon::Run()
       |
buffet::Daemon::OnInit()
       |
brillo::DBusServiceDaemon::OnInit(): object_manager_ = new ExportedObjectManager(bus_, object_manager_path_)
       |
brillo::DBusDaemon::OnInit(): bus_ = dbus_connection_.Connect()
       |
buffet::Daemon::RegisterDBusObjectsAsync()
 |
 +-> manager_ = new Manager{options_, bus_}
 |
 manager_->Start()
  |
 buffet::Manager::RestartWeave()
  |
 +-> task_runner_ = new TaskRunner
 |
 +-> config_ = new BuffetConfig
 |
 +-> http_client_ = new HttpTransportClient
 |
 +-> shill_client_ = new ShillClient
 |
 +-> mdns_client_ = MdnsClient::CreateInstance()
 |
 +-> web_serv_client_ = new WebServClient
 |
 +-> bluetooth_client_ = BluetoothClient::CreateInstance
 |
 buffet::Manager::CreateDevice()
  |
device_ = weave::Device::Create()
 |
new weave::DeviceManager::DeviceManager
 |
 +-> config_ = new Config
 |
 +-> component_manager_ = new ComponentManagerImpl
 |
 +-> auth_manager_ = new privet:AuthManager
 |
 +-> device_info_ = new DeviceRegistrationInfo
 |
 +-> base_api_handler_ = new BaseApiHandler
 |
 +-> black_list_manager_ = new AccessBlackListManagerImpl
 |
 +-> access_api_handler_ = new AccessApiHandler
 |
 +-> device_info_->Start()
 |
 weave::StartPrivet
  |
 +-> privet_ = new privet::Manager{task_runner_}
 |
 privet->Start()
  |
 +-> device_ = DeviceDelegate::CreateDefault
 |
 +-> cloud_ = CludDelegate::CreateDefault
 |
 +-> security_ = new SecurityManager
 |
 +-> privet_handler_ = new PrivetHandler
 |
 +-> privet_handler_->AddHttpRequestHandler
 |
 privet_handler_->AddHttpsRequestHandler
```
