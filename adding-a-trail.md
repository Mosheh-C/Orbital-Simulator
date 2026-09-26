# Adding a Trail

Date: July 25, 2026
Notes: I keep a list called trail that stores the rocket’s previous positions                                                                                                              Every frame, I add the rocket’s current position to the list                                                                                      If the list gets too long (over 500 points), I remove the oldest one to prevent lag                                                                                                                   Then I draw each saved point as a small grey dot                                                                                                           This creates a smooth orbit path behind the rocket.                                                                                                       Why It Matters
• Makes the simulator look more scientific
• Helps analyse orbit stability
• Shows the effect of Euler integration visually
Phase: Rendering