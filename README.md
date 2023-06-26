# -Flappy-Bird-Game
# Python  Flappy Bird Game
import pygame
import sys
import random

# Global Variables
WIDTH = 400
HEIGHT = 600
FPS = 60
GROUND = HEIGHT - 50
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
BLUE = (0, 0, 255)
GREEN = (0, 255, 0)

# Initialize Pygame
pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
clock = pygame.time.Clock()

# Load Assets
background_img = pygame.image.load("C:\\Users\\prave\\OneDrive\\Desktop\\python\\background.png.png")

bird_img = pygame.image.load("C:\\Users\\prave\\OneDrive\\Desktop\\python\\bird.png.png")
pipe_img = pygame.image.load("C:\\Users\\prave\\OneDrive\\Desktop\\python\\pipe.png.png")


# Bird Class
class Bird(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = bird_img
        self.rect = self.image.get_rect()
        self.rect.center = (100, HEIGHT / 2)
        self.velocity = 0
        self.gravity = 0.5

    def update(self):
        self.velocity += self.gravity
        self.rect.y += self.velocity

    def jump(self):
        self.velocity = -8


# Pipe Class
class Pipe(pygame.sprite.Sprite):
    def __init__(self, x):
        super().__init__()
        self.image = pygame.transform.flip(pipe_img, False, True)
        self.rect = self.image.get_rect()
        self.rect.x = x
        self.rect.height = random.randint(150, 400)
        self.rect.y = self.rect.height if x < WIDTH else GROUND - self.rect.height - 200

    def update(self):
        self.rect.x -= 3
        if self.rect.right < 0:
            self.rect.x = WIDTH
            self.rect.height = random.randint(150, 400)
            self.rect.y = self.rect.height if self.rect.x < WIDTH else GROUND - self.rect.height - 200


# Game Functions
def draw_text(text, size, x, y):
    font = pygame.font.Font(None, size)
    text_surface = font.render(text, True, WHITE)
    text_rect = text_surface.get_rect()
    text_rect.midtop = (x, y)
    screen.blit(text_surface, text_rect)


def game_over():
    draw_text("Game Over", 64, WIDTH / 2, HEIGHT / 4)
    draw_text("Press Space to Play Again", 22, WIDTH / 2, HEIGHT / 2)
    pygame.display.flip()
    waiting = True
    while waiting:
        for event in pygame.event.get():
            if event.type == pygame.QUIT or (event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE):
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
                waiting = False


# Game Initialization
def game():
    score = 0

    all_sprites = pygame.sprite.Group()
    pipes = pygame.sprite.Group()

    bird = Bird()
    all_sprites.add(bird)

    for i in range(2):
        pipe = Pipe(WIDTH + i * 300)
        all_sprites.add(pipe)
        pipes.add(pipe)

    running = True
    while running:
        # Game Loop
        for event in pygame.event.get():
            if event.type == pygame.QUIT or (event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE):
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
                bird.jump()

        all_sprites.update()

        hits = pygame.sprite.spritecollide(bird, pipes, False)
        if hits or bird.rect.bottom > GROUND:
            game_over()
            bird.rect.center = (100, HEIGHT / 2)
            bird.velocity = 0
            score = 0
            for sprite in all_sprites:
                sprite.rect.x -= 400
                if sprite.rect.right < 0:
                    sprite.kill()

        pipe_passed = pygame.sprite.spritecollide(bird, pipes, False, pygame.sprite.collide_rect_ratio(0.8))
        if pipe_passed:
            score += 1

        screen.blit(background_img, (0, 0))
        all_sprites.draw(screen)
        draw_text(str(score), 32, WIDTH / 2, 30)
        pygame.display.flip()
        clock.tick(FPS)


if __name__ == '__main__':
    game()
