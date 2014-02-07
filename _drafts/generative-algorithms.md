---
layout: post
title: Generative Algorithmen - Baum-Fraktale Zeichnen in Processing
excerpt: |
  *Audiovisuelle Installationen mit Generativen Algorithmen* Teil 2 von 3. Diesmal gehts um das Zeichnen von Bäumen in Processing.
date: 2014-01-31 18:00
categories: Blog IMM
tags: 
  - Processing
tools:  
  - Processing
image: lol
feature: <iframe width="960" height="336" src="http://www.youtube.com/embed/t3XHLunRFz4?showinfo=0&controls=1&theme=light&color=white" frameborder="0" allowfullscreen></iframe>
---

* TOC
{:toc}

##Intro

Falls du Interesse am ersten Teil hast, dann schau doch mal [hier][Teil_01]. 
Für ein gutes Verständnis von generativen Algorithmen in Processing empfehle ich [The_Nature_of_Code]. Daniel Schiffman erzählt dir auf seiner Seite mit Herzblut alles was du wissen musst. Wir behandeln in diesem Artikel speziell [Kapitel 8 - Fractals][The_Nature_of_Code_ch_08]. Das hört sich alles ganz schön kompliziert und intelligent an. Ich bin aber gar nicht so intelligent.

Wenn du dich bereit fühlst, dann nimm meine Hand und komm mit zur nächsten Überschrift.

> **Vorraussetzungen:**
> ich schaffe es nicht annähernd so gut euch die Basics über *Vektoren* zu erklären, wie Daniel Schiffman in seinem [ersten Kapitel][The_Nature_of_Code_ch_01]. Das solltest du im Vorhinein durchgehen oder wenigstens zur Hand haben. Keine Angst -- alles halb so wild.
{:.warning}


> **Nature of Code:** 
> Das Buch ist mit seinen Beispielen jeden Cent wert. Holt euch einfach das eBook und tut was gutes. Der Gute hat sogar einen [Channel auf Vimeo][The_Nature_of_Code_Video_Channel] und -- *OMG* -- ich hätte ihn gern als Dozenten!
{:.advice}

##Was wollen wir erreichen?

*Phew* -- wenn du hier bist, dann habe ich es auch geschafft weiter zu schreiben. Also: *Ziele*. Ziele sind ja für manche was wichtiges im Leben. Manch andere leben da auch ein bisschen planloser und spontaner. Ich präferiere irgendwas dazwischen mit Tendenz zum letzteren *lol* *:(*. Beim Programmieren sollten wir aber von Anfang an wissen, was wir erreichen wollen. Zumindest im Groben. Platz für Experimente sollte immer sein. 

Wir brauchen einen *Baum*, dafür brauchen wir einen *Ort* an dem unser Baum wachsen soll. Wir brauchen außerdem einen *Stamm*, *Äste* und ein paar *Blätter*. Den *Ort* machen wir erst mal fix in die Mitte des Fensters. Bei den anderen dreien schauen wir doch erst mal, was es da an Besonderheiten gibt:

![Bild vom Baum,Ast]

Aha? Der Stamm ist dabei quasi das Das Grundelement der Verästelung. Für unser Beispiel in Processing nehmen wir hierfür eine ganz einfache Linie bzw `line(x1, y1, x2, y2)`. `x1, y2` sind der Startpunkt, `x2, y2` dann das Ende. Soweit ja erstmal alles easy. Es wird so auch bleiben. 

Damit mal was in deinem [Lieblingstexteditor][Sublime_Text] steht:

~~~text
int displayWidth = 640;
int displayHeight = 320;
color backgroundColor = color(0xFFFFFF);



void setup() {
    size(displayWidth, displayHeight);
    background(backgroundColor);

    translate(displayWidth/2,displayHeight);
    line(0,0,0,-50);
}
~~~

Selbsterklärend, oder? Wir zeichnen eine 100px lange Linie in der horizontalen Mitte des Fensters.

##Ein Pfund groben Vektor, bitte

Diese Linie ist es also, unser Basiselement. Zumindest rein äußerlich. Innerlich ist es noch nicht ganz das, womit wir arbeiten können. Wenn wir uns nochmal unseren Baum als Vorbild nehmen, dann gibt es da meistens einen *Punkt* an dem der Samen gekeimt hat und eine *Richtung* in die unser Bäumchen mit einer gewissen *Geschwindigkeit* wächst. Begrüßen wir nun unseren nächsten Gast: *PVector*

Im [Vektor][The_Nature_of_Code_ch_01] speichern wir einen Punkt. In unserem Fall einen Startpunkt,  Geschwindigkeit und einen Endpunk der sich mit und unserer Geschwindigkeit bewegt. Cool.

~~~text
int displayWidth = 640;
int displayHeight = 320;
color backgroundColor = color(0xFFFFFF);

PVector startPoint = new PVector(displayWidth/2,displayHeight);
PVector endPoint = new PVector(startPoint.x, startPoint.y);
PVector velocity = new PVector(-0.1,-0.15); // Die "Geschwindigkeit"


void setup() {
    size(displayWidth, displayHeight);
}
void update() {
    endPoint.add(velocity);
}
void draw() {
    update();
    background(255);
    line(startPoint.x, startPoint.y, endPoint.x, endPoint.y);
}
~~~

Auch noch easy, oder? `velocity` gibt an, dass unser Endpunkt im nächsten [Draw Loop](http://www.processing.org/reference/draw_.html) um `0.1` nach links und `0.15` nach oben geschoben wird. So erhalten wir eine 'wachsende' Linie. Das hier war zwar nicht viel, aber wichtig. Wenn du es bis hier hin verstanden hast, dann können wir unsere wachsenden Äste mal in Klassen packen.

##Eine Klasse für sich

*ES TUT MIR LEID.* Ich kann manchmal nicht anders, wenn ich solche Wortspiele in meinem Kopf habe. Die müssen dann raus...

Dummlaberei baut leider keine Klassen, deswegen weiter: Wir sind ja furchtbar erfahren in [OOP], deswegen überlegen wir mal, wie unsere Klasse aussehen könnte. Sie trägt den Namen *Branch* oder Ast. Unser Ast hat einen *Startpunkt* und einen *Winkel*, in dem er wächst. Es gibt außerdem einen *Zeitpunkt*, an dem sich der Ast *teilt* und *neue Äste* wachsen. 

Wir brauchen eine neue Klasse im Processing, ich nenne sie mal *Branch.pde* und die sieht so aus:

~~~text
class Branch {
    PVector startPoint;
    PVector endPoint;
    PVector velocity;
    float timeToBranch;
    float timeSpentGrowing;

    void update() {
        
    }

    void render() {

    }

    Branch branch() {
        
    }
}
~~~

Wieder einfach. `timeToBranch` und `timeSpentGrowing` sind unser Timer und verantwortlich dafür, wann sich der Ast aufteilt. `branch()` erledigt dann das Teilen. 



[Sublime_Text]: http://sublimetext.com/
[Processing]: http://processing.org/
[Pure_Data]: http://puredata.info/
[Ableton_Live_9]: https://www.ableton.com/de/live/new-in-9/
[The_Nature_of_Code]:http://natureofcode.com/book/
[The_Nature_of_Code_ch_01]:http://natureofcode.com/book/chapter-1-vectors/
[The_Nature_of_Code_ch_08]:http://natureofcode.com/book/chapter-8-fractals/
[The_Nature_of_Code_Video_Channel]:http://vimeo.com/channels/natureofcode
[OSC]: http://en.wikipedia.org/wiki/Open_Sound_Control
[MIDI]: http://en.wikipedia.org/wiki/Musical_Instrument_Digital_Interface
[LoopBe1]: http://www.nerds.de/en/download.html
[LiveOSC]: http://livecontrol.q3f.org/ableton-liveapi/liveosc/
[Python_2.5.1]: http://www.python.org/download/releases/2.5.1/
[LMNS]: http://lmms.sourceforge.net/

[OOP]: http://de.wikipedia.org/wiki/Objektorientierte_Programmierung

[Teil_01]: /blog/imm/2014/01/31/generative-algorithmen-teil-01.html "Setup der Kommunikation zwischen Processing, Pure Data und Ableton Live über OSC und MIDI"