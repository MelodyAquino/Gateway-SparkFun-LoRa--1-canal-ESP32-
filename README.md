# Gateway-SparkFun-LoRa--1-canal-ESP32-

Materiales requeridos
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 
Para alimentar y programar la placa, necesitará un cable USB micro-B 
Computadora con Arduino instalado
 Antena  915MHz con la ganancia requerida por su aplicación
 Puede usar el conector U.FL incluido, con un adaptador U.FL a SMA y una antena SMA,  o soldar en una tira de cable de ~ 3 pulgadas 
 
Configuración de hardware
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Antena
-
Para permitir que la placa se comunique con otros dispositivos LoRa, deberá agregar una antena. Esto se puede conectar al conector U.FL o al pin ANT de la placa.
El conector U.FL se puede conectar a un cable adaptador SMA a U.FL , que luego se puede emparejar con una antena Duck de 915 MHz .
En lugar de una antena U.FL, también funcionará una tira de cable soldado al pin ANT y pegado hacia arriba. A continuación, se indican las longitudes de cable para antenas de cuarto de onda a 915 MHz .

Encendido de la placa
-
La placa se alimenta nominalmente a través del conector micro-USB integrado. El otro extremo de su cable USB se puede conectar a una computadora, adaptador de pared o una batería USB.
 Alternativamente, la placa se puede alimentar con una fuente de alimentación regulada de 3.3V . Este suministro debe aplicarse a los pines de 3.3V y GND.
 
Configuración de Arduino IDE
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Para configurar el software de la puerta de enlace, deberá instalar el núcleo Arduino ESP32, así como las dependencias de la biblioteca del boceto de la puerta de enlace ESP32 LoRa.
Configuración de la placa Arduino
-
El código de ejemplo y las bibliotecas de esta placa están escritos para el IDE de Arduino. Si aún no lo ha hecho, deberá instalar el núcleo Arduino para ESP32 . El núcleo ESP32 Arduino debe instalarse manualmente. Puede encontrar los archivos principales en GitHub de espressif: https://github.com/espressif/arduino-esp32 . Siga las instrucciones de instalación para agregar el núcleo a su IDE de Arduino.


 
Nota : Chequear en Administrador de dispositivos si aparece un signo de admiración al lado del puerto usb, se debe instalar el driver correspondiente 
Link del driver utilizado en esta guía: https://www.dropbox.com/s/4y0u2pbj6t45u8z/DCcduino_ch340-drivers.zip?dl=0


Gateway LoRaWAN de un solo canal
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Descarga de la biblioteca 
-
Descargar una biblioteca para ejecutar LoRaWAN y modificarla para adaptarla a la placa y necesidades.El código de la puerta de enlace ESP 1-ch está alojado en GitHub por things4u donde se  pueden obtener las últimas actualizaciones.
Link al archivo zip utilizado en esta guía: https://cdn.sparkfun.com/assets/learn_tutorials/8/2/6/ESP-1ch-Gateway-v5.0-master.zip

Este repositorio incluye  el boceto de Arduino y las bibliotecas de las que depende. Antes de compilar el boceto, deberá extraer todas las bibliotecas de la carpeta "biblioteca" del repositorio en la carpeta "bibliotecas" de su cuaderno de bocetos Arduino. Para la instalación de las bibliotecas, consulte la sección Introducción del archivo README. 

Abra el archivo ESP-sc-gway.ino . Cuando se cargue el IDE, debería incluir otra docena de pestañas más o menos. 

Configuraciones -
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Antes de cargar el boceto de ESP-1ch-Gateway , debe realizar algunas modificaciones en un par de archivo
Archivo ESP-sc-gway.hç

Este archivo es la fuente principal de configuración del esquema de la puerta de enlace. Las definiciones que probablemente tendrá que modificar son:
Radio
_LFREQ- Esto establece el rango de frecuencia en el que se comunicará su radio. Configure esto en 915(AU)
_SPREADING- Esto establece el factor de dispersión de LoRa. SF7, SF8, SF9, SF10, SF11, ó  SF12 puede ser utilizado. Tenga en cuenta que esto afectará con qué dispositivos se puede comunicar su puerta de enlace.
_CAD- Detección de actividad del canal. Si está habilitado (establecido en 1), CAD permitirá que la puerta de enlace supervise los mensajes enviados en cualquier factor de propagación. La compensación si está habilitada: es posible que la radio no capte señales muy débiles.
Hardware
OLED- Esta placa no incluye un OLED, configúrelo en 0 .
_PIN_OUT- Esto configura el SPI y otros ajustes de hardware. Establezca esto en 6 , agregaremos una definición de hardware personalizada más adelante.
CFG_sx1276_radio- Asegúrese de que esto esté definido y que CFG_sx1272_radio no lo esté . Esto configura la radio LoRa conectada al ESP32.
The Things Network (TTN)
_TTNSERVER- El enrutador TTN al que su puerta de enlace entregará sus datos. Se recomienda   au915.thethings.meshed.com.au  
_TTNPORT- 1700 es el puerto estándar para TTN
_DESCRIPTION - Personaliza el nombre de tu puerta de enlace
_EMAIL -  Su dirección de correo electrónico o la del propietario de la pasarela
_LATy _LON - coordenadas GPS de su puerta de enlace
Wifi
Agregue al menos una red WiFi a la wpas wpa[]matriz, pero deje la primera entrada en blanco. Por ejemplo:
wpas wpa[] = {
  { "" , "" },                          // Reserved for WiFi Manager
  { "my_wifi_network", "my_wifi_password" }
};
 
Para valores que se pueden configurar opcionalmente consulte la sección Edición de ESP-sc-gway.h del archivo README.

Archivo loramodem.h
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Este archivo define cómo se configura el módem LoRa, incluidos los canales de frecuencia que puede usar y los pines que usa el ESP32 para comunicarse con él. Tenga cuidado al modificar la mayoría de las definiciones, debe   modificar las declaraciones _PIN_OUT.
Busque la línea que dice #error "Pin Definitions _PIN_OUT must be 1(HALLARD) or 2 (COMRESULT)"y elimínela . Luego copie y pegue estas líneas en su lugar (entre #elsey #endif):

struct pins {
  uint8_t dio0 = 26;
  uint8_t dio1 = 33;
  uint8_t dio2 = 32;
  uint8_t ss = 16;
  uint8_t rst = 27; // Reset not used
} pins;
#define SCK  14
#define MISO 12
#define MOSI 13
#define SS  16
#define DIO0 26
 
La int freqs[]matriz se puede ajustar, si desea usar diferentes subbandas.

Suba el boceto
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Con las modificaciones realizadas, intente compilar y cargar el boceto en su ESP32. Después de que se cargue, abra su monitor en serie y configure la velocidad en baudios en 115200 . Los mensajes de depuración aquí pueden ser muy útiles, y encontrar la dirección IP de su puerta de enlace es fundamental si desea monitorear el servidor web.

![image](https://user-images.githubusercontent.com/72763026/108857765-2df7f300-75ca-11eb-8a22-f66f6523bd51.png)

El boceto puede tardar mucho en configurarse la primera vez; formateará su sistema de archivos SPIFFS y creará un archivo de configuración no volátil. Una vez que esté completo, debería ver el ESP32 intentar conectarse a su red WiFi, luego inicializar la radio.



Una vez que el ESP32 se haya conectado, búsquelo para imprimir una dirección IP. Abra el navegador web de su computadora y conéctelo a la barra de direcciones. Debería ser recibido por el portal web ESP Gateway Config

![image](https://user-images.githubusercontent.com/72763026/108858611-1ff6a200-75cb-11eb-8646-cb8a5d8eba01.png)




Portal  web  ESP Gateway Config.
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Esta página web se puede usar para monitorear los mensajes que llegan y las frecuencias y factores de propagación en los que ingresaron. También se puede utilizar para cambiar la configuración de su puerta de enlace sobre la marcha. Puede ajustar el canal o el factor de propagación, activar / desactivar CAD o incluso activar el salto de frecuencia simplista.

Solución de problemas
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Si copia su dirección IP en el navegador web  y está configurando por primera vez el boceto, esta configuración puede tardar y  puede recibir  un mensaje que indica que no se puede acceder al sitio web. Espere a que la configuración termine e intente nuevamente. 
Si copia su dirección IP en el navegador web y recibe un mensaje que indica que no se puede acceder al sitio web, asegúrese de que su LoRaWAN y su computadora estén conectados a la misma red.




















