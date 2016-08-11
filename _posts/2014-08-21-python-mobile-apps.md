---
layout: post
title:  "Python and mobile apps"
date:   2014-08-21
description: "My hack led me to Kivy, a desktop and mobile framework in Python."
tag: [Kivy, Python, Mobile, Copied]
comments: true
share: true
categories: [python]
project: false
---

I have come to love the [__Python__](https://www.python.org) programming language. So as you will have it, i started my week by running on a few hacks on how to develop mobile apps in python without thinking about the native languages. My hack led me to [__Kivy__](https://kivy.org), a desktop and mobile framework in Python. Kivy can be likened to [_Qt_](http://qt-project.org/) in that it has a GUI language tool known as _kv language_ and render all other functionality in the python language just like how Qt also uses the _qml_ for GUI and render all functionality in C++(default language for Qt).

<!-- more -->

I started off with a tutorial I watched and afterwards, with the help of the tutorial i managed to develop a text to speech app which i learnt can be deployed on mobile platforms as well as desktops. __Fantastic huh!!!__. First check _Kivy's_ site for how to get [started on installations](http://kivy.org/docs/gettingstarted/installation.html). Then join me through with the tutorial.

Even though kivy apps can be created in only one file, i chose to go the MV* way as advised by the tutorial. I separated logic from presentation. so if you follow suit then you will create a new file with a .kv extention. Remember the kv language specifies that a class with same name in python should be built for the root widget. what I did was to create a directory and create two files __(_saythis.py and saythis.kv_)__. Check below for corresponding files. Checkout the tutorial [here](https://www.youtube.com/watch?v=8zSNzUAfohA)

#### saythis.kv

{% highlight kivy %}

<SayThis>:
    orientation: 'vertical'
    buttonFontSize: '30sp'
    padding: root.width * 0.02, root.height * 0.02
    canvas:
        Rectangle:
            source: 'images/bg.gif'
            pos: self.pos
            size: self.size
    saywhat_text: say_text
    subprocess: sayit

    Label:
        text: 'Say this!'
        size_hint: 1, None
        height: self.texture_size[1] + (2 * root.padding[1])
        font_size: '30sp'

    TextInput:
        id: say_text
        text: 'Hello, world!'
        font_size: str(font_slider.value) + 'sp'

    Slider:
        id: font_slider
        orientation: 'horizontal'
        size_hint: 1, None
        min: 1
        max: 100
        value: 15

    BoxLayout:
        orientation: 'horizontal'
        size_hint: 1, None
        height: sayit.height

        Button:
            id: sayit
            text: 'Say it!'
            size_hint: 1, None
            height: self.texture_size[1] + (2 * root.padding[1])
            font_size: root.buttonFontSize
            on_press: root.say_something(say_text.text)

        Button:
            text: 'Clear'
            size_hint: 1, None
            height: self.texture_size[1] + (2 * root.padding[1])
            font_size: root.buttonFontSize
            on_press: root.clear()

{% endhighlight %}

#### saythis.py

{% highlight python %}

import subprocess
import kivy
kivy.require('1.8.0')

from kivy.app import App
from kivy.uix.boxlayout import BoxLayout
from kivy.properties import ObjectProperty
from kivy.uix.popup import Popup
from kivy.uix.label import Label

from plyer import tts

class SayThis(BoxLayout):
    saywhat_text = ObjectProperty(None)

    def say_something(self, text):
        try:
            tts.speak(text)
        except NotImplementedError:
            popup = Popup(
                title = "TTS Not Implemented",
                content = Label(text='Sorry, TTS is not available'),
                size_hint = (None, None),
                size = (300, 300)
            )
            popup.open()

    def clear(self):
        self.saywhat_text.text = ""
        self.saywhat_text.focus = True


class SayThisApp(App):
    def build(self):
        return SayThis()

if __name__ == '__main__':
    SayThisApp().run()

{% endhighlight %}