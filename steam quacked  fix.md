## This guide will show you how to run a steamworks fix in linux with Steam and Proton.

This guide assumes basic knowledge of Linux,Steam and SteamTinkerLaunch.

   1. Install the game by any means ex: using Lutris or wine(standalone) to install it.
   2. Install Steam Tinker Launch as a compatibility tool in steam. Guide to add the tool.
   3. In the terminal run “steamtinkerlaunch addnonsteamgame” without quotes, this will open tinkerlaunch’s GUI to add a game.
   4. Fill the app name, the exe path and the start dir(start dir is important for some games that report missing assets without it, usually it’s the same folder where the game’s exe is located).
   5. Restart Steam so the game shows up.
   6. In Steam select the new game entry and go to it’s properties.
   7. Force a compatibility tool and select steamtinkerlaunch.
   8. Start the game, when a popup appears(That’s the tinkerlaunch GUI) select “Main Menu”.
   9. In the “Main Menu” press “GAME MENU”
   10. Scroll down to the botton until you see “WINE DLL OVERRIDES”.
   11. In the Text Box place the dll’s used by the fix in this manner ex: “OnlineFix64=n;SteamOverlay64=n;winmm=n,b;dnet=n;steam_api64=n” without quotes.
   12. Press “Save and Play” and your game will boot up with the fix active.

## BONUS RUNNING TRAINERS INSIDE A STEAMWORKS FIX:

1. Open the “Game Menu” inside steamtinkerlaunch GUI and in the misc options(right at the top) there should be a custom command section.
2. Select “Use Custom Command” and in the text box select the trainer.exe or cheat engine.exe.
3. Select the option “Inject Custom Command”
4. Press “Save and Play” and your trainer should pop up with the game.

EDIT: It’s also be possible to skip steamtinkerlaunch and just use the options to launch the game as normal from Steam, but this aproach will not let you run trainers for your games!

    ``WINEDLLOVERRIDES=“OnlineFix64=n;SteamOverlay64=n;winmm=n,b;dnet=n;steam_api64=n” %command%``

In STEAM launch options for the game but SteamTinker let’s us well…tinker alot more like running trainers and stuff.

EDIT 2: IF THE GAME STILL TAKES YOU TO THE STORE OR REFUSES TO OPEN STEAMTINKERLAUNCH, PLEASE VERIFY THAT A FILE CALLED “steam_api.txt” IS NOT IN THE GAME’S FOLDER, IF IT IS REMOVE IT.
