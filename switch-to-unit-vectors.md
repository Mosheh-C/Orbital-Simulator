# Rewrote gravity function, switched to unit vector method instead of trig methodc

Date: July 23, 2026
Notes: Dropped atan2+cos/sin, used dx/distance and dy/distance unit vector instead. Simpler and faster. G constant omitted intentionally. Need to add min distance check to avoid force blowup.
Phase: Physics Engine