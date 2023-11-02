from pygame import *

back = (200, 100, 150)
win_width, win_height = 500, 500
window = display.set_mode((win_width, win_height))
window.fill(back)

clock = time.Clock()
fps = 60

class GameSprite(sprite.Sprite):
    def __init__(self, img, x, y, w, h, speed):
        super().__init__()

        self.speed = speed
        self.width = w
        self.height = h

        self.image = transform.scale(
            image.load(img),
            (w, h)
        )

        self.rect = self.image.get_rect()
        self.rect.x = x
        self.rect.y = y

    def reset(self):
        window.blit(self.image, (self.rect.x, self.rect.y))
        
class Player(GameSprite):
    def update_left_player(self):
        keys = key.get_pressed()
        if keys[K_s] and self.rect.y < win_height - self.height:
            self.rect.y += self.speed
        if keys[K_w] and self.rect.y > 0:
            self.rect.y -= self.speed
    
    def update_right_player(self):
        keys = key.get_pressed()
        if keys[K_DOWN] and self.rect.y < win_height - self.height:
            self.rect.y += self.speed
        if keys[K_UP] and self.rect.y > 0:
            self.rect.y -= self.speed
      
player_left = Player("player_pong.png", 30, 200, 10, 150, 12)
player_right = Player("player_pong.png", win_width - 30, 200, 10, 150, 12)
ball = GameSprite("ball_pong.png", win_width / 2, win_height / 2, 32, 32, 0)

ball_speed_x, ball_speed_y = 3, 3

font.init()
font_main = font.Font(None, 72)
lose1 = font_main.render("PLAYER 1 LOSE!", True, (200, 50, 0))
lose2 = font_main.render("PLAYER 2 LOSE!", True, (200, 50, 0))
      
game_run = True
game_finished = False
                
while game_run:
    
    for e in event.get():
        if e.type == QUIT:
            game_run = False
            
    if not game_finished:
        window.fill(back)
        player_left.update_left_player()
        player_right.update_right_player()
        ball.rect.x += ball_speed_x
        ball.rect.y += ball_speed_y
        
        if sprite.collide_rect(player_left, ball) or sprite.collide_rect(player_right, ball):
            ball_speed_x *= -1
            
        if ball.rect.y > win_height - 32 or ball.rect.y <= 0:
            ball_speed_y *= -1
            
        if ball.rect.x > win_width:
            game_finished = True
            window.blit(lose2, (win_width / 2 - lose1.get_width() / 2, win_height / 2))
            
        if ball.rect.x < 0:
            game_finished = True
            window.blit(lose1, (win_width / 2 - lose1.get_width() / 2, win_height / 2))
        
        player_left.reset()
        player_right.reset()
        ball.reset()
        
    
    clock.tick(fps)
    display.update()
    import sys, os

def resource_path(relative_path):
    try:
        base_path = sys._MEIPASS
    except Exception:
        base_path = os.path.abspath(".")

    return os.path.join(base_path, relative_path)
