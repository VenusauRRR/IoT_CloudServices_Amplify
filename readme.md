## System Architecture

ESP32-enheten samlar in data om temperatur och luftfuktighet med hjälp av en DHT11-sensor och skickar den på ett säkert sätt till AWS IoT Core via MQTT över TLS.

AWS IoT Core använder två IoT-regler: den ena aktiverar en Lambda-funktion som bearbetar och lagrar telemetridata i DynamoDB via Amplify GraphQL-API:et, medan den andra skickar en varning via Discord när temperaturen överstiger 28 °C.

Lambda-funktionen skickar även regelbundna notiser till Discord, förutsatt att detta har aktiverats för enheten. React-gränssnittet hämtar enhetsinformation och sensordata via Amplify GraphQL-API:et.

Säkerheten säkerställs genom X.509-enhetscertifikat, AWS IoT-policyer och TLS-kryptering.

Amplify url: https://master.d33olrgbgk9j91.amplifyapp.com/

```mermaid
flowchart TD
    A["ESP32 + DHT11"] --> |wifi| M["Internet"]
    M --> |TLS+MQTT| B["AWS IoT Core"]
    B --> |IoT Rule| D["AWS Lambda"]
    B --> |IoT Rule| E["AWS Lambda"]
    E --> |alert: if temperature > 28|I
    D --> G["Amplify GraphQL API"]
    G --> |store telemetry|H["DynamoDB"]
    D --> |HTTPS webhook: if enabled|I["Discord Webhook"]
    I --> F["Discord"]
    G --> |telemetry model|J["React Frontend"]
    N["AWS IoT Policy"] --> |device authorization|B
    O["X.509 Device Certificate"] --> |device authentication|B
```

## Screenshots

<img src="AmplifyGraph.png" height="200" alt="AmplifyGraph" /> <img src="DiscordMsg.png" height="200" alt="DiscordMessage" />
