# Purpose

To test the ability of giving formatted output of mini-GPT4, chatGPT 3.5, chatGPT 4. Since our research need the formatted output of LLM, I believe the research is useful.

# Method
Design some prompts, and input to the 3 models above, observe the results.

# Test cases
The test cases are some prompts that ask the LLM to give formatted output.

1. Robot Commands （DEMO）
    
    **System:** 
    
    You are a robot command interpreter, responsible for translating the natural language to robot command script. 

    Here are the robot command script format:

    \# 1. Every command has two parts: command name and arguments. Use "::" between the command name and arguments. Use "," to split each argument.
    
    \# 2. The script should starts with start command "START::" and ends with end command "END::"

    \# 3. One command occupy one line, comment should also be in the dependent line.

    \# 4. Comment line should start with "//", just like single line comment in C and C++.

    Here are the legal commands list, every command are quoted in [] and with explanation behind:

    \# [ START:: ] the start command, takes no argument, means the script starts here.

    \# [ END:: ] the end command, takes no argument, means the script ends here.

    \# [ GO::\<direction\>,\<distance\> ] the moving command, ask the robot to move along a direction for given distance. The direction should be one of [Forward, Right, Left, Back], the distance unit is meter. When using this command, the robot will translate (means shift) to the distination, the orientation of the head does not change.

    \# [ TAG:: ] put a tag in current piosition, takes no argument.

    \# [ TURN::\<direction\> ] the turning command, ask the robot's head turns to a new direction. The direction should be one of [Right, Left, Back], Considering the current facing direction as forward, to turn back means to turn to the opposite direction of the current one, to turn left means to turn to the left of the current direction, and to turn right means to turn to the right of the current direction.

    You need to obey the rules:

    \# 1. Only output the robot commands list and legal comment, make sure your output can run as a script directly. 

    \# 2. If there is any natural language content such as comment, explanation content, should be in the comment part.

    \# 3. If you think you cannot translating the command successfully, output an empty command and explan it in comment.

    \# 4. If the user didn't mention about "turn to some direction and go forward" or "shift to some direction", use turning and going forward as default instead of shifting.

    Here are some input and output examples:
    
    \```

    [User]: 
    
    Please go forward for 2 meters and turn back for 1.5 meters
    
    [Output]: 
    
    START::

    GO::Forward,2

    //Turn back

    TURN::Back

    GO::Forward,1.5

    END::

    \```

    \```

    [User]:

    Please shift right for 2 meters and shift back for 3 meters.

    [Output]:

    START::

    //Go right for 2 meters

    GO::Right,2

    GO::Back,3

    END::

    \```

    **User Prompts**

    1. Go forward for 1 meter, go back for 2 meters.
    
    2. Go forward for 2 meters, turn left and go forward for 0.7 meters, and shift right for 2 meters.

    3. Walk a square with 3-meter side length.

    4. Turn left and walk 5 meters, then turn back and walk 2 meters, and repeat for 3 times.

    5. Try walk a circle-like path, with radius of 3 meters.

2. JSON format

    **System**

    You are a JSON formatter AI assistant that helps people to answer questions with JSON format.

    You should answer every quesion using JSON.

    Here are the rules:

    \# 1. Your whole answer should be a JSON object.

    \# 2. Your answer should at least contains 2 parts: Answer and Explanation.

    \# 3. For the Answer part, you should put a sub json object to describe the answer.

    \# 4. For the Explanation part, you should try explaning the answer in natural language in 1~3 sentence brefily.

    Some examples:

    \```

    User: Tom has 3 apples and 2 oranges, Jimmy gives him 3 more apples and 2 penciles, how many of each kind fruits does Tom has now?

    Assiatant: {
        "Answer": {
            "Apple":6,
            "Orange":2
        },
        "Explanation":"A pencile is not fruit, so pencile is not calculated in."
    }

    \```

    \```

    User: Jack is an engineer, David is a soilder, Lily used to be a marketing manager but now she's a lawyer. what's the jobs now of each person above?

    Assiatant: {
        "Answer": [
            {
                "name":"Jack",
                "job":"engineer"
            },
            {
                "name":"David",
                "job":"soilder"
            },
            {
                "name":"Lily",
                "job":"lawyer"
            }
        ],
        "Explanation":"Lily isn't a marketing managet anymore."
    }

    \```

    **User prompts**
    
    1. Tom is 18 years old, Shawn is 5 years older than Tom, Tim is 3 years younger than Shawn. How old is each person above?  

    2. I have a Macbook pro, which values 3000 dollars. I use it for video editing and programming. I also have a iPad, I can do simple video editing work by iPad but cannot do programming work on it. What can I do with the two devices above?

    3. Tony has a box, in the box there is a pen, a can of cola, an envelope, inside the envelope there is a card-pocket, inside the card-pocket there is a bank card, a credit card, a passport. There is two photos sandwiched in the passport. Describe what Tony have and the containing relationship. 


# Expreiment Env & Results

We use GPT3.5-turbo, GPT-4 and miniGPT-4 deploied by Xiaofan to run the test cases.

The Model Tempature is set to be 0.2 .
For GPT3.5-turbo and GPT4, the top-p is 0.95 .
For miniGPT, the top-p cannot be adjusted.

Since we need to use the "system" prompt to set the preset instruction, and the chatGPT website version doesn't support inputing the "system" prompt. The results of GPT 3.5 and 4 are executed by API. 
And for the mini-GPT, there is no api, so we input the "system" prompt as a user prompt and add a sentence "After you read and understood the instructions, say "ok" to me." 
Also, miniGPT4 requires a picture before start chatting, we used a blank 300*300 picture of pure white in order to minimium the influence from the picture. 

-----

## Robot Commands

### Q1: Go forward for 1 meter, go back for 2 meters.

**GPT3.5-Turbo**

```
START::
// Move forward for 1 meter
GO::Forward,1
// Move back for 2 meters
GO::Back,2
END::
```
**GPT-4**

```
START::

GO::Forward,1

GO::Back,2

END::
```

**miniGPT-4**

```
START::
GO::Forward,1
GO::Back,2
END::
```

### Q2: Go forward for 2 meters, turn left and go forward for 0.7 meters, and shift right for 2 meters.

**GPT-3.5-Turbo**

```
START::

GO::Forward,2

//Turn left

TURN::Left

GO::Forward,0.7

//Shift right for 2 meters

GO::Right,2

END::
```

**GPT-4**

```
START::

GO::Forward,2

TURN::Left

GO::Forward,0.7

GO::Right,2

END::
```

**miniGPT-4**
```
START:: 
GO::Forward,2 
//Turn left 
TURN::Left 
GO::Forward,0.7 
//Shift right 
SHIFT::Right 
END::
```

### Q3: Walk a square with 3-meter side length.

**GPT-3.5-Turbo**
```
// Start at the first corner of the square facing forward
START::
// Move forward for 3 meters
GO::Forward,3
// Turn right to face the second corner of the square
TURN::Right
// Move forward for 3 meters
GO::Forward,3
// Turn right to face the third corner of the square
TURN::Right
// Move forward for 3 meters
GO::Forward,3
// Turn right to face the fourth corner of the square
TURN::Right
// Move forward for 3 meters
GO::Forward,3
// End at the first corner of the square facing forward
END::
```

**GPT-4**
```
START::

// Walk the first side of the square
GO::Forward,3

// Turn right to walk the second side of the square
TURN::Right
GO::Forward,3

// Turn right to walk the third side of the square
TURN::Right
GO::Forward,3

// Turn right to walk the fourth side of the square
TURN::Right
GO::Forward,3

END::
```

**miniGPT-4**

```
To walk a square with a 3-meter side length, you can use the following commands:

START::
GO::Forward,3
GO::Right,3
GO::Forward,3
GO::Left,3
GO::Forward,3
GO::Right,3
GO::Forward,3
END::

This will make the robot walk along the sides of a square with a 3-meter side length.
END::
```

### Q4: Turn left and walk 5 meters, then turn back and walk 2 meters, and repeat for 3 times.

**GPT-3.5-Turbo**
```
START::

//Turn left
TURN::Left

//Walk 5 meters forward
GO::Forward,5

//Turn back
TURN::Back

//Walk 2 meters forward
GO::Forward,2

//Repeat for 2 more times
TAG::
GO::Left,5
TURN::Back
GO::Forward,2
TAG::
GO::Left,5
TURN::Back
GO::Forward,2
TAG::
GO::Left,5
TURN::Back
GO::Forward,2

END::
```

**GPT-4**
```
START::

// Repeat 3 times
// First iteration
TURN::Left
GO::Forward,5
TURN::Back
GO::Forward,2

// Second iteration
TURN::Left
GO::Forward,5
TURN::Back
GO::Forward,2

// Third iteration
TURN::Left
GO::Forward,5
TURN::Back
GO::Forward,2

END::
```

**miniGPT-4**
```
START::
TURN::Left
GO::Forward,5
TURN::Back
GO::Forward,2
TURN::Left
GO::Forward,5
TURN::Back
GO::Forward,2
TURN::Left
END::
```

### Q5: Try walk a circle-like path, with radius of 3 meters.

**GPT-3.5-Turbo**
```
START::

// Move forward for 3 meters
GO::Forward,3

// Turn right and move forward for 6.28 meters (2pi3)
TURN::Right
GO::Forward,6.28

// Turn right and move forward for 6.28 meters
TURN::Right
GO::Forward,6.28

// Turn right and move forward for 6.28 meters
TURN::Right
GO::Forward,6.28

// Turn right and move forward for 6.28 meters
TURN::Right
GO::Forward,6.28

END::
```

**GPT-4**
```
// A circle-like path cannot be accurately represented with the available commands.
// However, we can approximate it by moving in small increments and turning slightly.
// This script will approximate a circle with a radius of 3 meters.

START::

// Approximate circle with 36 segments
// Each segment length: 2 * pi * 3 / 36 = 0.5236 meters
// Each turn angle: 360 / 36 = 10 degrees

// Repeat 36 times
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right
GO::Forward,0.5236
TURN::Right

END::
```

**miniGPT-4**
```
START:: 
GO::Forward,3 
//Turn right 
TURN::Right 
GO::Forward,3 
//Turn left 
TURN::Left 
GO::Forward,3 
//Turn right 
TURN::Right 
GO::Forward,3 
//Turn left 
TURN::Left 
GO::Forward,3 
END::
```

## JSON format

### Q1: Tom is 18 years old, Shawn is 5 years older than Tom, Tim is 3 years younger than Shawn. How old is each person above?

**GPT3.5-Turbo**
```
{
    "Answer": {
        "Tom": 18,
        "Shawn": 23,
        "Tim": 20
    },
    "Explanation": "Shawn is 5 years older than Tom, and Tim is 3 years younger than Shawn."
}
```

**GPT-4**
```
{
  "Answer": [
    { "name": "Tom", "age": 18 },
    { "name": "Shawn", "age": 23 },
    { "name": "Tim", "age": 20 }
  ],
  "Explanation": "Shawn is 5 years older than Tom (18+5=23), and Tim is 3 years younger than Shawn (23-3=20)."
}
```

**miniGPT-4**
```
{ “Answer”: { “Tom”:18, “Shawn”:23, “Tim”:15 }, “Explanation”:“Tom is 18 years old, Shawn is 5 years older than Tom, which is 23 years old, and Tim is 3 years younger than Shawn, which is 15 years old.” }
```

### Q2: I have a Macbook pro, which values 3000 dollars. I use it for video editing and programming. I also have a iPad, I can do simple video editing work by iPad but cannot do programming work on it. What can I do with the two devices above?

**GPT3.5-Turbo**
```
{
    "Answer": {
        "Macbook Pro": {
            "Value": 3000,
            "Usage": ["Video Editing", "Programming"]
        },
        "iPad": {
            "Value": "Unknown",
            "Usage": ["Simple Video Editing"]
        },
        "Recommendation": "Use Macbook Pro for video editing and programming tasks, and use iPad for simple video editing tasks on-the-go."
    },
    "Explanation": "Macbook Pro is suitable for heavy-duty tasks like video editing and programming, while iPad can be used for simple video editing tasks when you are not at your desk."
}
```

**GPT-4**
```
{
  "Answer": {
    "Macbook Pro": ["Video Editing", "Programming"],
    "iPad": ["Simple Video Editing"]
  },
  "Explanation": "You can do video editing and programming on your Macbook Pro, while you can only do simple video editing on your iPad."
}
```

**miniGPT-4**
```
{ “Answer”: { “Macbook Pro”:3000, “iPad”:1000 }, “Explanation”:“A Macbook Pro is worth 3000 dollars and can be used for video editing and programming. An iPad can be used for simple video editing, but it is not suitable for programming work.” }
```

### Q3: Tony has a box, in the box there is a pen, a can of cola, an envelope, inside the envelope there is a card-pocket, inside the card-pocket there is a bank card, a credit card, a passport. There is two photos sandwiched in the passport. Describe what Tony have and the containing relationship.  

**GPT3.5-Turbo**
```
{
"Answer": {
"Box": {
"Contains": {
"Pen": 1,
"Can of Cola": 1,
"Envelope": {
"Contains": {
"Card-Pocket": {
"Contains": {
"Bank Card": 1,
"Credit Card": 1,
"Passport": {
"Contains": {
"Photos": 2
}
}
}
}
}
}
}
}
},
"Explanation": "Tony has a box containing a pen, a can of cola, and an envelope. The envelope contains a card-pocket, which contains a bank card, a credit card, and a passport. The passport contains two photos."
}
```

**GPT-4**
```
{
  "Answer": {
    "Box": {
      "Pen": {},
      "Cola": {},
      "Envelope": {
        "Card-pocket": {
          "Bank card": {},
          "Credit card": {},
          "Passport": {
            "Photos": 2
          }
        }
      }
    }
  },
  "Explanation": "Tony has a box containing a pen, cola, and an envelope. Inside the envelope, there's a card-pocket with a bank card, credit card, and a passport. The passport has two photos sandwiched in it."
}
```

**miniGPT-4**
```
{
“Tony”: {
“Box”: {
“Pen”: true,
“Can of Cola”: true,
“Envelope”: {
“Card-Pocket”: {
“Bank Card”: true,
“Credit Card”: true,
“Passport”: {
“Photos”: [
{
“descriptions”: “sandwiched in the passport”
}
]
}
}
}
}
}
```









    


     
    

