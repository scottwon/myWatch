#include <Rtc_Pcf8563.h>
#include<Adafruit_NeoPixel.h>
#include "Wire.h"
#include "U8glib.h"
#include"I2Cdev.h"
#include<SHT2x.h>
#include<IRremote.h>

#define setFont_L u8g.setFont(u8g_font_7x13)
#define setFont_M u8g.setFont(u8g_font_fixed_v0r)
#define setFont_S u8g.setFont(u8g_font_fixed_v0r)
#define setFont_SS u8g.setFont(u8g_font_fub25n)
U8GLIB_SSD1306_128X64 u8g(U8G_I2C_OPT_NONE);
#define init_draw 10  
#define Light_PIN A0
#define Pixel_PIN 6
#define Buzzer_PIN 4
#define Buzzer_FRE 600
#define WATCH 1
#define TIMER 2
#define REV_TIMER 3
#define ALARM_SET 4

Rtc_Pcf8563 rtc;
Adafruit_NeoPixel strip=Adafruit_NeoPixel(1,Pixel_PIN,NEO_GRB+NEO_KHZ800);
unsigned long timer_draw,timer,timer_1,timer_2;
int pkj=0;
String dateStr, ret;
float sensor_tem;
float sensor_hum;
int sensor_light;
int RECV_PIN=2;
IRrecv irrecv(RECV_PIN);
decode_results results;
int m_status=WATCH;
int alarm_h,alarm_m,alarm_s;
boolean alarm_en=false;

void setup() {
  Serial.begin(9600);
  Wire.begin();
  strip.begin();
  strip.show();
  irrecv.enableIRIn();
  pinMode(Buzzer_PIN,OUTPUT);
  //setRtcTime(16, 12, 22, 4, 21, 43, 00);
  timer=millis();
}

void loop() {
  read();
  remote();
  ret = getRtcTimeString();
  if(m_status==WATCH)
  {
    if (millis() - timer_draw > init_draw)
    {pkj-=4;
    u8g.firstPage();
    do{
      draw();
      }
      while ( u8g.nextPage() );
      if(alarm_en=true&&rtc.getHour()==alarm_h&&rtc.getMinute()==alarm_m&&rtc.getSecond()==alarm_s)tone(Buzzer_PIN,Buzzer_FRE);
      timer_draw = millis();
    }
  }
  else if(m_status==TIMER){
    int H=0,M=0,S=0,MS=0;
    while(1){
      setFont_L;
      u8g.firstPage();
      do{
        u8g.setPrintPos(10, 49);
        u8g.print("00");
        u8g.setPrintPos(26,49);
        u8g.print(":");
        u8g.setPrintPos(34,49);
        u8g.print("00");
        u8g.setPrintPos(50,49);
        u8g.print(":");
        u8g.setPrintPos(58,49);
        u8g.print("00");
        u8g.setPrintPos(74,49);
        u8g.print(":");
        u8g.setPrintPos(82,49);
        u8g.print("000");
        }while(u8g.nextPage());
      if(irrecv.decode(&results)){
        irrecv.resume();
      }
      if(results.value==33456255){
        results.value=0;
        break;
      }
      delay(10);
    }
    timer_2=millis();
    while(1){
      u8g.firstPage();
      do{
        timer_1=millis()-timer_2;
        MS=timer_1%1000;
        S=(timer_1/1000)%60;
        M=((timer_1/1000)/60)%60;
        H=((timer_1/1000)/60/60)%100;
        setFont_L;
        u8g.setPrintPos(10, 49);
        if(H==0){
          u8g.print("00");
        }
        else if(H<10){
          u8g.print("0");
          u8g.print(H);
        }
        else{
          u8g.print(H);
        }
        u8g.setPrintPos(26,49);
        u8g.print(":");
        u8g.setPrintPos(34,49);
        if(M==0){
          u8g.print("00");        
        }
        else if(M<10){
          u8g.print("0");
          u8g.print(M);
        }
        else{
          u8g.print(M);
        }
        u8g.setPrintPos(50,49);
        u8g.print(":");
        u8g.setPrintPos(58,49);
        if(S==0){
          u8g.print("00");
        }
        else if(S<10){
          u8g.print("0");
          u8g.print(S);
        }
        else{
          u8g.print(S);
        }
        u8g.setPrintPos(74,49);
        u8g.print(":");
        u8g.setPrintPos(82,49);
        if(MS==0){
          u8g.print("000");
        }
        else if(MS<10){
          u8g.print("00");
          u8g.print(MS);
        }
        else if(MS<100){
          u8g.print("0");
          u8g.print(MS);
        }
        else{
          u8g.print(MS);
        }
        }while(u8g.nextPage());
      if(irrecv.decode(&results)){
        irrecv.resume();
      }
      if(results.value==33456255){
        results.value=0;
        break;
      }
    }
    while(1){
      if(irrecv.decode(&results)){
        irrecv.resume();
      }
      if(results.value==33456255){
        results.value=0;
        m_status=WATCH;
        break;
      }
      delay(10);
    }
  }
  else if(m_status==REV_TIMER){
    int H_S,M_S,S_S,H_P,M_P,S_P,H_R,M_R,S_R;
    time_set(&H_S,&M_S,&S_S,33439935);
    timer_2=millis();
    while(1){
      setFont_SS;
      u8g.firstPage();
      do{
        timer_1=millis()-timer_2;
        S_P=(timer_1/1000)%60;
        M_P=((timer_1/1000)/60)%60;
        H_P=((timer_1/1000)/60/60)%24;
        S_R=S_S-S_P;
        M_R=M_S-M_P;
        H_R=H_S-H_P;
        if(S_R<0){
          S_R+=60;
          --M_R;
        }
        if(M_R<0){
          M_R+=60;
          --H_R;
        }
        if(H_R<0){
          break;
        }
        u8g.setPrintPos(4,49);
        if(H_R<=0){
          u8g.print("00");
        }
        else if(H_R<10){
          u8g.print("0");
          u8g.print(H_R);
        }
        else{
          u8g.print(H_R);
        }
        u8g.setPrintPos(49,49);
        if(M_R==0){
          u8g.print("00");
        }
        else if(M_R<10){
          u8g.print("0");
          u8g.print(M_R);
        }
        else{
          u8g.print(M_R);
        }
        u8g.setPrintPos(94,49);
        if(S_R==0){
          u8g.print("00");
        }
        else if(S_R<10){
          u8g.print("0");
          u8g.print(S_R);
        }
        else{
          u8g.print(S_R);
        }
        if((timer_1/500)%2==1){
          u8g.setPrintPos(34,49); 
          u8g.print(":");
          u8g.setPrintPos(79,49);
          u8g.print(":");
        }
        else{
          u8g.setPrintPos(34,49); 
          u8g.print(" ");
          u8g.setPrintPos(79,49);
          u8g.print(" ");
        }
      }while(u8g.nextPage());
      if(H_R<0){
        break;
      }
    }
    while(1){      
      if(irrecv.decode(&results)){
        irrecv.resume();
      }
      if(results.value==33439935){
        noTone(Buzzer_PIN);
        results.value=0;
        m_status=WATCH;
        break;
      }
      else{
        tone(Buzzer_PIN,Buzzer_FRE);
      }
    }
  }
  else if(m_status==ALARM_SET){
    time_set(&alarm_h,&alarm_m,&alarm_s,33472575);
    alarm_en=true;
    m_status=WATCH;
  }
}

void draw()
{
  setFont_L;
  u8g.setPrintPos(4, 16);
  u8g.print(rtc.formatDate(RTCC_DATE_US));
  u8g.print("    ");
  switch (rtc.getWeekday()) {
    case 1:
      u8g.print("Mon");
      break;
    case 2:
      u8g.print("Tue");
      break;
    case 3:
      u8g.print("Wed");
      break;
    case 4:
      u8g.print("Thu");
      break;
    case 5:
      u8g.print("Fri");
      break;
    case 6:
      u8g.print("Sat");
      break;
    case 7:
      u8g.print("Sun");
      break;
  }
  u8g.setPrintPos(pkj, 64); 
  u8g.print("TEM:");
  u8g.print(sensor_tem,1);
  u8g.print("--");
  u8g.print("HUM:");
  u8g.print(sensor_hum,1);
  setFont_SS;
  u8g.setPrintPos(18, 49);
  u8g.print(rtc.getHour());
  u8g.setPrintPos(55, 46);
  if (rtc.getSecond() % 2 == 0)
    u8g.print(":");
  else
    u8g.print(" ");
  u8g.setPrintPos(68, 48);
  if (rtc.getMinute() < 10)
  {
    u8g.print("0");
    u8g.print(rtc.getMinute());
  }
  else
    u8g.print(rtc.getMinute());
}

void setRtcTime (byte _year, byte _month, byte _day, byte _week, byte _hour, byte _minute, byte _sec)
{
  //clear out all the registers
  rtc.initClock();
  rtc.setDate(_day, _week, _month, 0, _year);
  rtc.setTime(_hour, _minute, _sec);
}

String getRtcTimeString() {
  dateStr = rtc.formatDate(RTCC_DATE_US);
  dateStr += " ";
  dateStr += rtc.formatTime(RTCC_TIME_HMS);
  return dateStr;
}

void read(){
  sensor_tem=SHT2x.GetTemperature();
  sensor_hum=SHT2x.GetHumidity();
  sensor_light=analogRead(Light_PIN);
  if(sensor_light<400){
    colorWipe(strip.Color(0,map(sensor_light,0,400,255,0),0));    
  }
  else{
    colorWipe(strip.Color(0,0,0));
  }  
}

void colorWipe(uint32_t c){
  for(uint16_t i=0;i<strip.numPixels();i++){
    strip.setPixelColor(i,c);
    strip.show();
  }
}

void remote(){
  if(irrecv.decode(&results)){
    if(m_status==WATCH&&results.value==33456255){
      m_status=TIMER;
      results.value=0;
    }
    if(m_status==WATCH&&results.value==33439935){
      m_status=REV_TIMER;
      results.value=0;
    }
    if(m_status==WATCH&&results.value==33472575){
      m_status=ALARM_SET;
      results.value=0;
    }
    irrecv.resume();
  }  
}

void time_set(int* H_S,int* M_S,int* S_S,unsigned long ir_exit){
  int H1=0,H2=0,M1=0,M2=0,S1=0,S2=0,P=1;
  timer_2=millis();
  while(1){
      setFont_SS;
      u8g.firstPage();
      do{
        u8g.setPrintPos(4,49);
        u8g.print(H1);
        u8g.setPrintPos(19,49);
        u8g.print(H2);
        u8g.setPrintPos(34,49);
        u8g.print(":");
        u8g.setPrintPos(49,49);
        u8g.print(M1);
        u8g.setPrintPos(64,49);
        u8g.print(M2);
        u8g.setPrintPos(79,49);
        u8g.print(":");
        u8g.setPrintPos(94,49);
        u8g.print(S1);
        u8g.setPrintPos(109,49);
        u8g.print(S2);
        switch(P){
          case 0:
          u8g.setPrintPos(8,64);
          break;
          case 1:
          u8g.setPrintPos(23,64);
          break;
          case 2:
          u8g.setPrintPos(53,64);
          break;
          case 3:
          u8g.setPrintPos(68,64);
          break;
          case 4:
          u8g.setPrintPos(98,64);
          break;
          case 5:
          u8g.setPrintPos(113,64);
          break;        
        }
        timer_1=millis();
        if(((timer_1-timer_2)/500)%2==1){
          u8g.print("-");
        }
        else {
          u8g.print(" ");
        }
      }while(u8g.nextPage());
      if(irrecv.decode(&results)){
        irrecv.resume();
        if(results.value==33480735){
          if(P==0)P=6;
          --P;
        }
        else if(results.value==33460335){
          P=(P+1)%6;
        }
        else if(results.value==33464415){
          if(P==0){
            H1=(++H1)%3;
            if(H1==2&&H2>3){
              H1=0;
              H2=0;
            }
          }
          else if(P==1){
            H2=(++H2)%10;
            if(H2==0){
              ++H1;
            }
            if(H1==2&&H2==4){
              H1=0;
              H2=0;
            }
          }
          else if(P==2){
            M1=(++M1)%6;
          }
          else if(P==3){
            M2=(++M2)%10;
            if(M2==0){
              ++M1;
              if(M1==6){
                M1=0;
              }
            }
          }
          else if(P==4){
            S1=(++S1)%6;
          }
          else if(P==5){
            S2=(++S2)%10;
            if(S2==0){
              ++S1;
              if(S1==6){
                S1=0;
              }
            }
          }
        }
        else if(results.value==33478695){
          if(P==0){
            if(H1==0)H1=3;
            --H1;
          }
          else if(P==1){
            if(H2==0){
              H2=10;
              --H1;
            }
            --H2;
            if(H1<0){
              H1=2;
              H2=3;
            }
          }
          else if(P==2){
            if(M1==0)M1=6;
            --M1;
          }
          else if(P==3){
            if(M2==0){
              M2=10;
              --M1;
              if(M1<0)M1=5;
            }
            --M2;
          }
          else if(P==4){
            if(S1==0)S1=6;
            --S1;
          }
          else if(P==5){
            if(S2==0){
              S2=10;
              --S1;
              if(S1<0)S1=5;
            }
            --S2;
          }
        }
        else if(results.value==ir_exit){
            *H_S=H1*10+H2;
            *M_S=M1*10+M2;
            *S_S=S1*10+S2;
            results.value=0;
            break;
        }
        results.value=0;        
      }
    }
}
