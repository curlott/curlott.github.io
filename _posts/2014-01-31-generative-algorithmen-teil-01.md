---
layout: post
title: Generative Algorithmen - Setup der Kommunikation zwischen Processing, Pure Data und Ableton Live über OSC und MIDI
excerpt: |
  Dies ist der erste Teil des Artikels *Audiovisuelle Installationen mit Generativen Algorithmen*. Wir erstellen Fraktale, die wie Bäume aussehen und lassen sie von elfengleichen Klängen begleiten. Abgefahren!
date: 2014-01-31 18:00
categories: Blog IMM
tags: 
  - Processing
  - Pure Data
  - OSC
  - MIDI
tools:  
  - Processing
  - LiveOSC
  - LoopBe1
  - Pure Data
  - Ableton Live
image: lol
feature: <iframe width="960" height="336" src="http://www.youtube.com/embed/t3XHLunRFz4?showinfo=0&controls=1&theme=light&color=white" frameborder="0" allowfullscreen></iframe>
---

* TOC
{:toc}

##Intro

*Wie Sie in dem Video oben sehen, sehen Sie nichts*. Ha. Ha. Ha. ...... Ha. Ich werde dir nach und nach die drei Tools, die ich dafür benutzt habe, näher bringen. Du bekommst gezeigt, wie man OSC und MIDI benutzt, um [Processing], [Pure_Data] und [Ableton_Live_9] miteinander sprechen zu lassen. 

Dieser Artikel besteht aus drei Teilen: *Setup der Kommunikation zwischen Processing, Pure Data und Ableton Live über OSC und MIDI*, *Baum-Fraktale Zeichnen in Processing* und *Akkorde generieren in Pure Data Extended* Bereit für Teil 1? Abfahrt!

> **Voraussetzungen:** 
> Du solltest die Basics der drei Tools schon kennen und Programmieren sollte für dich kein neuer Hut sein. Naja, du wirst es schon merken, wenn es zu verrückt wird. Aber: Ich selbst habe hier [Pure_Data] und [Processing] zum ersten mal angefasst. 
{:.info}

##Das Setup

Ich habe leider keinen Mac und benutze Windows. Die drei Tools sollten allerdings auch auf Mac ohne Probleme laufen. Für Linux brauchst du einfach einen Ersatz für Ableton Live:

* **[Processing]**
    - Hat die Rechenarbeit übernommen. Fraktale gezeichnet und sogar schon Noten errechnet. Den Schritt möchte ich allerdings für diesen Artikel in Pure Data umsiedeln, weil cooler.
* **[Pure_Data] Extended**
    - War der Mittelsmann zwischen Processing und Ableton Live. Hat MIDI Signale erstellt und wird jetzt die komplette Berechnung der Noten und Akkorde übernehmen.
* **[Ableton_Live_9]**
    - War mein persönlicher Musiker, hat brav all das gespielt, was Pure Data ihm dirigiert hat und hat am Ende sogar selbst was dazu beigetragen und den Beat vorgegeben. *Kick, kick, kick, kick, ...*

> **Ableton Live ist kein Muss:**
> Du kannst natürlich jedes andere Programm verwenden, was MIDI Signale empfangen kann. Premium ist natürlich noch die Kompatibilität mit [OSC] (*Open Sound Protocol*). 
> Hast du Erfahrung mit [LMNS]? Das schaut gut aus, ist Open Source und umsonst. Lass es mich wissen.
{:.advice}

##Die Kommunikation zwischen Processing und Pure Data

Wir brauchen dafür in Processing die Libary `oscP5`. Die bekommst du aus Processing raus über den Menüpunk `Sketch` > `Import Libary...` > `Add Libary...` > `oscP5` eingeben und fertig!

Fangen wir endlich mal an und schreiben ein bisschen Code in Processing. Den hier habe ich übrigens direkt aus dem Beispiel von libOSC:

~~~ text

//libraries needed for osc
import oscP5.*;
import netP5.*;

//variables needed for osc
OscP5 oscP5;
NetAddress myRemoteLocation;

//set/change port numbers here
int incomingPort = 12000;
int outgoingPort = 12001;

//set/change the IP address that the OSC data is being sent to
//127.0.0.1 is the local address (for sending osc to an application on the same computer)
String ipAddress = "127.0.0.1";

void setup() {
  //for incoming OSC messages
  oscP5 = new OscP5(this,incomingPort); //port number set above
  
  //for outgoing OSC messages
  myRemoteLocation = new NetAddress(ipAddress, outgoingPort);
  
  //send start note1
  OscMessage Mode = new OscMessage("/note1");
  Mode.add(60);
  oscP5.send(Mode, myRemoteLocation);
}

~~~

Es passiert folgendes:

Wir importieren die Bibliotheken und bauen eine Verbindung auf. Die `incomingPort` und `outgoingPort` könnt ihr erst mal so lassen. Aber hier werden wir später etwas ändern.

`ipAddress` verweist auf das Gerät, auf dem das Programm läuft. Für lokale Tests ist `127.0.0.1` völlig ausreichend.

In der Funktion `setup()` bauen wir eine OSC Nachricht zusammen, die einmal aus dem String `"/note1"` besteht und dann noch eine `60` angeheftet bekommt. Die `60` wird gleich in Pure Data zu einem MIDI Signal. Sie wird unser *Pitch* sein und dich ein schönes *C* hören lassen. 

![Setup of Pure Data Extended](/images/osc_setup_pde.png)

`mrpeach` ist die OSC Bibliothek für Pure Data. `routeOSC` benutzen wir um zwischen mehreren eingehenden Signalen hin und her zu Switchen. Denk an mehrere Noten, denk an Akkorde, ... denk an Elfengesang. 

Unser Fall ist noch nicht ganz so cool, aber beim Programmieren ist es ähnlich wie mit Bausparverträgen. Haste jetzt nix von, dafür später mehr. *Applaus*

`makenote` baut uns Das MIDI Signal. Unsere `60` wird hier aus Processing über OSC eingespeist und überschreibt die `80` (das wäre ein hohes *G#*).

Wenn wir jetzt unser Programm in Processing starten, dann müsste Pure Data das versprochene *C* spielen.

> `makenote` kann drei Werte empfangen:
>
>   - **Pitch** Die Tonhöhe in Halbtonschritten
>   - **Velocity** Die Härte des Anschlages (127 ist Maximum)
>   - **Duration** Die Dauer des Signals in Millisekunden
{:.advice}

##Pure Data und Ableton Live über MIDI verbinden

Um Ableton Live MIDI Signale empfangen zu lassen, musste ich [LoopBe1] installieren. Das ist erstellt einen virtuellen MIDI Port -- Was es alles gibt. Wähle einfach beim Ausgangs-MIDI-Port in Pure Data `LoopBe1` aus und es funktioniert. 

In Ableton Live musste ich dann noch `LoopBe1` in `Preferences` > `MIDI Sync` selektieren:

![MIDI Einstellungen in Ableton_Live_9](/images/ableton_live_midi_prefs.png)

##Ableton Live, Processing und LiveOSC 

Zum Glück gibt es fleißige Hände, die nützliche Sachen schreiben und die für die Allgemeinheit ins Netz stellen. So auch bei [LiveOSC], eine Bibliothek für Ableton Live, die es uns erlaubt AL über OSC zu steuern und sogar Events abzufangen. 

> Ich habe bei der Installation vergessen [Python_2.5.1] zu installieren und dann hat gar nichts funktioniert. Also installiert alles brav nach der Anleitung auf der Seite von [LiveOSC].
{:.info}

![MIDI Einstellungen in Ableton Live 9 für LiveOSC](/images/ableton_live_osc_prefs.png) 

> **LiveOSC** sendet über den `port 9001` und empfängt auf `port 9000`. 
{:.advice}

Ich möchte, dass so Geschichten, wie der Beat aus Ableton Live auch in Pure Data landen. Also gehen wir den Umweg zu Processing einmal über unseren Mittelsmann Pure Data:

![OSC Nachrichten in Pure Data empfangen](/images/osc_setup_pde_02.png) 

> Klick auf den Knopf um eine Verbindung aufzubauen
{:.info}

Der letzte Schritt ist es dann diese Nachrichten weiter zu Processing zu schicken und sie dort zu Empfangen: 

~~~text

void oscEvent(OscMessage theOscMessage) {
  // println("theOscMessage: "+theOscMessage);
  ...
}

~~~


`oscEvent()` wird aufgerufen, wann immer ein neuer Beat anfängt. Geil? Geil!

##Und weiter?

Wir schicken jetzt Signale von und zu allen Programmen. Das ist cool, so haben wir Kontrolle über alles. Wir schicken sogar schon eine `60` aus Processing nach Pure Data. Dort wird sie zu einem `C` und als MIDI Signal verpackt. Das landet letztendlich in Ableton Live und wird abgespielt. Auch cool, aber es wird noch cooler: Als nächstes zeichnen wir in Processing ein paar Fraktale, die aussehen wie Bäume. Mega!

[Processing]: http://processing.org/
[Pure_Data]: http://puredata.info/
[Ableton_Live_9]: https://www.ableton.com/de/live/new-in-9/
[The_Nature_of_Code]:http://natureofcode.com/book/
[OSC]: http://en.wikipedia.org/wiki/Open_Sound_Control
[MIDI]: http://en.wikipedia.org/wiki/Musical_Instrument_Digital_Interface
[LoopBe1]: http://www.nerds.de/en/download.html
[LiveOSC]: http://livecontrol.q3f.org/ableton-liveapi/liveosc/
[Python_2.5.1]: http://www.python.org/download/releases/2.5.1/
[LMNS]: http://lmms.sourceforge.net/


