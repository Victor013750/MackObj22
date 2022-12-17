# MackObj22
A proposta desse projeto foi o desenvolvimento de uma rede de sensores e atuadores utilizando o conceito IOT, e sua aplicação de automação residencial.

#include <ESP8266WiFi.h> 
#include <PubSubClient.h> 
#define MSG_BUFFER_SIZE (50) 
char msg[MSG_BUFFER_SIZE];                                                                        
#include <Adafruit_Sensor.h>
#include <DHT.h> 
#include <DHT_U.h>
#define DHTPIN 5 
#define DHTTYPE DHT22 
DHT_Unified dht(DHTPIN, DHTTYPE);                                                                 
uint32_t delayMS;                                                                                 
const char* ssid = "Silva20_2G";                                                          
const char* password = "88161236";                                                              
const char* mqtt_server = "broker.mqtt-dashboard.com";                                            
WiFiClient espClient;                                                                             
PubSubClient client(espClient);                                                                   
unsigned long lastMsg = 0;                                                                        
int value = 0;                                                                                    

char Sala;                                                                                        
char Quarto;                                                                                      
char Cozinha;                                                                                     
char Banheiro;                                                                                    

void setup() {                                                                                    
  
  dht.begin();                                                                                    
  Serial.println(F("Sensor DHT22"));                                                              
  sensor_t sensor;                                                                                
  
  
  dht.temperature().getSensor(&sensor);                                                           
  Serial.println(F("------------------------------------"));                                      
  Serial.println(F("Temperatura"));                                                               
  Serial.print  (F("Sensor: ")); Serial.println(sensor.name);                                     
  Serial.print  (F("Valor máximo: ")); Serial.print(sensor.max_value); Serial.println(F("°C")); 
  Serial.print  (F("Valor mínimo: ")); Serial.print(sensor.min_value); Serial.println(F("°C")); 
  Serial.print  (F("Resolução: ")); Serial.print(sensor.resolution); Serial.println(F("°C"));    
  Serial.println(F("------------------------------------"));                                      
 
  
  dht.humidity().getSensor(&sensor);                                                              
   Serial.println(F("------------------------------------"));                                     
  Serial.println(F("Umidade"));                                                                   
  Serial.print  (F("Sensor: ")); Serial.println(sensor.name);                                     
  Serial.print  (F("Valor máximo: ")); Serial.print(sensor.max_value); Serial.println(F("%"));  
  Serial.print  (F("Valor mínimo: ")); Serial.print(sensor.min_value); Serial.println(F("%"));  
  Serial.print  (F("Resolução: ")); Serial.print(sensor.resolution); Serial.println(F("%"));     
  Serial.println(F("------------------------------------"));                                      
  
  delayMS = sensor.min_delay / 1000;        
  
  Sala = 4;                                                                                       
  Quarto = 2;                                                                                     
  Cozinha = 14;                                                                                   
  Banheiro = 13;                                                                                  

  pinMode(Sala, OUTPUT);                                                                          
  pinMode(Quarto, OUTPUT);                                                                        
  pinMode(Cozinha, OUTPUT);                                                                       
  pinMode(Banheiro, OUTPUT);                                                                      
  
  Serial.begin(115200);                                                                           
  
  setup_wifi();                                                                                   
  client.setServer(mqtt_server, 1883);                                                            
  client.setCallback(callback);                                                                   
}

void setup_wifi() {                                                                               

  delay(10);                                                                                      
  Serial.println("");                                                                             
  Serial.print("Conectando com ");                                                                
  Serial.println(ssid);                                                                           

  WiFi.begin(ssid, password);                                                                     

  while (WiFi.status() != WL_CONNECTED) {                                                         
    delay(500);                                                                                   
    Serial.print(".");                                    
  }                  

  Serial.println("");                                                                             
  Serial.println("WiFi conectado");                                                               
  Serial.println("IP: ");                                                                         
  Serial.println(WiFi.localIP());                                                                 
}

void callback(char* topic, byte* payload, unsigned int length) {                                  
  Serial.print("Mensagem recebida [");                                                            
  Serial.print(topic);                                                                            
  Serial.print("] ");                                                                             
  for (int i = 0; i < length; i++) {                                                              
    Serial.print((char)payload[i]);                                                               
  }
  Serial.println("");                                                                             
  if ((char)payload[0] == 'S') {                                                                  
    digitalWrite(Sala, HIGH);                                                                     
    snprintf (msg, MSG_BUFFER_SIZE, "A luz da sala está ligada");                                 
    Serial.print("Publica mensagem: ");                                                           
    Serial.println(msg);                                                                          
    client.publish("casa/sala", msg);                                                             
  }
  Serial.println("");                                                                             
  if ((char)payload[0] == 's') {                                                                  
    digitalWrite(Sala, LOW);                                                                      
    snprintf (msg, MSG_BUFFER_SIZE, "A luz da sala está desligada");                              
    Serial.print("Publica mensagem: ");                                                           
    Serial.println(msg);                                                                          
    client.publish("casa/sala", msg);                                                             
  }
  Serial.println("");                                                                             
  if ((char)payload[0] == 'Q') {                                                                  
    digitalWrite(Quarto, HIGH);                                                                   
    snprintf (msg, MSG_BUFFER_SIZE, "A luz do quarto está ligada");                               
    Serial.print("Publica mensagem: ");                                                           
    Serial.println(msg);                                                                          
    client.publish("casa/quarto", msg);                                                           
  }
   Serial.println("");                                                                            
   if ((char)payload[0] == 'q') {                                                                 
    digitalWrite(Quarto, LOW);                                                                    
    snprintf (msg, MSG_BUFFER_SIZE, "A luz do quarto está desligada");                            
    Serial.print("Publica mensagem: ");                                                           
    Serial.println(msg);                                                                          
    client.publish("casa/quarto", msg);                                                           
  }
  Serial.println("");                                                                             
  if ((char)payload[0] == 'C') {                                                                  
    digitalWrite(Cozinha, HIGH);                                                                  
    snprintf (msg, MSG_BUFFER_SIZE, "A luz da cozinha está ligada");                              
    Serial.print("Publica mensagem: ");                                                           
    Serial.println(msg);                                                                          
    client.publish("casa/cozinha", msg);                                                          
  }
  Serial.println("");                                                                             
   if ((char)payload[0] == 'c') {                                                                 
    digitalWrite(Cozinha, LOW);                                                                   
    snprintf (msg, MSG_BUFFER_SIZE, "A luz da cozinha está desligada");                           
    Serial.print("Publica mensagem: ");                                                           
    Serial.println(msg);                                                                          
    client.publish("casa/cozinha", msg);                                                          
  }
  Serial.println("");                                                                             
  if ((char)payload[0] == 'B') {                                                                  
    digitalWrite(Banheiro, HIGH);                                                                 
    snprintf (msg, MSG_BUFFER_SIZE, "A luz do banheiro está ligada");                             
    Serial.print("Publica mensagem: ");                                                           
    Serial.println(msg);                                                                          
    client.publish("casa/banheiro", msg);                                                         
  }
   Serial.println("");                                                                            
   if ((char)payload[0] == 'b') {                                                                 
    digitalWrite(Banheiro, LOW);                                                                  
    snprintf (msg, MSG_BUFFER_SIZE, "A luz do banheiro está desligada");                          
    Serial.print("Publica mensagem: ");                                                           
    Serial.println(msg);                                                                          
    client.publish("casa/banheiro", msg);                                                         
    
  }
}

void reconnect() {                                                                                
  while (!client.connected()) {                                                                   
    Serial.print("Aguardando conexão MQTT...");                                                   
    String clientId = "ESP8266Client";                                       
    clientId += String(random(0xffff), HEX);                                                      
    if (client.connect(clientId.c_str())) {                                                       
      Serial.println("conectado");                                                                
      client.subscribe("casa/publisher");                                                         
    } else {                                                                                      
      Serial.print("falhou, rc=");                                                                
      Serial.print(client.state());                                                               
      Serial.println("tente novamente em 5s");                                                    
      delay(5000);                                                                                
    }
  }
}

void loop() {                                                                                     
  delay(delayMS);                                                                                 
  sensors_event_t event;                                                                          
  dht.temperature().getEvent(&event);                                                             
  if (isnan(event.temperature)) {                                                                 
    Serial.println(F("Erro na leitura da temperatura!"));                                         
  }
  else {                                                                                          
    Serial.print(F("Temperatura: "));                                                             
    Serial.print(event.temperature);                                                              
    Serial.println(F("°C"));                                                                      
    sprintf(msg,"%f",event.temperature);                                                          
    client.publish("casa/temperatura", msg);                                                      
  }
  dht.humidity().getEvent(&event);                                                                
  if (isnan(event.relative_humidity)) {                                                           
    Serial.println(F("Erro na leitura da umidade!"));                                             
  }
  else {                                                                                          
    Serial.print(F("Umidade: "));                                                                 
    Serial.print(event.relative_humidity);                                                        
    Serial.println(F("%"));                                                                       
    sprintf(msg,"%f",event.relative_humidity);                                                    
    client.publish("casa/umidade", msg);                                                          
  }
  if (!client.connected()) {                                                                      
    reconnect();                                                                                  
  }
  client.loop();                                                                                  
}
