---
layout: single
title: "[ 미니 프로젝트 : Little Dune ]"
---

[ 문제상황 ]

코로나가 한창 유행인 지금, 밖에 나가지 못하고 집안에서 시간을 보내야하는 사람들이 늘어나고 있습니다. 
이러한 사람들이 집안에서 간편하고 가볍게 즐길수있는 게임을 제작해보고싶었습니다.

[ 작업조건 ]
  1. 파이게임 라이브러리을 사용한다
  2. 클래스로 타일들을 만든후 가져와서 맵을 로드한다
  3. 최대한 간단하고 가볍게 제작해야한다.
  4. 타일수와 화면에 크기의 값(설정값)에 따라 게임이 유동적으로 어느 기기에서든지 돌아갈수있어야한다.


⊙ 설계  
  1. 파이게임 라이브러리를 초기화한다.
  2. 화면을 만든다.
  3. 로드할 타일들의 각 클래스를 만들어서 정의한다.
  4. 타일맵의 파일(맵의 배열코드)를 가져와서 클래스에 지정된 타일들을 로드한다.
  5. 플레이어를 화면 중간에 로드한다.
  6. 키 입력을 받는다.
  7. 모든 스프라이트들을 계산처리에 따라 업데이트한다.
  8. 화면에 스프라이트를 그린다.
  9. 6.으로 돌아가서 작업이 끝날때까지 무한 루프한다.

⊙ 구현

[ 타일들 ]

![image](https://user-images.githubusercontent.com/80385994/125545133-93d744c7-6c0f-48a3-96be-73e577f5b00d.png)

[ main.py ]

```python
import pygame
import os
import sys
import csv
sys.path.insert(0,os.path.dirname(os.path.realpath(__file__)))
from sprite import *
from config import *

class Game:
    def __init__(self):
        pygame.init()
        self.screen = pygame.display.set_mode((SCREEN_WIDTH,SCREEN_HEIGHT))
        self.clock = pygame.time.Clock()
        pygame.display.set_caption((ORIGINAL_CAPTION))
        self.fps = FPS
        self.running = True

    def create_map(self):
        file = open(os.path.dirname(os.path.realpath(__file__)) + '//Map.csv')
        reader = csv.reader(file)
        for x,raw in enumerate(reader):
            for y,data in enumerate(raw):
                Dirt00(self,x,y)
                if data == 'D00':
                    Dirt00(self,x,y)
                if data == 'D01':
                    Dirt01(self,x,y)
                if data == 'D02':
                    Dirt02(self,x,y)
                if data == 'D03':
                    Dirt03(self,x,y)  
                if data == 'D04':
                    Dirt04(self,x,y)                 
                if data == 'PPP':
                    Player(self,x,y)
                if data == 'C00':
                    Cloud(self,x,y)
                if data == 'G00':
                    Grass00(self,x,y)
                if data == 'G01':
                    Grass01(self,x,y)
                if data == 'G02':
                    Grass02(self,x,y)
                if data == 'G03':
                    Grass03(self,x,y)
                if data == 'G04':
                    Grass04(self,x,y)
                if data == 'G05':
                    Grass05(self,x,y)
                if data == 'G06':
                    Grass06(self,x,y)
                if data == 'G07':
                    Grass07(self,x,y)
                if data == 'G08':
                    Grass08(self,x,y)
                if data == 'W00':
                    Water00(self,x,y)




    def initialize(self):
        self.all_sprites = pygame.sprite.LayeredUpdates()
        self.walls = pygame.sprite.LayeredUpdates()
        self.running = True
        self.create_map()
        


    def event(self):
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                self.running = False

    def update(self):
        self.all_sprites.update()

    def draw(self):
        self.screen.fill(White)
        self.all_sprites.draw(self.screen)
        self.clock.tick(self.fps)
        pygame.display.update()
        


    def main(self):
        while self.running:
            self.event()
            self.update()
            self.draw()


game = Game()
game.initialize()
while game.running:
    game.main()
```

[ sprite.py ]

```python
import pygame
import os
import sys
sys.path.insert(0,os.path.dirname(os.path.realpath(__file__)))
from sprite import *
from config import *


class Player(pygame.sprite.Sprite):
    def __init__(self,main,x,y):
        self.main = main
        self.tilesize = TILESIZE
        self.width = self.tilesize
        self.height  = self.tilesize
        self.x = self.tilesize*x
        self.y = self.tilesize*y
        self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/player00.png').convert_alpha()
        self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        self.rect = self.image.get_rect()

        self._layer = PLAYER_LAYER
        self.groups = self.main.all_sprites
        pygame.sprite.Sprite.__init__(self,self.groups)
        self.delta_x = 0
        self.delta_y = 0
        self.velocity = VELOCITY
        self.rect.x = SCREEN_WIDTH/2
        self.rect.y = SCREEN_HEIGHT/2

    def update(self):
        self.movement()
        self.rect.x += self.delta_x
        self.rect.y += self.delta_y
        self.collision('x')
        self.collision('y')
        self.delta_x = 0
        self.delta_y = 0

    def movement(self):
        key = pygame.key.get_pressed()
        if key[pygame.K_RIGHT]:
            self.delta_x += self.velocity
            self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/player01.png').convert_alpha()
            self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        if key[pygame.K_LEFT]:
            self.delta_x -= self.velocity
            self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/player03.png').convert_alpha()
            self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        if key[pygame.K_UP]:
            self.delta_y -= self.velocity
            self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/player02.png').convert_alpha()
            self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        if key[pygame.K_DOWN]:
            self.delta_y += self.velocity
            self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/player00.png').convert_alpha()
            self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))

    def collision(self,direction):
        if direction == 'x':
            hits = pygame.sprite.spritecollide(self,self.main.walls,False)
            if hits:
                if self.delta_x >0:
                    self.rect.x -= self.velocity
                if self.delta_x <0:
                    self.rect.x += self.velocity
        if direction == 'y':
            hits = pygame.sprite.spritecollide(self,self.main.walls,False)
            if hits:
                if self.delta_y >0:
                    self.rect.y -= self.velocity
                if self.delta_y <0:
                    self.rect.y += self.velocity


class Dirt00(pygame.sprite.Sprite):
    def __init__(self,main,x,y):
        self.main = main
        self.tilesize = TILESIZE
        self.width = self.tilesize
        self.height  = self.tilesize
        self.x = self.tilesize*x
        self.y = self.tilesize*y
        self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/dirt00.png').convert_alpha()
        self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        self._layer = GROUND_LAYER
        self.groups = self.main.all_sprites
        pygame.sprite.Sprite.__init__(self,self.groups)
        self.rect = self.image.get_rect()
        self.rect.x = self.x
        self.rect.y = self.y

class Dirt01(pygame.sprite.Sprite):
    def __init__(self,main,x,y):
        self.main = main
        self.tilesize = TILESIZE
        self.width = self.tilesize
        self.height  = self.tilesize
        self.x = self.tilesize*x
        self.y = self.tilesize*y
        self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/dirt01.png').convert_alpha()
        self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        self._layer = GROUND_LAYER
        self.groups = self.main.all_sprites
        pygame.sprite.Sprite.__init__(self,self.groups)
        self.rect = self.image.get_rect()
        self.rect.x = self.x
        self.rect.y = self.y

class Dirt02(pygame.sprite.Sprite):
    def __init__(self,main,x,y):
        self.main = main
        self.tilesize = TILESIZE
        self.width = self.tilesize
        self.height  = self.tilesize
        self.x = self.tilesize*x
        self.y = self.tilesize*y
        self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/dirt02.png').convert_alpha()
        self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        self._layer = GROUND_LAYER
        self.groups = self.main.all_sprites
        pygame.sprite.Sprite.__init__(self,self.groups)
        self.rect = self.image.get_rect()
        self.rect.x = self.x
        self.rect.y = self.y

class Dirt03(pygame.sprite.Sprite):
    def __init__(self,main,x,y):
        self.main = main
        self.tilesize = TILESIZE
        self.width = self.tilesize
        self.height  = self.tilesize
        self.x = self.tilesize*x
        self.y = self.tilesize*y
        self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/dirt03.png').convert_alpha()
        self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        self._layer = GROUND_LAYER
        self.groups = self.main.all_sprites
        pygame.sprite.Sprite.__init__(self,self.groups)
        self.rect = self.image.get_rect()
        self.rect.x = self.x
        self.rect.y = self.y

class Dirt04(pygame.sprite.Sprite):
    def __init__(self,main,x,y):
        self.main = main
        self.tilesize = TILESIZE
        self.width = self.tilesize
        self.height  = self.tilesize
        self.x = self.tilesize*x
        self.y = self.tilesize*y
        self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/dirt04.png').convert_alpha()
        self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        self._layer = GROUND_LAYER
        self.groups = self.main.all_sprites
        pygame.sprite.Sprite.__init__(self,self.groups)
        self.rect = self.image.get_rect()
        self.rect.x = self.x
        self.rect.y = self.y

class Grass00(pygame.sprite.Sprite):
    def __init__(self,main,x,y):
        self.main = main
        self.tilesize = TILESIZE
        self.width = self.tilesize
        self.height  = self.tilesize
        self.x = self.tilesize*x
        self.y = self.tilesize*y
        self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/grass00.png').convert_alpha()
        self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        self._layer = GROUND_LAYER
        self.groups = self.main.all_sprites
        pygame.sprite.Sprite.__init__(self,self.groups)
        self.rect = self.image.get_rect()
        self.rect.x = self.x
        self.rect.y = self.y

class Grass01(pygame.sprite.Sprite):
    def __init__(self,main,x,y):
        self.main = main
        self.tilesize = TILESIZE
        self.width = self.tilesize
        self.height  = self.tilesize
        self.x = self.tilesize*x
        self.y = self.tilesize*y
        self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/grass01.png').convert_alpha()
        self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        self._layer = GROUND_LAYER
        self.groups = self.main.all_sprites
        pygame.sprite.Sprite.__init__(self,self.groups)
        self.rect = self.image.get_rect()
        self.rect.x = self.x
        self.rect.y = self.y

class Grass02(pygame.sprite.Sprite):
    def __init__(self,main,x,y):
        self.main = main
        self.tilesize = TILESIZE
        self.width = self.tilesize
        self.height  = self.tilesize
        self.x = self.tilesize*x
        self.y = self.tilesize*y
        self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/grass02.png').convert_alpha()
        self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        self._layer = GROUND_LAYER
        self.groups = self.main.all_sprites
        pygame.sprite.Sprite.__init__(self,self.groups)
        self.rect = self.image.get_rect()
        self.rect.x = self.x
        self.rect.y = self.y

class Grass03(pygame.sprite.Sprite):
    def __init__(self,main,x,y):
        self.main = main
        self.tilesize = TILESIZE
        self.width = self.tilesize
        self.height  = self.tilesize
        self.x = self.tilesize*x
        self.y = self.tilesize*y
        self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/grass03.png').convert_alpha()
        self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        self._layer = GROUND_LAYER
        self.groups = self.main.all_sprites
        pygame.sprite.Sprite.__init__(self,self.groups)
        self.rect = self.image.get_rect()
        self.rect.x = self.x
        self.rect.y = self.y

class Grass04(pygame.sprite.Sprite):
    def __init__(self,main,x,y):
        self.main = main
        self.tilesize = TILESIZE
        self.width = self.tilesize
        self.height  = self.tilesize
        self.x = self.tilesize*x
        self.y = self.tilesize*y
        self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/grass04.png').convert_alpha()
        self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        self._layer = GROUND_LAYER
        self.groups = self.main.all_sprites
        pygame.sprite.Sprite.__init__(self,self.groups)
        self.rect = self.image.get_rect()
        self.rect.x = self.x
        self.rect.y = self.y

class Grass05(pygame.sprite.Sprite):
    def __init__(self,main,x,y):
        self.main = main
        self.tilesize = TILESIZE
        self.width = self.tilesize
        self.height  = self.tilesize
        self.x = self.tilesize*x
        self.y = self.tilesize*y
        self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/grass05.png').convert_alpha()
        self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        self._layer = GROUND_LAYER
        self.groups = self.main.all_sprites
        pygame.sprite.Sprite.__init__(self,self.groups)
        self.rect = self.image.get_rect()
        self.rect.x = self.x
        self.rect.y = self.y

class Grass06(pygame.sprite.Sprite):
    def __init__(self,main,x,y):
        self.main = main
        self.tilesize = TILESIZE
        self.width = self.tilesize
        self.height  = self.tilesize
        self.x = self.tilesize*x
        self.y = self.tilesize*y
        self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/grass06.png').convert_alpha()
        self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        self._layer = GROUND_LAYER
        self.groups = self.main.all_sprites
        pygame.sprite.Sprite.__init__(self,self.groups)
        self.rect = self.image.get_rect()
        self.rect.x = self.x
        self.rect.y = self.y

class Grass07(pygame.sprite.Sprite):
    def __init__(self,main,x,y):
        self.main = main
        self.tilesize = TILESIZE
        self.width = self.tilesize
        self.height  = self.tilesize
        self.x = self.tilesize*x
        self.y = self.tilesize*y
        self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/grass07.png').convert_alpha()
        self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        self._layer = GROUND_LAYER
        self.groups = self.main.all_sprites
        pygame.sprite.Sprite.__init__(self,self.groups)
        self.rect = self.image.get_rect()
        self.rect.x = self.x
        self.rect.y = self.y

class Grass08(pygame.sprite.Sprite):
    def __init__(self,main,x,y):
        self.main = main
        self.tilesize = TILESIZE
        self.width = self.tilesize
        self.height  = self.tilesize
        self.x = self.tilesize*x
        self.y = self.tilesize*y
        self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/grass08.png').convert_alpha()
        self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        self._layer = GROUND_LAYER
        self.groups = self.main.all_sprites
        pygame.sprite.Sprite.__init__(self,self.groups)
        self.rect = self.image.get_rect()
        self.rect.x = self.x
        self.rect.y = self.y

class Cloud(pygame.sprite.Sprite):
    def __init__(self,main,x,y):
        self.main = main
        self.tilesize = TILESIZE
        self.width = self.tilesize
        self.height  = self.tilesize
        self.x = self.tilesize*x
        self.y = self.tilesize*y
        self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/cloud.png').convert_alpha()
        self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        self._layer = CLOUD_LAYER
        self.groups = self.main.all_sprites
        pygame.sprite.Sprite.__init__(self,self.groups)
        self.rect = self.image.get_rect()
        self.rect.x = self.x
        self.rect.y = self.y

class Water00(pygame.sprite.Sprite):
    def __init__(self,main,x,y):
        self.main = main
        self.tilesize = TILESIZE
        self.width = self.tilesize
        self.height  = self.tilesize
        self.x = self.tilesize*x
        self.y = self.tilesize*y
        self.image = pygame.image.load(os.path.dirname(os.path.realpath(__file__)) + '/img/water00.png').convert_alpha()
        self.image = pygame.transform.scale(self.image,(TILESIZE,TILESIZE))
        self._layer = GROUND_LAYER
        self.groups = self.main.all_sprites, self.main.walls
        pygame.sprite.Sprite.__init__(self,self.groups)
        self.rect = self.image.get_rect()
        self.rect.x = self.x
        self.rect.y = self.y
      
```

[ config.py ]

```python

SCREEN_WIDTH = 640
SCREEN_HEIGHT = 640
ORIGINAL_CAPTION = 'Little Dune'
TILESIZE = 64
PLAYER_LAYER = 2
GROUND_LAYER = 1
CLOUD_LAYER = 3
VELOCITY = 3
White = (255,255,255)
Black = (0,0,0)
Brown = (150,75,0)
Green = (0,255,0)
Blue = (0,0,255)
FPS = 60
EMPTY_GREEN = (0, 255, 7)

```

[ Map.csv ]

```

W00,W00,W00,W00,W00,W00,W00,W00,W00,W00
W00,G05,G00,G00,G00,G00,G00,G00,G06,W00
W00,G00,D02,D00,D00,D00,D00,D03,G00,W00
W00,G00,D00,D00,D00,D00,D00,D00,G00,W00
W00,G00,D00,D00,D00,D00,D00,D00,G00,W00
W00,G00,D00,D00,D00,D00,D00,D04,G00,W00
W00,G00,D00,D00,D00,G04,G00,G00,G00,W00
W00,G00,D01,D00,D04,G00,G00,G00,G00,W00
W00,G08,G00,G00,G00,G00,G00,G00,G07,W00
W00,W00,W00,W00,W00,W00,W00,W00,W00,W00
PPP

```

⊙ 테스트

[ 창&배경 테스트 ]

![image](https://user-images.githubusercontent.com/80385994/125545488-c3f03e2d-5f56-4ecc-8c14-0b685c6be91f.png)


⊙ 실행결과

[ 플레이어 움직이기 ]

![image](https://user-images.githubusercontent.com/80385994/125545036-fd5282de-03aa-410f-82f7-73afeab8f390.png)

![image](https://user-images.githubusercontent.com/80385994/125545054-a8f3425b-022b-4a3a-bf86-556703476b02.png)

![image](https://user-images.githubusercontent.com/80385994/125545064-099e8a7b-0aef-441a-8647-cbb200c9d97d.png)

[ 바다타일과 충돌과 움직임 멈춤 ]

![image](https://user-images.githubusercontent.com/80385994/125545668-35a7d955-7656-4d3b-a984-ed3e22238d18.png)

![image](https://user-images.githubusercontent.com/80385994/125545640-53b4a68f-5fd0-41fb-8f73-bc94056a735b.png)

![image](https://user-images.githubusercontent.com/80385994/125545653-4be9ef05-86e5-421c-a341-2b792e0ca8cf.png)
