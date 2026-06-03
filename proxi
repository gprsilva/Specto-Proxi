#include <Wire.h>
#include <LiquidCrystal_I2C.h>

// ── Pinos ────────────────────────────────────────────────
int ledVerde    = 13;
int ledAmarelo  = 12;
int ledVermelho = 11;

const int ldrPin  = A0;
const int tmpPin  = A1;
const int umidPin = A2;

// ── Variaveis de leitura ─────────────────────────────────
int   ldrValue  = 0;
int   tmpRaw    = 0;
int   umidValue = 0;
float tempC     = 0.0;
int   ldrPct    = 0;
int   umidPct   = 0;

// ── LCD I2C ──────────────────────────────────────────────
LiquidCrystal_I2C lcd(0x27, 16, 2);

// ── Funcao: classifica o status geral dos sensores ───────
// Retorna: 0 = normal | 1 = atencao | 2 = critico
int classificarStatus(int umid, int ldr, float temp) {
  bool critico  = (umid < 30) || (ldr < 30) || (temp > 38.0);
  bool atencao  = (umid < 60) || (ldr < 60) || (temp > 30.0);

  if (critico) return 2;
  if (atencao) return 1;
  return 0;
}

// ── Funcao: acende apenas o LED correspondente ───────────
void atualizarLEDs(int status) {
  digitalWrite(ledVerde,    LOW);
  digitalWrite(ledAmarelo,  LOW);
  digitalWrite(ledVermelho, LOW);

  if      (status == 0) digitalWrite(ledVerde,    HIGH);
  else if (status == 1) digitalWrite(ledAmarelo,  HIGH);
  else                  digitalWrite(ledVermelho, HIGH);
}

// ── Funcao: exibe os dados no LCD ────────────────────────
void atualizarLCD(int umid, int ldr, float temp, int status) {
  lcd.clear();

  // Linha 0: temperatura e luminosidade
  lcd.setCursor(0, 0);
  lcd.print("T:");
  lcd.print((int)temp);
  lcd.print("C ");

  lcd.print("L:");
  lcd.print(ldr);
  lcd.print("%  ");

  // Linha 1: umidade e classificacao
  lcd.setCursor(0, 1);
  lcd.print("U:");
  lcd.print(umid);
  lcd.print("% ");

  if      (status == 0) lcd.print("NORMAL  ");
  else if (status == 1) lcd.print("ATENCAO ");
  else                  lcd.print("CRITICO!");
}

// ── Funcao: envia dados pela Serial ──────────────────────
void logSerial(int umid, int ldr, float temp, int status) {
  String statusStr = (status == 0) ? "NORMAL" :
                     (status == 1) ? "ATENCAO" : "CRITICO";

  Serial.println("-------------------------");
  Serial.print("Temperatura : "); Serial.print(temp);  Serial.println(" C");
  Serial.print("Luminosidade: "); Serial.print(ldr);   Serial.println(" %");
  Serial.print("Umidade     : "); Serial.print(umid);  Serial.println(" %");
  Serial.print("Status      : "); Serial.println(statusStr);
}

// ════════════════════════════════════════════════════════
void setup() {
  pinMode(ledVerde,    OUTPUT);
  pinMode(ledAmarelo,  OUTPUT);
  pinMode(ledVermelho, OUTPUT);

  Serial.begin(9600);

  // Inicializa o LCD
  lcd.init();
  lcd.backlight();
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("     Specto");
  lcd.setCursor(0, 1);
  lcd.print("   iniciando...");
  delay(2000);
  
  analogReference(DEFAULT); 

  // Pisca os tres LEDs em sequencia — teste de hardware
  digitalWrite(ledVerde,    HIGH); delay(400);
  digitalWrite(ledVerde,    LOW);
  digitalWrite(ledAmarelo,  HIGH); delay(400);
  digitalWrite(ledAmarelo,  LOW);
  digitalWrite(ledVermelho, HIGH); delay(400);
  digitalWrite(ledVermelho, LOW);

  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("  Sensor ativo");
  delay(1000);
  lcd.clear();
}

// ════════════════════════════════════════════════════════
void loop() {
  // ── Leitura dos sensores ──────────────────────────────
  ldrValue  = analogRead(ldrPin);
  tmpRaw    = analogRead(tmpPin);
  umidValue = analogRead(umidPin);

  // ── Conversao: LDR para porcentagem (0-100%) ─────────
  // Quanto mais luz, maior o valor analogico (0-1023)
  ldrPct = map(ldrValue, 54, 974, 0, 100);
  ldrPct = constrain(ldrPct, 0, 100);

  // ── Conversao: TMP36 para graus Celsius ──────────────
  float tensao = tmpRaw * (5.0 / 1023.0);
  tempC = map(tmpRaw, 113, 451, -40, 125);


  // ── Conversao: umidade para porcentagem ──────────────
  // Sensor resistivo: valor alto = seco | valor baixo = umido
  umidPct = map(umidValue, 1023, 39, 0, 100);
  umidPct = constrain(umidPct, 0, 100);

  // ── Classificacao do status geral ────────────────────
  int status = classificarStatus(umidPct, ldrPct, tempC);

  // ── Atualizacoes de saida ─────────────────────────────
  atualizarLEDs(status);
  atualizarLCD(umidPct, ldrPct, tempC, status);
  logSerial(umidPct, ldrPct, tempC, status);
  
  // Debug temporario
  Serial.print("RAW LDR : "); Serial.println(ldrValue);
  Serial.print("RAW UMID: "); Serial.println(umidValue);

  delay(2000);
}
