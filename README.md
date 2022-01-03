# Tutorial: Make WebXR Games With A-Frame

![Breakout WebXR animation](img/breakoutWebXR_.gif)

## Introduction:

WebXR is a JavaScript API for creating virtual reality and augmented reality experiences for the web. A user with a WebXR headset can go to this web page and use the app. This article walks you through how to write a WebXR game using JavaScript and Mozilla's A-Frame library. The game works on WebXR devices like Oculus Rift or HTC Vive.

I wrote the original version of this tutorial about 5 years ago. Back then the A-Frame library was very experimental and the term "WebXR" didn't exist yet. The original tutorial did not include a controller, the user played the game by moving their head around. The A-Frame library and WebXR evolved an incredible amount since then. This update to the tutorial brings it up to the current stable release of A-Frame (version 1.2.0). The code was re-written to take advantage of many paradigms that now exist in A-Frame, such as event listeners and controllers. The game made in this tutorial requires a WebXR capable devices with a controller. The device I used to test this code is an HTC Vive Flow.

## Step 1: Create your HTML

WebXR apps get accessed over the internet as web pages. For that reason the first step in the process is to create a basic HTML page.

    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>Breakout VR Tutorial</title>
    <meta name="description" content="Breakout VR Tutorial">
    </head>
    <body>
    </body>
    </html>

## Step 2: Add the A-Frame library

A-Frame is a JavaScript library which makes it easier to create WebXR apps. Add this using a <script> tag inside the <head> section of the HTML.

    <!-- Basic A-Frame library -->
    <script src="https://aframe.io/releases/1.2.0/aframe.min.js"></script>

## Step 3: Set up Game Elements

Most of the elements in our game will be dynamic and change as part of the game. Some elements will not move or have very little interaction. We will add elements inside the <body> tag of the HTML using special tags specific to the A-Frame library.

The a-scene element has to be in the body of the HTML. All the other A-Frame elements will get placed inside of it.

    <a-scene>	
    </a-scene>	

Mixins are a way of defining attributes for a group of objects without declaring the attributes on each of those objects.

Instead of using Mixins you could define the attributes directly. This doesn’t seem super useful to me, but it’s part of the A-Frame framework, so I included it in this tutorial.

    <!-- Mixins. -->
    <a-assets>
      <a-mixin id="red" material="color: red"></a-mixin>
      <a-mixin id="green" material="color: green"></a-mixin>
      <a-mixin id="blue" material="color: blue; opacity: 0.5"></a-mixin>
      <a-mixin id="url-red" material="color: #d63959"></a-mixin>
      <a-mixin id="cube" geometry="primitive: box"></a-mixin>
    </a-assets>

To help the player see the game area we are going to place flat planes behind and below the game area. These go in the <body> section. 

    <!-- set game background planes. -->
    <a-plane position="0 0 -3" rotation="-90 0 0" width="4" height="8" color="#a0a0a0"></a-plane>
    <a-plane position="0 2 -5" rotation="0 0 0" width="4" height="4" color="#bfabce"></a-plane>

We could set the scene background to a flat color. A 360 degree image looks more interesting, so I added in an example image provided by Mozilla. This is done with an element called <a-sky>.

    <!-- sky color. -->
    <a-sky src="https://cdn.aframe.io/360-image-gallery-boilerplate/img/city.jpg"></a-sky>

We need to specify where the camera is at. To do this we add an A-Frame camera tag. 

We will set wasd-controls to disabled. This prevents the player from moving the camera away from the game board. If we do not disable this then the player can move around the scene.

We will also included a setting for laser-controls. This will allow the user to interact with the app using a laser style VR controller.

    <!-- Set camera and controller starting position. -->
    <a-entity position="0 0 3.8">
      <a-camera look-controls wasd-controls="enabled: false"></a-camera>
      <a-entity laser-controls="hand: right"></a-entity>
    </a-entity>

There will be some text that we display to the user. First there will be some text that shows “Start Game”. This will default to visible (opacity of 1) and disappear when the game starts. 

Next there will be text displayed as “Game Over” if the user loses. This element starts out hidden (opacity of 0) when the game begins 

There will be text displayed as “You Win” if the user wins. This element also starts out hidden (opacity of 0) when the game begins. 

We will have text that shows how many “lives” the player has. This text will change as the game goes on.

We will have text that shows the “score” of the player. This text will also change as the game goes on. 

Each of these have a unique “id” attribute. The "id" attribute gets used to change the element with JavaScript. 

The “Start Game” will have also have a special attribute, so that we can add A-Frame listeners to it. We will name this attribute “handle-start”. This is a unique name that we made up for this particular element.

    <!-- set a plane to track where the user is pointing -->
    <a-plane id="moveTracker" color="#FFFFFF" rotation="0 0 0" position="0 0 -1.6" opacity="0" width="20" height="20" cursor-listener></a-plane>

There will be some text that we display to the user. First there will be some text that shows "Start Game". This will default to visible (opacity of 1) and disappear when the game starts.

Next there will be text displayed as "Game Over" if the user loses. This element starts out hidden (opacity of 0) when the game begins

There will be text displayed as "You Win" if the user wins. This element also starts out hidden (opacity of 0) when the game begins.

We will have text that shows how many "lives" the player has. This text will change as the game goes on.

We will have text that shows the "score" of the player. This text will also change as the game goes on.

Each of these have a unique "id" attribute. The "id" attribute gets used to change the element with JavaScript.

The "Start Game" will have also have a special attribute, so that we can add A-Frame listeners to it. We will name this attribute "handle-start". This is a unique name that we made up for this particular element.

    <!-- Start Game text -->
    <a-entity id="startGameText" text="font: mozillavr; width: 5; lineHeight: 50; letterSpacing: 5; color: green; value: Start" position="2.2 2 0.5" rotation="0 0 0" handle-start></a-entity>

    <!-- Game Over text -->
    <a-entity id="gameOverText" text="opacity: 0; font: mozillavr; width: 5; lineHeight: 50; letterSpacing: 5; color: red; value: Game Over" position="1.9 2.5 0.5" rotation="0 0 0"></a-entity>

    <!-- You Win text -->
    <a-entity id="youWinText" text="opacity: 0; font: mozillavr; width: 5; lineHeight: 50; letterSpacing: 5; color: green; value: You Win" position="2 2.5 0.5" rotation="0 0 0"></a-entity>

    <!-- Player Lives text -->
    <a-entity id="livesText" text="font: mozillavr; width: 5; lineHeight: 50; letterSpacing: 5; color: blue; value: Lives: 3" position="4 3.8 -0.8" rotation="0 0 0"></a-entity>

    <!-- Score text -->
    <a-entity id="scoreText" text="font: mozillavr; width: 5; lineHeight: 50; letterSpacing: 5; color: blue; value: Score: 0" position="0 3.8 -0.8" rotation="0 0 0"></a-entity>

We will also add a paddle and a ball. These will be more interactive.  The paddle will be a rectangular box which the user can move back and forth. The ball will bounce around in the play area. These each have a unique ID attribute and also unique A-Frame handler attributes.

    <!-- Add the game paddle -->
    <a-box id="gamePaddle" color="#42f4aa" position="0 0.3 -1" depth="0.2" height="0.2" width="1" handle-paddle></a-box>

    <!-- Add the game ball -->
    <a-sphere id="gameBall" color="#FFFFFF" radius="0.15" position="0 1.25 -1" handle-ball></a-box>

We could specify a light source to influence how the game elements appear. I couldn't tell much of a difference with the light source specified, so I left this out. Refer to A-Frame's documentation if you would like to specify the light source location(s) for your app.

We have most of our game elements defined. We want to also have rows of blocks that the game ball will break when it hits them. We could have defined these game blocks in the HTML like we did with the game paddle. We will add these elements through JavaScript to show how that to do it that way.

First we will add a script section to the HTML. JavaScript code will go in this. We will initialize arrays of variables. These will store information about the game blocks. In programming best practices these variables would go inside functions and get passed as parameters. This is game is a simple app though, so we will use them as top level variables for simplicity's sake.

    <script>		
      //initialize variables 
      
      //arrays to hold the blocks and their positions
      let gameBlocks = []; //array of objects
      let gameBlocksX = []; //X dimensions of the blocks
      let gameBlocksY = []; //Y dimensions of the blocks
      let gameBlocksZ = []; //Z dimensions of the blocks
      let gameBlocksActive = []; //whether the block is active      
      let blockWidth = 0.8; //how wide blocks are in the X dimension
      let blockHeight = 0.2; //how tall blocks are in the Y dimension
      let blockDepth = 0.2; //how deep blocks are in the Z dimension
      let blockColor = '#4CC3D9'; //the default color of the blocks (later this was changed to be dynamically generated)

    </script>

Next we will add a window.onload function in the JavaScript. Any code we put inside this function will run after the page is ready. Inside this function we are going to put code that appends our game blocks to the scene. In A-Frame we add boxes with a-box elements. We use JavaScript’s document.getElementById() method to identify the scene. Then we use JavaScript’s appendChild method to add the game blocks.

    //wait until the page loads to perform the following
    window.onload = function (){
      //create the game blocks

      //declare the blocks and their attributes
      for (i = 0; i < 12; i++) 
      {
        gameBlocks[i] = document.createElement('a-box');
        gameBlocks[i].setAttribute('width', blockWidth);
        gameBlocks[i].setAttribute('height', blockHeight);
        gameBlocks[i].setAttribute('depth', blockDepth);   
        blockColor = '#' + parseInt(Math.random() * 0xffffff).toString(16);
        gameBlocks[i].setAttribute('color', blockColor);
        gameBlocksActive[i] = "1";
      }

      //set the position of the blocks

      //Top row
      gameBlocksX[0] = -1.5;
      gameBlocksY[0] = 3.5;
      gameBlocksZ[0] = -1;

      gameBlocksX[1] = -0.5;
      gameBlocksY[1] = 3.5;
      gameBlocksZ[1] = -1;

      gameBlocksX[2] = 0.5
      gameBlocksY[2] = 3.5;
      gameBlocksZ[2] = -1;

      gameBlocksX[3] = 1.5;
      gameBlocksY[3] = 3.5;
      gameBlocksZ[3] = -1;

      //Middle row
      gameBlocksX[4] = -1.5;
      gameBlocksY[4] = 3;
      gameBlocksZ[4] = -1;

      gameBlocksX[5] = -0.5;
      gameBlocksY[5] = 3;
      gameBlocksZ[5] = -1;

      gameBlocksX[6] = 0.5
      gameBlocksY[6] = 3;
      gameBlocksZ[6] = -1;

      gameBlocksX[7] = 1.5;
      gameBlocksY[7] = 3;
      gameBlocksZ[7] = -1;

      //Bottom row
      gameBlocksX[8] = -1.5;
      gameBlocksY[8] = 2.5;
      gameBlocksZ[8] = -1;

      gameBlocksX[9] = -0.5;
      gameBlocksY[9] = 2.5;
      gameBlocksZ[9] = -1;

      gameBlocksX[10] = 0.5
      gameBlocksY[10] = 2.5;
      gameBlocksZ[10] = -1;

      gameBlocksX[11] = 1.5;
      gameBlocksY[11] = 2.5;
      gameBlocksZ[11] = -1;

      //add the blocks to the scene
      let scene = document.getElementById("scene"); //assign a name to the A-Frame scene
      for (i = 0; i < 12; i++) 
      {
        scene.appendChild(gameBlocks[i]);
        gameBlocks[i].setAttribute('position', gameBlocksX[i] + ' ' + gameBlocksY[i] + ' ' + gameBlocksZ[i]);
      }
    }

## Step 4: Add Sound

We want the game to have some sound effects, so we will initializze audio files.

    //initialize sound
    let soundWarp = new Audio('warp-sfx-6897.mp3');
    let soundImpact = new Audio('electronic-impact-soft-10019.mp3');
    let soundChime = new Audio('chime-sound-7143.mp3');

## Step 5: Add JavaScript Game Logic

Now we are going to add a bunch of functions to the code. These functions are pieces of the code that will run many times.

Note: In JavaScript there are lots of different ways to define a function. For example, you will sometimes see people write stuff like this: 

    const myFunctionName () => {}

I learned to program in C++ and I like to use that style of function definition:

    function myFunctionName() {}

Whatever style you like to use will work fine.

First we will add a function that stops playing sounds. Later we will call this to stop sounds before a new sound needs to play.

    //stop all of the sounds
    function stopAllSounds(){
      soundWarp.pause();
      soundImpact.pause();
      soundChime.pause();
      soundWarp.currentTime = 0;
      soundImpact.currentTime = 0;
      soundChime.currentTime = 0;
    }

Next we will have a function to detect if the player has broken all the blocks. We will use this to check if the player won the game.

    //function to check if all blocks are broken, return true if so
    function checkBlocks(){
      let returnValue = 1;
      for (i = 0; i < 12; i++){
        if(gameBlocksActive[i] == "1")
          returnValue = 0;
      }
      return returnValue;
    }

We will add a function which will move the ball. Moving the ball happens by adding the ball’s velocity to the coordinates. Later we will will update the position attribute of the ball with the new coordinates.

    function moveBall(){
      //move the game ball
      gameBallX = gameBallX + gameBallVelocityX;
      gameBallY = gameBallY + gameBallVelocityY;
    }

We will add a function to reset the location of the ball. To make the game more interesting we will randomize the X dimension of the game ball each time. Note how we use the setAttribute method to change information about the element.

    //function to reset the ball position
    function resetBall(){
      gameBallX = Math.floor(Math.random() * ((rightBorder - 0.5) - (leftBorder + 0.5) + 1)) + (leftBorder + 0.5);
      gameBallY = 1.25;
      gameBallZ = -1;
      gameBallVelocityX = 0.045;
      gameBallVelocityY = 0.075;
      gameBall.setAttribute('position', gameBallX + ' ' + gameBallY + ' ' + gameBallZ);
    }

We will add a function to update the colors of the paddle. If the player’s laser cursor points at the paddle then we will change the color. I initially designed the game so that the player would grab the cursor and drag it back and forth. I later found that using the point without grabbing made for a better experience. I left the grab coloring in this code to show how to do it though, because it seems like it might be useful for other apps.

    function updatePaddle(){
      if(boxGrabbed == true){
        gamePaddle.setAttribute('color', "#FFFF00");   
      } else if(boxHovered == true){
        gamePaddle.setAttribute('color', "#FF0000");                       
      } else {
        gamePaddle.setAttribute('color', "#0000FF");
      }
    }

We will add a function to reset all the blocks. This will happen if the player starts a new game. The blocks don’t go anywhere when broken. Instead we will hide them by changing the opacity to 0. This function changes the opacity back to 1 to make them visible again. To make the game more interesting the blocks get assigned random colors.

    //function to reset the blocks
    function resetBlocks(){
      for (i = 0; i < 12; i++) 
      {
        gameBlocksActive[i] = "1";
        let blockColor = '#' + parseInt(Math.random() * 0xffffff).toString(16);
        gameBlocks[i].setAttribute('color', blockColor);
        gameBlocks[i].setAttribute('opacity', '1');
      }
    }

We will add a function to check for collisions of the game ball with different game elements. There is at least one open source library for detecting collisions between A-Frame elements. I found that library to be a bit complex for our simple app though. It turned out to easier to roll my own collision detection code. This next function checks for collisions.

    function checkCollisions(){
      let startGameText = document.getElementById('startGameText');
      //checking border collisions
      if(gameBallY >= topBorder){
        gameBallVelocityY = gameBallVelocityY * -1; //make the ball bounce
        gameBallY = gameBallY - 0.1; //to help prevent ball getting stuck
      }
      if(gameBallY <= bottomBorder){
        gameBallVelocityY = gameBallVelocityY * -1; //make the ball bounce
        gameBallY = gameBallY + 0.1; //to help prevent balls getting stuck

        if(gameIsOn == 1){ //if the user is playing
          livesValue = livesValue - 1; //remove a life
          let livesText = document.getElementById('livesText');
          livesText.setAttribute('text', 'font: mozillavr; width: 5; lineHeight: 50; letterSpacing: 5; color: blue; value: Lives: ' + livesValue); //update the life text
          resetBall(); //reset the ball's location so it doesn't get stuck
          stopAllSounds();
          soundImpact.play(); //play a sound

          if(livesValue == 0){ //if the player runs out of lives
            //turn the game off
            gameIsOn = 0;

            //display the Game Over text by setting the opacity to 1
            let gameOverText = document.getElementById('gameOverText');
            gameOverText.setAttribute('text', 'opacity: 1; font: mozillavr; width: 5; lineHeight: 50; letterSpacing: 5; color: red; value: Game Over');

            //reset the blocks
            resetBlocks();

            //display the Start text by changing the opacity to 1
            startGameText.setAttribute('text', 'opacity: 1; font: mozillavr; width: 5; lineHeight: 50; letterSpacing: 5; color: green; value: Start');
          }
        }
      }
      if(gameBallX >= rightBorder){
        gameBallVelocityX = gameBallVelocityX * -1; //make the ball bounce
      }
      if(gameBallX <= leftBorder){
        gameBallVelocityX = gameBallVelocityX * -1; //make the ball bounce
      }
      
      //checking block collisions
      //for each block
      for (i = 0; i < 12; i++){	
        //block collisions
        if((((gameBallY + (gameBallRadius * .8)) >= (gameBlocksY[i] - blockHeight)) && ((gameBallY - (gameBallRadius * .8)) <= gameBlocksY[i])) && ((gameBallX + (gameBallRadius * .8)) >= (gameBlocksX[i]))  && ((gameBallX - (gameBallRadius * .8)) <= (gameBlocksX[i] + blockWidth)) && (gameBlocksActive[i] == "1")){

          gameBallVelocityY = gameBallVelocityY * -1; //make the ball bounce

          gameBlocksActive[i] = "0"; //mark the block as broken
          gameBlocks[i].setAttribute('opacity', '0'); //hide the block

          if(checkBlocks()){ //if all of the blocks are broken
            resetBlocks(); //reset the blocks
            resetBall(); //reset the ball position
          }

          if(gameIsOn == 1){
            scoreValue = scoreValue + 1; //increase the player's score
            let scoreText = document.getElementById('scoreText');
            scoreText.setAttribute('text', 'font: mozillavr; width: 5; lineHeight: 50; letterSpacing: 5; color: blue; value: Score: ' + scoreValue); //update the score text
            stopAllSounds();
            soundChime.play(); //play a sound 

            if(scoreValue == 12){ //if the player broke all of the blocks
              gameIsOn = 0; //turn off the game
              //display the You Win text by changing the opacity to 1
              let youWinText = document.getElementById('youWinText');
              youWinText.setAttribute('text', 'opacity: 1; font: mozillavr; width: 5; lineHeight: 50; letterSpacing: 5; color: green; value: You Win');
              //display the Start text by changing the opacity to 1
              startGameText.setAttribute('text', 'opacity: 1; font: mozillavr; width: 5; lineHeight: 50; letterSpacing: 5; color: green; value: Start');
            }
          }
        }
      }

      //checking paddle collisions		
      if( ((gameBallY + (gameBallRadius * .8)) >= (gamePaddleY - gamePaddleHeight)) && ((gameBallY - (gameBallRadius * .8)) <= (gamePaddleY) && ((gameBallX + (gameBallRadius * .8)) >= (gamePaddleX - gamePaddleWidth * .5))  && ((gameBallX - (gameBallRadius * .8)) <= (gamePaddleX + gamePaddleWidth *.5)))){
        gameBallVelocityY = gameBallVelocityY * -1; //make the ball bounce
      }
    }

The functions above need to interact with certain information throughout the game.  We will add variables to track that information used by the game code. I declared these as top level variables due to the simple nature of the app. (In large or complex apps you would pass this information back and forth to each function as parameters.)

    let gameIsOn = 0; //whether the game is active, controls certain functionality
    let intervalLength = 25; //determines the speed of the game
    let topBorder = 3.5; //border of game area in the Y dimension
    let bottomBorder = 0.25; //border of game area in the Y dimension
    let rightBorder = 1.8; //border of game area in the X dimension
    let leftBorder = -1.8; //border of game area in the X dimension
    let scoreValue = 0; //keeps 
    let boxGrabbed; // whether or not the user grabbed the box (the user doesn't drag the box in the final version, but I left this in for illustration) 
    let boxHovered; // whether or not the user is hovering over the box
    let livesValue = 3; // how many lives the player has
    let gamePaddleX = 0; //where the game paddle is in the X dimension
    let gamePaddleY = 0.3; //where the game paddle is in the Y dimension
    let gamePaddleZ = -1; //where the game paddle is in the Z dimension
    let gamePaddleWidth = 1; //how wide the game paddle is in the X dimension
    let gamePaddleHeight = 0.2; //how tall the game paddle is in the Y dimension
    let gamePaddleDepth = 0.2; //how deep the game paddle is in the Z dimension
    let gameBallX = 0; //the position of the game ball in the X dimension
    let gameBallY = 1.25; //the position of the game ball in the Y dimension
    let gameBallZ = -1; //the position of the game ball in the Z dimension
    let gameBallVelocityX = 0.045; //how fast the game ball is moving in the X dimension
    let gameBallVelocityY = 0.075; //how fast the game ball is moving in the Y dimension
    let gameBallRadius = 0.15; //how fast the game ball is moving in the Z dimension

## Step 6: Add A-Frame JavaScript Code

Next we will add A-Frame listeners to the interact game components. These listeners define how the user can interact with the game.

The first handler will add Event Listeners to the game paddle. We added a unique attribute called “handle-paddle” to the game paddle element in the HTML. In A-Frame we register that as a component and then add event listeners to it.

We will detect when the user points the laser cursor at the game paddle. For that we add “raycaster-intersected”. When this happens we will set the boxHovered variable to true and call the updatePaddle() function. This will change the color of the game paddle.

We will also detect if the user “grabs” the paddle, by holding down the controller button while pointing at the paddle. (This “grab” feature isn’t actually used to move the paddle in this final version, but it seems like a useful feature so I left the code for in for illustration.)

    AFRAME.registerComponent('handle-paddle', {
      init: function () {
        let el = this.el;
        
        el.addEventListener('mousedown', function (evt) {
          boxGrabbed = true;
        });
        
        el.addEventListener('mouseup', function (evt) {
          boxGrabbed = false;
        });     
        
        el.addEventListener('raycaster-intersected', evt => {  
          this.raycaster = evt.detail.el;
        });
        this.el.addEventListener('raycaster-intersected-cleared', evt => {
          this.raycaster = null;
        });
      },
      tick: function () {
        if (!this.raycaster) { 
          boxHovered = false;
          updatePaddle();
          return; 
        }// Not intersecting.
        let intersection = this.raycaster.components.raycaster.getIntersection(this.el);
        if (!intersection) { 
          boxHovered = false;
          updatePaddle();
          return; 
        } // Not intersecting
        // intersecting
        boxHovered = true;
        updatePaddle();
      } 
    });

Next we will register a component and add listeners to detect where the laser cursor is pointing. For the mechanics of this game we want to find where the laser cursor intersects a plane that the paddle can move on. We added an invisble (opacity 0) plane with a unique attribute called “cursor-listener”. If the laser cursor intersects this plane, we will get the X coordinates of that intersection. If the game is on then we will also move the paddle to that location.

    AFRAME.registerComponent('cursor-listener', {
      init: function () {
        this.el.addEventListener('raycaster-intersected', evt => {
          this.raycaster = evt.detail.el;
        });
        this.el.addEventListener('raycaster-intersected-cleared', evt => {
          this.raycaster = null;
        });
      },
      tick: function () {
          if (!this.raycaster) { 
            return; 
          }// Not intersecting.
          let intersection = this.raycaster.components.raycaster.getIntersection(this.el);
          if (!intersection) { 
            return; 
          } // Not intersecting
          // intersecting
          // move box if the game is running
          if(gameIsOn == 1){
            let gamePaddle = document.getElementById('gamePaddle');
            let tempY = gamePaddle.components.position.data.y;
            let tempZ = gamePaddle.components.position.data.z;
            let tempX = intersection.point.x;
            if(tempX < leftBorder){
              tempX = leftBorder + (gamePaddleWidth/2);
            }
            if(tempX > rightBorder){
              tempX = rightBorder - (gamePaddleWidth/2);
            }              
            gamePaddleX = tempX;
            gamePaddle.setAttribute('position', gamePaddleX + ' ' + tempY + ' ' + tempZ);
          }
      }
    });

After that, we want to detect when the user points the laser cursor at the “Start Game” text and clicks the controller button. Like before we do this by registering a component name which matches the unique attribute we game to the element. If the user points the laser cursor at the text, then we will change the color of the text. If the user clicks on the text while selecting it then it will start the game.

    AFRAME.registerComponent('handle-start', {
      init: function () {
        let el = this.el;
        let startGameText = document.getElementById('startGameText');

        el.addEventListener('mousedown', function (evt) {
          if(gameIsOn == 0){
            startGameText.setAttribute('text', 'opacity: 0; font: mozillavr; width: 5; lineHeight: 50; letterSpacing: 5; color: green; value: Start');

            //reset all game components
            resetBall();
            resetBlocks();

            //reset the score
            scoreValue = 0;
            livesValue = 3;

            //update the text
            let scoreText = document.getElementById('scoreText');
            scoreText.setAttribute('text', 'text: Score: ' + scoreValue);	
            let livesText = document.getElementById('livesText');
            livesText.setAttribute('text', 'text: Lives: ' + livesValue);
            //hide the Game Over text by setting the opacity to zero
            let gameOverText = document.getElementById('gameOverText');
            gameOverText.setAttribute('text', 'opacity: 0; font: mozillavr; width: 5; lineHeight: 50; letterSpacing: 5; color: red; value: Game Over');
            //hide the You Win text by setting the opacity to zero
            let youWinText = document.getElementById('youWinText');
            youWinText.setAttribute('text', 'opacity: 0; font: mozillavr; width: 5; lineHeight: 50; letterSpacing: 5; color: green; value: You Win');
            //hide the Start Game text by setting the opacity to zero
            startGameText.setAttribute('text', 'opacity: 0; font: mozillavr; width: 5; lineHeight: 50; letterSpacing: 5; color: green; value: Start');

            //set the game on flag
            gameIsOn = 1;
            
            //play the game start sound
            stopAllSounds();
            soundWarp.play();

          };            
        });
        
        el.addEventListener('raycaster-intersected', evt => {  
          startGameText.setAttribute('text', "font: mozillavr; width: 5; lineHeight: 50; letterSpacing: 5; color: #FF0000; value: Start;");
        });
        this.el.addEventListener('raycaster-intersected-cleared', evt => {
          startGameText.setAttribute('text', "font: mozillavr; width: 5; lineHeight: 50; letterSpacing: 5; color: green; value: Start"); 
        });
      }
    });

Finally we add a game loop. A game contains logic that gets run continuously as the game goes on. In A-Frame this is done using the tick function. I found that I had to add some additional code using A-Frame's throttle method. Without this code then the game would run faster on some devices and slower on others. This method evens out the speed so that it runs at about the same pace for all devices.

    AFRAME.registerComponent('handle-ball', {
      init: function () {
        this.throttledFunction = AFRAME.utils.throttle(this.gameLoop, gameLoopSpeed, this);
      },
      gameLoop: function () {
        checkCollisions(); //check to see if anything collided
        moveBall(); //update the X and Y coordinates of game objects 
        gameBall.setAttribute('position', gameBallX + ' ' + gameBallY + ' ' + gameBallZ); //reposition the ball
      },
      tick: function (t, dt) {
        this.throttledFunction();  // Called every frame.
      } 
    });

## Step 7: Play the Game

Now try out the game.  It has to run on a web server with HTTPS enabled. 

(Tip: If you want a web based tool for testing simple apps with, you could try it in Glitch.com. This is a popular tool for people experimenting with WebXR, because you don’t have to set up a web server.)

The steps to start the app are: 
1. Upload the code to a web server.
2. Put on your VR headset.
3. In your VR headset, use a Mozilla Reality Browser (or similar WebXR web browser).
4. Navigate to the web URL of your app.
5. You should now see the game in VR and be able to play it.

A demo of the app can be found here: https://www.mattnutsch.com/breakoutwebxr/

Full source code can be found here: https://github.com/mnutsch/BreakoutWebXRTutorial