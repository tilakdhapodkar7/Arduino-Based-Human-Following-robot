# Arduino-Based-Human-Following-robot
#include <NewPing.h>

#define ULTRASONIC_SENSOR_TRIG 11
#define ULTRASONIC_SENSOR_ECHO 12

#define MAX_FORWARD_MOTOR_SPEED 60   // increased speed
#define MAX_MOTOR_TURN_SPEED_ADJUSTMENT 40

#define MIN_DISTANCE 5
#define MAX_DISTANCE 80   // DISTANCE OF HUMAN 

#define IR_SENSOR_RIGHT 2
#define IR_SENSOR_LEFT 3

// RIGHT MOTOR
int enableRightMotor = 5;
int RightMotorPin1 = 7;
int RightMotorPin2 = 8;

// LEFT MOTOR
int enableLeftMotor = 6;
int LeftMotorPin1 = 9;
int LeftMotorPin2 = 10;

// 🔥 Increased max distance to 100 cm
NewPing mySensor(ULTRASONIC_SENSOR_TRIG, ULTRASONIC_SENSOR_ECHO, 100);

void setup()
{
  pinMode(enableRightMotor, OUTPUT);
  pinMode(RightMotorPin1, OUTPUT);
  pinMode(RightMotorPin2, OUTPUT);

  pinMode(enableLeftMotor, OUTPUT);
  pinMode(LeftMotorPin1, OUTPUT);
  pinMode(LeftMotorPin2, OUTPUT);

  pinMode(IR_SENSOR_RIGHT, INPUT);
  pinMode(IR_SENSOR_LEFT, INPUT);

  rotateMotor(0, 0);
}

void loop()
{
  delay(50); //  sensor stability

  int distance = mySensor.ping_cm();

  // handle no echo case
  if (distance == 0) {
    distance = 200;
  }

  int RIGHTIRSensorValue = digitalRead(IR_SENSOR_RIGHT);
  int LeftIRSensorValue = digitalRead(IR_SENSOR_LEFT);

  if (RIGHTIRSensorValue == LOW && LeftIRSensorValue == HIGH)
  {
    rotateMotor(MAX_FORWARD_MOTOR_SPEED - MAX_MOTOR_TURN_SPEED_ADJUSTMENT,
                MAX_FORWARD_MOTOR_SPEED + MAX_MOTOR_TURN_SPEED_ADJUSTMENT);
  }
  else if (RIGHTIRSensorValue == HIGH && LeftIRSensorValue == LOW)
  {
    rotateMotor(MAX_FORWARD_MOTOR_SPEED + MAX_MOTOR_TURN_SPEED_ADJUSTMENT,
                MAX_FORWARD_MOTOR_SPEED - MAX_MOTOR_TURN_SPEED_ADJUSTMENT);
  }
  else if (distance >= MIN_DISTANCE && distance <= MAX_DISTANCE)
  {
    rotateMotor(MAX_FORWARD_MOTOR_SPEED, MAX_FORWARD_MOTOR_SPEED);
  }
  else
  {
    rotateMotor(0, 0);
  }
}

void rotateMotor(int RIGHTMotorSpeed, int LeftMotorSpeed)
{
  // RIGHT MOTOR
  if (RIGHTMotorSpeed < 0)
  {
    digitalWrite(RightMotorPin1, LOW);
    digitalWrite(RightMotorPin2, HIGH);
  }
  else if (RIGHTMotorSpeed > 0)
  {
    digitalWrite(RightMotorPin1, HIGH);
    digitalWrite(RightMotorPin2, LOW);
  }
  else
  {
    digitalWrite(RightMotorPin1, LOW);
    digitalWrite(RightMotorPin2, LOW);
  }

  // LEFT MOTOR
  if (LeftMotorSpeed < 0)
  {
    digitalWrite(LeftMotorPin1, LOW);
    digitalWrite(LeftMotorPin2, HIGH);
  }
  else if (LeftMotorSpeed > 0)
  {
    digitalWrite(LeftMotorPin1, HIGH);
    digitalWrite(LeftMotorPin2, LOW);
  }
  else
  {
    digitalWrite(LeftMotorPin1, LOW);
    digitalWrite(LeftMotorPin2, LOW);
  }

  analogWrite(enableRightMotor, abs(RIGHTMotorSpeed));
  analogWrite(enableLeftMotor, abs(LeftMotorSpeed));
}
