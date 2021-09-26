
```
neu version
```

```
  _   _            _             _ _             _     
 | \ | | ___ _   _| |_ _ __ __ _| (_)_ __   ___ (_)___ 
 |  \| |/ _ \ | | | __| '__/ _` | | | '_ \ / _ \| / __|
 | |\  |  __/ |_| | |_| | | (_| | | | | | | (_) | \__ \
 |_| \_|\___|\__,_|\__|_|  \__,_|_|_|_| |_|\___// |___/
                                              |__/     
--- Global ---
Neutralinojs binaries: v2.8.0
Neutralinojs client: v1.5.0
neu CLI: v6.2.0
INFO Run this command inside your project directory to get project specific Neutralinojs version.
```

react version information

```
  "dependencies": {
    "@testing-library/jest-dom": "^5.11.4",
    "@testing-library/react": "^11.1.0",
    "@testing-library/user-event": "^12.1.10",
    "@types/jest": "^26.0.15",
    "@types/node": "^12.0.0",
    "@types/react": "^17.0.0",
    "@types/react-dom": "^17.0.0",
    "react": "^17.0.2",
    "react-dom": "^17.0.2",
    "react-scripts": "4.0.3",
    "typescript": "^4.1.2",
    "web-vitals": "^1.0.1"
  },
```


### Create neu project

```
neu create demo
```

### Create React Project with name `temp`

```
npx create-react-app temp --template typescript
```
or
```
yarn create react-app temp --template typescript
```

> I will merge `demo` and `temp` folders with some files modify.


### Merge `README.md` and `.gitignore`

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

### Move `resources/*` to `public/` 

Neutralinojs default config url is `/resources/` folder in `neutralino.config.json`. I will not change it, so I have to change the file path of build react path to /resources.

Move files in `resources/` to `public/`, but exclude `index.html`

```
mv resources/icons public/
mv resources/js public/
mv resources/styles.css public/ 
```

There already has `public/index.html`, it is entry target to start the react app. 

React use `public/index.html` as entry target. I just copy some lines from `demo/resources/index.html` into `demo/public/index.html`.
 
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

### Change react build path to `./resources`

With react-scripts >= 4.0.2, this is [officially supported](https://create-react-app.dev/docs/advanced-configuration):

Edit `package.json`, configure react build path.

```
// package.json
  "scripts": {
+    "build": "BUILD_PATH='./resources' react-scripts build",
  },
```

or adding a .env file to the root of your project:

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


### Change Cli.clientLibrary path 

Modify `./neutralino.config.json`

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

### Edit `./package.json` scripts

* homepage: './' will be served relative to index.html
* modify scripts for use.

```
{
  "name": "demo-neu",
  "version": "0.1.0",
+ "homepage": "./",
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

With react-scripts >= 4.0.2, this is [officially supported](https://create-react-app.dev/docs/advanced-configuration):

>By default, Create React App will output compiled assets to a `/build` directory adjacent to `/src`. You may use this variable to specify a new path for Create React App to output assets. `BUILD_PATH` should be specified as a path relative to the root of your project.




## References

* [Use custom build output folder when using create-react-app - stackoverflow.com](https://stackoverflow.com/questions/41495658/use-custom-build-output-folder-when-using-create-react-app/62378644#62378644)


