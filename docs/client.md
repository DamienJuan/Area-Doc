Part 3: Client
==============

Description
===========
To have a clear and ergonomic game we made the user interface with Raylib and stick to the original design of R-Type.
We will describe below how our GUI works.

Main menu
=========
First of all, a main menu will appear with the possibility to play alone or with friends.

![](/assets/menu.png)

```Cpp
  Scene Menu::loop()
  {
      this->music->play();
      this->music->update();
      Scene tmp = this->inputHandler();
      if (tmp != MENU)
          return tmp;
      if (*this->currentScene == MENU) {
          switch (this->selectionIndex)
          {
              case 0:
                  DrawTextEx(this->font.GetFont() , "SinglePlayer", (Vector2){ (float)(this->screenSize.width / 1.25), (float)(this->screenSize.height / 10) }, this->font.GetFont().baseSize*0.5f, (float)2, WHITE);
                  DrawTextEx(this->font.GetFont() , "MultiPlayer", (Vector2){ (float)(this->screenSize.width / 1.25), (float)(this->screenSize.height / 7.5) }, this->font.GetFont().baseSize*0.5f, (float)2, RTYPEBLUE);
                  DrawTextEx(this->font.GetFont() , "Options", (Vector2){ (float)(this->screenSize.width / 1.25), (float)(this->screenSize.height / 6) }, this->font.GetFont().baseSize*0.5f, (float)2, RTYPEBLUE);
                  DrawTextEx(this->font.GetFont() , "How to Play", (Vector2){ (float)(this->screenSize.width / 1.25), (float)(this->screenSize.height / 5) }, this->font.GetFont().baseSize*0.5f, (float)2, RTYPEBLUE);
                  DrawTextEx(this->font.GetFont() , "Quit", (Vector2){ (float)(this->screenSize.width / 1.25), (float)(this->screenSize.height / 4.2) }, this->font.GetFont().baseSize*0.5f, (float)2, RTYPEBLUE);
                  break;
          }
          (*this->textures)["logo"].draw(this->screenSize.width / 1.45, this->screenSize.height / 1.7, WHITE);
      }
      return MENU;
  }
```

And here is the UML class diagram to represent the menu's structure.

![](/assets/diagram_icore.png)

The complete game
=================
In the game, you control a spaceship trying to dodge lasers and destro aliens.
You can gain power ups.
The solo version of the game aim to reach the end by defeating the final boss while the multiplayer is based on rounds to score the maximum points and go as far as possible.

![](/assets/game.png)

![](/assets/final_boss.png)

This UML diagram shows how the player interact with entities :


![](/assets/Game/actor.png)

    The graphical user interface is in permanent waiting of a command.
![](/assets/Game/server_client.png)

Architecture for client networking :
![](/assets/Client/class_model.png)
