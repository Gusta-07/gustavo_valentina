```cpp
#include <ESP8266WiFi.h>
#include <ArduinoOTA.h>
```
importando as bibliotecas acima 
```Cpp
const char* ssid = "iPhone de Yuri";
const char* password = "batata1234";
```
 inclusão da rede ( nome da rede e senha)

```cpp
const int LED_PIN = 5;
```
 Define o pino do LED como 5 equivalente ao D1
```cpp
IPAddress local_IP(172,20,10,13);
```
Definindo um IP fixo (no caso o da nossa faixa)
```cpp
IPAddress gateway(172,20,10,1);
```
inserindo o gatway
```cpp
IPAddress subnet(255, 255, 255, 0);
```
 define a máscara sa subrede

```cpp
WiFiServer server(23);
```
 Cria um servidor WiFi na porta 23
```cpp
WiFiClient client;
```
Define um cliente para se comunicar com os dispositivos 

```cpp
void setup() {

  pinMode(LED_PIN, OUTPUT);
```
 Define o pino do LED como saída
 ```cpp
  Serial.begin(9600);
```
 Inicia a comunicação serial com velocidade de 9600 bps.
  ```cpp
  WiFi.mode(WIFI_STA);
 ```
  Configura o WiFi no modo Station (para conectar-se a uma rede WiFi).
   ```cpp
  WiFi.begin(ssid, password);
 ```
  Conecta à rede WiFi usando o nome (SSID) e a senha fornecidos.



  ```cpp
  while (WiFi.waitForConnectResult() != WL_CONNECTED) {
 ```
   Verifica se a conexão WiFi foi bem-sucedida.
  ```cpp
    Serial.println("Conexão falhou, tentando novamente...");
 ```
      Exibe mensagem em caso de falha
  ```cpp
    WiFi.begin(ssid, password);

    delay(5000);

  }
 ```
  Espera 5 segundos antes de tentar novamente.


  ```cpp
  ArduinoOTA.onStart([]() {
 ```
  Configuração para atualizações OTA 

  ```cpp
    Serial.println("Iniciando atualização OTA...");
 ```
  });
   Informa que a atualização OTA começou.
 ```cpp
  ArduinoOTA.onEnd([]() {

    Serial.println("\nAtualização concluída.");

  });
 ```
Informa que a atualização OTA foi concluída
 ```cpp
  ArduinoOTA.onProgress([](unsigned int progress, unsigned int total) {

    Serial.printf("Progresso: %u%%\r", (progress / (total / 100)));

  });

  ArduinoOTA.onError([](ota_error_t error) {

    Serial.printf("Erro[%u]: ", error);

    if (error == OTA_AUTH_ERROR) Serial.println("Falha na autenticação");

    else if (error == OTA_BEGIN_ERROR) Serial.println("Falha no início");

    else if (error == OTA_CONNECT_ERROR) Serial.println("Falha na conexão");

    else if (error == OTA_RECEIVE_ERROR) Serial.println("Falha no recebimento");

    else if (error == OTA_END_ERROR) Serial.println("Falha no finalização");

  });

 Mostra o progresso em porcentagem e exibe mensagens detalhadas caso ocorra algum erro na atualização OTA.


 ```cpp
  ArduinoOTA.begin();

  Serial.println("Pronto para atualizações OTA.");

  Serial.print("Endereço IP: ");
 ```
   Inicia o serviço de atualização OTA.


 ```cpp
  Serial.println(WiFi.localIP());



  server.begin();

  server.setNoDelay(true);

  Serial.println("Servidor Telnet iniciado...");

}
 ```
Exibe o endereço IP do dispositivo na rede WiFi.
 Inicia o servidor Telnet na porta 23
  Configura o servidor para não atrasar a transmissão de dados.
```cpp
void loop() {

  ArduinoOTA.handle();
 ```
 Mantém o serviço OTA ativo para permitir atualizações remotas.
Verifica se um novo cliente tentou se conectar ao servidor Telnet

```cpp
  if (server.hasClient()) {

    if (!client || !client.connected()) {

      if (client) client.stop();

      client = server.available();

      Serial.println("Cliente conectado via Telnet.");

      client.println("Digite '1' para ligar o LED e '0' para desligar o LED.");
```
Essa parte gerencia a conexão de um cliente via Telnet. Quando o cliente tenta se conectar, o código verifica se há uma conexão anterior e, se houver, a encerra. Depois ele aceita o novo cliente e envia uma mensagem para controlar um LED (ligando ou desligando). Também imprime uma mensagem no monitor serial indicando que o cliente foi conectado.


```cppp

    } else {

      server.available().stop();

    }

  }

```
Fecha qualquer conexão de cliente adicional, caso já exista um cliente ativo conectado ao servidor.


```cpp
  if (client && client.connected() && client.available()) {

    char command = client.read();

    if (command == '1') {

      digitalWrite(LED_PIN, LOW); 

      client.println("LED ligado.");

      Serial.println("LED ligado.");

    } else if (command == '0') {

      digitalWrite(LED_PIN, HIGH); 

      client.println("LED desligado.");

      Serial.println("LED desligado.");

    } else {

      client.println("Comando inválido. Digite '1' para ligar o LED e '0' para desligar o LED.");
```
Este código ve se o cliente está conectado e se tem dados para leitura. Se tiver, ele lê o comando enviado pelo cliente. Se o comando for '1', o LED é ligado; se for '0', o LED é desligado. Caso contrário, o código informa ao cliente que o comando é inválido.

    }

  }

}

