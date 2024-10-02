# Mindwave carrinho

---

## Descrição

### [PT-BR]
Este projeto utiliza o Mindwave Mobile 2 e um Arduino para controlar um carrinho movido a motores DC. O nível de atenção detectado pelo Mindwave determina se o carrinho se move para frente. Quando o nível de atenção atinge um valor superior a 45, o carrinho começa a andar.

### [ENG]
This project uses the Mindwave Mobile 2 and an Arduino to control a car powered by DC motors. The attention level detected by the Mindwave determines whether the car moves forward. When the attention level exceeds 45, the car starts moving.

 ---

## Versão / Version

### [PT-BR]
- **Arduino IDE**: 1.8.13
- **Bibliotecas**:
  - [Mindwave](https://github.com/orgicus/Mindwave.git): Acesse o link, baixe o repositório como um arquivo ZIP e coloque na IDE Arduino.
  - SoftwareSerial: Incluída na IDE Arduino.

### [ENG]
- **Arduino IDE**: 1.8.13
- **Libraries**:
  - [Mindwave](https://github.com/orgicus/Mindwave.git): Access the link, download the repository as a ZIP file, and add it to the Arduino IDE.
  - SoftwareSerial: Included in the Arduino IDE.

---

## Materiais / Materials

### [PT-BR]
- 1 Arduino UNO
- 1 Mindwave Mobile 2
- 1 Módulo Bluetooth **HC-05** (não pode ser outro modelo)
- 1 Ponte H L298N
- 2 Motores DC
- 1 Chassi de carrinho cortado a laser (qualquer formato)
- 2 Rodas para motores DC
- 1 Roda "solta" para apoio
- 1 Suporte para 4 pilhas AA
- 4 Pilhas AA
- Vários jumpers

### [ENG]
- 1 Arduino UNO
- 1 Mindwave Mobile 2
- 1 Bluetooth Module **HC-05** (no other model allowed)
- 1 L298N Motor Driver
- 2 DC Motors
- 1 Car chassis cut by laser (any shape)
- 2 Wheels for DC motors
- 1 "Free" wheel for support
- 1 Battery holder for 4 AA batteries
- 4 AA batteries
- Various jumper wires

---

## Como Fazer / How to 

### 1. Comandos AT para o HC-05 / AT Commands for HC-05:

### [PT-BR]
Para configurar o HC-05 e pareá-lo com o Mindwave, siga os comandos AT abaixo. Conecte o HC-05 ao Arduino nas portas 10 (TX do HC-05 para RX do Arduino) e 11 (RX do HC-05 para TX do Arduino). Para começar, rode o seguinte programa no IDE:

#include <SoftwareSerial.h>
SoftwareSerial BT(11, 10); // Rx/Tx

---
void setup() {
    BT.begin(38400);
    Serial.begin(9600);
}

void loop() {
    if (BT.available()) Serial.write(BT.read());
    if (Serial.available()) BT.write(Serial.read());
}

---
Após rodar o código, coloque esses comandos 1 por 1 no monitor serial; o monitor deverá responder "OK" para cada linha:

- `AT+UART=57600,0,0`
- `AT+ROLE=1`
- `AT+PSWD="0000"`
- `AT+CMODE=0`
- `AT+BIND=xxxx,xx,xxxxxx` (endereço MAC do Mindwave, utilizando esse formato, obtenha conectando ele ao bluetooth de outro dispositivo)
- `AT+IAC=9E8B33`
- `AT+CLASS=0`
- `AT+INQM=1,9,48`

Certifique-se de que o monitor serial está configurado com NL&CR e com a taxa de baud a 9600 para enviar os comandos.

### [ENG]
To configure the HC-05 and pair it with the Mindwave, follow the AT commands below. Connect the HC-05 to the Arduino using pins 10 (TX from HC-05 to Arduino RX) and 11 (RX from HC-05 to Arduino TX) then run this code:

#include <SoftwareSerial.h>
SoftwareSerial BT(11, 10); // Rx/Tx

---
void setup() {
    BT.begin(38400);
    Serial.begin(9600);
}

void loop() {
    if (BT.available()) Serial.write(BT.read());
    if (Serial.available()) BT.write(Serial.read());
}

---

After running the code, place these commands 1 by 1 on the serial monitor; the monitor should respond "OK" for each line:

- `AT+UART=57600,0,0`
- `AT+ROLE=1`
- `AT+PSWD="0000"`
- `AT+CMODE=0`
- `AT+BIND=xxxx,xx,xxxxxx` (the Mindwave MAC adress, using this format, you can get that pairing the mindwave to a bluetooth device)
- `AT+IAC=9E8B33`
- `AT+CLASS=0`
- `AT+INQM=1,9,48`

Ensure that the serial monitor is set to NL&CR and a 9600 baud rate to send commands.

---

### 2. Conexões dos Fios / Wiring:

### [PT-BR]
- **Módulo Bluetooth HC-05**: RX na porta 10, TX na porta 11 (conectar apenas após a carga do código no Arduino).
- **Ponte H L298N**: 
    - ENA na porta 9, IN1 na porta 8, IN2 na porta 7, IN3 na porta 6, IN4 na porta 5, ENB na porta 4.
    - Motores conectados aos bornes de saída da ponte H.
    - Alimentação dos motores via o suporte de 4 pilhas AA.
    - GND da ponte H conectado ao GND do Arduino.
- **Motores DC**: 
    - Conectar os dois motores nas saídas da ponte H.

### [ENG]
- **Bluetooth Module HC-05**: RX on pin 10, TX on pin 11 (connect only after uploading the code).
- **L298N Motor Driver**: 
    - ENA on pin 9, IN1 on pin 8, IN2 on pin 7, IN3 on pin 6, IN4 on pin 5, ENB on pin 4.
    - Motors connected to the motor driver output.
    - Motors powered by a 4x AA battery pack.
    - GND from the motor driver connected to Arduino GND.
- **DC Motors**: 
    - Connect both motors to the L298N motor driver outputs.

---

### 3. Carregamento do Programa / Uploading the Program:

### [PT-BR]
Para carregar o código no Arduino, você precisará seguir estas etapas. O código abaixo controla os motores do carrinho com base no nível de atenção detectado pelo Mindwave. Certifique-se de que os fios RX e TX do módulo Bluetooth estejam desconectados antes de fazer o upload. Após o upload, reconecte os fios e inicie o monitor serial.

Aqui está o código que você deve usar:

```cpp
#include <SoftwareSerial.h>
#include <Mindwave.h>

Mindwave mindwave;   // Inicializa o Mindwave

// Pinos do módulo L298N para controlar os motores
const int motor1Pin1 = 9; // Motor A - Pino 1
const int motor1Pin2 = 8; // Motor A - Pino 2
const int motor2Pin1 = 7; // Motor B - Pino 1
const int motor2Pin2 = 6; // Motor B - Pino 2

void setup() {
  // Configuração dos pinos dos motores
  pinMode(motor1Pin1, OUTPUT);
  pinMode(motor1Pin2, OUTPUT);
  pinMode(motor2Pin1, OUTPUT);
  pinMode(motor2Pin2, OUTPUT);

  // Inicializa a comunicação serial para o Mindwave
  Serial.begin(57600);
  delay(500); // Pequeno delay para garantir a inicialização

  Serial.println("Mindwave Ready!");
}

// Função para mover o carrinho para frente
void moveForward() {
  digitalWrite(motor1Pin1, HIGH);
  digitalWrite(motor1Pin2, LOW);
  digitalWrite(motor2Pin1, HIGH);
  digitalWrite(motor2Pin2, LOW);
}

// Função para parar os motores
void stopMotors() {
  digitalWrite(motor1Pin1, LOW);
  digitalWrite(motor1Pin2, LOW);
  digitalWrite(motor2Pin1, LOW);
  digitalWrite(motor2Pin2, LOW);
}

// Função para exibir o nível de atenção e controlar o carrinho
void onMindwaveData() {
  int attentionLevel = mindwave.attention(); // Obter o valor de atenção

  if (attentionLevel == 0 || attentionLevel == 1) {
    Serial.println("Carregando..."); // Exibir "Carregando" se o valor for 0 ou 1
  } else {
    // Exibir o nível de atenção
    Serial.print("Nível de atenção: ");
    Serial.println(attentionLevel);

    // Se o nível de atenção for maior que 45, mover o carrinho; caso contrário, parar
    if (attentionLevel > 45) {
      moveForward();
      Serial.println("Movendo para frente");
    } else {
      stopMotors();
      Serial.println("Parado");
    }
  }
}

void loop() {
  // Atualiza os dados do Mindwave no loop principal
  mindwave.update(Serial, onMindwaveData);
}

 
