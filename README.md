import pygame
import random

# Inicialización de PyGame
pygame.init()

# Configuración de la pantalla
ANCHO, ALTO = 800, 600
pygame.display.set_caption("Juego")

# Colores
NEGRO = (0, 0, 0)
BLANCO = (255, 255, 255)
ROJO = (255, 0, 0)


jugador_tamano = 50
jugador_pos = [ANCHO // 2, ALTO - jugador_tamano * 2]
velocidad_jugador = 5

enemigo_tamano = 50
enemigo_pos = [random.randint(0, ANCHO - enemigo_tamano), 0]
enemigo_velocidad = 5
puntuacion = 0
fuente = pygame.font.SysFont("monospace", 35)
juego_activo = True
mostrar_menu = True

def mostrar_menu_principal():
    pantalla.fill(NEGRO)
    texto = fuente.render("Presiona ESPACIO para empezar", True, BLANCO)
    pantalla.blit(texto, (ANCHO // 2 - texto.get_width() // 2, ALTO // 2))

def detectar_colision(jugador_pos, enemigo_pos):
    jx, jy = jugador_pos
    ex, ey = enemigo_pos
    if (ex < jx < ex + enemigo_tamano or ex < jx + jugador_tamano < ex + enemigo_tamano) and \
       (ey < jy < ey + enemigo_tamano or ey < jy + jugador_tamano < ey + enemigo_tamano):
        return True
    return False

def mover_enemigo(enemigo_pos, puntuacion):
    if enemigo_pos[1] >= 0 and enemigo_pos[1] < ALTO:
        enemigo_pos[1] += enemigo_velocidad + puntuacion // 10
    else:
        enemigo_pos[0] = random.randint(0, ANCHO - enemigo_tamano)
        enemigo_pos[1] = 0
        puntuacion += 1
    return puntuacion

def mover_jugador(teclas_presionadas):
    if teclas_presionadas[pygame.K_LEFT] and jugador_pos[0] > 0:
        jugador_pos[0] -= velocidad_jugador
    if teclas_presionadas[pygame.K_RIGHT] and jugador_pos[0] < ANCHO - jugador_tamano:
        jugador_pos[0] += velocidad_jugador

while juego_activo:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            juego_activo = False

    teclas_presionadas = pygame.key.get_pressed()
    
    if mostrar_menu:
        mostrar_menu_principal()
        if teclas_presionadas[pygame.K_SPACE]:
            mostrar_menu = False
        pygame.display.update()
        continue

    pantalla.fill(NEGRO)
    
    mover_jugador(teclas_presionadas)

    puntuacion = mover_enemigo(enemigo_pos, puntuacion)

    
    if detectar_colision(jugador_pos, enemigo_pos):
        juego_activo = False

    pygame.draw.rect(pantalla, BLANCO, (jugador_pos[0], jugador_pos[1], jugador_tamano, jugador_tamano))
    pygame.draw.rect(pantalla, ROJO, (enemigo_pos[0], enemigo_pos[1], enemigo_tamano, enemigo_tamano))

    texto_puntuacion = fuente.render("Puntuación: {}".format(puntuacion), True, BLANCO)
    pantalla.blit(texto_puntuacion, (10, 10))

    pygame.display.update()
    pygame.time.Clock().tick(30)

pygame.quit()
