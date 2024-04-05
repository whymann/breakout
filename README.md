# breakout
python
Copy code
import pygame
import random

# Constants
WIDTH = 800
HEIGHT = 600
PADDLE_WIDTH = 100
PADDLE_HEIGHT = 20
BALL_RADIUS = 10
BRICK_WIDTH = 80
BRICK_HEIGHT = 30
BRICK_ROWS = 5
BRICK_COLS = 10
BRICK_SPACING = 4
BRICK_OFFSET_TOP = 50

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
BLUE = (0, 0, 255)
RED = (255, 0, 0)
GREEN = (0, 255, 0)
YELLOW = (255, 255, 0)

# Initialize Pygame
pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Breakout")
clock = pygame.time.Clock()

class Paddle(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((PADDLE_WIDTH, PADDLE_HEIGHT))
        self.image.fill(WHITE)
        self.rect = self.image.get_rect()
        self.rect.x = (WIDTH - PADDLE_WIDTH) // 2
        self.rect.y = HEIGHT - PADDLE_HEIGHT - 10
        self.speed = 0

    def update(self):
        self.rect.x += self.speed
        if self.rect.left < 0:
            self.rect.left = 0
        elif self.rect.right > WIDTH:
            self.rect.right = WIDTH

class Ball(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((BALL_RADIUS * 2, BALL_RADIUS * 2), pygame.SRCALPHA)
        pygame.draw.circle(self.image, WHITE, (BALL_RADIUS, BALL_RADIUS), BALL_RADIUS)
        self.rect = self.image.get_rect()
        self.rect.center = (WIDTH // 2, HEIGHT // 2)
        self.speed_x = 5 * random.choice([-1, 1])
        self.speed_y = -5

    def update(self):
        self.rect.x += self.speed_x
        self.rect.y += self.speed_y

        if self.rect.left <= 0 or self.rect.right >= WIDTH:
            self.speed_x *= -1
        if self.rect.top <= 0:
            self.speed_y *= -1

class Brick(pygame.sprite.Sprite):
    def __init__(self, color, x, y):
        super().__init__()
        self.image = pygame.Surface((BRICK_WIDTH, BRICK_HEIGHT))
        self.image.fill(color)
        self.rect = self.image.get_rect()
        self.rect.x = x
        self.rect.y = y

def create_bricks():
    bricks = pygame.sprite.Group()
    for row in range(BRICK_ROWS):
        for col in range(BRICK_COLS):
            brick = Brick(random.choice([RED, GREEN, BLUE, YELLOW]), col * (BRICK_WIDTH + BRICK_SPACING),
                          row * (BRICK_HEIGHT + BRICK_SPACING) + BRICK_OFFSET_TOP)
            bricks.add(brick)
    return bricks

all_sprites = pygame.sprite.Group()
bricks = create_bricks()
all_sprites.add(bricks)
paddle = Paddle()
all_sprites.add(paddle)
ball = Ball()
all_sprites.add(ball)

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                paddle.speed = -8
            elif event.key == pygame.K_RIGHT:
                paddle.speed = 8
        elif event.type == pygame.KEYUP:
            if event.key == pygame.K_LEFT and paddle.speed < 0:
                paddle.speed = 0
            elif event.key == pygame.K_RIGHT and paddle.speed > 0:
                paddle.speed = 0

    all_sprites.update()

    hits = pygame.sprite.spritecollide(ball, bricks, True)
    if hits:
        ball.speed_y *= -1

    if pygame.sprite.collide_rect(ball, paddle):
        ball.speed_y *= -1

    screen.fill(BLACK)
    all_sprites.draw(screen)
    pygame.display.flip()
    clock.tick(60)

pygame.quit()
