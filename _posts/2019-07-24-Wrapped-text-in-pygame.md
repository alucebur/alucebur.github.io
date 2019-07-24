---
layout: post
title: 'Wrapped text in Pygame'
description: 'Playing with pygame'
date: '2019-07-24 16:13:50 +0200'
categories: []
tags: [pygame, python]
---

Hello again. Lately I am learning to use **pygame**, the set of Python modules to create videogames. I had a JSON file with long quotes
that I wanted to show in the gameover screen of my game, but they didn't fit on the screen, so I started looking for a solution.
Searching on the internet I found [this code](https://stackoverflow.com/questions/49432109/how-to-wrap-text-in-pygame-using-pygame-font-font#49433498)
from **SpoonMeiser** in **StackOverflow** and I adapted it to my needs.

The function `render_wrapped_text` divides the text into lines that fit the specified `max_width`, render them on
a transparent surface of said width, and then returns that surface, along with the rectangle defining that surface area, so
we can place the text where we want to later.

In this way, we can use `functools.lru_cache(x)` to cache the last x returning values, so the text is rendered just once,
then the cache returns the values instead. This LRU cache uses the arguments passed to the function as key, so they
**need to be hashable**.

The result of calling the function with `centered=False`:

![Not centered text](https://i.imgur.com/PLasJh7.png)

With `centered=True` we get this:

![Centered text](https://i.imgur.com/2eKQNCy.png)

And the code I am using:

```python
from functools import lru_cache
from typing import Tuple, NewType
import pygame
import pygame.freetype

WIDTH, HEIGHT = 800, 600
# pygame.Color() is not hashable
Color = NewType('Color', Tuple[int, int, int])
WHITE = Color((200, 200, 200))
DARK_GREY = Color((40, 40, 40))


@lru_cache(maxsize=32)
def render_wrapped_text(text: str, font: pygame.freetype.Font,
                        color: Color, centered: bool, offset_y: int,
                        max_width: int) -> Tuple[pygame.Surface, pygame.Rect]:
    """Returns a surface with text split over several lines rendered on it
    and a sized rectangle. Offset-y defines the distance between lines."""
    words = text.split()
    lines = []
    lines_h = 0

    # Separate text into lines, storing each line size
    while words:
        line_words = []
        while words:
            line_words.append(words.pop(0))
            _, _, l_w, l_h = font.get_rect(
                ' '.join(line_words + words[:1]))
            if l_w > max_width:
                break
            line_w, line_h = l_w, l_h
        lines_h += line_h
        lines.append((' '.join(line_words), (line_w, line_h)))

    # Create transparent surface and rectangle to be returned
    final_height = lines_h + (len(lines) - 1) * offset_y if lines else lines_h
    final_surf = pygame.Surface((max_width, final_height), pygame.SRCALPHA, 32)
    final_rect = final_surf.get_rect()

    # Render lines on the surface
    pos_y = 0
    for line in lines:
        if centered:
            pos_x = int(max_width/2 - line[1][0]/2)
        else:
            pos_x = 0
        font.render_to(final_surf, (pos_x, pos_y), line[0], color)
        pos_y += line[1][1] + offset_y
    return final_surf, final_rect


def run_example(width: int, height: int, fps: int):
    """Draw on the screen a long text, centered, with 10 pixels of
    separation between lines."""
    pygame.init()
    screen = pygame.display.set_mode((width, height))
    clock = pygame.time.Clock()
    font = pygame.freetype.Font(None, 20)
    long_text = ("Lorem ipsum dolor sit amet, consectetur adipiscing elit. "
                 "Integer molestie, mauris ut bibendum rhoncus, lorem libero "
                 "molestie arcu, sit amet finibus sapien enim sed nunc. Cras "
                 "dapibus, eros ac vulputate convallis, dui est venenatis ex, "
                 "a ultricies urna justo at lectus. Sed aliquet orci at urna "
                 "iaculis cursus ac sit amet lectus. Suspendisse sodales "
                 "dignissim felis. Nulla ut ante blandit, aliquam odio quis, "
                 "cursus neque. In dapibus nisi sem, eu elementum risus "
                 "facilisis et. Praesent pulvinar ante arcu, et mattis lorem "
                 "posuere vel.")
    running = True

    while running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False

        screen.fill(DARK_GREY)
        rd_text, rd_rect = render_wrapped_text(long_text, font, WHITE, True,
                                               10, width-150)
        rd_rect.centerx, rd_rect.centery = width//2, height//2
        screen.blit(rd_text, rd_rect)

        pygame.display.flip()
        clock.tick(fps)
        print(render_wrapped_text.cache_info())

if __name__ == "__main__":
    run_example(800, 640, 60)
    pygame.quit()

```
