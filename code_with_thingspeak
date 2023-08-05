#include <WiFi.h>
#include <HTTPClient.h>
#include <ArduinoJson.h>
#include <ThingSpeak.h> 
const int BUZZER_PIN = 4; 
const char* ssid = "enter wifi name";
const char* password = "enter password";
const char* api_endpoint = "https://api.coindesk.com/v1/bpi/currentprice.json";
//const float price_threshold = 50000.0; // Set the price threshold in USD
float price_threshold=0.0;
String phone_number = "+91xxxxxxxxxx";
String apiKey = "xxxxxx"; 
const int redLedPin = 19;    // Pin for red LED
const int greenLedPin = 18;  // Pin for green LED
String url;

WiFiClient client;
unsigned long channelID = 215618; // Replace with your ThingSpeak channel ID
const char *writeAPIKey = "X4KEQKATRT2P7JLD"; 

void setup() {
  Serial.begin(115200);
  pinMode(redLedPin, OUTPUT);
  pinMode(greenLedPin, OUTPUT);
  pinMode(BUZZER_PIN, OUTPUT);

  // Connect to WiFi network
  Serial.println();
  Serial.println("Connecting to WiFi...");
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting...");
  }
  Serial.println("WiFi connected!");
  ThingSpeak.begin(client);

}

void loop() {
   while (!Serial.available());
  price_threshold = Serial.parseInt();
  Serial.println("Enter input price:");
  Serial.println("Input price is " + String(price_threshold) + " USD");
  
  HTTPClient http;
  http.begin(api_endpoint);
  int httpCode = http.GET();

  if (httpCode > 0) {
    
    String payload = http.getString();
    DynamicJsonDocument doc(1024);
    deserializeJson(doc, payload);
    JsonObject bpi = doc["bpi"]["USD"];
    float rate_float = bpi["rate_float"];
    

    // Display the current price on the serial monitor
    Serial.print("Current Bitcoin price: ");
    Serial.println(rate_float);

    ThingSpeak.writeField(channelID, 1, rate_float, writeAPIKey);
    String message = "Current Bitcoin price is " + String(rate_float) + " USD";
    message_to_whatsapp(message);

    

    // Check if the price is above the threshold and display an alert
    if (rate_float > price_threshold) {
       digitalWrite(redLedPin, LOW);
    digitalWrite(greenLedPin, HIGH);
      
    Serial.println("Green LED is on");
    message_to_whatsapp("GO ahead buy it!!");
    }
    else{
      digitalWrite(redLedPin, HIGH);
    digitalWrite(greenLedPin, LOW);
    Serial.println("red LED is on");
    message_to_whatsapp("Alert!! The price is very high");
 digitalWrite(BUZZER_PIN, HIGH); 
  delay(500); 
  digitalWrite(BUZZER_PIN, LOW); 
  delay(500); 
    }
  }

  
  delay(10000);
}
void  message_to_whatsapp(String message)       
{
  url = "https://api.callmebot.com/whatsapp.php?phone=" + phone_number + "&apikey=" + apiKey + "&text=" + urlencode(message);

  postData(); 
}

void postData()     
{
  int httpCode;     
  HTTPClient http;  
  http.begin(url);  
  httpCode = http.POST(url);
  if (httpCode == 200)      
  {
    Serial.println("Sent ok."); 
  }
  else                      
  {
    Serial.println("Error."); 
  }
  http.end();          
}

String urlencode(String str)  
{
    String encodedString="";
    char c;
    char code0;
    char code1;
    char code2;
    for (int i =0; i < str.length(); i++){
      c=str.charAt(i);
      if (c == ' '){
        encodedString+= '+';
      } else if (isalnum(c)){
        encodedString+=c;
      } else{
        code1=(c & 0xf)+'0';
        if ((c & 0xf) >9){
            code1=(c & 0xf) - 10 + 'A';
        }
        c=(c>>4)&0xf;
        code0=c+'0';
        if (c > 9){
            code0=c - 10 + 'A';
        }
        code2='\0';
        encodedString+='%';
        encodedString+=code0;
        encodedString+=code1;
        //encodedString+=code2;
      }
      yield();
    }
    return encodedString;
}





