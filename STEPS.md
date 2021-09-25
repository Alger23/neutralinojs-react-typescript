  1. Create neu project

  ```
  neu create demo
  ```

  2. Create React Project with name `temp`
 
  ```
  npx create-react-app temp --template typescript
  ```
  or
  ```
  yarn create react-app temp --template typescript
  ```

  > I will merge `demo` and `temp` folders with some files modify.

  3. Merge `README.md` and `.gitignore`
  
  There are `README.md` and `.gitignore` same name in `demo` and `temp` folders. After modify files I will move `temp/*` into `demo/`. So I made change on `temp` folder.
 
  Combine `Readme.md`

  ```
  cat demo/README.md >> temp/README.md 
  ```

  Combine `.gitignore`
  ```
  cat demo/.gitignore >> temp/.gitignore
  ```

  Movie `temp/*` into `demo/` folder
  ```
  mv temp/* demo/
  # In macOS will not move .* files, So I need next command to move .gitignore
  mv temp/.gitignore demo/
  rm -rfd temp
  ```

  Now, we have one folder combine neu and react. Then change directory to demo.

  ```
  cd demo
  ```

  4. Move `resources/*` to `public/` 
  
  Neutralinojs default config url is `/resources/` folder in `neutralino.config.json`. I will not change it, so I have to change the file path of build react path to /resources.
  
  Move files in `resources/` to `public/`, but exclude `index.html`

  ```
  mv resources/icons public/
  mv resources/js public/
  mv resources/styles.css public/ 
  ```

  There already has `public/index.html`, it is entry target to start the react app. 
  
  I will use react `public/index.html` as entry target. I just copy some lines from `demo/resources/index.html` into `demo/public/index.html`.
   
  In this sample code I add `+` symbol as change line copy from `demo/resources/index.html`.

  *demo/public/index.html* full content after modified.
  ```
  <!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <link rel="icon" href="%PUBLIC_URL%/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="theme-color" content="#000000" />
    <meta
      name="description"
      content="Web site created using create-react-app"
    />
    <link rel="apple-touch-icon" href="%PUBLIC_URL%/logo192.png" />
    <!--
      manifest.json provides metadata used when your web app is installed on a
      user's mobile device or desktop. See https://developers.google.com/web/fundamentals/web-app-manifest/
    -->
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json" />
    <!--
      Notice the use of %PUBLIC_URL% in the tags above.
      It will be replaced with the URL of the `public` folder during the build.
      Only files inside the `public` folder can be referenced from the HTML.

      Unlike "/favicon.ico" or "favicon.ico", "%PUBLIC_URL%/favicon.ico" will
      work correctly both with client-side routing and a non-root public URL.
      Learn how to configure a non-root public URL by running `npm run build`.
    -->
    <title>React App</title>

+    <link rel="stylesheet" href="styles.css">

  </head>
  <body>
+    <div id="neutralinoapp">
+      <h1>NeutralinoJs</h1>
+      <div id="info"></div>
+      <br/>
+      <div>
+        <a href="#" onclick="window.myApp.openDocs();">Docs</a> &middot;  
+        <a href="#" onclick="window.myApp.openTutorial();">Tutorial</a>
+      </div>
+    </div>
+    <!-- Neutralino.js client. This file is gitignored, 
+        because `neu update` typically downloads it.
+        Avoid copy-pasting it. 
+        -->
+    <script src="js/neutralino.js"></script>
+    <!-- Your app's source files -->
+    <script src="js/main.js"></script>
    
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
    <!--
      This HTML file is a template.
      If you open it directly in the browser, you will see an empty page.

      You can add webfonts, meta tags, or analytics to this file.
      The build step will place the bundled scripts into the <body> tag.

      To begin the development, run `npm start` or `yarn start`.
      To create a production bundle, use `npm run build` or `yarn build`.
    -->
  </body>
</html>
  ```

  5. Change react build path to `./resources`
  
  Edit `package.json`
  configure react build path.
  ```
    "scripts": {
  +    "build": "BUILD_PATH='./resources' react-scripts build",
    },
  ```

  or

  ```
  # .env 
  BUILD_PATH='./resources' 
  ```

  Set git ignore `resources` folder if you have use git.
  ```
  echo "/resources" >> .gitignore
  ```

  Now you can run `npm run build` or `yarn build`. It will build react app into `./resources` folder.
  

  ---
  **Important warning**

  When writing a BUILD_PATH make sure that the path you write doesn't exist or you don't mind be erased. Doing `BUILD_PATH='..'` made some serious damage to progress I made on my project. (It deleted git as well.)

  ---


  6. Modify `./neutralino.config.json`

  Change Cli clientLibrary path to `/public/js/neutralino.js`. 
  When your use `neu update` command, it will update or restore neutralino.js.

  ```
  {
  ...
  "url": "/resources/",
  ...
  },
  "modes": {
    "window": {
      ...
      "title": "demo",
      ...
      "icon": "/resources/icons/appIcon.png",
      ...
    },
    "browser": {},
    "cloud": {}
  },
  "cli": {
    "binaryName": "demo-neu",
    "resourcesPath": "/resources/",
    "clientLibrary": "/public/js/neutralino.js",
    "binaryVersion": "2.8.0",
    "clientVersion": "1.5.0"
  }
  }
  ```

  Run neu app
  
  ```
  neu run
  ```

  > Ensure you run `yarn build` or `npm run build` before `neu run`.
  

  If you are running on macOS, the window will close after it is displayed. There may be a problem with setTray().

  Add check OS is not macOS then setTray().

  *./public/js/main.js*
  ```
    setTray: () => {
        if(NL_MODE != "window") {
            console.log("INFO: Tray menu is only available in the window mode.");
            return;
        }
        let tray = {
            icon: "/resources/icons/trayIcon.png",
            menuItems: [
                {id: "VERSION", text: "Get version"},
                {id: "SEP", text: "-"},
                {id: "QUIT", text: "Quit"}
            ]
        };
+        if(NL_OS!='Darwin'){
            Neutralino.os.setTray(tray);
+        }
    },
  ```

  it should be run success

  7. Edit `./package.json` scripts

  * homepage: './' will be served relative to index.html
  * modify scripts for use.

  ```
  {
  "name": "demo-neu",
  "version": "0.1.0",
+  "homepage": "./",
 "private": true,
 
  "scripts": {
+    "react-start": "react-scripts start",
+    "react-build": "BUILD_PATH='./resources' react-scripts build",
+    "react-test": "react-scripts test",
+    "react-eject": "react-scripts eject",
+    "start": "yarn react-build && neu run",
+    "build": "yarn react-build && neu build"
    },
  ```

  You can run

  ```
  yarn react-build
  neu run
  ```

  will build react project in `./resources` folder 
  then run neu app

  or just run `yarn start`. It will run `yarn react-build && neu run`


## References

* [Create React App - Advanced Configuration](https://create-react-app.dev/docs/advanced-configuration/)