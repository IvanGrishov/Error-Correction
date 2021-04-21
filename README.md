# Error Correction

### Error Correction is an embedded application that displays a hint when a user selects text on a page on a site to report an error. With an affirmative action, a form with the following fields opens:
* email
* message 

### The form is equipped with validation of the input data. Dispatch occurs to a pre-provided REST method. In addition to the above data, debugging information is attached to the message:
 * date and time
 * Sender IP address
 * device
 * browser (userAgent)
 * screen resolution
 * link to the current page
 * the selected piece of text that led to the call of the widget
 * position of the selection (coordinates in pixels)

####The application has a minimal set of dependencies in order to have a minimum of conflicts with the sites where this widget will be embedded.


[![ErrorApplication.gif](https://s3.gifyu.com/images/ErrorApplication.gif)](https://gifyu.com/image/tWtc)

## Connect to your project

```
<script src="https://grishov.ru/js/app.js"></script>
```

## Debug information to the console

```
<script src="https://grishov.ru/js/app.js?dbg"></script>
```

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```




### [Project on Github](https://github.com/IvanGrishov/Error-Correction).


