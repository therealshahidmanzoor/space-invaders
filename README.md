# space-invaders
Here is the source code for space invaders game using python pygame 
from os import rename
import pygame
from pygame import key
from pygame.constants import QUIT
import random
from math import *
from pygame import mixer
pygame.init()

# add game windo
screen = pygame.display.set_mode((800, 600))

# add title to the game window:
pygame.display.set_caption("Space invaders by Shahid Manzoor")

# add logo to the game window:
icon = pygame.image.load("python\Python Pygame\Logo.png")
pygame.display.set_icon(icon)

#add background image:
bg=pygame.image.load("python\Python Pygame\Bg.png")

#bg sound
mixer.music.load("python\Python Pygame\Background.wav")

#playing sound on loop
mixer.music.play(-1)
# add players:-
playerImg = pygame.image.load("python\Python Pygame\Logo2.png")

# coordinates of a player
playerX = 370
playerY = 480
x_inc = 0
y_inc = 0

# function for drawing player on game window
def player(x, y):
    screen.blit(playerImg, (x, y))

#add enemy image:
num_of_enemy=5
enemyImg =[]
enemyX=[]
enemyY=[]
enemy_xinc=[]
enemy_yinc=[]
for i in range(num_of_enemy):
    enemyImg.append(pygame.image.load("python\Python Pygame\enemy.png"))
    enemyX.append(random.randint(0,735))
    enemyY.append(random.randint(50,150))
    enemy_xinc.append(4)
    enemy_yinc.append(40)

# function for drawing enemy on game window
def Enemy(x,y,i):
    screen.blit(enemyImg[i], (x,y))

#bullet add
bullet=pygame.image.load("python\Python Pygame\Bullet.png")
bulletX=0
bulletY=480
bulletXchange=0
bulletYchange=40

#state of bullet 
#ready:can't see bullet
#fire:bullet is fired
bulletstate="ready"
def firebullet(x,y):
    global bulletstate
    bulletstate="fire"
    screen.blit(bullet,(x+16,y+10))

def collision(enemyx,enemyy,bulletx,bullety):
    distance=sqrt(pow((enemyx-bulletx),2)+pow((enemyy-bullety),2))
    if distance <27:
        return True
    else:
        return False
#score
score_val=0
font=pygame.font.Font("freesansbold.ttf",32)
textX=10
textY=10

#gameover

def showscore(x,y):
    score=font.render("score:"+str(score_val),True,(255,255,255))
    screen.blit(score,(x,y))

over_font=pygame.font.Font("freesansbold.ttf",64)
def game_over(x,y):
    over_text=over_font.render("GAME OVER",True,(255,255,255))
    text=font.render("Game By Shahid Manzoor",True,(255,255,255))
    screen.blit(over_text,(x,y))
    screen.blit(text,(200,350))
# game loop
run = True
while run:
    # add color to the game window:
    screen.fill((0, 0, 0))

    #background change
    screen.blit(bg,(0,0))

# checking quit window condition:
    for event in pygame.event.get():
        if event.type == QUIT:
            run = False

    # key movement of objects in game window:
        if event.type == pygame.KEYDOWN:
           # print("key pressed--other")
            if event.key == pygame.K_LEFT:
                x_inc -= 10
            if event.key == pygame.K_RIGHT:
                x_inc += 10
            
            #spacebar fires bullet
            if event.key==pygame.K_SPACE:
                if bulletstate is "ready":
                  bullet_sound= mixer.Sound("python\Python Pygame\laser.wav")
                  bullet_sound.play()
                #current x coordinate of spaceship
                  bulletX=playerX
                  firebullet(bulletX,bulletY)
        if event.type == pygame.KEYUP:
            #print("key released--other")
            if event.key == pygame.K_LEFT:
                x_inc = 0
            if event.key == pygame.K_RIGHT:
                x_inc = 0
            if event.key == pygame.K_UP:
                y_inc = 0
            if event.key == pygame.K_DOWN:
                y_inc = 0

    playerX += x_inc
    playerY += y_inc
    if playerX <= 0:
        playerX = 0
    if playerX >= 736:
        playerX = 736
    if playerY <= 0:
        playerY = 0
    if playerY >= 536:
        playerY = 536

#enemy movement:
    for i in range(num_of_enemy):

        #game over:
        if enemyY[i]>440:
            for j in range(num_of_enemy):
                enemyY[j]=2000
            game_over(200,250)
            break

        #enemy add:
        enemyX[i] += enemy_xinc[i]
        if enemyX[i] <= 0:
            enemy_xinc[i] = 4
            enemyY[i]+=enemy_yinc[i]
        elif enemyX[i]>= 736:
            enemy_xinc[i] =-4
            enemyY[i]+=enemy_yinc[i]
        Enemy(enemyX[i],enemyY[i],i)

        #collision
        coll=collision(enemyX[i],enemyY[i],bulletX,bulletY)
        if coll:

            #collision sound:
            explosion_sound=mixer.Sound("python\Python Pygame\laser.wav")
            explosion_sound.play()

            bulletY=480
            bulletstate="ready"

            #incr score by 1
            score_val+=1

            enemyX[i]=random.randint(0,735)
            enemyY[i]=random.randint(50,150)
        
#bullet movement
    if bulletY<=0:
        bulletY=480
        bulletstate="ready"

    if bulletstate is "fire":
        firebullet(bulletX,bulletY)
        bulletY-=bulletYchange

# adding player(by calling function)
    player(playerX, playerY)
    
#score
    showscore(textX,textY)

# updating game window with colors added:
    pygame.display.update()
