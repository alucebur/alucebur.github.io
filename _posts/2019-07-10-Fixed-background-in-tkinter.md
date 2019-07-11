---
layout: post
title: 'Fixed background in Tkinter'
description: 'Tkinter headaches'
date: '2019-07-10 16:13:41 +0200'
categories: []
tags: [tkinter, python]
---
Past week I joined Python Discord group and started lurking around. One user (hi Sham!) posted a chunk of code
using tkinter module in one of the help channels requesting some assistance, and I got interested since I had
been working with it recently, so I set a new virtual environment, installed tkinter and pillow on it, and got
to work.

We needed a window with a background image and many buttons, so many that we had to scroll down to see them all.

![Many buttons](https://i.imgur.com/9Zqxinm.png)

But the background image was too short and it finished before all buttons were revealed, so an ugly white gap appeared.
Enlarging the background image was not an option because it got distorted too much.

![Problem](https://i.imgur.com/nrVP6U6.png)

The solution I tried was faking a fixed background image by moving the image each time we scrolled, so the
image looked like it was static all the time.

![Fixed](https://i.imgur.com/1f5rRWn.png)

Here is the code. The magic is in the scroll_canvas method:
```python
import tkinter as tk
from PIL import Image, ImageTk


class Canvas(tk.Canvas):
    """ Canvas widget is the only container that can have scroll bar """
    def __init__(self, parent, width, height):
        super().__init__(parent, width=width, height=height)

    def create_background(self, bg_im):
        return self.create_image(0, 0, image=bg_im, anchor=tk.NW)

    def create_button(self, bt_im, width, height, x, y, text):
        a = Button(self, image=bt_im, height=height, width=width,
                   hlt=0, bd=1, text=text)
        # We open a window so buttons can show over the background
        self.create_window(x, y, window=a)

    def scroll_canvas(self, moveto, y, pos=[]):
        """
        Moves the canvas when we scroll, and at the same time moves the
        background image to compensate that movement, so it appears static
        """
        self.yview(moveto, y)
        if pos == []:
            pos.append(0.0)
        pos.append(float(y))
        if len(pos) > 2:
            pos.pop(0)
        # How much we have scrolled. Negative if we scroll up
        dif = pos[1] - pos[0]
        rel_pos = dif * 800  # 800 is the height of the scroll region
        self.move(self.background_image, 0, rel_pos)


class Button(tk.Button):
    def __init__(self, parent, image, height, width, hlt, bd, text):
        super().__init__(parent, image=image, height=height, width=width,
                         highlightthickness=hlt, bd=bd,
                         text=text, compound=tk.CENTER)


def App(root, width, height):
    canvas = Canvas(root, width, height)
    canvas.pack(side=tk.LEFT, expand=tk.YES, fill=tk.BOTH)
    # Every time we move the scroll bar, we call the function
    scroll_y = tk.Scrollbar(root, orient=tk.VERTICAL,
                            command=canvas.scroll_canvas)
    scroll_y.pack(side=tk.RIGHT, fill=tk.Y)
    # Let's define scroll region and remove the default white border
    canvas.config(yscrollcommand=scroll_y.set,
                  scrollregion=(0, 0, width, height+300),
                  bd=0, highlightthickness=0)

    # Loading image for background
    # Tkinter only allows gif images so we use PIL module
    img = Image.open("background.png")
    img = img.resize((width, height), Image.ANTIALIAS)
    bg_im = ImageTk.PhotoImage(img)
    canvas.background_image = canvas.create_background(bg_im)

    # Loading image for buttons
    img = Image.open("button.png")
    img = img.resize((115, 30), Image.ANTIALIAS)
    bt_im = ImageTk.PhotoImage(img)
    # Creating lots of buttons!
    for i in range(14):
        canvas.create_button(bt_im, width=115, height=30,
                             x=width//2, y=i*50 + 100,
                             text=f"Click {i}!")
    root.mainloop()

if __name__ == "__main__":
    root = tk.Tk()
    App(root, 300, 500)

```
