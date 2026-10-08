# Final Code

Phase: Overall Code

```jsx
import pygame
import math

# ============================
# BUTTON CLASS
# Handles drawing buttons and detecting clicks
# ============================
class Button:
    def __init__(self, x, y, w, h, text):
        # Create a rectangle for the button
        self.rect = pygame.Rect(x, y, w, h)
        self.text = text

        # Normal and hover colors
        self.color = (180, 180, 180)
        self.hover_color = (210, 210, 210)

        # Font used for button text
        self.font = pygame.font.SysFont(None, 30)

    def draw(self, screen):
        # Check if mouse is hovering over the button
        mouse_pos = pygame.mouse.get_pos()
        if self.rect.collidepoint(mouse_pos):
            pygame.draw.rect(screen, self.hover_color, self.rect)
        else:
            pygame.draw.rect(screen, self.color, self.rect)

        # Draw the button text centered inside the rectangle
        text_surf = self.font.render(self.text, True, (0, 0, 0))
        text_rect = text_surf.get_rect(center=self.rect.center)
        screen.blit(text_surf, text_rect)

    def is_clicked(self, event):
        # Detect left mouse button click inside the button area
        if event.type == pygame.MOUSEBUTTONDOWN:
            if event.button == 1:  # left click
                if self.rect.collidepoint(event.pos):
                    return True
        return False

# ============================
# PYGAME INITIAL SETUP
# ============================
pygame.init()
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
clock = pygame.time.Clock()

# ============================
# PLANET SETTINGS
# ============================
planet_x = WIDTH // 2
planet_y = HEIGHT // 2
planet_mass = 5000  # controls gravity strength

# ============================
# ROCKET INITIAL STATE
# ============================
rocket_x = WIDTH // 2 + 200
rocket_y = HEIGHT // 2
vel_x = 0
vel_y = -5  # small starting velocity

# Trail stores past rocket positions for orbit visualization
trail = []

# ============================
# BUTTON CREATION
# ============================
reset_button = Button(20, 20, 120, 40, "Reset")
preset_orbit_button = Button(20, 70, 160, 40, "Preset Orbit")

# ============================
# NEWTONIAN ACCELERATION FIELD
# Evaluates gravitational pull at any custom (X, Y) coordinate
# ============================
def get_acceleration(rx, ry, px, py, mass):
    dx = px - rx  # horizontal distance
    dy = py - ry  # vertical distance
    distance = math.sqrt(dx*dx + dy*dy)

    if distance == 0:
        return 0, 0

    # Newton-style gravity (simplified): a = M / r^2
    # We calculate acceleration directly rather than force, as mass cancels out.
    acceleration_magnitude = mass / (distance**2)

    # Convert force into x/y components
    ax = acceleration_magnitude * (dx / distance)
    ay = acceleration_magnitude * (dy / distance)
    return ax, ay

# ============================
# RUNGE-KUTTA 4TH ORDER ENGINE
# Advanced numeric solver substituting the baseline Euler step
# ============================
def rk4_step(rx, ry, vx, vy, px, py, mass, dt):
    """
    MATHEMATICAL THEORY FOR CREST REPORT:
    Euler integration assumes the gravitational field is uniform across a frames timeframe.
    Because gravity curves sharply, this linear approximation accumulates energy drift.
    RK4 operates by sampling four distinct vector derivatives (slopes) across the time step:
    K1: The initial gradient at the current position.
    K2: A trial step to the midpoint using K1's vector trajectory.
    K3: A second trial step to the midpoint using K2's updated vector trajectory.
    K4: A full trial step to the endpoint using K3's vector trajectory.
    By forming a Simpson's Rule weighted average of these points, the error drops to O(dt^5).
    """
    
    # --- Sample 1: The Initial Boundary Conditions ---
    v1_x, v1_y = vx, vy
    a1_x, a1_y = get_acceleration(rx, ry, px, py, mass)

    # --- Sample 2: The First Midpoint Prediction ---
    # Projects the rocket ahead by half a frames timeframe using sample 1 data.
    # Evaluates how gravity curves at this forecasted intermediate zone.
    v2_x = vx + a1_x * (dt / 2)
    v2_y = vy + a1_y * (dt / 2)
    a2_x, a2_y = get_acceleration(rx + v1_x * (dt / 2), ry + v1_y * (dt / 2), px, py, mass)

    # --- Sample 3: The Refined Midpoint Prediction ---
    # Re-evaluates the midpoint using the newer velocity and acceleration from Sample 2.
    # Corrects the trajectory if the gravitational gradient changed sharply.
    v3_x = vx + a2_x * (dt / 2)
    v3_y = vy + a2_y * (dt / 2)
    a3_x, a3_y = get_acceleration(rx + v2_x * (dt / 2), ry + v2_y * (dt / 2), px, py, mass)

    # --- Sample 4: The Final Horizon Prediction ---
    # Projects the rocket completely across to the end of the time step using sample 3 data.
    v4_x = vx + a3_x * dt
    v4_y = vy + a3_y * dt
    a4_x, a4_y = get_acceleration(rx + v3_x * dt, ry + v3_y * dt, px, py, mass)

    # --- The Final Integration Step (Weighted Integration Math) ---
    # Midpoints (v2, v3, a2, a3) represent the central path and are weighted twice as heavily.
    # The final movement vectors perfectly mimic an active curve rather than a jagged line.
    final_rx = rx + (dt / 6) * (v1_x + 2 * v2_x + 2 * v3_x + v4_x)
    final_ry = ry + (dt / 6) * (v1_y + 2 * v2_y + 2 * v3_y + v4_y)
    
    final_vx = vx + (dt / 6) * (a1_x + 2 * a2_x + 2 * a3_x + a4_x)
    final_vy = vy + (dt / 6) * (a1_y + 2 * a2_y + 2 * a3_y + a4_y)

    return final_rx, final_ry, final_vx, final_vy

# ============================
# MAIN GAME LOOP
# Runs until window is closed
# ============================
running = True
while running:

    # ----------------------------
    # EVENT HANDLING (buttons, quit)
    # ----------------------------
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        # Preset orbit button: places rocket in perfect circular orbit
        if preset_orbit_button.is_clicked(event):
            rocket_x = planet_x + 200
            rocket_y = planet_y

            distance = 200
            vel_x = 0
            vel_y = math.sqrt(planet_mass / distance)  # v = √(GM / r)

            trail.clear()

        # Reset button: returns rocket to starting position
        if reset_button.is_clicked(event):
            rocket_x = WIDTH // 2 + 200
            rocket_y = HEIGHT // 2
            vel_x = 0
            vel_y = -5
            trail.clear()

    # ----------------------------
    # KEYBOARD INPUT (WASD + QE)
    # ----------------------------
    keys = pygame.key.get_pressed()

    # Change planet mass (gravity strength)
    if keys[pygame.K_w]:
        planet_mass += 50
    if keys[pygame.K_s]:
        planet_mass -= 50

    # Horizontal velocity control
    if keys[pygame.K_a]:
        vel_x -= 0.2
    if keys[pygame.K_d]:
        vel_x += 0.2

    # Vertical velocity control
    if keys[pygame.K_q]:
        vel_y -= 0.2
    if keys[pygame.K_e]:
        vel_y += 0.2

    # ----------------------------
    # PHYSICS UPDATE - Runge-Kutta 4th Order (RK4)
    # ----------------------------
    # Your old code used Euler Integration (Gravity → velocity → position).
    # We replaced those 5 lines with this high-precision multi-sampling engine loop.
    dt = 1.0  # Step size factor representing frames
    rocket_x, rocket_y, vel_x, vel_y = rk4_step(
        rocket_x, rocket_y, vel_x, vel_y, planet_x, planet_y, planet_mass, dt
    )

    # Save rocket position for orbit trail
    trail.append((int(rocket_x), int(rocket_y)))
    if len(trail) > 500:
        trail.pop(0)

    # ----------------------------
    # DRAWING EVERYTHING
    # ----------------------------
    screen.fill((0, 0, 0))  # background

    # Draw planet + rocket
    pygame.draw.circle(screen, (0, 100, 255), (planet_x, planet_y), 40)
    pygame.draw.circle(screen, (255, 255, 255), (int(rocket_x), int(rocket_y)), 5)

    # Draw buttons
    reset_button.draw(screen)
    preset_orbit_button.draw(screen)

    # Draw orbit trail
    for point in trail:
        pygame.draw.circle(screen, (150, 150, 150), point, 2)

#---------Draw Gravity Annotations--------#
    def draw_gravity_arrow():
        pass
    #Calculate direction vector subtract planets position from rockets position
    direction_y = planet_y - rocket_y
    direction_x = planet_x - rocket_x

    #Finding Magnitude of direction vector
    magnitude_squared = direction_x**2 + direction_y**2
    magnitude = math.sqrt(magnitude_squared)

    #Turning a direction vector to unit vector
    # Safeguard introduced to prevent system crashes due to zero division if rocket hits coordinates (0,0)
    if magnitude > 0:
        unit_x = direction_x/magnitude
        unit_y = direction_y/magnitude

        start_x = int(rocket_x)
        start_y = int(rocket_y)

        arrow_length = 100  # pixels
        end_x = start_x + unit_x * arrow_length
        end_y = start_y + unit_y * arrow_length

        pygame.draw.line(screen, (255, 0, 0), (start_x, start_y), (end_x, end_y), 3)

        # Arrowhead size
        head_size = 10

        # Perpendicular vector for arrowhead
        perp_x = -unit_y
        perp_y = unit_x

        # Two points for arrowhead
        left_x = end_x - unit_x * head_size + perp_x * head_size
        left_y = end_y - unit_y * head_size + perp_y * head_size

        right_x = end_x - unit_x * head_size - perp_x * head_size
        right_y = end_y - unit_y * head_size - perp_y * head_size

        pygame.draw.line(screen, (255, 0, 0), (end_x, end_y), (left_x, left_y), 3)
        pygame.draw.line(screen, (255, 0, 0), (end_x, end_y), (right_x, right_y), 3)

        #unit vector components drawn next to arrow 
        #It’s telling you: 👉 How much the arrow points horizontally (x)👉 How much the arrow points vertically (y)
        # SO will be in format (x,y)

        font = pygame.font.SysFont(None, 24)

        text = font.render(f"({unit_x:.2f}, {unit_y:.2f})", True, (255, 255, 255))
        screen.blit(text, (end_x + 10, end_y + 10))
        pygame.display.flip()
        clock.tick(60)
pygame.quit()

```
