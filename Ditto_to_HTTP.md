
# Connection between Ditto to HTTP 

## Screenshots

![App Screenshot](https://www.eclipse.org/ditto/images/ditto-overview-1.png)


#### 
At the Ditto API there are different ways for getting notified of such events:

- Via the WebSocket API a WebSocket client gets all Events the authenticated subject (e.g. a user) is authorized to receive as Ditto Protocol messages.
- Via HTTP SSEs a consumer of the SSE EventSource gets all Events the authenticated subject (e.g. a user) is authorized to receive directly in the format of the changed entity (e.g. as Thing JSON format).
- Via an established connection in the connectivity service

## Server sent events
[Reference](/https://www.eclipse.org/ditto/basic-changenotifications.html "ref")

### Connection
[connection details](https://www.eclipse.org/ditto/docson/index.html#../jsonschema/connection.json$$expand)

### Establishing connecting to an HTTP endpoint
#### Important attributes in connection
        "connectionType": "http-push",
        "uri": "http://ditto:ditto@192.168.64.14:31603",
        "address": "PUT:/api/2/things/{{ thing:id }}",
        "topics": [
          "_/_/things/twin/events"
        ],

#### Sample Connection Payload

    curl --location --request POST 'http://192.168.64.14:31603/devops/piggyback/connectivity' \
    --header 'Content-Type: application/json' \
    --header 'x-ditto-pre-authenticated: ngnix:ditto' \
    --data-raw '{
        "targetActorSelection": "/system/sharding/connection",
        "headers": {
            "aggregate": false
        },
        "piggybackCommand": {
            "type": "connectivity.commands:createConnection",
    "connection": {
        "id": "http-example-connection-1",
        "connectionType": "http-push",
        "connectionStatus": "open",
        "failoverEnabled": true,
        "uri": "http://ditto:ditto@192.168.64.14:31603",
        "specificConfig": {
        "parallelism": "2"
        },
        "sources": [],
        "targets": [
        {
            "address": "PUT:/api/2/things/{{ thing:id }}",
            "topics": [
            "_/_/things/twin/events"
            ],
            "authorizationContext": ["ditto:outbound-auth-subject"],
            "headerMapping": {
            "content-type": "{{ header:content-type }}"
            }
        }
        ]
    }
    }
    }'

# Implement by Javascript Example
        <script>
            let source = new EventSource('http://192.168.64.14:31603/api/2/things?ids=org.eclipse.packages.c2e:demo-device', { withCredentials: true });
            if(typeof(EventSource) !== "undefined") {
                    source.onopen = function(event) {
                document.getElementById("result").innerHTML += "<br>open";
                };
                source.onmessage = function (event) {
                    
                if(event.data.length==0){
                    console.log((event.data));
                }else{
                    console.log((event.data));
                    document.getElementById("result").innerHTML += "<br> "+event.data+"---message<br>";
                }
                    
                }

            }
            function close(){
                source.close();
            }
        </script>

# Implement by Jaxrs
### Code snippet in Java
        public class SseClientApp {

        private static final String url = "http://192.168.64.14:31603/api/2/things?ids=org.eclipse.packages.c2e:demo-device";

        public static void main(String... args) throws Exception {

        // Client client = ClientBuilder.newClient();
            HttpAuthenticationFeature feature = HttpAuthenticationFeature.basic("ditto", "ditto");
            Client client = ClientBuilder.newBuilder().register(feature).build();

            WebTarget target = client.target(url);
            System.setProperty("sun.net.http.allowRestrictedHeaders", "true");
            try (SseEventSource eventSource = SseEventSource.target(target).build()) {

                eventSource.register(onEvent, onError, onComplete);
                eventSource.open();

                //Consuming events for one hour
                Thread.sleep(60 * 60 * 1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            client.close();
            System.out.println("End");
        }
### References
[Source Code](https://www.baeldung.com/java-ee-jax-rs-sse,"Ref")

# on-hold
- Implementation by webflux
- RQL

## Authors

- Amit Raghav






