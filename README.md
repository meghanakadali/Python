# Python
# Snake Game
import pygame
import sys
import random
import time
from os import path

pygame.init()
white = (255, 255, 255)
green = (0, 155, 0)
BRIGHTBLUE = (  0,  50, 255)
BROWN      = (128,  0,   0)
black = (0, 0, 0)
red = (25, 25, 112)
clock = pygame.time.Clock()
display_width = 600
display_height = 400
gameDisplay = pygame.display.set_mode((display_width,display_height))
smallfont = pygame.font.SysFont("comicsansms", 30)
medfont = pygame.font.SysFont("Helvetica", 50)
largefont = pygame.font.SysFont("Helvetica", 80)
def text_objects(text, color, size):
	if size == "small":
		textSurface = smallfont.render(text, True, color)
	elif size == "medium":
		textSurface = medfont.render(text, True, color)
	elif size == "large":
		textSurface = largefont.render(text, True, color)
	return textSurface, textSurface.get_rect()
def message_to_screen(msg, color, y_displace=0, size="small"):
	textSurf, textRect = text_objects(msg, color, size)
	textRect.center = (display_width/2), (display_height/2)+y_displace
	gameDisplay.blit(textSurf, textRect)
	
def game_intro():
	intro = True
	while intro:
		for event in pygame.event.get():
			if event.type == pygame.QUIT:
				pygame.quit()
				quit()
			if event.type == pygame.KEYDOWN:
				if event.key == pygame.K_p:
					intro = False
				if event.key == pygame.K_q:
					pygame.quit()
					quit()
		size = width, height = 1000, 667
		screen = pygame.display.set_mode(size)
		bg = pygame.image.load("snakey.jpg")
		screen.blit(bg, (0, 0))
		message_to_screen("                       Snake Game", red, -160, "large")
		message_to_screen("                                            The objective of the game is to eat red apples", black, -60)
		message_to_screen("                                            The more apples you eat, the longer you get", black, -20)
		message_to_screen("                                            If you run into yourself, or the edges, you die!", black, 20)
		message_to_screen("                                                              Press p to play and q to quit                  ", black, 110)
		pygame.display.update()
		clock.tick(15)
def pause():
	paused = True
	while paused:
		#gameDisplay.fill(white)
		size = width, height = 1000, 667
		screen = pygame.display.set_mode(size)
		bg = pygame.image.load("kim.jpg")
		screen.blit(bg, (0, 0))
		message_to_screen("                     Game Paused" ,BRIGHTBLUE, -150, "large")
		message_to_screen("                                   To continue press y" ,black, -90, "medium")
		message_to_screen("                                   To quit press q" ,black, -40, "medium")
		pygame.display.update()
		for event in pygame.event.get():
				if event.type == pygame.QUIT:
					pygame.quit()
					quit()
				if event.type == pygame.KEYDOWN:
					if event.key == pygame.K_q:
						pygame.quit()
						quit()
					elif event.key == pygame.K_y:
						paused = False
def gameOver():
	go = True
	while go:
		for event in pygame.event.get():
			if event.type == pygame.QUIT:
				pygame.quit()
				quit()
			if event.type == pygame.KEYDOWN:
				if event.key == pygame.K_p:
					go = False
				if event.key == pygame.K_q:
					print("The final score is: " + str(score))
					pygame.quit()
					quit()
		#gameDisplay.fill(white)
		size = width, height = 1000, 667
		screen = pygame.display.set_mode(size)
		bg = pygame.image.load("end.jpg")
		screen.blit(bg, (0, 0))
		message_to_screen("                        Game Over", BROWN, -160, "large")
		message_to_screen("Your Score:                       ", black, -70, "medium")
		message_to_screen(str(score), black, -70, "medium")
		
		pygame.display.update()
		clock.tick(15)
class Snake():
	def __init__(self):
		self.position = [100,50]
		self.body = [[100,50],[90,50],[80,50]]
		self.direction = "RIGHT"
		self.changeDirectionTo = self.direction
	def changeDirTo(self,dir):
		if dir=="RIGHT" and not self.direction=="LEFT":
			self.direction = "RIGHT"
		if dir=="LEFT" and not self.direction=="RIGHT":
			self.direction = "LEFT"
		if dir=="UP" and not self.direction=="DOWN":
			self.direction = "UP"
		if dir=="DOWN" and not self.direction=="UP":
			self.direction = "DOWN"
	def move(self,foodPos):
		if self.direction == "RIGHT":
			self.position[0] += 10
		if self.direction == "LEFT":
			self.position[0] -= 10
		if self.direction == "UP":
			self.position[1] -= 10
		if self.direction == "DOWN":
			self.position[1] += 10
		self.body.insert(0,list(self.position))
		if self.position == foodPos:
			return 1
		else:
			self.body.pop()
			return 0

	def checkCollision(self):
		if self.position[0] > 1000 or self.position[0]< 0:
			soundObj1.play()
			time.sleep(0.5)
			soundObj1.stop()
			return 1
		elif self.position[1] > 1000 or self.position[1]<0:
			soundObj1.play()
			time.sleep(0.5)
			soundObj1.stop()
			return 1
		for bodyPart in self.body[1:]:
			if self.position[0] == bodyPart:
				soundObj1.play()
				time.sleep(0.5)
				soundObj1.stop()
				return 1
		return 0
	def getHeadPos(self):
		return self.position
	def getBody(self):
		return self.body
class FoodSpawer():
	def __init__(self):
		self.position = [random.randrange(1,40)*10,random.randrange(1,40)*10]
		self.isFoodOnScreen = True

	def spawnFood(self):
		if self.isFoodOnScreen == False:
			self.position = [random.randrange(1,40)*10,random.randrange(1,40)*10]
			self.isFoodOnScreen = True
		return self.position
	def setFoodOnScreen(self,b):
		self.isFoodOnScreen = b
window = pygame.display.set_mode((1000,600))
pygame.display.set_caption("wow snake")
pygame.mixer.pre_init(44100, 16, 2, 4096) #frequency, size, channels, buffersize
pygame.init()
soundObj = pygame.mixer.Sound('sound.wav')
soundObj1 = pygame.mixer.Sound('sound2.wav')
fps = pygame.time.Clock()
game_intro()
snake = Snake()
foodSpawner = FoodSpawer()
score = 0

while True:
	for event in pygame.event.get():
		if event.type == pygame.QUIT:
			gameOver();
		elif event.type == pygame.KEYDOWN:
			if event.key ==pygame.K_RIGHT:
				snake.changeDirTo('RIGHT')
			if event.key ==pygame.K_UP:
				snake.changeDirTo('UP')	
			if event.key ==pygame.K_DOWN:
				snake.changeDirTo('DOWN')
			if event.key ==pygame.K_LEFT:
				snake.changeDirTo('LEFT')
			if event.key == pygame.K_p:
					pause()
	foodPos = foodSpawner.spawnFood()
	if(snake.move(foodPos)==1):
		score+=1
		soundObj.play()
		time.sleep(0.5)
		soundObj.stop()
		foodSpawner.setFoodOnScreen(False)
	size = width, height = 1000, 667
	screen = pygame.display.set_mode(size)
	bg = pygame.image.load("simple.jpg")
	screen.blit(bg, (0, 0))
	message_to_screen("                                                Press p to pause", white, 440)
	#window.fill(pygame.Color(0,0,0))
	for pos in snake.getBody():
		pygame.draw.rect(window,pygame.Color(69,139,0),pygame.Rect(pos[0],pos[1],10,10))
	pygame.draw.rect(window,pygame.Color(255,0,0),pygame.Rect(foodPos[0],foodPos[1],10,10))
	if(snake.checkCollision()==1):
		gameOver()
	pygame.display.set_caption("wow Snake | Score : "+str(score))
	pygame.display.flip()
	fps.tick(24)
