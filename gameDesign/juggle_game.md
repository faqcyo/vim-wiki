[Back to index](index)

# Juggle Game GDD

[Reference](https://www.gamedeveloper.com/business/how-to-write-a-game-design-document)

1. [Characters](#1. Characters)
2. [Story](#2. Story)
    1. [Theme](#2.1 Theme)
3. [Story Progression](#3. Story Progression)
4. [Gameplay](#4. Gameplay)
    1. [Goals](#4.1 Goals)
    2. [User Skills](#4.2 User Skills)
    3. [Core Game Mechanics](#4.3 Core Game Mechanics)
    4. [Items and Powerups](#4.4 Items and Powerups)
    5. [Progression and Challenges](#4.5 Progression and Challenges)
    6. [Losing](#4.6 Losing)
5. [Art Style](#5. Art Style)
6. [Music and Sounds](#6. Music and Sounds)
7. [Technical Description](#7. Technical Description)
8. [Marketing and Funding](#8. Marketing and Funding)
    1. [Demographics](#8.1 Demographics)
    2. [Platforms and Monetization](#8.2 Platforms and Monetization)
    3. [Localization](#8.3 Localization)
9. [Other Ideas](#9. Other Ideas)

## 1. Characters

The main character is the player, which name will be asked the first
time they play (After the tutorial), from now on i will refer to it as
"player". A clown dealer (Kind of like a drug dealer but more friendly),
which is responsible for selling items and powerups to the player. A
rival that will make the impossible to sabotage all the efforts of the
player (TBD).

## 2. Story

The player is near homeless and the only skill he has is juggling. So he
embarks in the journey of being a professional juggler and impress
people around him.
An alternate story could be that the player is unemployed since the 
robot job revolution, and now he is going to try its hardest to entertain
robots and humans for a living, since its the only thing that the player
can do better than a robot.

### 2.1 Theme

At first i want to create the experience of 
**passion** and **hard work**. The player will have to go through difficult decisions that
could end up getting him homeless or succesful. After that initial part
i want to reflect a sense of **responsibility** and/or **extravagance** 
(depending of the user decision), giving the player options to spend all the 
money and fame he has, and return him to poverty, or keep pushing his carreer to the
limits.

## 3. Story Progression

The player will begin in a nearly abandoned and poor area. He will need
to surpass this by growing in popularity and gathering enough money to
move out. This will continue until the player gets to the final area,
where he has enough money and fame to do whatever he wants (or so he
thinks). (TODO Think of more areas) (TODO Think of how the player could
throw away all of his progress)

## 4. Gameplay

The gameplay will be centered on juggling. I've already made a prototype
in Godot based on what i had in mind. Its a simple drag and drop. You
touch and drag a falling ball repeatedly to create the experience of
juggling.

### 4.1 Goals

For the juggling gameplay the goal will be to get the most points. For
the game in general the goal is to get out of poverty and generate
revenue. Once that's done the end game consists of keep improving on
juggling and generate more money or just keep spending the money and
losing fame by not doing more juggling shows.

### 4.2 User Skills

What we expect the user to have is eye to finger coordination. The
simple mechanic of touch and drag is the basis for good foundation on
the juggling gameplay.

### 4.3 Core Game Mechanics

Touch the ball to grab it. Once the ball is grabbed by the user, the
user should drag and release the ball to generate momentum on it. Keep
doing it with whatever quantity of ball the player is able to keep up.

### 4.4 Items and Powerups

List of different Juggling Items: List of different Powerups inside the
Juggling activity: List of different locations where the player can move
to: List of different decorations for that location: List of different
decorations the player can put on his juggling items:

### 4.5 Progression and Challenges

Once the player dominates the juggling with one ball, the fame will stop
increasing at the rate that was increasing before, forcing the player to
increase the amount of balls he juggle with. Every juggling item will
have a difficulty tag on it that makes the player gain more or less fame
(depending on its difficulty). If the player chooses to increase the
amount of a juggling item, the **fame gained is doubled**.

### 4.6 Losing

The player loses in the juggling activity if one of the balls falls to
the floor. The player loses the game when he goes homeless. More
specifically, when he doesn't have more funds to pay rent and/or food to
survive.

## 5. Art Style

The main art style chosen to the game is **2D Pixel art**. The background resolution is
180x320px. Each scenario will have limited colorpalettes.

## 6. Music and Sounds

I was thinking of using chiptune music or calm classical music for the
main themes. To simplify the scope of the project, i will stay with
chiptune music for now. The different sounds is going to be determined.

## 7. Technical Description

The platforms that i'm thinking of launching my game is (ordered by
priority):

1.  Android
2.  Desktop (Windows, Linux, MacOS)
3.  iOS

I will prioritize the launching on android first though. I'm not going
to use any Engine. I want to make it purely on SDL2.

## 8. Marketing and Funding

I know it sounds stupid, but i dont plan on doing any marketing for now.
As the development settles i could setup a youtube channel to document
the process of creating a game with SDL on android. But for now, i keep
focus on the game.

### 8.1 Demographics

Age: 12-35 Sex: Everyone Casual Players

### 8.2 Platforms and Monetization

- Android (Google Play Store)
- Desktop (Itch.io)
- iOS (App Store)

For the monetization, the game will be free on all platforms. On mobile,
the game will contain non-intrusive ads, i.e. the user chooses to watch
an ad to get an award. For desktop, i can't put ads, so i will depend
entirely on what the people can contribute to the project through
itch.io donations and tips.

### 8.3 Localization

Initially only English/Spanish

## 9. Other Ideas

...
