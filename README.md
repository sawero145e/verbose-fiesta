import pygame 

pygame.init()

mw = pygame.display.set_mode((500,500))

dx = 3
dy = -3

move_right = False
move_left = False
game_over = False

class Area():
    def __init__(self, x=0, y=0, width=10, height=10, color=None):
        self.rect = pygame.Rect(x, y, width, height)
        self.fill_color = color

    def fill(self, color):
        pygame.draw.rect(mw, color, self.rect)

    def outline(self, frame_color, thickness):
        pygame.draw.rect(mw, frame_color, self.rect, thickness)

    def collideRect(self, rect):
        return self.rect.colliderect(rect)


class Label(Area):
    def set_text(self, text, fsize, text_color):
        self.text = text 
        self.image = pygame.font.Font(None, fsize).render(text, True, text_color)
    def draw(self, shift_x=0, shift_y=0): 
        pygame.draw.rect(mw, self.fill_color, self.rect)
        mw.blit(self.image, (self.rect.x + shift_x, self.rect.y + shift_y))


class Picture(Area):
    def __init__(self, filname, x=0, y=0, width=10, height=10):
        super().__init__(x=x, y=y, width=width, height=height, color=None)
        self.image = pygame.image.load(filname)

    def draw(self):
        mw.blit(self.image,(self.rect.x, self.rect.y))
    
monstres = []
x = 5
y = 5
n = 9 

for i in range(1,4):
    for k in range(0,n):
        e = Picture('enemy.png',x,y, 50,50)
        monstres.append(e)
        x += 55
    y += 55
    x = 25 * i
    n -= 1

ball = Picture('ball.png',225,350, 50,50)
platform = Picture('platform.png',200,400,50,50)

clock = pygame.time.Clock()
fps = 40
game = True
while game:
    if game_over == True:
        pass
    else:
        mw.fill((146, 237, 247))
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                game = False
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_d:
                    move_right = True
                if event.key == pygame.K_a:
                    move_left = True
            elif event.type == pygame.KEYUP:
                if event.key == pygame.K_d:
                    move_right = False
                if event.key == pygame.K_a:
                    move_left = False
        if move_right:
            platform.rect.x +=6
        if move_left:
            platform.rect.x -=6
        if ball.rect.y < 0:
            dy *= -1
        if ball.rect.x > 450 or ball.rect.x < 0:
            dx *= -1
        if  ball.collideRect(platform.rect):
            dy *= -1
    
        for monstre in monstres:
            monstre.draw()
            if monstre.rect.colliderect(ball.rect):
                monstres.remove(monstre)
                dy *= -1
        
        ball.rect.x += dx
        ball.rect.y += dy 

        if ball.rect.y > platform.rect.y + 50:
            time_text = Label(150,150,0,0,(0,0,0))
            time_text.set_text("Играть научись",60,(255,0,0))
            time_text.draw(10, 10)
            game_over = True

        if len(monstres) == 0:
            time_text = Label(150,150,0,0,(0,0,0))
            time_text.set_text("лан выиграл",60,(0,255,0))
            time_text.draw(10,10)
            game_over = True
        platform.draw()
        ball.draw()
    clock.tick(fps)
    pygame.display.update()

