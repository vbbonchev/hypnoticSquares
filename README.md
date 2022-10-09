This is a game in which rotating black/white squares appear in random coordinates of the screen.

To score points click on the white squares. All of your clicks on the screen leave a red "shadow". If a black square touches your shadow you lose the game.

So in essence the point of the game is to click on the white squares when they appear and in the meantime hide your "shadow" from the black ones by clicking on empty/corner areas of the screen. The game, of course, speeds up as time goes by in order to make your life miserable.                                                   

# Setup and workflow
For the setup, there is no need to install Hugo separately, since it should come naturally via `npm`. You can install it only if you want to use the `hugo` command directly from the CLI. You should, however, update your npm to the latest version, especially if you encounter any errors during installation.

There are a few ways to start using the docs, as explained below.
|                                      | documentation.py --run  | npm run start | hugo server start |
| -------------------                  | ----------------------- | ------------- | ----------------- |
| Generate API reference               | Yes                     | No            |        No         |
| Downloads Hugo and other dependencies| Yes                     | Yes           |        No         |
| Starts a server at `localhost:1313`  | Yes                     | Yes           |        Yes*       |
*Requires you to have Hugo in your` PATH` - the other commands use the npm-provided `hugo.exe` directly.

In other words, `python documentation.py --run` is the slowest command but it does everything, so it is preferable on first use. After using it once you can just navigate to `localhost:1313`(by default) and start changing the content of the docs, and the server will start live reloading your new content and showing it to you.
For subsequent developement, you can use the other commands, since they are faster and there is no need to regenerate the API reference or re-update your npm packages. 

# Developing documentation

## Adding a new page

### Create the page itself
There are two main options:
 1. Use the command `hugo new my_subfolder/my_page.md`. This will create a page with metadata written in it that you will need to configure afterward, for example like this:
    ```
    ---
    title:"My title"
    date: 2019-03-26T08:47:11+01:00
    draft: true
    ---
    ```
    ❗ You need to remove the `draft: true ` line or change it to `false` , otherwise your page will not appear in the docs. ❗

 2. Create a new page in `contents/my_subfolder/my_page.md` directly. This requires you to manually write the metadata at the start of your page, similar to the one above. This approach does not require you to have `hugo` in your `PATH` and does not fill your page with boilerplate metadata.

❗ If you want your page shown in native or Unity **only**, you should add the `_Native.md` or `_Unity.md` suffix to your filename. ❗

### Configure the page metadata
 Here is the "good" and "bad" metadata to have:
  - `title: (some string)` - **Definitely required**. As you can probably guess, otherwise your page will have no title.
  - `draft: true` - **not recommended to have this** in your page, unless you explicitly want your page to not appear in the docs.
  - `weight: (some integer)` - add this to your page **if** you want to control the ordering of the page. Lower weight means higher up in its section. Otherwise, your page will be placed based on its *alphabetical* order.
  - `date: (some date)` - this can be used for some settings that only show pages after/before a given date. In our infrastructure, this field is **not used** for now, so there is no need to have it.
  - `toc: false` - this will turn off the Table of Contents feature for your page only. You should generally **not use this**, unless the ToC is broken for some reason, or there is only a single heading in your page and a ToC is not required, etc.

You can find the full list of possible metadata [here](https://gohugo.io/content-management/front-matter/).

## Writing the page

### Content

Hugo supports the latest version of markdown, so you should have everything you need, syntax-wise. You can see the full specs on the [goldmark parser GitHub page](https://github.com/yuin/goldmark).
A table of contents will be generated automatically.

### References
References are just links to other pages. Hugo automatically finds the page you are referencing, based on its name. Links can contain anchors too.
Examples:
```
Go to [this]({{< ref "MyPage.md" >}}) page.
Go to [this]({{< ref "MyPage#anchor" >}}) anchor.
```

Note that anchors are automatically generated in our documentation for every section, based on the section name.

### Shortcodes
Shortcodes in Hugo are basically "functions" or "templates" that you can write beforehand, then use to insert or modify some content of your page. You can find a full explanation [here](https://gohugo.io/content-management/shortcodes/).
Below you can find some of the "main" shortcodes used in our docs. Their code is inside `/layouts/shortcodes/...` if you want to see them.
#### Productname
Resolves to "Gameface" or Prysm" (capitalized)

Example usage:
```
Welcome to {{< productname >}}.
```
Example result:
```
"Welcome to Gameface." or "Welcome to Prysm.", depending on the product we are building.
```
#### IF
Shows or hides a part of the content, can depend on product (Gameface/Prysm) or type (cxx/native).

Example usage: 
```
{{< if "product" "prysm" >}}
Some Prysm-specific content.
{{< /if >}
```
Example result:
```
The words "Some Prysm-specific content" or nothing, depending on the product we are currently building
```

#### Alert
Creates a warning or a note with a custom starting symbol, usually ">". You can control if you want a warning or a note with the "context" variable
Example usage: 
```
{{< alert icon=">" context="warning" >}}
Be very careful of this!
{{< /alert >}}
```
Example result:
```
">Be very careful of this!" with warning CSS styling (i.e. yellow background)
```
#### Figure
Creates an HTML `<figure>` (a fancy image with a caption) element and resolves the src of the image smartly (i.e. adds BaseURL, such as `/cxx-gameface/`, if needed).
Note that it is preferable to use this for images, instead of the basic `[title](image/link)`, since it is more flexible. It is preferable for images to be housed inside the `./static/` folder, so that they can be used in all translations of our documentation.

Example usage:
```
{{< figure src="/img/my-image.png" title="my-title" >}}
```
Example result
```
<figure>
  <img src="cxx-gameface/img/my-image.png">
  <figcaption>my-title</figcaption>
</figure>
```
#### Typeref
This is a special type of reference. It can resolve to different files, depending on the type of documentation we are building. The parameters are title/reference_if_native/reference_if_unity in that order.
Example usage:
```
Go {{< typeref "here" "Somepage_Native.md" "Someotherpage_Unity.md">}}.
```
Example result
```
Go [here]({{< ref "Somepage_Native.md" >}}). // if native docs
or
Go [here]({{< ref "Someotherpage_Unity.md" >}}) // if unity docs
```

# Generating documentation for the website
You can generate docs for the website using `python documentation.py --product [Gameface/Prysm] --type [cxx/unity3d] --domain [https://your.preferred.domain]`
The domain should be something like `https://coherent-labs.com/Documentation/` or similar. A suffix, such as `/cxx-gameface/` or `/unity-prysm/` will be appended to the domain automatically, depending on the type and product, and you should place the docs in the corresponding subfolder on the server (i.e. `server_root/Documentation/cxx-gameface/`).
The docs will be generated inside a similarly named subfolder in `repo_root/Documentation/`.
