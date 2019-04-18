# Angular Express and Node CheatSheet

==============

Step-by-step guide:
--------------

Create file **server.js** and add the code below

```
var express = require('express');
var app = express();
const bodyParser = require('body-parser');
app.use(bodyParser.urlencoded({ extended: true }));
app.use(bodyParser.json());
const path = require('path');
const flash = require('express-flash');
app.use(flash());

app.use(express.static( __dirname + '/ANGULAR_APP_NAME/dist/public' ));

//some computer npm install bcryptjs, change required below to ('bcryptjs')
var bcrypt = require('bcrypt');
var session = require('express-session');
app.use(session({
    secret: 'keyboardkitteh',
    resave: false,
    saveUninitialized: true,
    cookie: { maxAge: 60000 }
  }))

//If you don't want to use Angular, just uncomment this and create folder views/index.ejs
// app.set('views', path.join(__dirname, './views'));
// app.set('view engine', 'ejs');
app.listen(8000, function(){
    console.log('listening at port 8000');
})

require('./server/config/mongoose');
require('./server/config/routes')(app);
```

Create **server** folder,

go inside **server** folder and create 3 folders: **config**, **controllers**, **models**

go inside **config** folder create 2 files: mongoose.js and routes.js

go inside **controllers** folder create file controllers.js

go inside **models** folder create file models.js

Add code below to file **mongoose.js**

```
var path = require('path');
var fs = require('fs')
var mongoose = require('mongoose');

mongoose.connect('mongodb://localhost/DATA_NAME_HERE'); //Just change database name here
let models_path = path.join(__dirname, './../models');

//iterates through the models folder and searches for all the model js files
fs.readdirSync(models_path).forEach(function(file){
    if(file.indexOf('.js')>=0){
        require(models_path + '/' + file);
    }
});

```

Add code below to file **routes.js**

```
var quotes = require('../controllers/controllers');

module.exports = function(app){
    app.get('/', function (req, res) {
        // This is where we will retrieve the users from the database and include them in the view page we will be rendering.
        quotes.index(req, res) ;
    })

    // Below is example of post method
    app.post('/btquote', function (req, res) {
        quotes.btquote(req, res);
    })
}
```

Add code below to file **controllers.js**

```
var mongoose = require('mongoose');
require('../models/models.js');

var Quote = mongoose.model('DATANAME'); //Any name is okay

module.exports={
    index: function(req, res){
        res.render('index');
    },
}
```

Add code below to file **models.js**

```
var mongoose = require('mongoose');

//Below is example create a schema
var QuoteSchema = new mongoose.Schema({
    name: { type: String, required: 'Your name must be longer than 2 characters', trim: true, minlength: 2},
    quote: { type: String, required: 'Quote must be longer than 10 characters', trim: true, minlength: 10 },
},{ timestamps: true })

mongoose.model('DATANAME', QuoteSchema); // 'DATANAME' , matching DATANAME you set in controllers.js

```

Run the following installations on terminal

//For Mac add sudo prior to each npm install e.g sudo npm install	

Open new terminal and run these commands

```
yourcommandline> npm init -y
yourcommandline> npm install express --save
yourcommandline> npm install express-session --save
yourcommandline> npm install ejs --save
yourcommandline> npm install body-parser --save
yourcommandline> npm install mongoose --save
yourcommandline> npm install --save express-flash
yourcommandline> npm install bcrypt --save <== use bcryptjs if bcrypt doesnt work

yourcommandline> npm install -g @angular/cli <== one time installation only(global install), you don't need to run for future angular project
```

Create new Angular App (Any name is okay, I called it public)

```
yourcommandline> ng new public --routing

(Don't forget hit Enter key when they ask for CSS) 
```

To build the Angular app, Open another terminal and move in Angular App

```
yourcommandline/Angular_App> ng build --watch
```

//For Windows
Open new terminal and move in MongoDB folder in Program Files and run mongod
```
yourcommandline/MongoDB>mongod
```

//For Mac, run directly on terminal
```
yourcommandline> sudo mongod
```
***DON'T FORGET TO GO BACK TO ORIGINAL FOLDER AND RUN PROJECT BY COMMAND BELOW***

```
nodemon server.js
```

========================== ***Create Service with Angular*** ==============================

Navigate into folder Angular App to create services
```
yourcommandline/Angular_App> ng g s http
```

Navigate inside Angular app and open this file ...src/app/app.module.ts and change to code below

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { AppComponent } from './app.component';
import { HttpService } from './http.service';
import { HttpClientModule } from '@angular/common/http';
import { AppRoutingModule } from './app-routing.module';

@NgModule({
  declarations: [
    AppComponent,
  ],
  imports: [
    BrowserModule,
    FormsModule,
    AppRoutingModule,
    HttpClientModule
  ],
  providers: [HttpService],
  bootstrap: [AppComponent]
})
export class AppModule { }

```

Open this file ...src/app/http.service.ts and change to code below

```
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Injectable({
  providedIn: 'root'
})
export class HttpService {

  constructor(private _http: HttpClient){}
}
```

Open this file ...src/app/app.component.ts and  change to code below

```
import { Component } from '@angular/core';
import { HttpService } from './http.service';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'app';
  constructor(private _httpService: HttpService){}
}
```

Now go back to your orginal folder and run nodemon server.js and Enjoy!!!
