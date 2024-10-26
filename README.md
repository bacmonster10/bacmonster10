import pygame
import random

# Initialize Pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Space Invaders")

# Colors
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
GREEN = (0, 255, 0)
RED = (255, 0, 0)

# Player settings
player_width = 50
player_height = 30
player_speed = 5

# Bullet settings
bullet_width = 5
bullet_height = 10
bullet_speed = 7

# Enemy settings
enemy_width = 50
enemy_height = 30
enemy_speed = 2

# Classes
class Player:
    def __init__(self):
        self.rect = pygame.Rect(WIDTH // 2, HEIGHT - player_height - 10, player_width, player_height)

    def move(self, dx):
        self.rect.x += dx
        if self.rect.x < 0:
            self.rect.x = 0
        elif self.rect.x > WIDTH - player_width:
            self.rect.x = WIDTH - player_width

    def draw(self):
        pygame.draw.rect(screen, GREEN, self.rect)

class Bullet:
    def __init__(self, x, y):
        self.rect = pygame.Rect(x + player_width // 2 - bullet_width // 2, y, bullet_width, bullet_height)

    def move(self):
        self.rect.y -= bullet_speed

    def draw(self):
        pygame.draw.rect(screen, WHITE, self.rect)

class Enemy:
    def __init__(self, x, y):
        self.rect = pygame.Rect(x, y, enemy_width, enemy_height)

    def move(self):
        self.rect.y += enemy_speed

    def draw(self):
        pygame.draw.rect(screen, RED, self.rect)

# Game loop
def main():
    clock = pygame.time.Clock()
    player = Player()
    bullets = []
    enemies = [Enemy(random.randint(0, WIDTH - enemy_width), random.randint(-150, -50)) for _ in range(5)]
    running = True

    while running:
        screen.fill(BLACK)

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False

        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT]:
            player.move(-player_speed)
        if keys[pygame.K_RIGHT]:
            player.move(player_speed)
        if keys[pygame.K_SPACE]:
            if len(bullets) < 5:  # Limit the number of bullets
                bullets.append(Bullet(player.rect.x, player.rect.y))

        # Update bullets
        for bullet in bullets[:]:
            bullet.move()
            if bullet.rect.y < 0:
                bullets.remove(bullet)

        # Update enemies
        for enemy in enemies[:]:
            enemy.move()
            if enemy.rect.y > HEIGHT:
                enemies.remove(enemy)
                enemies.append(Enemy(random.randint(0, WIDTH - enemy_width), random.randint(-150, -50)))

        # Collision detection
        for bullet in bullets[:]:
            for enemy in enemies[:]:
                if bullet.rect.colliderect(enemy.rect):
                    bullets.remove(bullet)
                    enemies.remove(enemy)
                    enemies.append(Enemy(random.randint(0, WIDTH - enemy_width), random.randint(-150, -50)))

        # Draw everything
        player.draw()
        for bullet in bullets:
            bullet.draw()
        for enemy in enemies:
            enemy.draw()

        pygame.display.flip()
        clock.tick(60)

    pygame.quit()

if __name__ == "__main__":
    main()






