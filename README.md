# Wifi-scanning
Roll call by scanning Wifi signal (Python)

import time,network 
scanTime=3000                         # scan 30 seconds
from uFirebase import uFirebase 
def scan():
  global now
  #counter=0
  pathName="WiFiScan"
  listName={
  "STUDENT-C2-1":                     # password of wifi AP
    "Student A","MicroPython-3ee163": # wifi MAC address of Student A
    "Student B","MicroPython-60f073": # wifi MAC address of Student B
    "Student C","MicroPython-60f109": # wifi MAC address of Student C
    "Student D","MicroPython-ed8198": # wifi MAC address of Student D
    "Student E","MicroPython-3ee192": # wifi MAC address of Student E
  }
  fire=uFirebase(firebase=
  "https://bluetooth-device-searching.firebaseio.com/") # URL of firebase
  wlan=network.WLAN(0)
  try:
    while True:
      #counter=counter+1
      #if not counter%50:
      state=fire.get("WiFiScan/beginScan/scan")  # database name of firebase 
      print(state)
      if state:
        aps=wlan.scan()
        print(aps)
        time.sleep_ms(scanTime)
        dt=now()
        if dt:
          today=pathName+"/"+"{}{:0>2d}{:0>2d}".format(dt[0],dt[1],dt[2])
          checkTime='\"{:0>2d}:{:0>2d}:{:0>2d}\"'.format(dt[3],dt[4],dt[5])
          fire.delete(today)
          for ap in aps:
            ssid=ap[0].decode("UTF-8")
            id=listName.get(ssid)
            if ap[3]>-80 and id:
              print(id)
              fire.patch(today,{id:checkTime})
        fire.patch("WiFiScan/beginScan",{"scan":0})      
      #time.sleep_ms(10)
  except Exception:
    print("Raise Exception?")

