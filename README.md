# Sensor-DHT22-y-sensor-ultrasonico-HC-SR04-con-node-red

## Introduccion

Node-RED es una plataforma de programación visual basada en flujos, que permite a los usuarios conectar dispositivos hardware, API y servicios en línea de forma intuitiva. Permite construir aplicaciones basadas en eventos, integrando información de diversas fuentes y realizando acciones en consecuencia, todo a través de una interfaz web fácil de usar. 

### Descripcion

Este repositorio muestra como podemos programar una ESP32 con el sensor ultrasonico (HC-SR04), el sensor DTH22 utilizando NODE-RED como plataforma visual.

## Material necesario

-WOKWI

-Tarjeta ESP32

-Sensor ultrasonico (HC-SR04)

-Sensor DTH22

-Plataforma NODE RED

## Instrucciones de preparación de entorno

1. Abrir NODE RED y colocar los siguientes nodos:

   -MQQT IN y colocar la IP : 52.29.87.71 como se muestra en la siguiente imagen.

![]()
 
   - Colocar el bloque JSON y configurarlo como se muestra en la imagen.

![]()

   -Colocamos tres bloques function y los configuramos con los siguentes codigos.

```
msg.payload = msg.payload.TEMPERATURA;
msg.topic = "TEMPERATURA";
return msg;
```

```
msg.payload = msg.payload.HUMEDAD;
msg.topic = "HUMEDAD";
return msg;
```

```
msg.payload = msg.payload.DISTANCIA;
msg.topic = "DISTANCIA";
return msg;
```

   -Colocamos tres bloques gauge y los configuramos como se muestran en las siguientes imagenes.
   
![]()

![]()

![]()

   -Colocamos un bloque chart y lo configuramos como se muestran en la siguiente imagen.

![]()

2. Realizamos las siguientes conexiones como se muestran en la siguiente imagen.

![]()

3. Abrir la terminal de programación (esp32) y colocar la siguente programación:

```
#include <ArduinoJson.h>
#include <WiFi.h>
#include <PubSubClient.h>
#define BUILTIN_LED 2
#include "DHTesp.h"
const int DHT_PIN = 15;
DHTesp dhtSensor;
// Update these with values suitable for your network.

const int Trigger = 4;   //Pin digital 2 para el Trigger del sensor
const int Echo = 2;   //Pin digital 3 para el Echo del sensor

const char* ssid = "Wokwi-GUEST";
const char* password = "";
const char* mqtt_server = "52.29.87.71";
String username_mqtt="OSCAROV2058";
String password_mqtt="1234";

WiFiClient espClient;
PubSubClient client(espClient);
unsigned long lastMsg = 0;
#define MSG_BUFFER_SIZE  (50)
char msg[MSG_BUFFER_SIZE];
int value = 0;

void setup_wifi() {

  delay(10);
  // We start by connecting to a WiFi network
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);

  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  randomSeed(micros());

  Serial.println("");
  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
}

void callback(char* topic, byte* payload, unsigned int length) {
  Serial.print("Message arrived [");
  Serial.print(topic);
  Serial.print("] ");
  for (int i = 0; i < length; i++) {
    Serial.print((char)payload[i]);
  }
  Serial.println();

  // Switch on the LED if an 1 was received as first character
  if ((char)payload[0] == '1') {
    digitalWrite(BUILTIN_LED, LOW);   
    // Turn the LED on (Note that LOW is the voltage level
    // but actually the LED is on; this is because
    // it is active low on the ESP-01)
  } else {
    digitalWrite(BUILTIN_LED, HIGH);  
    // Turn the LED off by making the voltage HIGH
  }

}

void reconnect() {
  // Loop until we're reconnected
  while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");
    // Create a random client ID
    String clientId = "ESP8266Client-";
    clientId += String(random(0xffff), HEX);
    // Attempt to connect
    if (client.connect(clientId.c_str(), username_mqtt.c_str() , password_mqtt.c_str())) {
      Serial.println("connected");
      // Once connected, publish an announcement...
      client.publish("outTopic", "hello world");
      // ... and resubscribe
      client.subscribe("inTopic");
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");
      // Wait 5 seconds before retrying
      delay(5000);
    }
  }
}

void setup() {
  pinMode(BUILTIN_LED, OUTPUT);     // Initialize the BUILTIN_LED pin as an output
  Serial.begin(115200);
  setup_wifi();
  client.setServer(mqtt_server, 1883);
  client.setCallback(callback);
  dhtSensor.setup(DHT_PIN, DHTesp::DHT22);

  pinMode(Trigger, OUTPUT); //pin como salida
  pinMode(Echo, INPUT);  //pin como entrada
  digitalWrite(Trigger, LOW);//Inicializamos el pin con 0

}

void loop() {

long t; //tiempo que demora en llegar el eco
  long d; //distancia en centimetros

  digitalWrite(Trigger, HIGH);
  delayMicroseconds(10);          //Enviamos un pulso de 10us
  digitalWrite(Trigger, LOW);
  
  t = pulseIn(Echo, HIGH); //obtenemos el ancho del pulso
  d = t/59;             //escalamos el tiempo a una distancia en cm
  
delay(1000);
TempAndHumidity  data = dhtSensor.getTempAndHumidity();
  if (!client.connected()) {
    reconnect();
  }
  client.loop();

  unsigned long now = millis();
  if (now - lastMsg > 2000) {
    lastMsg = now;
    //++value;
    //snprintf (msg, MSG_BUFFER_SIZE, "hello world #%ld", value);

    StaticJsonDocument<128> doc;

    doc["DEVICE"] = "ESP32";
    //doc["NOMBRE"] = "OSCAR OCAMPO";
    doc["TEMPERATURA"] = String(data.temperature, 1);
    doc["HUMEDAD"] = String(data.humidity, 1);
doc["DISTANCIA"] = String(d);

    String output;
    
    serializeJson(doc, output);

    Serial.print("Publish message: ");
    Serial.println(output);
    Serial.println(output.c_str());
    client.publish("DiplomadoOJOV", output.c_str());
  }
}
```

4. Instalar las siguientes librerias:
   
   -DTH sensor library for ESPx.

   -ArduinoJson
   
   -WiFi
   
   -PubSubClient

5. Hacer la conexion del sensor ultrasonico y DHT22 con la ESP32 como se muestra en la siguente imagen.

![]()

6. Al hacer la conexion WIFI en la pagina NODE RED debemos seleccionar el boton DEPLOY para cargar los datos.

## Instrucciónes de operación

1. Iniciar simulador.

2. Colocar la temperatura y humedad dando doble click al sensor DHT22.
   
3. Colocar la distancia dando doble click al sensor ultrasonico.

4. Visualizar en la seccion dashboard los gauges y la grafica generada en tiempo real.

## Resultados

Cuando haya funcionado, verás los valores de los sensores reflejajdos en los gauges como se muestra en las siguentes imagenes, variando los valores de temperatura, humedad y distancia.

![]()

![]()

![]()


## Creditos

Desarrollado por Ing. Oscar Jair Ocampo Villarreal
- ([github](https://github.com/OSCAROV2058))

