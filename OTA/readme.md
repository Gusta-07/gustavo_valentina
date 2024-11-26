```cpp
#include <ESP8266WiFi.h>

#include <ArduinoOTA.h>
```

```cpp

const char* ssid = "Prof.Rafael";
define o nome da rede que o controlador vai se conectar
```

```cpp
envolve configuração de redes wifi, por exemplo, microcontroladores

```
```cpp
const char* password = "senhasenha";
```

configuração especialmente em dispositivos IoT


```cpp
const int LED_PIN = 5; // D1
```
usado em projeto como arduíno 



```cpp
WiFiClient client;
```
usado para funções de à comunicação em redes TCP/IP


```cpp
void setup() {
```
ponto de partida 

```cpp
  pinMode(LED_PIN, OUTPUT);
```
  mandar energia pro led 

```cpp
  Serial.begin(9600);
```
  configurar comunicação

```cpp
  WiFi.mode(WIFI_STA);
```
   controlador manda dados para o projeto

   ```cpp
  WiFi.begin(ssid, password);
```
  liberação de acesso a rede wifi



```cpp

  while (WiFi.waitForConnectResult() != WL_CONNECTED) {
```
comando repetitivo em frente ao código

```cpp
    Serial.println("Conexão falhou, tentando novamente...");
```

comunicação com o controlador com o computador 

```cpp
    WiFi.begin(ssid, password);
```

comando para o equipamento ter acesso a rede 

```cpp
    delay(5000);
```
  pausa pro dispositivo a cada 5 segundos 

  }


```cpp
  ArduinoOTA.onStart([]() {
```
  desconectar equipamento antes de uma possível atualização

  ```cpp
    Serial.println("Iniciando atualização OTA...");
```

comnunicação do controlador com o computador 
  });

```cpp
  ArduinoOTA.onEnd([]() {
```
  depois de executar atualização, faz o necessário

```cpp
    Serial.println("\nAtualização concluída.");
```

    atualização concluída 

  });
``` cpp
  ArduinoOTA.onProgress([](unsigned int progress, unsigned int total) {
```
  configura o OTA enquanto ocorre atualização 

```cpp
    Serial.printf("Progresso: %u%%\r", (progress / (total / 100)));
```
    formata a mensagem e escreve a linha anterior

  });
```cpp
  ArduinoOTA.onError([](ota_error_t error) {
```
  identifica erros na atualização do OTA

```cpp
    Serial.printf("Erro[%u]: ", error);
```
mostra código do erro 

````cpp
    if (error == OTA_AUTH_ERROR) Serial.println("Falha na autenticação");
```
    analisa o erro e confirma se é falha de autenticação

```cpp
    else if (error == OTA_BEGIN_ERROR) Serial.println("Falha no início");
```
    detectado possível erro após atualização do OTA

```cpp
    else if (error == OTA_CONNECT_ERROR) Serial.println("Falha na conexão");
```
    verifica erro de conexão 

```cpp
    else if (error == OTA_RECEIVE_ERROR) Serial.println("Falha no recebimento");
```
    falha no recebimento de dados depois da atualização do OTA

```cpp
    else if (error == OTA_END_ERROR) Serial.println("Falha no finalização");
```
    erro no fim da atualização do OTA

  });


```cpp
  ArduinoOTA.begin();
```
recebe novas atualizações de versões do OTA 

```cpp
  Serial.println("Pronto para atualizações OTA.");
```
  envia mensagem dizendo estar preparado para receber atualização OTA

```cpp
  Serial.print("Endereço IP: ");
```
  mostra de endereço IP

```cpp
  Serial.println(WiFi.localIP());
```
  imprime endereço IP para encontrr n rede wifi



}



void loop() {

  ArduinoOTA.handle();


}
