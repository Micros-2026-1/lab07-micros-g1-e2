[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/rb0M7Pn8)
[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-2e0aaae1b6195c2367325f4f02e2d04e9abb55f0b24a779b69b11b9e10269abc.svg)](https://classroom.github.com/online_ide?assignment_repo_id=23851507&assignment_repo_type=AssignmentRepo)
# Lab07: Visualización en LCD 16x2 usando módulo I²C con microcontrolador PIC

## Integrantes

*Andrés Felipe Muñoz Martinéz

*Paula Andrea Cortés Espinosa

*Reny Alexander Roncancio

*Laura Katerin Sanchez

## Documentación

En este laboratorio se desarrolló un sistema basado en el microcontrolador PIC18F45K22 capaz de:

-Leer señales analógicas mediante el ADC.
-Mostrar el voltaje en una pantalla LCD controlada por I²C.
-Enviar los datos mediante comunicación UART.
-Visualizar los datos en tiempo real usando Python y Matplotlib.

El proyecto integra varios protocolos de comunicación y módulos internos del PIC, permitiendo comprender el funcionamiento del MSSP, la comunicación serial y el control de periféricos externos.

## Objetivos

### Objetivo General 

Implementar un sistema de adquisición y visualización de datos utilizando el microcontrolador PIC18F45K22, integrando los módulos ADC, UART e I²C para la lectura de señales analógicas, visualización en una pantalla LCD y monitoreo en tiempo real mediante una interfaz gráfica en Python.

### Objetivos Específicos 

-Configurar el módulo ADC del PIC18F45K22 para realizar la conversión de señales analógicas a datos digitales.
-Implementar comunicación I²C utilizando el módulo MSSP del microcontrolador para controlar una pantalla LCD mediante el expansor PCF8574.
-Configurar la comunicación UART para transmitir los datos de voltaje desde el PIC hacia un computador.
-Desarrollar un programa en Python capaz de recibir los datos seriales y generar una gráfica en tiempo real del voltaje medido.
-Analizar las diferencias entre el control de LCD en modo paralelo y en modo I²C, identificando ventajas y desventajas de cada método.
-Comprender el funcionamiento de los registros de configuración del MSSP y del protocolo I²C aplicado en sistemas embebidos.

## Procedimiento

Se configuró el microcontrolador PIC18F45K22 utilizando el compilador XC8 y un oscilador interno de 16 MHz. Posteriormente, se inicializó el módulo ADC para leer una señal analógica desde el pin RA0 y convertirla a un valor de voltaje. Luego, se implementó la comunicación I²C mediante el módulo MSSP para controlar una pantalla LCD a través del expansor PCF8574, permitiendo visualizar el voltaje medido. También se configuró la comunicación UART a 9600 baudios para transmitir los datos al computador. Finalmente, se desarrolló un programa en Python utilizando las librerías PySerial y Matplotlib para recibir los datos enviados por el PIC y generar una gráfica en tiempo real del comportamiento del voltaje leído por el ADC.

## Evidencias de implementación

### 1. I²C como Half-Duplex
El protocolo I²C utiliza únicamente dos líneas:

-SDA → datos
-SCL → reloj

La línea SDA es bidireccional, lo que significa que tanto el maestro como el esclavo utilizan el mismo cable para transmitir y recibir información.

Sin embargo, en un instante determinado solo un dispositivo puede transmitir datos. Por esta razón, I²C se clasifica como un protocolo Half-Duplex, Es decir,puede transmitir y recibir,pero no simultáneamente.

### SPI como Full-Duplex
SPI utiliza líneas separadas para transmisión y recepción:

-MOSI→ Master Out Slave In
-MISO → Master In Slave Out
-SCL → reloj
-SDA → selección de esclavo

Como existen líneas independientes para enviar y recibir datos, ambos procesos pueden ocurrir al mismo tiempo, por ello SPI se clasifica como Full-Duplex.

Implicación práctica en el control de una LCD mediante I²C:

El microcontrolador únicamente envía datos,la LCD normalmente no responde información y la velocidad no es crítica.
Por tanto, el hecho de que I²C sea Half-Duplex no representa una limitación importante.

Sin embargo, la actualización de pantalla es más lenta cada dato requiere varias transmisiones I²C y existe más sobrecarga de comunicación.
Con SPI la actualización sería más rápida debido a la transmisión simultánea y a una mayor velocidad de reloj.

## Diagrama del montaje

<img width="1227" height="778" alt="WhatsApp Image 2026-05-15 at 10 35 52 PM" src="https://github.com/user-attachments/assets/640a0f2d-e66c-4b6b-a8ee-82d388e300be" />

## Python 

En este proyecto, Python cumple la función de recibir y procesar los datos enviados por el microcontrolador PIC18F45K22 mediante comunicación UART. El programa desarrollado en Python abre el puerto serial, lee continuamente los valores de voltaje transmitidos por el PIC, extrae la información numérica y la representa gráficamente en tiempo real utilizando la librería Matplotlib. De esta manera, Python actúa como una interfaz de monitoreo que permite visualizar el comportamiento del voltaje medido por el ADC de forma dinámica y más fácil de interpretar.

## Desarrollo del codigo 

SSPCON1 = 0x28;

 Bit          Valor   Función         
 SSPEN        1       Activa MSSP     
 CKP          0       Clock idle      
 SSPM3:SSPM0  1000    Modo I²C Master 

 SSPM3:SSPM0 = 1000 --> I²C Master Mode
 
 -iniciar las transmisiones.
 -enviar direcciones y datos al PCF8574.

while(!PIR1bits.SSPIF);
-Quiere decir que, una transmisión terminó o una operación I²C finalizó.

#pragma config PBADEN = OFF
-En este proyecto garantiza que el puerto B arranque como digital, ya que usamos varios pines de datos digitales, especialmente para UART, LCD y control general.

TRISCbits.TRISC3 = 1;
TRISCbits.TRISC4 = 1;
-Es más práctico ya que nos permite usar dos cables para el montaje y envío de datos, en desventaja tenemos que hace muchas transmisiones I²C por cada carácter.Dando por hecho que el código es más complejo, y la LCD actualiza más lento.

#define LCD_ADDRESS 0x4E
Porque I²C permite que muchos esclavos compartiendo SDA y SCL.El maestro selecciona cuál usar mediante la dirección.Dejando el sistema funcionando asi lee el voltaje adc_val = ADC_Read(); , muestra el voltaje MostrarVoltajeLCD(adc_val); , envia datos UART_WriteVoltage(adc_val); y por ultimo en Python recibe y grafica los datos en tiempo real.

## Conclusiones
-El protocolo I²C simplifica considerablemente el hardware al requerir únicamente dos líneas de comunicación.
-El módulo MSSP del PIC permite implementar I²C de manera eficiente mediante registros especializados.
-La bandera SSPIF es fundamental para sincronizar correctamente cada operación serial.
-Aunque I²C es más lento y complejo que el modo paralelo, ofrece mayor escalabilidad y organización del sistema.


