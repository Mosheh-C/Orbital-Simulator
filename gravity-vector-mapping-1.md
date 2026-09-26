# Gravity Vector Mapping (1)

Date: August 2, 2026
Notes:  1. What a Gravity Vector is                                                      A gravity vector shows the direction and strength of gravitational force acting on an object.
For my orbital simulator:
• The planet pulls the rocket toward its center.
• This pull is represented as a vector pointing from the rokcet → planet.
So the gravity vector always points inward, toward the planet.                                                                                                                                                                                                     2. Magnitude
Gravity strength depends on distance.
Closer = stronger, farther = weaker.
Uses the formula  $ F=GM/r2.$

3. Unit Gravity Vector
Normalize the direction vector so its length = 1.
This gives the pure direction of gravity without strength.                                                                                                
4. Applying Gravity
Multiply the unit vector by the gravity force.
This produces the actual gravity vector used to update velocity.                                                                                                                                                                                                  5. Visualizing Gravity
Draw a line from the satellite toward the planet.
Scale it so it’s visible 
                                                                                                      6. Why It Matters
Shows how gravity changes with distance and how it shapes the orbit.
Helps compare gravity vs velocity to understand orbital motion.                                                                                                                                   
Phase: Rendering

```python
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
```

```python
# ============================
# GRAVITY FUNCTION
# Calculates gravitational pull from planet → rocket
# ============================
def gravity_force(px, py, rx, ry, mass):
    dx = px - rx  # horizontal distance
    dy = py - ry  # vertical distance
    distance = math.sqrt(dx*dx + dy*dy)

    # Newton-style gravity (simplified): F = GM / r^2
    force = mass / (distance**2)

    # Convert force into x/y components
    return force * (dx/distance), force * (dy/distance)

```