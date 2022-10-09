This is a game in which rotating black/white squares appear in random coordinates of the screen.

To score points click on the white squares. All of your clicks on the screen leave a red "shadow". If a black square touches your shadow you lose the game.

So in essence the point of the game is to click on the white squares when they appear and in the meantime hide your "shadow" from the black ones by clicking on empty/corner areas of the screen. The game, of course, speeds up as time goes by in order to make your life miserable.
| Shortcode   | Description                                                                     | Example usage                                  | Result                                                                  |
| ------------| ------------------------------------------                                      | ------------------------------------------     |-------------------------------------                                    |
| productname | Resolves to "Gameface" or Prysm" (capitalizes as well)                          | Welcome to {{< productname >}}.                | Welcome to Gameface. // assuming product="gameface"                     |
|             |                                                                                 |                                                |                                                                         |
| if          | Shows or hides a part of the content, can depend on                             | {{< if "product" "prysm" >}}                   | The word mycontent or nothing, depending on the product we are building |
|             | product (Gameface/Prysm) or type (cxx/native)                                   | mycontent                                      |                                                                         |
|             |                                                                                 | {{< /if >}                                     |                                                                         |
| alert       | Creates a warning or a note, with a custom starting symbol, usually ">"         | {{< alert icon=">" context="warning" >}}Be very careful of this{{< /alert >}}|">Be very careful of this" with warning styling (i.e. yellow background)       |
| figure      |                                                                                 |                                                |              |
