weaved
========================================

* system/weaved

This directory contains the a Brillo service for registering a device and
sending/receiving remote commands.

buffet::Daemon
----------------------------------------

![buffet::Daemon](https://github.com/yudatun/Documentation/tree/master/brillo/weaved/res/buffet::Daemon.png)

#### FlowChart

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
 +-> manager_->Start()
```

buffet::Manager
----------------------------------------

![buffet::Manager](https://github.com/yudatun/Documentation/tree/master/brillo/weaved/res/buffet::Manager.png)

#### FlowChart

```
buffet::Manager::Start()
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
 +-> buffet::Manager::CreateDevice()
     |
     +-> device_ = weave::Device::Create()
         |
         +-> new weave::DeviceManager::DeviceManager
```

weave::DeviceManager
----------------------------------------

![weave::DeviceManager](https://github.com/yudatun/Documentation/tree/master/brillo/weaved/res/weave::DeviceManager.png)

#### FlowChart

```
weave::DeviceManager::DeviceManager
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
 +-> weave::DeviceManager::StartPrivet()
      |
      +-> privet_ = new privet::Manager{task_runner_}
      |
      +-> privet->Start()
```

weave::privet::Manager
----------------------------------------

![weave::privet::Manager](https://github.com/yudatun/Documentation/tree/master/brillo/weaved/res/weave::privet::Manager.png)

#### privet::Manager::Start

```
privet::Manager::Start()
 |
 +-> device_ = DeviceDelegate::CreateDefault
 |
 +-> cloud_ = CludDelegate::CreateDefault
 |
 +-> security_ = new SecurityManager
 |
 +-> privet_handler_ = new PrivetHandler
 |
 +-> http_server->AddHttpRequestHandler(path, base::Bind(&Manager::PrivetRequestHandler,
 |                        weak_ptr_factory_.GetWeakPtr()))
 +-> http_server->AddHttpsRequestHandler(path, base::Bind(&Manager::PrivetRequestHandler,
                         weak_ptr_factory_.GetWeakPtr()))
```

* http_server is buffet::WebServClient

#### PrivetRequestHandler

```
privet::Manager::PrivetRequestHandler()
```

weave::privet::PrivetHandler
----------------------------------------

```
PrivetHandler::PrivetHandler()
 |
 +-> AddHandler("/privet/info", &PrivetHandler::HandleInfo, AuthScope::kNone);
 |
 +-> AddHandler("/privet/v3/pairing/start", &PrivetHandler::HandlePairingStart, AuthScope::kNone);
 |
 +-> AddHandler("/privet/v3/pairing/confirm", &PrivetHandler::HandlePairingConfirm, AuthScope::kNone);
 |
 +-> AddHandler("/privet/v3/pairing/cancel", &PrivetHandler::HandlePairingCancel, AuthScope::kNone);
 |
 +-> AddSecureHandler("/privet/v3/auth", &PrivetHandler::HandleAuth, AuthScope::kNone);
 |
 +-> AddSecureHandler("/privet/v3/accessControl/claim", &PrivetHandler::HandleAccessControlClaim, AuthScope::kOwner);
 |
 +-> AddSecureHandler("/privet/v3/accessControl/confirm", &PrivetHandler::HandleAccessControlConfirm, AuthScope::kOwner);
 |
 +-> AddSecureHandler("/privet/v3/setup/start", &PrivetHandler::HandleSetupStart, AuthScope::kManager);
 |
 +-> AddSecureHandler("/privet/v3/setup/status", &PrivetHandler::HandleSetupStatus,
 |                  AuthScope::kManager);
 +-> AddSecureHandler("/privet/v3/state", &PrivetHandler::HandleState,
 |                  AuthScope::kViewer);
 +-> AddSecureHandler("/privet/v3/commandDefs", &PrivetHandler::HandleCommandDefs,
 |                  AuthScope::kViewer);
 +-> AddSecureHandler("/privet/v3/commands/execute",
 |                  &PrivetHandler::HandleCommandsExecute, AuthScope::kViewer);
 +-> AddSecureHandler("/privet/v3/commands/status",
 |                  &PrivetHandler::HandleCommandsStatus, AuthScope::kViewer);
 +-> AddSecureHandler("/privet/v3/commands/cancel",
 |                  &PrivetHandler::HandleCommandsCancel, AuthScope::kViewer);
 +-> AddSecureHandler("/privet/v3/commands/list",
 |                  &PrivetHandler::HandleCommandsList, AuthScope::kViewer);
 +-> AddSecureHandler("/privet/v3/checkForUpdates",
 |                  &PrivetHandler::HandleCheckForUpdates, AuthScope::kViewer);
 +-> AddSecureHandler("/privet/v3/traits", &PrivetHandler::HandleTraits,
 |                  AuthScope::kViewer);
 +-> AddSecureHandler("/privet/v3/components", &PrivetHandler::HandleComponents,
 |                  AuthScope::kViewer);
```

buffet::WebServClient
----------------------------------------

![buffet::WebServClient](https://github.com/yudatun/Documentation/tree/master/brillo/weaved/res/buffet::WebServClient.png)

### WebServClient

```
buffet::WebServClient::WebServClient
 |
 +-> web_server_ = libwebserv::Server::ConnectToServerViaDBus("com.android.Weave")
 |
 +-> web_server_->OnProtocolHandlerConnected(base::Bind(&WebServClient::OnProtocolHandlerConnected,
 |                weak_ptr_factory_.GetWeakPtr()))
 +-> web_server_->OnProtocolHandlerDisconnected(base::Bind(&WebServClient::OnProtocolHandlerDisconnected,
 |                weak_ptr_factory_.GetWeakPtr()));
```

### AddHttpRequestHandler

```
buffet::WebServClient::AddHttpRequestHandler(
 | const std::string& path, const RequestHandlerCallback& callback))
 +-> web_server_->GetDefaultHttpHandler()->AddHandlerCallback(path, "",
 |    base::Bind(&WebServClient::OnRequest, weak_ptr_factory_.GetWeakPtr(), callback))
```

### OnRequest

```
buffet::WebServClient::OnRequest(const RequestHandlerCallback& callback,
 |  std::unique_ptr<libwebserv::Request> request, std::unique_ptr<libwebserv::Response> response)
 +-> std::unique_ptr<Request> weave_request{new RequestImpl{std::move(request), std::move(response)}}
 |
 +-> callback.Run(std::move(weave_request))
```

libwebserv::Server
----------------------------------------

### ConnectToServerViaDBus

```
libwebserv::Server::ConnectToServerViaDBus("com.android.Weave")
 |
 +-> server = new DBusServer
 |
 +-> server->Connect("com.android.Weave")
```

libwebserv::DBusServer
----------------------------------------

### DBusServer

```
libwebserv::DBusServer::DBusServer
 |
 +-> request_handler_ = new RequestHandler
 |
 +-> dbus_adaptor_ = new org::chromium::WebServer::RequestHandlerAdaptor(request_handler_)
```

### Connect

```
libwebserv::DBusServer::Connect
 |
 +-> service_name = "com.android.Weave"
 |
 +-> dbus_object_ = new brillo::dbus_utils::DBusObject(
 |       bus, dbus_adaptor_.GetObjectPath())
 |            |
 |            +-> dbus::ObjectPath("/org/chromium/WebServer/RequestHandler")
 +-> dbus_adaptor_->RegisterWithDBusObject(dbus_object_)
 |
 +-> dubs_object_->RegisterAsync()
 |
 +-> object_manager_ = new org::chromium::WebServer::ObjectManagerProxy{bus}
 |
 +-> object_manager_->SetServerAddedCallback(
 |     base::Bind(&DBusServer::Online, base::Unretained(this)));
 +-> object_manager_->SetServerRemovedCallback(
 |     base::Bind(&DBusServer::Offline, base::Unretained(this)));
 +-> object_manager_->SetProtocolHandlerAddedCallback(
 |     base::Bind(&DBusServer::ProtocolHandlerAdded, base::Unretained(this)));
 +-> object_manager_->SetProtocolHandlerRemovedCallback(
 |     base::Bind(&DBusServer::ProtocolHandlerRemoved, base::Unretained(this)));
```

### GetDefaultHttpHandler

```
libwebserv::DBusServer::GetDefaultHttpHandler
 |
libwebserv::DBusServer::GetProtocolHandler(ProtocolHandler::kHttp)
 |
libwebserv::DBusServer::GetProtocolHandlerImpl()
 |
 +-> protocol_handlers_names_.emplace(name,
 |     std::unique_ptr<DBusProtocolHandler>{new DBusProtocolHandler{name, this}}).first;
```

### OnProtocolHandlerConnected

```
DBusServer::OnProtocolHandlerConnected
 |
 +-> on_protocol_handler_connected_ = callback
```

### ProtocolHandlerAdded

```
libwebserv::DBusServer::ProtocolHandlerAdded(
 |  org::chromium::WebServer::ProtocolHandlerProxyInterface* handler))
 +-> protocol_handler_id_map_.emplace(handler->GetObjectPath(), handler->id());
 |
 +-> DBusProtocolHandler* registered_handler = GetProtocolHandlerImpl(handler->name());
 |
 +-> protocol_handlers_ids_.emplace(handler->id(), registered_handler);
 |
 +-> registered_handler->Connect(handler);
 |
 +-> on_protocol_handler_connected_.Run(registered_handler);
```

libwebserv::DBusProtocolHandler
----------------------------------------

### Connect

```
libwebserv::DBusProtocolHandler::Connect
 |
 +-> proxies_.emplace(proxy->GetObjectPath(), proxy)
 |
 +-> proxy->AddRequestHandlerAsync
```

### AddHandlerCallback

```
libwebserv::DBusProtocolHandler::AddHandlerCallback(const std::string& url, const std::string& method,
 |   const base::Callback<RequestHandlerInterface::HandlerSignature>& handler_callback)
 +-> handler{new RequestHandlerCallback{handler_callback}}
 |
 +-> libwebserv::DBusProtocolHandler::AddHandler(url, method, std::move(handler))
```

### AddHandler

```
libwebserv::DBusProtocolHandler::AddHandler(const std::string& url,
 |   const std::string& method, std::unique_ptr<RequestHandlerInterface> handler)
 +-> request_handlers_.emplace(++last_handler_id_, HandlerMapEntry{url, method,
 |     std::map<ProtocolHandlerProxyInterface*, std::string>{}, std::move(handler)});
 +-> proxy->AddRequestHandlerAsync
```