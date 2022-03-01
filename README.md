# Flecs Explorer
Web-based UI for monitoring Flecs applications, trying out queries &amp; learning ECS

<img width="995" alt="Screen Shot 2021-11-07 at 10 20 51 PM" src="https://user-images.githubusercontent.com/9919222/140693729-12b9c7bb-3147-4040-9c15-d830e9bc1080.png">

A live version of the explorer is running @ https://flecs.dev/explorer

## Usage
The flecs explorer can be used in standalone mode (default) or remote mode. In standalone mode, the application runs 100% in the browser with a webasm build of Flecs. In remote mode, the explorer connects to a running flecs application.

### Connecting to a running Flecs application
Before connecting the explorer to an application, first make sure that the REST interface is enabled:

In C:
```c
ecs_singleton_set(world, EcsRest, {0});
```

In C with the flecs app addon:
```c
ecs_app_run(world, &(ecs_app_desc_t) {
  .enable_rest = true
});
```

In C++:
```cpp
world.set<flecs::rest::Rest>({});
```

When the application is running, verify that the server works by going to:
http://localhost:27750/entity/flecs

This should return a JSON string that looks similar to:
```json
{"path":"flecs", "type":[{"pred":"Module"}, {"pred":"Identifier", "obj":"Name"}, {"pred":"flecs.doc.Description", "obj":"flecs.doc.Brief", "value":{"value":"Flecs root module"}}, {"pred":"flecs.doc.Description", "obj":"flecs.doc.Link", "value":{"value":"https://github.com/SanderMertens/flecs"}}]}
```

You can now go to https://flecs.dev/explorer which should automatically connect to your application. 

The explorer sends a request with a short timeout to determine if a running application can be found. In some cases this timeout is too short, which can cause the explorer to sometimes not connect. To fix this, add `?remote=true` to the URL (See URL options).

Note that _no_ data is sent from your application to a remote machine. The explorer runs 100% in the browser, so any information sent to the explorer uses a local loopback interface (in other words, no information leaves your machine).

The following browsers have known policies that prevent connecting to localhost from a remote URL:
 - Safari
 - Brave (can be overridden by configuring "Shield" to be down)

To get around this, you can:

### Host the explorer locally
If your browser does not support connecting to localhost from a remote URL, or you just prefer to host the explorer yourself, first clone the repository:

```
git clone https://github.com/flecs-hub/explorer
```

Then start an HTTP server in the `etc` folder:

```
cd explorer/etc
python3 -m http.server
```

You can now go to http://localhost:8000 to open the explorer.

### URL options
The following options can be added to the URL:

**Always connect to remote app (will keep trying to connect to localhost:27750)**
```
?remote=true
```

**Always connect to app on same URL as explorer (will keep trying to connect to url:27750)**
```
?remote_self=true
```

**Never connect to remote app (will always use the webasm version of flecs that runs in the browser)**
```
?local=true
```

**Specify a custom host to connect to (sets remote to true)**
```
?host=10.20.30.40:1234
```
