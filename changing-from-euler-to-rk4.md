# Changing from Euler                           To RK4

Notes: ❌ Euler Integration (Why it Failed)
• What it did: Calculated gravity at the current frame and moved linearly.
• The physics flaw: Gravity curves sharply, so straight lines overshot the path.
• The bug: Rocket gained fake energy and spiralled out of control after a few minutes.                                     🔄 The Pivot
• The discovery: Realised baseline game math is too weak for astrophysics.
• The change: Upgraded from single-step math to multi-sample math.RK4 Engine (Why it Worked)
• What it does: Takes four trial mini-steps into the future per frame.
• The math: Uses a weighted average to calculate the true orbit curve.
• The result: Energy is perfectly conserved, locking the path in place forever.
Phase: Research

![Screenshot 2026-08-30 200513.png](Changing%20from%20Euler%20To%20RK4/Screenshot_2026-08-30_200513.png)

**Figure 1: Orbit path accuracy comparison.**The green RK4 line follows the true gravity curve perfectly. The red Euler method makes small math errors every single frame. These errors add up quickly, injecting fake energy into the rocket and causing it to drift completely off course.