# Metin2 Stone Bot

**This project was made only for educational and learning purposes.**

Project built using .NET desktop application and EmguCV library (.NET wrapper for OpenCV image processing library). Main functionality of this project is to automate farming of stones in game Metin2. This bot does not use code injection but relies on image processing algorithms, so its not dependent on any libraries used by game client.

### How metin stones work
Metin stones are objects that spawn at random places throughout the world. They have certain amount of hit points which needs to be decreased to 0 for stone to be destroyed. Destroying stone yields items which are dropped on the ground.

### User interface

<img src="https://github.com/AndrejVysinsky/metin2-stone-bot-readme/assets/59775817/c649e3de-91cd-426b-86f2-288416f2be4c" width="300">
 
1.	Status information. When running provides additional information.
2.	Metin level selection. Switches between color filters to help distinguish metin stones.
3.	Show/hide configuration windows needed for bot to function properly.
4.	Controls to start/stop the program.

### Configuration

<img src="https://github.com/AndrejVysinsky/metin2-stone-bot-readme/assets/59775817/774f48a9-3892-4c5c-a7f4-9a7037c44464">
 
Stone bot offers multiple configuration windows to help distinguish between different parts of the user interface and speed up processing time. Two main windows consist of metin area and name.

Metin area window represents part of the screen where the main image filtering is performed to find metin stones. This area should not contain UI elements to improve scanning speed.

Some parts of the environment may be similar to metin stones which could make bot stuck and move in wrong direction. Name window helps resolve this issue. When player clicks on metin stone or other entity (player/enemy), its name is shown at the top of the screen. We can use this to make sure we clicked on metin stone by reading its name using OCR algorithms. 

<img src="https://github.com/AndrejVysinsky/metin2-stone-bot-readme/assets/59775817/d68802e0-c1c2-4ece-9ad5-fdcf880895a6" width="400">

This game does not offer any pathfinding and its very easy to get stuck on parts of the environment as the colliders for random clutter on the ground are larger than their model. Bot has an internal clock which takes average time to destroy a metin and if current time is significantly larger than average, we can say with certainty that we got stuck. There are 3 windows to help with this issue. Port1, port2 and port indicator.

Port1 and port2 contain scroll of teleportation with 2 different locations which are used by bot to reset player position and continue with execution. Teleportation takes some amount of time depending on PC bot is running on and Metin2 servers. Port indicator windows checks for UI elements if they are present on the screen or if loading screen is still displayed.

Besides the main functionality, bot provides features for rebuffing with switching gear and titles to maximize buff value. For this, buffs window is used and regularly checked if character buffs are applied. If not, bot switches gear, switches titles and buffs player.
Bot also regularly checks if thief’s gloves (regular + golden) are equipped to maximize loot yield.

### Scanning metin stones

Metin stone are scanned using image processing algorithms. First the picture is taken, and color mask is extracted depending on metin level selected in application.
We then perform connected component analysis on extracted color mask. This analysis helps us partition an image into distinct regions or components, where each component corresponds to a connected group of pixels.
After that the bounding boxes are displayed on the screen representing where metin stones are located. However not every bounding box can be a metin stone. Sometimes parts of environment are similar color as metin stone and get through the filtering into final scan result.
All scan results are ordered by distance from player and checked for entity name. If entity name contains “metin”, bot starts destroying it and waits certain amount of time. Then collects loot and repeats the process again.

![scanning gif](https://github.com/AndrejVysinsky/metin2-stone-bot-readme/assets/59775817/f9251a62-fac6-41c3-ba60-2ac795ee4e83)

### Captcha solver

<img src="https://github.com/AndrejVysinsky/metin2-miner-readme/assets/59775817/71d808c4-77b3-42b6-8fc9-463a3eea0860" width="200">

Server Stellaria uses captcha system to prevent players from using auto clickers or some other automation. Captcha system consists of image with 3 random numbers and numpad to enter the numbers from image. Captcha is displayed every 15 minutes and failing to complete it disconnects player from server.

Captcha solving is not happening in this application as OCR algorithms by default are not built for it and were failing reading the numbers most of the time. Instead, application sends image to backend where we are calling AZCaptcha API (https://azcaptcha.com/) which solves the image for us. Result is sent back to the client and numbers are automatically inputted.

### Showcase

[![Stone bot](https://img.youtube.com/vi/nYjPW6Mf3tA/0.jpg)](https://www.youtube.com/watch?v=nYjPW6Mf3tA)
