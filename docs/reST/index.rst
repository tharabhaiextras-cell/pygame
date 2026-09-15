import pygame
import random
import sys

pygame.init()

# --- Settings ---
CELL_SIZE = 20
GRID_WIDTH = 30
GRID_HEIGHT = 20
WIDTH = CELL_SIZE * GRID_WIDTH
HEIGHT = CELL_SIZE * GRID_HEIGHT
FPS = 10

BLACK = (0, 0, 0)
GREEN = (0, 200, 0)
RED = (200, 0, 0)
WHITE = (255, 255, 255)

screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Snake")
clock = pygame.time.Clock()
font = pygame.font.SysFont(None, 48)


def random_food(snake):
    while True:
        pos = (random.randint(0, GRID_WIDTH - 1), random.randint(0, GRID_HEIGHT - 1))
        if pos not in snake:
            return pos


def draw_cell(pos, color):
    rect = pygame.Rect(pos[0] * CELL_SIZE, pos[1] * CELL_SIZE, CELL_SIZE, CELL_SIZE)
    pygame.draw.rect(screen, color, rect)


def show_game_over(score):
    screen.fill(BLACK)
    text = font.render(f"Game Over! Score: {score}", True, WHITE)
    text_rect = text.get_rect(center=(WIDTH // 2, HEIGHT // 2 - 20))
    screen.blit(text, text_rect)
    sub = pygame.font.SysFont(None, 28).render("Press R to restart or Q to quit", True, WHITE)
    sub_rect = sub.get_rect(center=(WIDTH // 2, HEIGHT // 2 + 30))
    screen.blit(sub, sub_rect)
    pygame.display.flip()

    waiting = True
    while waiting:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_r:
                    waiting = False
                    main()
                if event.key == pygame.K_q:
                    pygame.quit()
                    sys.exit()


def main():
    snake = [(GRID_WIDTH // 2, GRID_HEIGHT // 2)]
    direction = (1, 0)
    food = random_food(snake)
    score = 0

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key in (pygame.K_UP, pygame.K_w) and direction != (0, 1):
                    direction = (0, -1)
                elif event.key in (pygame.K_DOWN, pygame.K_s) and direction != (0, -1):
                    direction = (0, 1)
                elif event.key in (pygame.K_LEFT, pygame.K_a) and direction != (1, 0):
                    direction = (-1, 0)
                elif event.key in (pygame.K_RIGHT, pygame.K_d) and direction != (-1, 0):
                    direction = (1, 0)

        # Move snake
        head = snake[0]
        new_head = (head[0] + direction[0], head[1] + direction[1])

        # Check collisions: walls or self
        if (
            new_head[0] < 0 or new_head[0] >= GRID_WIDTH
            or new_head[1] < 0 or new_head[1] >= GRID_HEIGHT
            or new_head in snake
        ):
            show_game_over(score)
            return

        snake.insert(0, new_head)

        if new_head == food:
            score += 1
            food = random_food(snake)
        else:
            snake.pop()

        # Draw everything
        screen.fill(BLACK)
        for segment in snake:
            draw_cell(segment, GREEN)
        draw_cell(food, RED)

        score_text = pygame.font.SysFont(None, 28).render(f"Score: {score}", True, WHITE)
        screen.blit(score_text, (5, 5))

        pygame.display.flip()
        clock.tick(FPS)


if __name__ == "__main__":
    main()
