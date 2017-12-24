---



layout: post
title: "Craft Beer"
date: 2016-02-19
categories:
  - Juice
description:
image: https://unsplash.it/2000/1200?image=1003
image-sm: https://unsplash.it/500/300?image=1003
---
opensource ict 2017012842 DO HYUNMIN

Proud to present our gini project!!



<ol>
  <li>발표 시연 영상 링크</li>
  <li>3D프린팅 모델링 관련 자료</li>
  <li>프로젝트 발표 ppt</li>
  <li>프로젝트 코드</li>
<ol>

<h3>GINI PROJECT </h3>

 <li>발표 시연 영상 youtube 링크
  https://youtu.be/6qEnQoS94xo</li>
 <li>3D프린팅 출력 결과 및 모델링 관련 링크
  https://spencerDo.github.io/오픈소스 도안1.jpeg
https://spencerDo.github.io/오픈소스 제품4.jpeg
https://spencerDo.github.io/오픈소스 제품5.jpeg
  
  
https://spencerDo.github.io/제품3.jpeg
https://spencerDo.github.io/제품5.jpeg</li>
<li>프로젝트 발표 ppt 링크
  https://spencerDo.github.io/오픈소스.pptx</li>
<li>프로젝트 코드 링크
  https://spencerDo.github.io/Final Code.py
  
  
  #--------------------------------------
import vlc
import smbus
import RPi.GPIO as GPIO
import time
import os
def read():
    f = open("newfile.txt","r")
    num= f.readline()
    num = int(num)
    f.close()
    return num
def write(a):
    f =open("newfile.txt","w")
    f.write(a)
    f.close()
def readc():
  f = open("new.txt","r")
  num = f.readline()
  num = int(num)
  f.close()
  return num
def writec(a):
  f = open("new.txt","w")
  f.write(a)
  f.close()
# button
GPIO.setmode(GPIO.BCM)
GPIO.setup(16,GPIO.IN)#channel up
GPIO.setup(17,GPIO.IN)#channel down
GPIO.setup(18,GPIO.IN)#pause
GPIO.setup(19,GPIO.IN)#change
GPIO.setup(20,GPIO.IN)#down
GPIO.setup(21,GPIO.IN)#break


# Define some device parameters
I2C_ADDR  = 0x27 # I2C device address
LCD_WIDTH = 16   # Maximum characters per line

# Define some device constants
LCD_CHR = 1 # Mode - Sending data
LCD_CMD = 0 # Mode - Sending command

LCD_LINE_1 = 0x80 # LCD RAM address for the 1st line
LCD_LINE_2 = 0xC0 # LCD RAM address for the 2nd line
LCD_LINE_3 = 0x94 # LCD RAM address for the 3rd line
LCD_LINE_4 = 0xD4 # LCD RAM address for the 4th line

LCD_BACKLIGHT  = 0x08  # On
#LCD_BACKLIGHT = 0x00  # Off

ENABLE = 0b00000100 # Enable bit

# Timing constants
E_PULSE = 0.0005
E_DELAY = 0.0005

#Open I2C interface
#bus = smbus.SMBus(0)  # Rev 1 Pi uses 0
bus = smbus.SMBus(1) # Rev 2 Pi uses 1

def lcd_init():
  # Initialise display
  lcd_byte(0x33,LCD_CMD) # 110011 Initialise
  lcd_byte(0x32,LCD_CMD) # 110010 Initialise
  lcd_byte(0x06,LCD_CMD) # 000110 Cursor move direction
  lcd_byte(0x0C,LCD_CMD) # 001100 Display On,Cursor Off, Blink Off 
  lcd_byte(0x28,LCD_CMD) # 101000 Data length, number of lines, font size
  lcd_byte(0x01,LCD_CMD) # 000001 Clear display
  time.sleep(E_DELAY)

def lcd_byte(bits, mode):
  # Send byte to data pins
  # bits = the data
  # mode = 1 for data
  #        0 for command

  bits_high = mode | (bits & 0xF0) | LCD_BACKLIGHT
  bits_low = mode | ((bits<<4) & 0xF0) | LCD_BACKLIGHT

  # High bits
  bus.write_byte(I2C_ADDR, bits_high)
  lcd_toggle_enable(bits_high)

  # Low bits
  bus.write_byte(I2C_ADDR, bits_low)
  lcd_toggle_enable(bits_low)

def lcd_toggle_enable(bits):
  # Toggle enable
  time.sleep(E_DELAY)
  bus.write_byte(I2C_ADDR, (bits | ENABLE))
  time.sleep(E_PULSE)
  bus.write_byte(I2C_ADDR,(bits & ~ENABLE))
  time.sleep(E_DELAY)

def lcd_string(message,line):
  # Send string to display

  message = message.ljust(LCD_WIDTH," ")

  lcd_byte(line, LCD_CMD)

  for i in range(LCD_WIDTH):
    lcd_byte(ord(message[i]),LCD_CHR)

def main():
  # Main program block

  # Initialise display
  lcd_init()
  path = "/home/pi/Music/"
  path2 = "/home/pi/girl/"
  gi = readc()
  if gi ==0:
    a = os.listdir(path)
    channel = read()
    file = ('/home/pi/Music/'+a[channel])
    instance = vlc.Instance()
    player=instance.media_player_new()
    media = instance.media_new(file)
    player.set_media(media)
    player.play()
    time.sleep(1)
  elif gi ==1:
    b = os.listdir(path2)
    channel = read()
    file = ('/home/pi/girl/'+b[channel])
    instance = vlc.Instance()
    player=instance.media_player_new()
    media = instance.media_new(file)
    player.set_media(media)
    player.play()
    time.sleep(1)


  while True:
    number = player.get_state()
    if(number==6):
      channel+=1
      if gi ==0:
        file = ('/home/pi/Music/'+a[channel])
        instance = vlc.Instance()
        player=instance.media_player_new()
        media = instance.media_new(file)
        player.set_media(media)
        player.play()
      elif gi ==1:
        file = ('/home/pi/girl/'+b[channel])
        instance = vlc.Instance()
        player=instance.media_player_new()
        media = instance.media_new(file)
        player.set_media(media)
        player.play()
    if GPIO.input(16)==0:
      print("channel +1")
      gi = gi+1
      channel =0
      file = ('/home/pi/girl/'+b[channel])
      instance = vlc.Instance()
      player=instance.media_player_new()
      media = instance.media_new(file)
      player.set_media(media)
      player.play()
    if GPIO.input(17)==0:
      print("channel -1")
      gi = gi-1
      channel=0
      file = ('/home/pi/Music/'+a[channel])
      instance = vlc.Instance()
      player=instance.media_player_new()
      media = instance.media_new(file)
      player.set_media(media)
      player.play()
    if GPIO.input(18)==0:
        if number ==3:
            player.pause()
            time.sleep(1)
        if number ==4:
            player.play()
            time.sleep(1)
    elif GPIO.input(19)==0:
        if gi ==0:
          player.pause()
          time.sleep(1)
          print("change")
          channel+=1
          file = ('/home/pi/Music/'+a[channel])
          instance = vlc.Instance()
          player=instance.media_player_new()
          media = instance.media_new(file)
          player.set_media(media)
          player.play()
        if gi ==1:
          player.pause()
          time.sleep(1)
          print("change")
          channel+=1
          file = ('/home/pi/girl/'+b[channel])
          instance = vlc.Instance()
          player=instance.media_player_new()
          media = instance.media_new(file)
          player.set_media(media)
          player.play()

    elif GPIO.input(20)==0:
        if gi ==0:
          player.pause()
          time.sleep(1)
          print("change down")
          channel = channel -1
          file = ('/home/pi/Music/'+a[channel])
          instance = vlc.Instance()
          player=instance.media_player_new()
          media = instance.media_new(file)
          player.set_media(media)
          player.play()
        elif gi ==1:
          player.pause()
          time.sleep(1)
          print("change down")
          channel = channel -1
          file = ('/home/pi/girl/'+b[channel])
          instance = vlc.Instance()
          player=instance.media_player_new()
          media = instance.media_new(file)
          player.set_media(media)
          player.play()

    elif GPIO.input(21)==0:
        print("finished")
        channel=str(channel)
        write(channel)
        gi = str(gi)
        writec(gi)
        break
    if gi ==0:
      if channel ==0:
        lcd_string("Where(feat.DEAN)",LCD_LINE_1)
        lcd_string("   Killagramz   ",LCD_LINE_2)
        time.sleep(1)
      if channel ==1:
        lcd_string("       Run      ",LCD_LINE_1)
        lcd_string("     Flo Rida   ",LCD_LINE_2)
        time.sleep(1)

      if channel ==2:
        lcd_string("   Amazing You  ",LCD_LINE_1)
        lcd_string("  Han Dong Geun ",LCD_LINE_2)
        time.sleep(1)

      if channel ==3:
        lcd_string("     Like it    ",LCD_LINE_1)
        lcd_string(" Jong Shin Yoon ",LCD_LINE_2)
        time.sleep(1)
    if gi ==1:#channel 1
      if channel ==0:
        lcd_string(".   BUM Ba Ya   ",LCD_LINE_1)
        lcd_string("   black pink   ",LCD_LINE_2)
        time.sleep(1)
      if channel ==1:
        lcd_string("when The Cherry ",LCD_LINE_1)
        lcd_string("      I.O.O     ",LCD_LINE_2)
        time.sleep(1)

      if channel ==2:
        lcd_string("   Ring my bell ",LCD_LINE_1)
        lcd_string("    Girl,s day  ",LCD_LINE_2)
        time.sleep(1)

      if channel ==3:
        lcd_string("   Heart attack ",LCD_LINE_1)
        lcd_string("      A.O.A.    ",LCD_LINE_2)
        time.sleep(1)




if __name__ == '__main__':

  try:
    main()
  except KeyboardInterrupt:
    pass
  finally:
    lcd_byte(0x01, LCD_CMD)</li>
