; ROM Section (Cartridge???)
.org $0100                  ; Entry point after logo
    nop
    jp Main

; Memory Map
.define KIRBY_X      $C000  ; Kirby's X position 
.define KIRBY_Y      $C001  ; Kirby's Y position 
.define KIRBY_STATE  $C002  ; 0 = idle, 1 = jumping 
.define JUMP_VEL     $C003  ; Jump velocity 

; Constants
.define GRAVITY      1      ; Pixels per frame
.define JUMP_START   -4     ; Initial upward velocity
.define MAX_FALL     4      ; Terminal velocity
.define GROUND_Y     128    ; Floor height 

.org $0150                  ; Main code start
Main:
    call Init               ; Set up initial state
    call EnableInterrupts   ; Enable VBlank interrupt

GameLoop:
    halt                    ; Wait for VBlank (saves CPU)
    call ReadInput          ; Check joypad
    call UpdateKirby        ; Move and apply physics
    call DrawKirby          ; Update sprite on screen
    jp GameLoop             ; Repeat forever

Init:
    ld a, 80                ; Starting X (middle-ish of 160px screen)
    ld (KIRBY_X), a
    ld a, GROUND_Y          ; Starting Y (on ground)
    ld (KIRBY_Y), a
    ld a, 0                 ; Start idle
    ld (KIRBY_STATE), a
    ret

ReadInput:
    ld a, ($FF00)           ; Joypad register
    cpl                     ; Invert bits (1 = pressed)
    and $0F                 ; Mask directional buttons
    bit 0, a                ; Right button
    call nz, MoveRight
    bit 1, a                ; Left button
    call nz, MoveLeft
    bit 4, a                ; A button (jump)
    call nz, StartJump
    ret

MoveRight:
    ld a, (KIRBY_X)
    inc a                   ; Move 1 pixel right
    cp 152                  ; Screen edge (160 - 8px sprite)
    ret nc                  ; Don’t go offscreen
    ld (KIRBY_X), a
    ret

MoveLeft:
    ld a, (KIRBY_X)
    dec a                   ; Move 1 pixel left
    cp 255                  ; Check underflow
    ret z                   ; Don’t go offscreen
    ld (KIRBY_X), a
    ret

StartJump:
    ld a, (KIRBY_STATE)
    cp 0                    ; Only jump if idle
    ret nz
    ld a, 1                 ; Set jumping state
    ld (KIRBY_STATE), a
    ld a, JUMP_START        ; Initial upward velocity
    ld (JUMP_VEL), a        ; Store in JUMP_VEL, not KIRBY_STATE
    ret

UpdateKirby:
    ld a, (KIRBY_STATE)
    cp 0                    ; If idle, skip physics
    ret z

    ; Apply velocity to Y position
    ld a, (KIRBY_Y)
    ld b, a
    ld a, (JUMP_VEL)
    add b                   ; Y = Y + velocity
    ld (KIRBY_Y), a

    ; Apply gravity
    ld a, (JUMP_VEL)
    add GRAVITY             ; Increase velocity downward
    cp MAX_FALL             ; Cap falling speed
    jr c, .saveVel
    ld a, MAX_FALL    
.saveVel:
    ld (JUMP_VEL), a

    ; Check ground collision
    ld a, (KIRBY_Y)
    cp GROUND_Y
    jr c, .done             ; If above ground, keep going
    ld a, GROUND_Y          ; Snap to ground
    ld (KIRBY_Y), a
    ld a, 0                 ; Reset to idle
    ld (KIRBY_STATE), a
    ld a, 0                 ; Reset velocity
    ld (JUMP_VEL), a
.done:
    ret

DrawKirby:
    ; Move Kirby’s sprite in OAM (Object Attribute Memory)
    ld hl, $FE00            ; OAM start
    ld a, (KIRBY_Y)         ; Y position
    ld (hl), a
    inc hl
    ld a, (KIRBY_X)         ; X position
    ld (hl), a
    inc hl
    ld a, 0                 ; Tile number (assume Kirby’s sprite is tile 0)
    ld (hl), a
    inc hl
    ld a, 0                 ; Attributes (no flip/priority)
    ld (hl), a
    ret

EnableInterrupts:
    ld a, $01               ; Enable VBlank interrupt
    ld ($FFFF), a           ; Interrupt enable register
    ei                      ; Enable interrupts globally
    ret

; VBlank Interrupt Handler
.org $0040                  ; VBlank vector
    push af
    push hl
    ; Screen updates happen here in real code (DMA OAM transfer)
    pop hl
    pop af
    ret

