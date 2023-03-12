/*
 ESP-NOW RC Receiver Code 
  This code will RECEIVE ORDERS for the RC tank on the tank side
*/

#include <esp_now.h>
#include <WiFi.h>
#include "Arduino.h"

int period = 100; //the frequency at which the state machine changes
unsigned long time_trig = 0; 
unsigned long currentTime = 0;

int state_machine = 1; // Defaut to STOP 
int pin_state; 

// Motor A
#define motorAPin1 13 
#define motorAPin2  12 

// Motor B
#define motorBPin1 25 
#define motorBPin2 26 

// Structure example to receive data
// Must match the sender structure
typedef struct struct_message {
  bool right_for;
  bool right_back;
  bool left_for;
  bool left_back;
} struct_message;

// Create a struct_message called myData
struct_message myData;

// callback function that will be executed when data is received
void OnDataRecv(const uint8_t * mac, const uint8_t *incomingData, int len) {
  memcpy(&myData, incomingData, sizeof(myData));
  Serial.print("Bytes received: ");
  Serial.println(len);
  Serial.print("Right Forwards : ");
  Serial.println(myData.right_for);
  Serial.print("Right Backwards : ");
  Serial.println(myData.right_back);
  Serial.print("Left Forwards: ");
  Serial.println(myData.left_for);
  Serial.print("Left Backwards : ");
  Serial.println(myData.left_back);
  Serial.println();
  if (myData.right_for == 1 && myData.left_for == 1){
    state_machine = 2; //Drive motor forward
  } else if (myData.left_back == 0 && myData.left_for == 0 && myData.right_back == 0 && myData.right_for == 0){
    state_machine = 1; //STOP motors
    Serial.println("Sending STOP order");    
  } else if (myData.left_back && myData.right_back){
    state_machine = 3; //Back up motors
  } else if (myData.left_for && myData.right_back){
    state_machine = 5; //turn right
  } else if (myData.right_for && myData.left_back){
    state_machine = 4; //turn left
  } else {
    state_machine = 1; //Defaut to STOP in case of incoherent data
  }

}
 
void setup() {
  // Initialize Serial Monitor
  Serial.begin(115200);

  pinMode(motorAPin1, OUTPUT);
  pinMode(motorAPin2, OUTPUT);

  pinMode(motorBPin1, OUTPUT);
  pinMode(motorBPin2, OUTPUT);



  
  // Set device as a Wi-Fi Station
  WiFi.mode(WIFI_STA);

  // Init ESP-NOW
  if (esp_now_init() != ESP_OK) {
    Serial.println("Error initializing ESP-NOW");
    return;
  }
  
  // Once ESPNow is successfully Init, we will register for recv CB to
  // get recv packer info
  esp_now_register_recv_cb(OnDataRecv);
}
 
void loop() {

while(millis() > (time_trig + period)){

    Serial.print("SM:");
    Serial.print(state_machine);

    switch (state_machine) {
      case 1: 
        //Serial.print(" --  Motor Stopped\n");
        digitalWrite(motorAPin1, 1);
        digitalWrite(motorAPin2, 1); 
        digitalWrite(motorBPin1, 1);
        digitalWrite(motorBPin2, 1); 
        break; 

      case 2:
        Serial.print(" -- Moving Forward\n");
        digitalWrite(motorAPin1, 1);        
        digitalWrite(motorAPin2, 0);
        digitalWrite(motorBPin1, 1);
        digitalWrite(motorBPin2, 0);
        break;

      case 3:
        Serial.print(" -- Motor backwards\n");
        digitalWrite(motorAPin1, 0);
        digitalWrite(motorAPin2, 1);
        digitalWrite(motorBPin1, 0);
        digitalWrite(motorBPin2, 1);
        break;

      case 4:
          Serial.print(" -- SPIN LEFT? \n");
          digitalWrite(motorAPin1, 0);
          digitalWrite(motorAPin2, 1);
          digitalWrite(motorBPin1, 1);
          digitalWrite(motorBPin2, 0);
          break;
      
      case 5:
          Serial.print(" -- SPIN RIGHT?\n");
          digitalWrite(motorAPin1, 1);
          digitalWrite(motorAPin2, 0);
          digitalWrite(motorBPin1, 0);
          digitalWrite(motorBPin2, 1);
          break;


    } //end of state_machine SWITCH
 
    time_trig = millis(); 
  }  //end of while_millis


}
