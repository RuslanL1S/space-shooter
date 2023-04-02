
#Создай собственный Шутер!
from pygame import *
from random import randint

mixer.init()
mixer.music.load('space.ogg')
mixer.music.play()
fire_sound=mixer.Sound('fire.ogg')

font.init()
font1 = font.Font(None,80)
win=font1.render('YOU WIN!',True,(255,255,255))
lose=font1.render('YOU LOSE!',True,(180,0,0))
font2 = font.Font(None,36)


img_back = 'galaxy.jpg'
img_hero = 'rocket.png'
img_enemy = 'ufo.png'
img_bullet='bullet.png'

score=0
goal=10
lost=0
max_lost=3

#класс-родитель для спрайтов
class GameSprite(sprite.Sprite):
   #конструктор класса
   def __init__(self, player_image, player_x, player_y,size_x, size_y, player_speed):
       sprite.Sprite.__init__(self)
       # каждый спрайт должен хранить свойство image - изображение
       self.image = transform.scale(image.load(player_image), (size_x, size_y))
       self.speed = player_speed
       # каждый спрайт должен хранить свойство rect - прямоугольник, в который он вписан
       self.rect = self.image.get_rect()
       self.rect.x = player_x
       self.rect.y = player_y


   def reset(self):
       window.blit(self.image, (self.rect.x, self.rect.y))

    
#класс-наследник для спрайта-игрока (управляется стрелками)
class Player(GameSprite):
    def update(self):
        keys = key.get_pressed()
        if keys[K_LEFT] and self.rect.x > 5:
            self.rect.x -= self.speed
        if keys[K_RIGHT] and self.rect.x < win_width - 80:
            self.rect.x += self.speed
    def fire(self):
        bullet = Bullet(img_bullet, self.rect.centerx, self.rect.top, 15, 20, -15)
        bullets.add(bullet)

class Enemy(GameSprite):
    def update(self):
        self.rect.y += self.speed
        global lost
        if self.rect.y > win_height:
            self.rect.x =randint(80,win_width - 80)
            self.rect.y = 0
            lost=lost + 1

class Bullet(GameSprite):
    def update(self):
        self.rect.y +=self.speed
        if self.rect.y < 0:
            self.kill()




#Игровая сцена:
win_width = 700
win_height = 500
window = display.set_mode((win_width, win_height))
display.set_caption("Maze")
background = transform.scale(image.load("galaxy.jpg"), (win_width, win_height))

ship = Player('rocket.png', 5, win_height - 80,50,50, 40)

monsters = sprite.Group()
for i in range(1,6):
    monster = Enemy(img_enemy,randint(80,win_width - 80),-40,80,50,randint(1,5))
    monsters.add(monster)

bullets=sprite.Group()

finish=False

run=True

while run:
    for e in event.get():
        if e.type == QUIT:
            run=False
        
        elif e.type ==KEYDOWN:
            if e.key == K_SPACE:
                fire_sound.play()
                ship.fire()
    
    if not finish:
        window.blit(background,(0,0))

        text = font2.render("Счёт: " + str(score), 1, (255, 255, 255))
        window.blit(text, (10, 20))

        text_lose = font2.render("Пропущено: " + str(lost), 1, (255, 255, 255))
        window.blit(text_lose, (10, 50))

        ship.update()
        monsters.update()
        bullets.update()

        ship.reset()
        monsters.draw(window)
        bullets.draw(window)

        collides =sprite.groupcollide(monsters,bullets,True,True)
        for c in collides:
            score=score+1
            monster=Enemy(img_enemy,randint(80,win_width - 80),-40,80,50,randint(1,5))
            monsters.add(monster)
        
        if sprite.spritecollide(ship,monsters,False) or lost >= max_lost:
            finish=True
            window.blit(lose,(200,200))
        
        if score >= goal:
            finish=True
            window.blit(win,(200,200))
        
        text = font2.render("Счёт: " + str(score), 1, (255, 255, 255))
        window.blit(text, (10, 20))

        text_lose = font2.render("Пропущено: " + str(lost), 1, (255, 255, 255))
        window.blit(text_lose, (10, 50))

        display.update()
    
    else:
        finish=False
        score=0
        lost=0
        for b in bullets:
            b.kill()
        for m in monsters:
            m.kill()
        
        time.delay(3000)
        for i in range(1,6):
            monster=Enemy(img_enemy,randint(80,win_width - 80),-40,80,50, randint(1,6))
            monsters.add(monster)

    time.delay(50)

_______________________________________________________________________________________________________________________________________________________
                       
Данное приложение очень интересное и увлекательное 
В нем довольно сложно работать
И здесь я хочу описать данный код


from pygame import *
from random import randint
подключаем библиотеки


mixer.init()
добовляет музыку и включает ее


font.init()
добовляет шрифты, и показывает определенную надпись при проигрыше и выигрыше



img_back = 'galaxy.jpg'
img_hero = 'rocket.png'
img_enemy = 'ufo.png'
img_bullet='bullet.png'
добовляем картинки врага,игрока,пули,фона



class GameSprite(sprite.Sprite):
   #конструктор класса
   def __init__(self, player_image, player_x, player_y,size_x, size_y, player_speed):
       sprite.Sprite.__init__(self)
       # каждый спрайт должен хранить свойство image - изображение
       self.image = transform.scale(image.load(player_image), (size_x, size_y))
       self.speed = player_speed
       # каждый спрайт должен хранить свойство rect - прямоугольник, в который он вписан
       self.rect = self.image.get_rect()
       self.rect.x = player_x
       self.rect.y = player_y


   def reset(self):
       window.blit(self.image, (self.rect.x, self.rect.y))
 
самый главный спрайт в коде от которого будут отталкиваться другие классы
в нем лежит скорость,размер,расположение по x,y игрока



class Player(GameSprite):
    def update(self):
        keys = key.get_pressed()
        if keys[K_LEFT] and self.rect.x > 5:
            self.rect.x -= self.speed
        if keys[K_RIGHT] and self.rect.x < win_width - 80:
            self.rect.x += self.speed
    def fire(self):
        bullet = Bullet(img_bullet, self.rect.centerx, self.rect.top, 15, 20, -15)
        bullets.add(bullet)
        
 класс игрока где описано движение в права и влево и описана стрельба
 
 
 
 class Enemy(GameSprite):
    def update(self):
        self.rect.y += self.speed
        global lost
        if self.rect.y > win_height:
            self.rect.x =randint(80,win_width - 80)
            self.rect.y = 0
            lost=lost + 1
                        
класс врага где описана его скорость и появление в разных местах


class Bullet(GameSprite):
    def update(self):
        self.rect.y +=self.speed
        if self.rect.y < 0:
            self.kill()
            
класс пули где написано о том как она будет убивать врага


#Игровая сцена:
win_width = 700
win_height = 500
window = display.set_mode((win_width, win_height))
display.set_caption("Maze")
background = transform.scale(image.load("galaxy.jpg"), (win_width, win_height))




 



































































