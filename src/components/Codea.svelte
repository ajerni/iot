<script>
    import { CodeSnippet } from "carbon-components-svelte/src";
    import photona from '../assets/photona.jpg'

let code = `
//Hauptprojekt mit Photon A + B + Homepage

/*
Photon A
- LED rot (D6) reagiert auf Magnetsensor von Photon B
- LED grün auf D0
- Servo Motor auf D4
- Lichtsensor auf A0 sendet events auf die Photon B hört und dort das LED leuten lässt 
bei event "zu"
- Knopf auf D5 sendet event und erhöht die Variable counter. Dieser event knopf wird 
auch auf der Homepage abgehört über einen eventListener (siehe Knopf.svelte)
- Hört auf den alarmall event ab der Homepage und schaletet das LED
- Published event knopfifttt wenn counter auf 5 ist - siehe IFTTT App (sendet dann ein
E-Mail)

Vars:
- messagevar
- counter

Functions:
- setmessage
(plus alle 4 Grundfunktionen für die Tinker-App)
*/

/* Function prototypes ------------für Tinker-App----------------------------------*/
int tinkerDigitalRead(String pin);
int tinkerDigitalWrite(String command);
int tinkerAnalogRead(String pin);
int tinkerAnalogWrite(String command);

//Globale Initialisierungen
Servo servoMain; // Define our Servo

//PINS DEFINIEREN (in setup() die pinModes dazu definieren)
int switchPin = D2; // wird über D4 angeseuert (D4 Output steuert D2 Input)
int buttonPin = D5;
int wcLed = D6;
int greenLed = D0;
int builtinled = D7;

//VARIABELN & KONSTANTEN DEFINIEREN
int counter = 0;
bool ifttt_sent = false; //damit der event knopfifttt nur einmal getriggert wird
bool ledswitch = false;
bool done = false;
int reading = 0;

const int EPROMadress = 10;

//char message[15] = "gugus"; //char arrays können direkt in Strings geschrieben 
werden - Umgekeht braucht Konvertierung
//char *message = "guguseli"; //andere Syntax für char array
String message = "Ich bin ein Text";

bool hell_dunkel_sent = false;

//used for "debouncing of the button", damit er nicht mehrmals reagiert resp. 
nicht flackert.

unsigned long Last_Change_Time = 0;
int Debounce_Delay = 300;

/* This function is called once at start up ----------------------------------*/
void setup()
{
    // Read an object from the EEPROM address
    char text[30] = "";
    EEPROM.get(EPROMadress, text);
    message = text; //einem String type einen char array zuweisen geht direkt
    (umgekeht braucht conversion --> siehe set_new_message())
    //Erstmalig zur Initialisierung verwendet:
    /*struct MyObject {
        uint8_t version;
        char text[30];
    };
    MyObject myObj;
    EEPROM.get(addr, myObj);
    if(myObj.version != 0) {
      // EEPROM was empty -> initialize myObj
     MyObject defaultObj = { 0, "Initialtext" };
         myObj = defaultObj;
    }
    */
    
    //Set all pinModes
	pinMode(switchPin, INPUT_PULLDOWN); //wird von D4 angesteuert (D4 mit D2 
    verbunden auf BB) / D4 Output steuert D2 Input, da auf D2 digitalRead gemacht wird

	pinMode(buttonPin, INPUT);
	pinMode(builtinled, OUTPUT);
	pinMode(wcLed, OUTPUT);
	pinMode(greenLed, OUTPUT);
    
    //VARIABLES
	Particle.variable("messagevar", message); // sichtbar in Particla app und extern 
    abrufbar über GET request

	Spark.variable("counter", counter);
	/* Zugriff mit GET Request (geht direkt in Browser und retouniert ein json mit result):
    curl
    https://api.particle.io/v1/devices/123/messagevar?access_token=xyz
    */
    
	//FUNCTIONS
	Particle.function("redgreen", redgreen);
	Particle.function("setmessage", set_new_message);
	/* Funktionsaufruf mit POST request:
	curl https://api.particle.io/v1/devices/123/setmessage -d access_token=xyz -d "args=ich bin neu hier"
    */
    
    //Register all the Tinker-App functions
	Spark.function("digitalread", tinkerDigitalRead);
	Spark.function("digitalwrite", tinkerDigitalWrite);
	Spark.function("analogread", tinkerAnalogRead);
	Spark.function("analogwrite", tinkerAnalogWrite);
	
	//Servo Motor
    servoMain.attach(D1); // servo on digital pin D1
    servoMain.write(45);
    
    //SUBSCRIPTIONS to Events from other devices
    Spark.subscribe("zu", zu_from_wc); //event zu wird published von Photon B's Magnetsensor
    Particle.subscribe("alarmall", alarmtriggered);

}

//EIGENE FUNKTIONEN:
int set_new_message(String newMessage){ //der Cloud zur verfügung gestellte Funkionen (Particle.function) 
    müssen einen Wert zurückgeben (deshalb return 1 oder falls error -1)
    
    message = newMessage;
    
    //convert String to fixed size char array als Vorbereitung für EEPROM Speicherung (String to char array)
    char name[strlen(message)+1];
    char fname[30] = "";
    strcpy(name, message);
    strcpy(fname, name);
    
    //permament auf EEPROM:
    EEPROM.put(EPROMadress, fname);
    
    return 1;
}


void zu_from_wc(const String event, const String data) {
    
        digitalWrite(wcLed, HIGH);
        delay(5000);
        digitalWrite(wcLed, LOW);

}

void alarmtriggered(const String event, const String data) {
    for(int i = 0; i < 8; i++){
        digitalWrite(wcLed, HIGH);
        delay(300);
        digitalWrite(wcLed, LOW);
        delay(300);
    }
}

int redgreen(String color){
    
    int c;
    if(color == "red"){
        c = 1;
    }
    if(color == "green"){
        c = 2;
    }
    if(color == "both"){
        c = 3;
    }
    if(color == "none"){
        c = 4;
    }
    
    //switch(int(atoi(color)))
    switch(c) {
        case 1:
        // red
        digitalWrite(wcLed, HIGH);
        digitalWrite(greenLed, LOW);
        delay(5000);
        digitalWrite(wcLed, LOW);
        break;
    case 2:
        // green
        digitalWrite(wcLed, LOW);
        digitalWrite(greenLed, HIGH);
        delay(5000);
        digitalWrite(greenLed, LOW);
        break;
    case 3:
        // both
        digitalWrite(wcLed, HIGH);
        digitalWrite(greenLed, HIGH);
        delay(5000);
        digitalWrite(wcLed, LOW);
        digitalWrite(greenLed, LOW);
        break;
    default:
        // off
        digitalWrite(wcLed, LOW);
        digitalWrite(greenLed, LOW);
    }

    return 1;
}

/* This function loops forever --------------------------------------------*/
void loop()
{
	//Servo Motor
	if (digitalRead(switchPin) == LOW && done == false) {
        servoMain.write(45);  
        delay(500);          
        servoMain.write(90);
        delay(500);
        servoMain.write(45);
        Spark.publish("Motor","bewegt auf low"); // see Dashboard :-) resp. Events 
        in Particle App
        done = true;
    }
    
    if (digitalRead(switchPin) == HIGH && done == true) {
        
        servoMain.write(45);  
        delay(500);          
        servoMain.write(90);
        delay(500);
        servoMain.write(45);
        Spark.publish("Motor","bewegt auf high");
        done = false;
    }
    
    //Lichtsensor (Photovoltaik)
    reading = analogRead(A0);
    
    if (reading < 1000 && hell_dunkel_sent == false) {
        
        Spark.publish("andi_dunkel");
        hell_dunkel_sent = true;
        
    } 
    
    if (reading >= 1000 && hell_dunkel_sent == true) {
        
        Spark.publish("andi_hell");
        hell_dunkel_sent = false;
        
    }
    
    //Knopf auf BB
    if (digitalRead(buttonPin) == HIGH) {
        
        
        if ((millis() - Last_Change_Time) > Debounce_Delay) {
            
            counter++;
            delay(200);
            
            Particle.publish("knopf","Knopf wurde gedückt!");
            
            
            ledswitch = !ledswitch;
            if (ledswitch == true) {
                digitalWrite(builtinled, HIGH);
            } else {
                digitalWrite(builtinled, LOW);
            }
            
            Last_Change_Time = millis();
        }
    }
    
    //für Integration mit IFTTT (If This Than That - App)
    if (counter == 5 && ifttt_sent == false) {
        Particle.publish("knopfifttt", "Triggers IFTTT Event");
        ifttt_sent = true;
    }
}


//Für Tinker-App:
/*******************************************************************************
 * Function Name  : tinkerDigitalRead
 * Description    : Reads the digital value of a given pin
 * Input          : Pin
 * Output         : None.
 * Return         : Value of the pin (0 or 1) in INT type
                    Returns a negative number on failure
 *******************************************************************************/
int tinkerDigitalRead(String pin)
{
	//convert ascii to integer
	int pinNumber = pin.charAt(1) - '0';
	//Sanity check to see if the pin numbers are within limits
	if (pinNumber< 0 || pinNumber >7) return -1;

	if(pin.startsWith("D"))
	{
		pinMode(pinNumber, INPUT_PULLDOWN);
		return digitalRead(pinNumber);
	}
	else if (pin.startsWith("A"))
	{
		pinMode(pinNumber+10, INPUT_PULLDOWN);
		return digitalRead(pinNumber+10);
	}
	return -2;
}

/*******************************************************************************
 * Function Name  : tinkerDigitalWrite
 * Description    : Sets the specified pin HIGH or LOW
 * Input          : Pin and value
 * Output         : None.
 * Return         : 1 on success and a negative number on failure
 *******************************************************************************/
int tinkerDigitalWrite(String command)
{
	bool value = 0;
	//convert ascii to integer
	int pinNumber = command.charAt(1) - '0';
	//Sanity check to see if the pin numbers are within limits
	if (pinNumber< 0 || pinNumber >7) return -1;

	if(command.substring(3,7) == "HIGH") value = 1;
	else if(command.substring(3,6) == "LOW") value = 0;
	else return -2;

	if(command.startsWith("D"))
	{
		pinMode(pinNumber, OUTPUT);
		digitalWrite(pinNumber, value);
		return 1;
	}
	else if(command.startsWith("A"))
	{
		pinMode(pinNumber+10, OUTPUT);
		digitalWrite(pinNumber+10, value);
		return 1;
	}
	else return -3;
}

/*******************************************************************************
 * Function Name  : tinkerAnalogRead
 * Description    : Reads the analog value of a pin
 * Input          : Pin
 * Output         : None.
 * Return         : Returns the analog value in INT type (0 to 4095)
                    Returns a negative number on failure
 *******************************************************************************/
int tinkerAnalogRead(String pin)
{
	//convert ascii to integer
	int pinNumber = pin.charAt(1) - '0';
	//Sanity check to see if the pin numbers are within limits
	if (pinNumber< 0 || pinNumber >7) return -1;

	if(pin.startsWith("D"))
	{
		return -3;
	}
	else if (pin.startsWith("A"))
	{
		return analogRead(pinNumber+10);
	}
	return -2;
}

/*******************************************************************************
 * Function Name  : tinkerAnalogWrite
 * Description    : Writes an analog value (PWM) to the specified pin
 * Input          : Pin and Value (0 to 255)
 * Output         : None.
 * Return         : 1 on success and a negative number on failure
 *******************************************************************************/
int tinkerAnalogWrite(String command)
{
	//convert ascii to integer
	int pinNumber = command.charAt(1) - '0';
	//Sanity check to see if the pin numbers are within limits
	if (pinNumber< 0 || pinNumber >7) return -1;

	String value = command.substring(3);

	if(command.startsWith("D"))
	{
		pinMode(pinNumber, OUTPUT);
		analogWrite(pinNumber, value.toInt());
		return 1;
	}
	else if(command.startsWith("A"))
	{
		pinMode(pinNumber+10, OUTPUT);
		analogWrite(pinNumber+10, value.toInt());
		return 1;
	}
	else return -2;
}
`

</script>

<img src={photona} alt="Photon A" />
<h3>Photon A</h3>
<CodeSnippet type="multi" copy={() => {}} {code} wrapText/>


<style>
    img{
        width: 600px;
        height: 400px;
    } 
    h3{
        display:flex;
    }
</style>

    

