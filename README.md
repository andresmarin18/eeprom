#include "mbed.h"
#include "TextLCD.h"
#include "Keypad.h"
#include <cstdint>

TextLCD lcd(D7,D6,D5,D4,D3,D2,TextLCD::LCD16x2);  //rs,e,d4-d7
Keypad kpad(D12,D13,D14,D15,D11,D10,D9,D8);
char key;//lectura del teclado matricial
int x=0;
int enter;//variable de la tecla enter"D"
int cantidad=0;//cantidad de numeros digitados
int released = 1;//para detectar si la tecla esta presionada 
int numero1,numero2,numero3,numero4,numero,numero_total;
char almacen[4];
char rx[4];
char txu[5];
char rxu[4];
char clave_adm[4];
char clave_usu[4];
I2C i2c(A4,A5);
DigitalOut led(A0);

void i2(){
    char tx[5]={0x00,'1','1','1','1'};
    i2c.write(0xA0,tx,5);
    wait(0.05);
    i2c.write(0xA0,tx,1);
    wait(0.05);
    i2c.read(0xA0,rx,4);  
    //lcd.cls();
    //lcd.printf("x%c%c%c%c",rx[0],rx[1],rx[2],rx[3]);
    
}
void read (){
    char tx[1];
    
    tx[0]=0x0A;
    i2c.write(0xA0,tx,1);
    wait(0.1);
    i2c.read(0xA0,almacen,4);

}
void pantalla1(){
lcd.cls();
lcd.locate(0,0);
lcd.printf("Administrador");
lcd.locate(0,1);
lcd.printf("Usuario");
lcd.locate (14,0);
lcd.printf("*");
lcd.locate(10,1);
}
void pantalla1_1(){
lcd.cls();
lcd.locate(0,0);
lcd.printf("adm password");
lcd.locate(0,1);
lcd.printf("%d",numero_total);
}
void pantalla1_1_1(){
lcd.cls();
lcd.locate(0,0);
lcd.printf("new password");
lcd.locate(0,1);
lcd.printf("%d",numero_total);
;
}
void pantalla2(){
lcd.cls();
lcd.locate(0,0);
lcd.printf("Administrador");
lcd.locate(0,1);
lcd.printf("Usuario");
lcd.locate (14,1);
lcd.printf("*");
}
void  pantalla2_1(){
    lcd.cls();
    lcd.locate(0,0);
    lcd.printf("user password");
    lcd.locate(1,1);
    lcd.printf("%d",numero_total);
    }
void  pantalla2_1_1(){
    lcd.cls();
    lcd.locate(0,0);
    lcd.printf("led");
    lcd.locate(0,1);
    lcd.printf("on  *     off");
}
void  pantalla2_1_2(){
    lcd.cls();
    lcd.locate(0,0);
    lcd.printf("led");
    lcd.locate(0,1);
    lcd.printf("on        off *");
}
void teclado(){
        
        key = kpad.ReadKey();
         if(key == '\0') {
            released = 1;   
             enter = 0;
         }
                                 //set the flag when all keys are released
            
         if((key != '\0') && (released == 1)) {  //if a key is pressed AND previous key was released
            //lcd.cls();
            //lcd.locate(0,1);
                        
             released = 0;
              if (key == 'A' ){
            
            x--;
             wait_us(200000);

             }
             if (key == 'B'){
            x++;
             wait_us(200000);
  
            } 
             if (key == 'D'){
             enter=1;
             wait_us(200000);
             }
            if (x==10 or x==20 or x==40){
            if (key>='0'&& key<='9'){
     
            cantidad++;

            if (key=='0'){ //configuracion de teclas numericas
                numero =0;

            }
            if (key=='1'){
                numero =1;
            }
            if (key=='2'){
                numero =2;
            }
            if (key=='3'){
                numero =3;
            }
            if (key=='4'){
                numero =4;
            }
            if (key=='5'){
                numero =5;
            }
            if (key=='6'){
                numero =6;
            }
            if (key=='7'){
                numero =7;
            }
            if (key=='8'){
                numero =8;
            }
            if (key=='9'){
               numero =9; 
            }
           
            if (cantidad<5){          
            lcd.printf("%c",key);
            }if(cantidad==5){
             cantidad=0;
            }if (cantidad==0){
                lcd.cls();
                numero1=0;
                numero2=0;
                numero3=0;
                numero4=0;
                numero_total = 0;
                
            }
            if (cantidad==1){
                numero1=numero;
                numero_total= numero1;
                clave_adm[0] = key;
                if (x==20){
                    txu[1] = key;
                }
                if (x==40){
                    clave_usu[0] = key;
                }

            }else if (cantidad ==2){
                numero2 = (numero1*10)+ numero;
                numero_total= numero2;
                clave_adm[1] = key;
                if (x==20){
                    txu[2] = key;
                }if (x==40){
                    clave_usu[1] = key;
                }
            
            }else if (cantidad==3){
                numero3=(numero2*10)+numero;
                numero_total=numero3;
                clave_adm[2] = key;
                if (x==20){
                    txu[3] = key;
                }
                if (x==40){
                    clave_usu[2] = key;
                }
            }else if (cantidad==4){
                numero4=(numero3*10)+numero;
                numero_total=numero4;
                clave_adm[3] = key;
                if (x==20){
                    txu[4] = key;
                }
                if (x==40){
                    clave_usu[3] = key;
                }                 
            }
        }
        }
    }
}

// Read temperature from LM75BD
 // rs,e,d4-d7

  
const int addr = 0x50 << 1;

 
int main() {  
      i2();
      led = 0;
      
    while (1) {  
    read();
    teclado();
    if (x==-1){
        x=1;
    }
    if (x==0){
        pantalla1();
    }
    if (x==1){
        pantalla2();
    }
    if (x==2){
        x=0;
    }
    if (x==10){
        pantalla1_1();
    }if (x==9 or x==11){
         x=10;
    }if (x==20){
        pantalla1_1_1();
    }if (x==19 or x==21){
        x=20;      
    }if (x==40){
        pantalla2_1();
        
    }if (x==39 or x==41){
        x=40;
    }if (x==50){
        pantalla2_1_1();
    }if (x==49 or x==52){
        x=50;
    }if (x==51){
        pantalla2_1_2();
    }
    

    if (enter==1){
        if (x==0){
            x=10;
        }else if (x==10){
        if (clave_adm[0]==almacen[0]&&clave_adm[1]==almacen[1]&&clave_adm[2]==almacen[2]&&clave_adm[3]==almacen[3]){
            lcd.cls();
            lcd.printf("correct");
            wait(1);
            x = 20;
            numero_total=0;
        }else if (clave_adm[0] != almacen[0]or clave_adm[1]!=almacen[1]or clave_adm[2]!=almacen[2]or clave_adm[3]!=almacen[3]){
            lcd.cls();
            lcd.locate(0,0);
            lcd.printf("incorrecta");
            wait(3);
            clave_adm[0]= {};
            clave_adm[1]= {};
            clave_adm[2]= {};
            clave_adm[3]= {};
            numero_total=0;
            cantidad=0;
            x=0;
            }
        }else if(x==20){
            i2c.write(0xA0,txu,5);
            wait(0.05);
            i2c.write(0xA0,txu,1);
            wait(0.05);
            i2c.read(0xA0,rxu,4);
            wait(0.05);
            lcd.cls();
            lcd.locate (0,0);
            lcd.printf("successful");
            wait(3);
            cantidad =0;
            numero_total = 0;
            x=0;
        }else if(x==1){
            x = 40;
        }else if (x==40){
            if (clave_usu[0]== rxu[0]&& clave_usu[1]== rxu[1]&& clave_usu[2]== rxu[2]&& clave_usu[3]== rxu[3]){
                lcd.cls();
                lcd.locate(0,0);
                lcd.printf("correct");
                wait(2);
                x=50;
            }else if (clave_usu[0]!= rxu[0]or clave_usu[1]!= rxu[1]or clave_usu[2]!= rxu[2]or clave_usu[3]!= rxu[3]){
                lcd.cls();
                lcd.locate(0,0);
                lcd.printf("incorrecta");
                clave_usu[0]= {};
                clave_usu[1]= {};
                clave_usu[2]= {};
                clave_usu[3]= {};
                numero_total=0;
                cantidad=0;
                wait(2);
                x=0;
            }
        }else if(x==50){
            
            led = 1;
            wait (2);
            numero_total=0;
            cantidad=0;
            clave_usu[0]= {};
            clave_usu[1]= {};
            clave_usu[2]= {};
            clave_usu[3]= {};
            x=0;
        }else if (x==51){
            led = 0;
            wait (2);
            numero_total=0;
            cantidad=0;
            clave_usu[0]= {};
            clave_usu[1]= {};
            clave_usu[2]= {};
            clave_usu[3]= {};
            x=0;
        }
    }
   
  }
}
