<script>
    import { CodeSnippet } from "carbon-components-svelte/src";
    import photonb from '../assets/photonb.jpg'

let code = `
//Hauptprojekt mit Photon A + B + Homepage

/*
Photon B (wc_sensor)
- Magnetsensor published event auf den Photon A subscribed und dort LED leutchten lässt. Auch die Homepage reagiert auf diese events (frei/besetzt)
- LED das auf event von Photon A's Lichtsensor reagiert 
- Hört auf den alarmall event ab der Homepage und schaletet das LED
*/

int switchPin = D0;
int ledPin = D7;

bool closed = false;

void setup() {

    pinMode(switchPin, INPUT);
    pinMode(ledPin, OUTPUT);
    
    Particle.function("first", first);
    
    Particle.subscribe("andi_dunkel", put_on); //wird von Photon A published
    Particle.subscribe("andi_hell", put_off);
    Particle.subscribe("alarmall", alarmtriggered); // alarmall event wird ab Homepage getriggert
}

void loop() {
    
    if (digitalRead(switchPin) == LOW && closed == true) {
        
        Particle.publish("offen");
        closed = false;
    } 
    
    if (digitalRead(switchPin) == HIGH && closed == false) {
        
        Particle.publish("zu");
        closed = true;
    }
}

//nur für alte jquery wc.html gebraucht:
int first(String command) {
    
    if (digitalRead(switchPin) == LOW) {
        Particle.publish("offen");
        closed = false;
        return 1;
    }
    else if (digitalRead(switchPin) == HIGH) {
        Particle.publish("zu");
        closed = true;
        return 1;
    }
    return -1;
}

void alarmtriggered(const String event, const String data) {
    for(int i = 0; i < 8; i++){
        digitalWrite(ledPin, HIGH);
        delay(300);
        digitalWrite(ledPin, LOW);
        delay(300);
    }
}

void put_on(const char *event, const char *data){ //char array kann immer in String gespeichert werden. Umgekeht braucht konvertierung (siehe Firmeware auf Photon A)
    digitalWrite(ledPin, HIGH);
}
    
void put_off(const String event, const String data){
    digitalWrite(ledPin, LOW);
}
`
</script>

<img src={photonb} alt="Photon B" />
<h3>Photon B</h3>
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
