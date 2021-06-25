# Stewart-s-platform
Made stewarts platform successfully :)

Arduino Code :

#include <Servo.h>
const float rbase = 7,Rpat = 7,d = 12,h = 3,pi = 3.14159,alpha = radians(15),rpat = Rpat*sin(pi/3)/cos(alpha),
beta[] = {radians(90),radians(150),radians(210),radians(270),radians(330),radians(30)},
base[6][3] = {{rbase,0,0},{rbase/2,rbase*sqrt(3)/2,0},{-rbase/2,rbase*sqrt(3)/2,0},{-rbase,0,0},{-rbase/2,-rbase*sqrt(3)/2,0},{rbase/2,-rbase*sqrt(3)/2,0}},
plat[6][3] = {{rpat*cos(pi/6 - alpha),rpat*sin(pi/6 - alpha),0},{rpat*cos(pi/6 + alpha),rpat*sin(pi/6 + alpha),0},{-rpat*cos(pi/6 + alpha),rpat*sin(pi/6 + alpha),0},{-rpat*cos(pi/6 - alpha),rpat*sin(pi/6 - alpha),0},{-rpat*sin(alpha),-rpat*cos(alpha),0},{rpat*sin(alpha),-rpat*cos(alpha),0}};

const int servo_pin[] = {9,3, 5, 11, 6, 10}, servo_zero[6] = {1710, 1280, 1700, 1300, 1680, 1300};
float x=10,y=0,z=10,A=radians(30),B =radians(30) ,C = radians(10);
Servo servo[6];
void setup() {
  
  Serial.begin(9600);
  Serial.print(" press 1 to change angle:");
  for(int i = 0; i < 6; i++)
 {
 servo[i].attach(servo_pin[i]);
 servo[i].writeMicroseconds(servo_zero[i]);
 }
 delay(1000);

  }

void loop() {
  /*
  if(Serial.available()){
     Serial.print("x value:");
     Serial.println(" ");
     x = Serial.read();
     delay(2000);
     Serial.print("y Value:");
     Serial.println(" ");
     y = Serial.read();
     delay(2000);
     Serial.print("z Value:");
     Serial.println(" ");
     z = Serial.read();
     delay(2000);
     Serial.print("A value:");
     Serial.println(" ");
     A = Serial.read();
     delay(2000);
     Serial.print("B Value:");
     Serial.println(" ");
     B = Serial.read();
     delay(2000);
     Serial.print("C Value:");
     Serial.println(" ");
     C = Serial.read();
     delay(2000);     
    }
    */

  
  static float R[3][3] =  {{cos(C)*cos(B), -sin(C)*cos(A)+cos(C)*sin(A)*sin(B), sin(C)*sin(A)+cos(C)*sin(B)*cos(A)},
                          {sin(C)*cos(B), cos(C)*cos(A)+sin(C)*sin(A)*sin(B), -cos(C)*sin(A)+sin(C)*sin(B)*cos(A)},
                          {-sin(B), cos(B)*sin(A), cos(B)*cos(A)}
                         };

  static float T[3] = {x,y,z};
 for(int k=0;k<6;k++){ 
  static float platform[3] = {plat[k][0],plat[k][1],plat[k][2]}, kbase[3] = {base[k][0],base[k][1],base[k][2]};
  static float noice[3] = {0,0,0};
  for(int i=0 ; i <3;i++){
    for(int j=0; j<3; j++){
     noice[i] = R[i][j]*platform[j]; 
    }
  }
  static float lk[3] = {T[0] + noice[0] - kbase[0],T[1] + noice[1] - kbase[1],T[2] + noice[2] - kbase[2]};
  static float ek = 2*h*lk[2];
  static float fk = 2*h*(cos(beta[k])*lk[0] + sin(beta[k])*lk[1]);
  static float gk = lk[0]*lk[0]+lk[1]*lk[1]+lk[2]*lk[2] - d*d + h*h;
  static float alphak = asin(gk/sqrt(ek*ek+fk*fk))+atan2(fk,ek);
  Serial.print(alphak);
  servo[k].writeMicroseconds(alphak);
 }
}
