#define BLYNK_TEMPLATE_ID "  "
#define BLYNK_TEMPLATE_NAME "  "
#define BLYNK_AUTH_TOKEN " "
#define BLYNK_PRINT Serial
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include <DHT.h>
// Auth Token Blynk dan Kredensial Wi-Fi
char ssid[] = "...."; // Ganti dengan SSID Wi-Fi Anda
char pass[] = "...";            // Ganti dengan password Wi-Fi Anda
char auth[] = BLYNK_AUTH_TOKEN;
#define DHTPIN D5        // Pin DHT11 terhubung
#define DHTTYPE DHT11    // Tipe sensor DHT
#define RELAY1 D1        // Relay 1 di pin D1
#define RELAY2 D2        // Relay 2 di pin D2
#define RELAY3 D3        // Relay 3 di pin D3
#define RELAY4 D4        // Relay 4 di pin D4

DHT dht(DHTPIN, DHTTYPE);
BlynkTimer timer;

// Variabel relay
int value0, value1, value2, value3;

// Fungsi untuk membaca data sensor dan mengirimkan ke Blynk
void sendSensor() {
  float temperature = dht.readTemperature();
  float humidity = dht.readHumidity();

  // Validasi data dari sensor
  if (isnan(temperature) || isnan(humidity)) {
    Serial.println("DHT11 tidak terbaca...");
    return;
  }

  // Tampilkan data di Serial Monitor
  Serial.print("Suhu: ");
  Serial.println(temperature);
  Serial.print("Kelembapan: ");
  Serial.println(humidity);

  // Kirim data ke Virtual Pin Blynk
  Blynk.virtualWrite(4, temperature); // Virtual Pin V4 untuk suhu
  Blynk.virtualWrite(5, humidity);   // Virtual Pin V5 untuk kelembapan
}

// Fungsi untuk mengontrol relay berdasarkan data dari Blynk
BLYNK_WRITE(V0) {
  value0 = param.asInt();
  digitalWrite(RELAY1, value0);
}

BLYNK_WRITE(V1) {
  value1 = param.asInt();
  digitalWrite(RELAY2, value1);
}

BLYNK_WRITE(V2) {
  value2 = param.asInt();
  digitalWrite(RELAY3, value2);
}

BLYNK_WRITE(V3) {
  value3 = param.asInt();
  digitalWrite(RELAY4, value3);
}

void setup() {
  // Inisialisasi Serial Monitor
  Serial.begin(9600);

  // Inisialisasi DHT sensor
  dht.begin();

  // Inisialisasi pin relay sebagai output
  pinMode(RELAY1, OUTPUT);
  pinMode(RELAY2, OUTPUT);
  pinMode(RELAY3, OUTPUT);
  pinMode(RELAY4, OUTPUT);

  // Default relay mati
  digitalWrite(RELAY1, LOW);
  digitalWrite(RELAY2, LOW);
  digitalWrite(RELAY3, LOW);
  digitalWrite(RELAY4, LOW);

  // Hubungkan ke Blynk
  Blynk.begin(auth, ssid, pass);

  // Timer untuk mengirim data sensor setiap 2 detik
  timer.setInterval(2000L, sendSensor);
}
void loop() {
  Blynk.run();
  timer.run();
}
