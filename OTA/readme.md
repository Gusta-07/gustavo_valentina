```cpp
#include <ESP8266WiFi.h>

#include <ArduinoOTA.h>
```



const char* ssid = "Prof.Rafael";

const char* password = "senhasenha";



const int LED_PIN = 5; // D1




WiFiClient client;



void setup() {

  pinMode(LED_PIN, OUTPUT);

  Serial.begin(9600);

  WiFi.mode(WIFI_STA);

  WiFi.begin(ssid, password);





  while (WiFi.waitForConnectResult() != WL_CONNECTED) {

    Serial.println("Conexão falhou, tentando novamente...");

    WiFi.begin(ssid, password);

    delay(5000);

  }



  ArduinoOTA.onStart([]() {

    Serial.println("Iniciando atualização OTA...");

  });

  ArduinoOTA.onEnd([]() {

    Serial.println("\nAtualização concluída.");

  });

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



  ArduinoOTA.begin();

  Serial.println("Pronto para atualizações OTA.");

  Serial.print("Endereço IP: ");

  Serial.println(WiFi.localIP());



}



void loop() {

  ArduinoOTA.handle();


}
