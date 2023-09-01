+++
title = "Raspi Story"
lang = "it"
type = "posts"
date = "2023-07-24"
displayInList = true
enablePostShare = true
original = false
author = "Udianon"
+++

# Brain Surgery on a RaspberryPi B (2012)
Il paziente non dà segni di vita.

Solo il led PWR rosso viene acceso, senza altre attività. Via HDMI non c'è segnale.

La scheda SD usata è funzionante e formattata correttamente, testata su di un modello identico perfettamente funzionante. Carica RaspberryOS headless.

## Prima ipotesi
Prima ipotesi è l'alimentazione.

Consultiamo gli [schematici originali](https://www.raspberrypi.com/app/uploads/2012/04/Raspberry-Pi-Schematics-R1.0.pdf) del Raspberry B per identificare il power regulator e capire dove e come viene alimentato la board

Testiamo con successo la linea di input a 5V e i regolatori di tensione sotto che forniscono le linee a 3.3V, 2.5V, 1.8V. 

Tutte funzionano correttamente.

## UART

Così decidiamo di accedere all'Output UART, possibilmente la fase di avvio del processore, prima del Kernel.

Abbiamo iniziato cercando di usare un CP2102 integrato in un NodeMCU ESP8266 come ponte UART a USB, bypassando completamente il processore già presente.

Dopo tentativi di saldatura e problemi di connessione, ci siamo spostati ad una scheda specifica per UART, sempre basata su CP210x.

I pin UART del Raspberry sono sempre definiti nelle schematics, specificamente GPIO 8 e 10.
Connessi questi e una common Ground fra UART e Raspberry.

Lì, dopo aver modificato come necessario il file /boot/config.txt, aggiungendo 
```yaml
uart_2ndstage=1
enable_uart=1
```

Abbiamo verificato che la risposta sia corretta, verificando sempre con il gemello funzionante. 

Possiamo vedere  l'UART del firmware e del boot del Kernel.

Il paziente continua a non dare segni di vita.

## Camera IR

Nel frattempo, usando una telecamera termica notiamo che, al contrario di quello funzionante, nella board problematica il controller USB-Ethernet LAN9512 che fa anche da PSU non si scalda, suggerendo che non sia attivo.

Qui rimane l'indagine, ipotizzando che il processore non si avvii correttamente e non mandi il segnale sulla rail LAN_RUN che dovrebbe controllare la PSU. 


## Link rilevanti

1. [RaspberryPi B Schematics](https://www.raspberrypi.com/app/uploads/2012/04/Raspberry-Pi-Schematics-R1.0.pdf)
2. [Controller USB-Ethernet](https://ww1.microchip.com/downloads/en/DeviceDoc/00002304A.pdf)
3. [Chip UART del ESP8266](https://www.silabs.com/documents/public/data-sheets/CP2102-9.pdf)

## Futuri step
https://www.jeffgeerling.com/blog/2021/attaching-raspberry-pis-serial-console-uart-debugging