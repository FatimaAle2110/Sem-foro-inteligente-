// ===============================
// SEMAFORO INTELIGENTE
// CON DETECCION DE INUNDACION
// ===============================

// ---------- SEMAFORO 1 ----------
int rojo1 = 2;
int amarillo1 = 3;
int verde1 = 4;

// ---------- SEMAFORO 2 ----------
int rojo2 = 5;
int amarillo2 = 6;
int verde2 = 7;

// ---------- SENSORES ULTRASONICOS ----------
int trig1 = 8;
int echo1 = 9;

int trig2 = 10;
int echo2 = 11;

// ---------- SENSOR DE AGUA ----------
int sensorInundacion = 12;

// ---------- AJUSTES ----------
int distanciaDeteccion = 25;

// ---------- TIEMPOS ----------
int tiempoVerde = 2000;
int tiempoVerdeLibre = 700;
int tiempoAmarillo = 400;


// ======================================
// FUNCION PARA MEDIR DISTANCIA
// ======================================
long medirDistancia(int trig, int echo) {

  digitalWrite(trig, LOW);
  delayMicroseconds(2);

  digitalWrite(trig, HIGH);
  delayMicroseconds(10);

  digitalWrite(trig, LOW);

  long duracion = pulseIn(echo, HIGH, 30000);

  if (duracion == 0) return 999;

  return duracion * 0.034 / 2;
}


// ======================================
// CONFIGURACION INICIAL
// ======================================
void setup() {

  pinMode(rojo1, OUTPUT);
  pinMode(amarillo1, OUTPUT);
  pinMode(verde1, OUTPUT);

  pinMode(rojo2, OUTPUT);
  pinMode(amarillo2, OUTPUT);
  pinMode(verde2, OUTPUT);

  pinMode(trig1, OUTPUT);
  pinMode(echo1, INPUT);

  pinMode(trig2, OUTPUT);
  pinMode(echo2, INPUT);

  pinMode(sensorInundacion, INPUT);

  Serial.begin(9600);
}


// ======================================
// LOOP PRINCIPAL
// ======================================
void loop() {

  long d1 = medirDistancia(trig1, echo1);
  long d2 = medirDistancia(trig2, echo2);

  bool s1 = d1 > 0 && d1 < distanciaDeteccion;
  bool s2 = d2 > 0 && d2 < distanciaDeteccion;

  // Cambiar HIGH por LOW si trabaja invertido
  bool inundacion = digitalRead(sensorInundacion) == HIGH;


  // ---------- MONITOR SERIAL ----------
  Serial.print("S1: ");
  Serial.print(d1);
  Serial.print(s1 ? " ACTIVO" : " LIBRE");

  Serial.print(" | S2: ");
  Serial.print(d2);
  Serial.print(s2 ? " ACTIVO" : " LIBRE");

  Serial.print(" | Inundacion: ");
  Serial.println(inundacion ? "SI" : "NO");


  // ======================================
  // INUNDACION DETECTADA
  // ======================================
  if (inundacion) {

    // SEMAFORO 1 EN ROJO
    digitalWrite(rojo1, HIGH);
    digitalWrite(amarillo1, LOW);
    digitalWrite(verde1, LOW);

    // SEMAFORO 2 EN VERDE
    digitalWrite(rojo2, LOW);
    digitalWrite(amarillo2, LOW);
    digitalWrite(verde2, HIGH);

    Serial.println("INUNDACION EN AVENIDA 1");

    delay(100);
  }

  // ======================================
  // SOLO SENSOR 1 ACTIVO
  // ======================================
  else if (s1 && !s2) {

    ponerVerde1();

    delay(50);
  }

  // ======================================
  // SOLO SENSOR 2 ACTIVO
  // ======================================
  else if (s2 && !s1) {

    ponerVerde2();

    delay(50);
  }

  // ======================================
  // AMBOS SENSORES ACTIVOS
  // ======================================
  else if (s1 && s2) {

    ponerVerde1();

    delay(tiempoVerde);

    ponerAmarillo1();

    delay(tiempoAmarillo);

    ponerVerde2();

    delay(tiempoVerde);

    ponerAmarillo2();

    delay(tiempoAmarillo);
  }

  // ======================================
  // NINGUN SENSOR ACTIVO
  // ======================================
  else {

    ponerVerde1();

    delay(tiempoVerdeLibre);

    ponerAmarillo1();

    delay(tiempoAmarillo);

    ponerVerde2();

    delay(tiempoVerdeLibre);

    ponerAmarillo2();

    delay(tiempoAmarillo);
  }
}


// ======================================
// SEMAFORO 1 VERDE
// ======================================
void ponerVerde1() {

  digitalWrite(verde1, HIGH);
  digitalWrite(amarillo1, LOW);
  digitalWrite(rojo1, LOW);

  digitalWrite(rojo2, HIGH);
  digitalWrite(amarillo2, LOW);
  digitalWrite(verde2, LOW);
}


// ======================================
// SEMAFORO 2 VERDE
// ======================================
void ponerVerde2() {

  digitalWrite(verde2, HIGH);
  digitalWrite(amarillo2, LOW);
  digitalWrite(rojo2, LOW);

  digitalWrite(rojo1, HIGH);
  digitalWrite(amarillo1, LOW);
  digitalWrite(verde1, LOW);
}


// ======================================
// SEMAFORO 1 AMARILLO
// ======================================
void ponerAmarillo1() {

  digitalWrite(verde1, LOW);
  digitalWrite(amarillo1, HIGH);
  digitalWrite(rojo1, LOW);

  digitalWrite(rojo2, HIGH);
  digitalWrite(amarillo2, LOW);
  digitalWrite(verde2, LOW);
}


// ======================================
// SEMAFORO 2 AMARILLO
// ======================================
void ponerAmarillo2() {

  digitalWrite(verde2, LOW);
  digitalWrite(amarillo2, HIGH);
  digitalWrite(rojo2, LOW);

  digitalWrite(rojo1, HIGH);
  digitalWrite(amarillo1, LOW);
  digitalWrite(verde1, LOW);
}entes 
