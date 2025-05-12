*MY ATTEMPT AT THE RECREATION OF THE GAME: KIRBY DREAM LAND*

Current features (subject to change):
- Initialization: Main calls Init to set Kirby’s starting position (X=80, Y=128) and state (idle). 
- EnableInterrupts sets up VBlank.
- Game Loop: Each frame, GameLoop waits for VBlank (halt), then:
- Checks input (ReadInput) to move Kirby left/right or start a jump.
- Updates Kirby’s position (UpdateKirby) with velocity and gravity, snapping to the ground if needed.
- Updates Kirby’s sprite (DrawKirby) in OAM for rendering.
- Input Handling: ReadInput reads the joypad, calling MoveRight, MoveLeft, or StartJump based on button presses.
- Physics: UpdateKirby applies velocity to Y, adds gravity, caps falling speed, and checks for ground collision.
- Rendering: DrawKirby writes Kirby’s position to OAM, which the Game Boy’s PPU uses to draw him.
- Interrupts: VBlank ensures smooth screen updates, though the handler is minimal here.

Written in Z80 Assembly. (The instruction set architecture used for the GameBoy) Will get patched later. 
