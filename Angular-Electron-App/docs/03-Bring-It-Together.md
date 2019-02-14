# Bringing It All Together

Right now we can only run our Angular application in a browser by opening http://localhost:4200. To enable our Electron app to use angular we need to make some changes.

The starting point of any Electron application is to create a main.js file. This is the file that will get our app up and running using some default settings.

Create a main.js file in the root of your project alongside your package.json etc. Copy and paste the following code to your newly created file.

```javascript
const {app, BrowserWindow} = require('electron')
const path = require('path')
const url = require('url')

// Keep a global reference of the window object, if you don't, the window will
// be closed automatically when the JavaScript object is garbage collected.
let win

function createWindow () {
    // Create the browser window.
    win = new BrowserWindow({width: 800, height: 600})
    // load the dist folder from Angular
    win.loadURL(url.format({
        pathname: path.join(__dirname, 'dist/index.html'),
        protocol: 'file:',
        slashes: true
    }))

    // Open the DevTools optionally:
    // win.webContents.openDevTools()

    // Emitted when the window is closed.
    win.on('closed', () => {
        // Dereference the window object, usually you would store windows
        // in an array if your app supports multi windows, this is the time
        // when you should delete the corresponding element.
        win = null
    })
}

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// Some APIs can only be used after this event occurs.
app.on('ready', createWindow)

// Quit when all windows are closed.
app.on('window‐all‐closed', () => {
    // On macOS it is common for applications and their menu bar
    // to stay active until the user quits explicitly with Cmd + Q
    if (process.platform !== 'darwin') {
        app.quit()
    }
})

app.on('activate', () => {
    // On macOS it's common to re-create a window in the app when the
    // dock icon is clicked and there are no other windows open.
    if (win === null) {
        createWindow()
    }
})
```

The above code is taken from the official Electron [Quick Start Doc]("https://electronjs.org/docs/tutorial/quick-start") with a minor modification to load our angular app from it's output directory.

Next we need to make an amendment to our Angular apps index.html.

```html

<!‐‐ CHANGE FROM: ‐‐>
<base href="/">

<!‐‐ TO: ‐‐>
<base href="./">

```

Without this change Electron will not be able to find our Angular app files.

Lastly we need to make some minor changes to our package.json file as shown below:

```json

// Other properties removed for brevity

"main": "main.js", // Add this line
"scripts": {

    // Other properties removed for brevity

    "electron": "ng build && electron .",
    "electron‐aot": "ng build ‐‐prod && electron .",
},

// Other properties removed for brevity

}

```

The first property which we are adding is `main`, and has a value of `main.js` the file Electron uses.

The other two properties are commands launch by npm run:

- electron : Uses Angular CLI to build our app and then launches our Electron app.
- electron-aot : This adds the `--prod` flag to our Angular build. This should be used for production ready deployment.
