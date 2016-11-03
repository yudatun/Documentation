webservd
========================================

path: system/webservd

```
main
 |
 +-> Daemon::RegisterDBusObjectsAsync
     |
     +-> webservd::Server::RegisterAsync
         |
         +-> webservd::Server::CreateProtocolHandler
             |
             +-> webservd::ProtocolHandler::Start
                 |
                 +-> MHD_start_daemon (external/libmicrohttpd) -> webservd::ServerHelper::ConnectionHandler
                 |
                 +-> webservd::Server::ProtocolHandlerStarted
                 |   |
                 |   +-> new DBusProtocolHandler
                 +-> webservd::ProtocolHandler::DoWork
```