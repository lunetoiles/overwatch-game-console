# Console variables

All variables below are global variables. Player variables are reserved for game logic, syscall arguments/return values, and game state.

## Read/write variables

    A - VRAM
    B - pallet
        0: blank
        1: white
        2: blue
        3: red
        4: purple
        5: green
    C - (VRAM width, VRAM height)
    D - (VRAM x offset, VRAM y offset)
    E - Display settings
        0: (Display width, Display height)
        1: Dot spacing
        2: Distance from camera
    F - Effect size
    G - Color count array [ white count, blue count, red count, purple count, green count ]
    H - 
    I - input array [ up, down, left, right, primary, secondary, jump, crouch, ultimate, ability 1, ability 2 ]
    J - 
    K -
    L -
    M - Error code
    
    Z - Console function call and state
    
## Internal variables

    N - Screen positions array
    O - White dot positions
    P - Blue dot positions
    Q - Red dot positions
    R - purple dot positions
    S - green dot positions
    T - player in index 0
    U through Y - intermdiate variables
    
# Console function calls

#### System call 100 - Initialization

Initializes console. Should be called as soon as game is loading and player has spawned.

#### System call 200 - Init screen

Initializes screen based on global variable E - Display settings. Should be called whenever any change has been made to E.

#### System call 300 - Create effects

Initializes effects based on global variable G - Color count array. Should be called when any change has been made to G.

If the sum of all color counts is greater than 64, M will be set to error code 100 and no effects will be changed.

#### System call 400 - Redraw screen

Redraws the screen based on the current contents of A - VRAM, B - pallet, C - VRAM width/height, and D - VRAM x/y offset.

If the total number of required effects for any color is greater than the amount of effects for that color that
exist according to G - Color count array, M will be set to one of the following error codes:

    200 - too many white
    201 - too many blue
    202 - too many red
    203 - too many purple
    204 - too many green

If more than one color has too many rendered, the highest error code will be set.
