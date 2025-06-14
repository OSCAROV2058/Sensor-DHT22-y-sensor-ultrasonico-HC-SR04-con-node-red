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

   -Colocamos tres bloques function y lo configuramos con los siguentes codigos.

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

   -

   -

   -
1. Abrir la terminal de programación y colocar la siguente programación:





```

```

2. Instalar la libreria de LiquidCrystal I2C y DTH sensor library for ESPx como se muestra en la siguente imagen.

![]()
3. Hacer la conexion del sensor ultrasonico, DHT22 y LCD con la ESP32 como se muestra en la siguente imagen.

![]()
## Instrucciónes de operación

1. Iniciar simulador.

2. Visualizar los datos en el monitor serial. (CURSO, NOMBRE Y CARRERA, FECHA).

3. Colocar la temperatura y humedad dando doble click al sensor DHT22.
   
4. Colocar la distancia dando doble click al sensor ultrasonico.

## Resultados

Cuando haya funcionado, verás los valores dentro del monitor serial como se muestra en las siguentes imagenes.

![]()

![]()

![]()

![]()

![]()

## Creditos

Desarrollado por Ing. Oscar Jair Ocampo Villarreal
- ([github](https://github.com/OSCAROV2058))

