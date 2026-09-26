# Euler V RK4

Notes: 🔬 Test 1: Baseline Euler Integration
• Setup: Pressed Preset Orbit button (Planet Mass = 5000, Starting Orbit Radius = 200 pixels).
• Observation: The rocket completed its first loop, but the white trail did not overlap. Every couple of frames, the path expanded outward.
• Data Result: After 5 minutes of continuous running, the rocket gained artificial kinetic energy and spiraled completely off the screen.
• Conclusion: Euler integration fails because it uses a single straight-line estimation per frame. It cannot track a curving gravity field accurately.                  🔬 Test 2: Upgraded RK4 Integration
• Setup: Pressed Preset Orbit button using identical variables (Mass = 5000, Radius = 200).
• Observation: The rocket tracked a smooth path. The gray trail formed a perfectly clean, sharp circle with zero shifting.
• Data Result: Left the simulation running for over 10 minutes. The rocket traced the exact same pixels continuously without a single millimeter of drift.
• Conclusion: The RK4 engine successfully eliminates numerical drift by taking four look-ahead sample steps to calculate a true mathematical average curve.
Phase: Testing

```jsx
# =========================================================================
# OLD EULER MATH (Single Sample)      |  NEW RK4 ENGINE (4 Look-Ahead Samples)
# =========================================================================

# Gravity → velocity → position      |  # Multi-sampling loop eliminates drift
g_x, g_y = gravity_force(             |  dt = 1.0  
    planet_x, planet_y,               |  rocket_x, rocket_y, vel_x, vel_y = rk4_step(
    rocket_x, rocket_y, planet_mass   |      rocket_x, rocket_y, vel_x, vel_y, 
)                                     |      planet_x, planet_y, planet_mass, dt
vel_x += g_x                          |  )
vel_y += g_y                          |  
                                      |  # (The math function looks ahead 4 times 
rocket_x += vel_x                     |  # inside rk4_step before updating variables)
rocket_y += vel_y                     |  

```