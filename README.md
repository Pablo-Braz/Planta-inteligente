# 🌱 Planta Inteligente

Sistema automático de irrigação para vaso de planta, desenvolvido com Arduino para monitorar a umidade do solo e acionar uma bomba de água quando necessário.

O projeto foi pensado para facilitar o cuidado com plantas domésticas, jardins pequenos e vasos de decoração, evitando tanto a falta de água quanto o excesso de irrigação.

## ✨ Ideia do projeto

A ideia central é criar um irrigador inteligente que:

- detecta a umidade do solo em tempo real;
- avalia se a planta está seca, em nível médio ou em condição adequada;
- liga a bomba de água automaticamente quando a planta precisa de irrigação;
- mostra a leitura atual e o estado do sistema em um display LCD;
- utiliza LEDs para indicar visualmente o nível de umidade;
- oferece uma base para evoluir para automação e integração com app ou monitoramento remoto.

> O projeto atual já conta com sensor de umidade do solo, Arduino, relé, bomba de água, display LCD e LEDs. A ideia é manter uma solução prática e compacta, com espaço para futuras melhorias.

---

## 🧩 Componentes do projeto

| Componente | Função |
|---|---|
| Arduino Uno / Nano | Controla o sistema e processa os sensores |
| Sensor de umidade do solo | Mede a umidade da terra do vaso |
| Módulo relé 5V | Liga e desliga a bomba de água |
| Bomba de água | Irriga a planta automaticamente |
| Display LCD 16x2 | Mostra a umidade atual e o estado do sistema |
| LEDs | Indicador visual do nível da planta |
| Caixa de pilhas / fonte | Alimenta o circuito de forma portátil |
| Fios e jumpers | Conexões entre os módulos |
| Protoboard | Montagem e organização do circuito |

### Função de cada componente

- Arduino: cérebro do sistema
- Sensor de umidade: detecta a condição do solo
- Relé: atua como chave para a bomba
- Bomba: fornece água para irrigar a planta
- LCD: mostra o valor da leitura em tempo real
- LEDs: sinal visual para estados de secura, atenção e normal
- Caixa de pilhas: fornece energia ao sistema de forma simples e compacta

---

## 🔌 Conexões principais

Abaixo está a referência de ligação usada no código:

- Sensor de umidade no pino A0
- Relé no pino 6
- LED 1 no pino 2
- LED 2 no pino 3
- LED 3 no pino 4
- LCD:
  - RS -> 12
  - E -> 11
  - D4 -> 10
  - D5 -> 9
  - D6 -> 8
  - D7 -> 7

### Estrutura básica do sistema

- Solo -> Sensor de umidade -> Arduino
- Arduino -> Relé -> Bomba de água
- Arduino -> LEDs
- Arduino -> Display LCD

---

## 📝 Como funciona

O sistema lê a umidade do solo e compara com os limites definidos no código:

- Muito seco: bomba ligada por tempo maior
- Médio: irrigação em pulsos
- Normal: sistema desligado

Essa lógica mantém a planta saudável e evita desperdício de água.

---

## 📁 Estrutura do projeto

```text
Planta-inteligente/
├── README.md
├── planta_inteligente.ino
├── esquematico/
│   └── (opcional: imagens e diagramas do circuito)
└── assets/
    └── (opcional: fotos do projeto montado)
```

---

## 💻 Código principal

```cpp
#include <LiquidCrystal.h>

// Configuração dos pinos do LCD: (RS, E, D4, D5, D6, D7)
LiquidCrystal lcd(12, 11, 10, 9, 8, 7);

// Pinos dos componentes
const int pinoSensor = A0;
const int pinoRele = 6;

// Pinos dos LEDs
const int led1 = 2; // D2 - Muito Seco
const int led2 = 3; // D3 - Médio
const int led3 = 4; // D4 - Normal / OK

// Limiares de umidade (0 a 1023)
const int limiteMuitoSeco = 750;
const int limiteMedio = 500;

void setup() {
  Serial.begin(9600);

  pinMode(pinoRele, OUTPUT);
  pinMode(led1, OUTPUT);
  pinMode(led2, OUTPUT);
  pinMode(led3, OUTPUT);

  digitalWrite(pinoRele, HIGH);
  digitalWrite(led1, LOW);
  digitalWrite(led2, LOW);
  digitalWrite(led3, LOW);

  lcd.begin(16, 2);

  lcd.setCursor(0, 0);
  lcd.print("  IFCiencia  ");
  lcd.setCursor(0, 1);
  lcd.print("Sistema Irrigao");
  delay(3000);

  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Umidade: ");
}

void loop() {
  int valorUmidade = analogRead(pinoSensor);

  lcd.setCursor(9, 0);
  lcd.print(valorUmidade);
  lcd.print("    ");

  if (valorUmidade > limiteMuitoSeco) {
    Serial.print("Sensor: ");
    Serial.print(valorUmidade);
    Serial.println(" -> MUITO SECO | Bomba: ON");

    lcd.setCursor(0, 1);
    lcd.print("REGANDO CONTINUO");

    digitalWrite(led1, HIGH);
    digitalWrite(led2, LOW);
    digitalWrite(led3, LOW);

    digitalWrite(pinoRele, LOW);
    delay(2000);
  }
  else if (valorUmidade > limiteMedio && valorUmidade <= limiteMuitoSeco) {
    Serial.print("Sensor: ");
    Serial.print(valorUmidade);
    Serial.println(" -> MEDIO | Bomba: PULSANDO");

    lcd.setCursor(0, 1);
    lcd.print("REGANDO PULSADO ");

    digitalWrite(led1, LOW);
    digitalWrite(led3, LOW);

    digitalWrite(pinoRele, LOW);
    digitalWrite(led2, HIGH);
    delay(800);

    digitalWrite(pinoRele, HIGH);
    digitalWrite(led2, LOW);
    delay(1200);
  }
  else {
    Serial.print("Sensor: ");
    Serial.print(valorUmidade);
    Serial.println(" -> NORMAL | Bomba: OFF");

    lcd.setCursor(0, 1);
    lcd.print("SISTEMA NORMAL  ");

    digitalWrite(pinoRele, HIGH);

    digitalWrite(led1, LOW);
    digitalWrite(led2, LOW);
    digitalWrite(led3, HIGH);

    delay(1000);
  }
}
```

---

## 🛠️ Como usar

1. Conecte os componentes conforme o esquema de pinos.
2. Abra o arquivo `planta_inteligente.ino` no Arduino IDE.
3. Compile e envie o código para o Arduino.
4. Coloque o sensor no solo da planta.
5. Observe o LCD para verificar a umidade atual.
6. A bomba será acionada automaticamente quando necessário.

---

## 📌 Observações importantes

- O sensor de umidade do solo pode variar de acordo com o tipo de solo e a sensibilidade do módulo.
- Ajuste os valores de `limiteMuitoSeco` e `limiteMedio` conforme a planta.
- O relé deve ser alimentado corretamente para garantir funcionamento estável da bomba.
- Para uma versão mais robusta, vale organizar os fios e montar o sistema em caixa compacta.

---

## 🚀 Possíveis melhorias

- integrar o projeto com aplicação mobile ou painel web;
- adicionar IoT para monitoramento remoto via Wi-Fi/Bluetooth;
- criar um modo de irrigação programada por horário;
- adicionar sensor de temperatura e umidade do ambiente;
- compactar o sistema em uma caixa estética e portátil;
- implementar alertas e histórico de irrigação;
- criar uma interface para visualizar os dados em tempo real.

---

## 👩‍💻 Autor

Projeto desenvolvido para automação de irrigação inteligente com foco em praticidade, sustentabilidade e monitoramento eficiente do vaso da planta.

Se quiser, posso continuar e criar uma versão final mais profissional do README com badges, capa, diagrama do circuito e instruções de montagem em formato pronto para publicar no GitHub.
