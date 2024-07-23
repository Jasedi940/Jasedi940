import pygame
import random

# Initialisation de pygame
pygame.init()

# Dimensions de la fenêtre de jeu
largeur_ecran = 800
hauteur_ecran = 600

# Couleurs
blanc = (255, 255, 255)
noir = (0, 0, 0)
rouge = (255, 0, 0)
vert = (0, 255, 0)

# Création de la fenêtre
ecran = pygame.display.set_mode((largeur_ecran, hauteur_ecran))
pygame.display.set_caption("Jeu de capture")

# Horloge pour contrôler la vitesse de rafraîchissement
horloge = pygame.time.Clock()

# Classe pour le joueur
class Joueur(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((50, 50))
        self.image.fill(vert)
        self.rect = self.image.get_rect()
        self.rect.center = (largeur_ecran // 2, hauteur_ecran - 50)
        self.vitesse = 5

    def update(self):
        touches = pygame.key.get_pressed()
        if touches[pygame.K_LEFT]:
            self.rect.x -= self.vitesse
        if touches[pygame.K_RIGHT]:
            self.rect.x += self.vitesse

        # Empêcher le joueur de sortir de l'écran
        if self.rect.left < 0:
            self.rect.left = 0
        if self.rect.right > largeur_ecran:
            self.rect.right = largeur_ecran

# Classe pour les objets à attraper
class Objet(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((30, 30))
        self.image.fill(rouge)
        self.rect = self.image.get_rect()
        self.rect.x = random.randint(0, largeur_ecran - self.rect.width)
        self.rect.y = -self.rect.height
        self.vitesse = 3

    def update(self):
        self.rect.y += self.vitesse
        if self.rect.top > hauteur_ecran:
            self.rect.x = random.randint(0, largeur_ecran - self.rect.width)
            self.rect.y = -self.rect.height

# Initialisation du joueur et des objets
joueur = Joueur()
tous_les_sprites = pygame.sprite.Group()
tous_les_sprites.add(joueur)

objets = pygame.sprite.Group()
for _ in range(5):
    objet = Objet()
    tous_les_sprites.add(objet)
    objets.add(objet)

# Boucle principale du jeu
en_cours = True
while en_cours:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            en_cours = False

    # Mettre à jour les sprites
    tous_les_sprites.update()

    # Vérifier les collisions
    if pygame.sprite.spritecollideany(joueur, objets):
        print("Objet attrapé!")

    # Dessiner les éléments
    ecran.fill(blanc)
    tous_les_sprites.draw(ecran)

    # Rafraîchir l'écran
    pygame.display.flip()
    
    # Contrôler la vitesse de rafraîchissement
    horloge.tick(60)

# Quitter pygame
pygame.quit()
