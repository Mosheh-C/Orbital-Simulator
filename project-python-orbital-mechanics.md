# Date: July 23 2026                            Project: Python Orbital Mechanics Simulator (v1.0)
                                                                  Starting Script, use AI to create an exoskeleton for program

Date: July 23, 2026
Notes: import pygame
import math

pygame.init()
screen = pygame.display.set_mode((800, 800))
clock = pygame.time.Clock()

px, py = 400, 400
rx, ry = 400, 200
vel_x, vel_y = 0, -5

def gravity_force(px, py, rx, ry, mass):
dx = px - rx
dy = py - ry
distance = math.sqrt(dxdx + dydy)
angle = math.atan2(dy, dx)
force = mass / (distance**2)
return force * math.cos(angle), force * math.sin(angle)

running = True
while running:
for event in pygame.event.get():
if event.type == pygame.QUIT:
running = False

g_x, g_y = gravity_force(px, py, rx, ry, mass=5000)

vel_x += g_x
vel_y += g_y

rx += vel_x
ry += vel_y

screen.fill((0, 0, 0))
pygame.draw.circle(screen, (0, 100, 255), (px, py), 20)
pygame.draw.circle(screen, (255, 255, 255), (int(rx), int(ry)), 5)

pygame.display.update()
clock.tick(60)

pygame.quit()







Phase: Physics Engine