# Console system calls

### System call 100 - Initialization

Initializes console. Should be called as soon as game is loading and player has spawned.

**State 100 - Initialization**

    //init player 0 convienence variable
    T <= player in slot(team all, 0)
    
    //Look at black background
    start camera(T, {camera position}, {camera position} + down)
    
    //assuming throttle inputs still register while rooted
    set stauts(T, rooted, {max time})
    
    //assuming rooted stops throttle inputs.
    //will require an additional system to lock in place (or just hope they don't fall off a map)
    set throttle(T, {very low max throttle})
    
    //lock inputs
    {repeat for all inputs}
    enable button(T,jump,false)
    
    //Create error display
    create hud text(
        visible: filtered array(all players, M > 0)
        Header: "Error: {M}"
        position: Left
        Color: Red
        sort: -99
    )
    
    //init color count
    G <= [0,0,0,0,0]
    
    //init vram offset
    D <= (0,0)
    
    //init input array
    I <= [0,0,0,0,0,0,0,0,0,0,0]
    
    //finish init
    state <= -1
    T:state <= T:A
    

### System call 200 - Init screen

Initializes screen based on global variable E - Display settings. Should be called whenever any change has been made to E.

**State 200 - Init screen start**
    
    W <= E[1] //set W to spacing for quick access
    U <= E[0] * W //set U to screen dimensions
    V <= {camera position} //start V at camera
    V += down * E[2] //move V away from camera
    V += left * (X component(U) / 2) // move V to x starting position
    V += forward * (y component(U) / 2) //move V to Y starting position
    
    U <= 0 //set index
    X <= x component(E[0]) //get stride of screen
    Y <= X * y component(E[0]) //get total screen elements
    
    //init screen positions to empty array
    N <= []
    
    state <= 210
    
**State 210 - Init screen loop**
    
    // add screen position
    append N <= V + right*(U % X) + down*(round down(U / X))
    U += 1
    
    wait 0
    loop if (U < Y) //loop until all screen positions created
    
    state <= -1
    T:Z <= T:A //finish system call

### System call 300 - Create effects

Initializes effects based on global variable G - Color count array. Should be called when any change has been made to G.

If the sum of all color counts is greater than 64, M will be set to error code 100 and no effects will be changed.

**state 300 - effect creation start**
    
    //check we aren't making too many effects
    U = G[0]
    U += G[1]
    U += G[2]
    U += G[3]
    U += G[4]
    
    skip if ( U <= 64) {
        M <= 100 //set error code
        abort
    }
    
    destory all effects()
    
    state <== 310
    
**state 310 - create white effects**

    skip(64 - G[0])
    (repeat 63 to 0)
    create effect(
        type: sphere
        color: white
        position: O[{index}]
        size: F
        reeavaluate: all
    )
    state <= 311
    
 **state 311 - create blue effects**

    skip(64 - G[1])
    (repeat 63 to 0)
    create effect(
        type: sphere
        color: blue
        position: P[{index}]
        size: F
        reeavaluate: all
    )
    state <= 312
    
**state 312 - create red effects**

    skip(64 - G[2])
    (repeat 63 to 0)
    create effect(
        type: sphere
        color: red
        position: Q[{index}]
        size: F
        reeavaluate: all
    )
    state <= 311
    
**state 313 - create purple effects**

    skip(64 - G[3])
    (repeat 63 to 0)
    create effect(
        type: sphere
        color: purple
        position: R[{index}]
        size: F
        reeavaluate: all
    )
    state <= 311
    
**state 314 - create green effects**

    skip(64 - G[4])
    (repeat 63 to 0)
    create effect(
        type: sphere
        color: green
        position: S[{index}]
        size: F
        reeavaluate: all
    )
    
    state <= -1
    T:state <= T:A

### System call 400 - Redraw screen

Redraws the screen based on the current contents of A - VRAM, B - pallet, C - VRAM width/height, and D - VRAM x/y offset.

If the total number of required effects for any color is greater than the amount of effects for that color that
exist according to G - Color count array, M will be set to one of the following error codes:

    200 - too many white
    201 - too many blue
    202 - too many red
    203 - too many purple
    204 - too many green

If more than one color has too many rendered, the highest error code will be set.

**state 400 - start redraw effects**

    //seperate offset into temp variables for convienence
    X <= x component(D)
    Y <= y component(D)
    
    //Save vram and display stride for convienence
    U <= x component(C)
    V <= x component(E[0])
    
    state <= 401
    
**state 401 - redraw white effects**

    O <= filtered array(
        array: N
        condition:
            value in array(
                array: B
                index: value in array(
                    array: A
                    index: add(
                        value 1: add(modulo(index of value(N, cu),V),X)
                        value 2: multiply(
                            value 1: add(rount down(devide(index of value(N, cu),V)),Y)
                            value 2: U
                        )
                    )
                )
            ) == 1 
            
    state <= 402
            
**state 402 - redraw blue effects**

    P <= filtered array(
        array: N
        condition:
            value in array(
                array: B
                index: value in array(
                    array: A
                    index: add(
                        value 1: add(modulo(index of value(N, cu),V),X)
                        value 2: multiply(
                            value 1: add(rount down(devide(index of value(N, cu),V)),Y)
                            value 2: U
                        )
                    )
                )
            ) == 2
            
    state <= 403
            
**state 403 - redraw red effects**

    Q <= filtered array(
        array: N
        condition:
            value in array(
                array: B
                index: value in array(
                    array: A
                    index: add(
                        value 1: add(modulo(index of value(N, cu),V),X)
                        value 2: multiply(
                            value 1: add(rount down(devide(index of value(N, cu),V)),Y)
                            value 2: U
                        )
                    )
                )
            ) == 3
            
    state <= 404
            
**state 404 - redraw purple effects**

    R <= filtered array(
        array: N
        condition:
            value in array(
                array: B
                index: value in array(
                    array: A
                    index: add(
                        value 1: add(modulo(index of value(N, cu),V),X)
                        value 2: multiply(
                            value 1: add(rount down(devide(index of value(N, cu),V)),Y)
                            value 2: U
                        )
                    )
                )
            ) == 4
            
    state <= 405
            
**state 405 - redraw green effects**

    S <= filtered array(
        array: N
        condition:
            value in array(
                array: B
                index: value in array(
                    array: A
                    index: add(
                        value 1: add(modulo(index of value(N, cu),V),X)
                        value 2: multiply(
                            value 1: add(rount down(devide(index of value(N, cu),V)),Y)
                            value 2: U
                        )
                    )
                )
            ) == 5
    
    state <= -1
    T:state <= T:A
