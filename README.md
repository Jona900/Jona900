import pygame
import random

# Initialisieren von Pygame
pygame.init()

# Bildschirmgröße
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600

# Farben
WHITE = (255, 255, 255)
RED = (255, 0, 0)

# Spieler
player_size = 50
player_pos = [SCREEN_WIDTH/2, SCREEN_HEIGHT/2]

# Gegner (Zombies)
enemy_size = 50
enemy_pos = [random.randint(0, SCREEN_WIDTH-enemy_size), random.randint(0, SCREEN_HEIGHT-enemy_size)]

# Fenster
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Zombie Shooter")

# Spiel-Ende
game_over = False

# Clock
clock = pygame.time.Clock()

# Schuss
bullet_speed = 10
bullet_pos = []
bullet_state = "ready"  # "ready" = bereit, "fire" = abgeschossen

# Spielerbewegung
player_speed = 10

# Schussfunktion
def fire_bullet():
    global bullet_pos, bullet_state
    bullet_state = "fire"
    bullet_pos = [player_pos[0], player_pos[1]]

# Kollisionsfunktion
def is_collision(en_pos, pl_pos):
    return (en_pos[0] >= pl_pos[0] and en_pos[0] < (pl_pos[0] + player_size)) or (pl_pos[0] >= en_pos[0] and pl_pos[0] < (en_pos[0] + enemy_size)) and \
           (en_pos[1] >= pl_pos[1] and en_pos[1] < (pl_pos[1] + player_size)) or (pl_pos[1] >= en_pos[1] and pl_pos[1] < (en_pos[1] + enemy_size))

# Spiel-Schleife
while not game_over:
    # Event Loop
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            game_over = True
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE and bullet_state == "ready":
                fire_bullet()

    # Spielerbewegung
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        player_pos[0] -= player_speed
    elif keys[pygame.K_RIGHT]:
        player_pos[0] += player_speed
    elif keys[pygame.K_UP]:
        player_pos[1] -= player_speed
    elif keys[pygame.K_DOWN]:
        player_pos[1] += player_speed

    # Bildschirm leeren
    screen.fill(WHITE)

    # Gegner (Zombies) zeichnen
    pygame.draw.rect(screen, RED, (enemy_pos[0], enemy_pos[1], enemy_size, enemy_size))

    # Spieler zeichnen
    pygame.draw.rect(screen, RED, (player_pos[0], player_pos[1], player_size, player_size))

    # Schussbewegung
    if bullet_state == "fire":
        bullet_pos[1] -= bullet_speed
        pygame.draw.rect(screen, RED, (bullet_pos[0], bullet_pos[1], 5, 10))
        if bullet_pos[1] <= 0:
            bullet_state = "ready"

    # Kollision prüfen
    if is_collision(enemy_pos, player_pos):
        game_over = True

    # Refresh
    pygame.display.update()
    clock.tick(30)

# Spiel beenden
pygame.quit()
