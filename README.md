# echobot for facebook messenger

A facebook messenger bot in node.js deployed on Heroku [[link]](https://peaceful-reaches-51734.herokuapp.com)

### Follows Facebook Messenger Platform tutorial 

1. Configure Node.js
- Configure Heroku

  ```
  heroku login
  heroku create
  git push heroku master
  ```

3. Configure Facebook Page
- Configure Facebook App


### Working Code for Echo

Place this code in a file called index.js

```
var express = require('express');
var bodyParser = require('body-parser');
var request = require('request');
var app = express();


app.use(bodyParser.urlencoded({extended: false}));
app.use(bodyParser.json());
app.listen((process.env.PORT || 1337), function () {
  console.log('app listening on port 1337');
});

// Server default URL
app.get('/', function (req, res) {
    res.send('This is a Bot Server');
});

// Facebook Webhook
app.get('/webhook', function (req, res) {
    if (req.query['hub.verify_token'] === 'mybot_verify_token') {
        res.send(req.query['hub.challenge']);
    } else {
        res.send('Invalid verify token');
    }
});

/**
 *  POST handler to send and receive messages
 */ 
app.post('/webhook', function (req, res) {
    var events = req.body.entry[0].messaging;
    for (i = 0; i < events.length; i++) {
        var event = events[i];
        if (event.message && event.message.text) {
          
            sendMessage(event.sender.id, {text: "Echo: " + event.message.text});
        }
    }
    res.sendStatus(200);
});

/**
 *  function to send messages
 */ 
function sendMessage(recipientId, message) {
    request({
        url: 'https://graph.facebook.com/v2.6/me/messages',
        qs: {access_token: process.env.PAGE_ACCESS_TOKEN},
        method: 'POST',
        json: {
            recipient: {id: recipientId},
            message: message,
        }
    }, function(error, response, body) {
        if (error) {
            console.log('Error sending message: ', error);
        } else if (response.body.error) {
            console.log('Error: ', response.body.error);
        }
    });
};


```


