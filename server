// server.js
// where your node app starts

// init project
var express = require('express');
var app = express();
var requests = require('request');
var MongoClient = require('mongodb').MongoClient;
var url = 'mongodb://ramiel:RmZ#1357@ds157964.mlab.com:57964/rmdb';


// we've started you off with Express, 
// but feel free to use whatever libs or frameworks you'd like through `package.json`.

// http://expressjs.com/en/starter/static-files.html
app.use(express.static('public'));

// http://expressjs.com/en/starter/basic-routing.html
app.get("/", function (request, response) {
  response.sendFile(__dirname + '/views/index.html');
});

app.get('/imagesearch', function(request, response) {
  response.writeHead(200, { 'Content-Type': 'application/json' });
  var query = request.query.q;
  var offset = (parseInt(request.query.offset) + 1) || 10;
  offset = offset > 10 ? 10:offset;
  var results='';
  MongoClient.connect(url, function(err, db) {
    if (err) throw err;
    var collection = db.collection('img-query');
    var date = new Date();
    var latest={
      "query": query,
      "date": date.toJSON()
    }
    collection.insert(latest, function(err, data) {
        // handle error
      if (err) throw err;
        // other operations
        db.close();
      });
   });
  var stream = requests({
    uri: 'https://www.googleapis.com/customsearch/v1',
    qs: {
      key: 'AIzaSyDROxbTecpjlwBnMUVlGepsKWqM5AyE3fM',
      cx: '005446572173876105669:3mvdjcz4skc',
      searchType:'image',
      num: offset,
      q: query
    }
  });
  stream.on('data',function(chunk){
    results += chunk;
  });
  stream.on('end',function(){
    results = JSON.parse(results);
    let res = results.items;
    response.end(JSON.stringify(res));
   // console.log(res)
  });
});
app.get("/latest", function (request, response) {
  response.writeHead(200, { 'Content-Type': 'application/json' });
  MongoClient.connect(url, function(err, db) {
    if (err) throw err;
    var collection = db.collection('img-query');
    collection.find({},{"_id":false}).sort({"date": -1}).toArray(function(err, data) {
      if (err) throw err;
      if(data){
        response.end(JSON.stringify(data));
      }
      else{
        response.end("not found");
      }
      //console.log(JSON.stringify(data));
    // response.end("hi");
      db.close();
    });
  });
  
});
// listen for requests :)
var listener = app.listen(process.env.PORT, function () {
  console.log('Your app is listening on port ' + listener.address().port);
});
