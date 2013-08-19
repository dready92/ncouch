Introduction
============

ncouch is a couchdb server driver for node.js. It's uncomplete but works really well.

Usage
=====

Code
----

    var ncouch = require("ncouch");
    var server = ncouch.server("http://127.0.0.1:5984/");
    var database = server.database("test");
    wrapper.view("artists/songs",function(err, resp) {
      if ( err ) {
        console.log("something failed badly: ", err, resp);
        return;
      }
      console.log("View response: ",resp);
    });
    
    wrapper.getDoc("docid1",function(err, doc) {
      if ( err ) {
        console.log("something failed badly: ", err, doc);
        return;
      }
      console.log("Doc: ", doc);
      doc.newProperty = "I'm new";
      wrapper.storeDoc(doc, function(err, resp) {
        if ( err ) {
          console.log("something failed badly: ", err, doc);
          return;
        }
        console.log("Doc recorded. New revision is : ", doc._rev);
      });
    });

API
===

Server
------

**var server = ncouch.server( url );**  

###Parameters

**url**: the URL of your CouchDB server

###Returns

A ncouch server object.

###Example

    var server = ncouch.server("http://localhost:5984/");

Database
--------

**var db = server.database( name );**

###Parameters

**name**: name of the CouchDB database

###Returns

a ncouch database object

###Example

    var server = ncouch.server("http://localhost:5984/");
    var db = server.database("test");


Database creation
-----------------

**server.createDatabase(name, callback)**

###Parameters

**name**: name of the Couch database to create  
**callback**: a node style (err,resp) callback that will be called when the request is processed

###Example

    var server = ncouch.server("http://localhost:5984/");
    server.createDatabase("test", function(err, resp) {
      if ( err ) {
        console.log("Database creation failed: ",err,resp);
      } else {
        console.log("database created");
      }
    });

Database existence test
-----------------------

**server.databaseExists(name, callback)**

###Parameters

**name**: name of the database  
**callback**: a node style (err,resp) callback that will be called when the request is processed

###Example

    var server = ncouch.server("http://localhost:5984/");
    server.databaseExists("test", function(err, resp) {
      if ( err ) {
        console.log("Database existence test failed");
      } else {
        if ( resp ) {
          console.log("database exists");
        } else {
          console.log("databse doesn't exist");
        }
      }
    });

Document: get
-------------

**db.getDoc(id, [data,] callback)**

###Parameters

**id**: id of the document to fetch  
**data**: optionnal data to be passed (an object of keys => values)  
**callback**: a node style (err,resp) callback that will be called when the request is processed

###Example

    var server = ncouch.server("http://localhost:5984/");
    var db = server.database("test");
    db.getDoc("docid1", {conflicts: true, open_revs: "all"}, function(err,doc) {
      if ( err ) {
        console.log("Document retrieval failed: ",err,doc);
        return ;
      }
      console.log("document retrieved: ",doc);
    });

Document: store
---------------

**db.storeDoc(doc, callback)**

###Parameters

**doc**: the document to store  
**callback**: a node style (err,resp) callback that will be called when the request is processed

###Example

    var server = ncouch.server("http://localhost:5984/");
    var db = server.database("test");
    db.getDoc("docid1", {conflicts: true, open_revs: "all"}, function(err,doc) {
      if ( err ) {
        console.log("Document retrieval failed: ",err,doc);
        return ;
      }
      doc.newProperty = "property";
      db.storeDoc(doc, function(err, resp) {
        if ( err ) {
          console.log("document storage failed: ", err, resp);
          return ;
        }
        console.log("Doc stored, last revision is now", doc.rev);
      });
    });

Document: delete
----------------

**db.deleteDoc(doc, callback)**

###Parameters

**doc**: the document to delete  
**callback**: a node style (err,resp) callback that will be called when the request is processed

###Example

    var server = ncouch.server("http://localhost:5984/");
    var db = server.database("test");
    db.getDoc("docid1", {conflicts: true, open_revs: "all"}, function(err,doc) {
      if ( err ) {
        console.log("Document retrieval failed: ",err,doc);
        return ;
      }
      doc.newProperty = "property";
      db.deleteDoc(doc, function(err, resp) {
        if ( err ) {
          console.log("document removal failed: ", err, resp);
          return ;
        }
        console.log("Doc deleted");
      });
    });

Document: delete by id
----------------------

**db.deleteDoc(id, callback)**

###Parameters

**id**: the id of the document to delete  
**callback**: a node style (err,resp) callback that will be called when the request is processed

###Example

    var server = ncouch.server("http://localhost:5984/");
    var db = server.database("test");
    db.deleteDoc("docid1", function(err, resp) {
      if ( err ) {
        console.log("document removal failed: ", err, resp);
        return ;
      }
      console.log("Doc deleted");
    });


Document: storage of multiple docs in one go
--------------------------------------------

**db.storeDocs(docs, [data,] callback)**

###Parameters

**docs**: array of documents to store  
**data**: optionnal data to be passed (an object of keys => values)  
**callback**: a node style (err,resp) callback that will be called when the request is processed

###Example

    var server = ncouch.server("http://localhost:5984/");
    var db = server.database("test");
    var doc1 = {color: "red"};
    var doc2 = {color: "blue"};
    var docs = [doc1, doc2];
    db.storeDocs(docs, function(err, resp) {
      if ( err ) {
        console.log("document storage failed: ", err, resp);
        return ;
      }
      console.log("Docs stored");
    });

Document: overwriting a document
--------------------------------

This will replace the document, without taking care of the document revision and conflict management of couchDB.

**db.overwriteDoc(doc, cb)**

###Parameters

**doc**: the document to store  
**callback**: a node style (err,resp) callback that will be called when the request is processed

###Example

    var server = ncouch.server("http://localhost:5984/");
    var db = server.database("test");
    db.getDoc("docid1", {conflicts: true, open_revs: "all"}, function(err,doc) {
      if ( err ) {
        console.log("Document retrieval failed: ",err,doc);
        return ;
      }
      var newDoc = {_id: "docid1", color: "black"};
      db.overwriteDoc(newDoc, function(err,resp) {
        if ( err ) {
          console.log("Document storage failed: ",err,resp);
          return ;
        }
        console.log("document stored, last revision is now",newDoc._rev);
      });
    });

Documents: getting several documents in one go
----------------------------------------------

**db.getAlldocs([data,] callback)**

###Parameters

**data**: optionnal data to be passed as options  
**callback**: a node style (err,resp) callback that will be called when the request is processed

###Example

    var server = ncouch.server("http://localhost:5984/");
    var db = server.database("test");
    db.getAllDocs(function(err, resp) {
      if ( err ) {
        console.log("Document retrieval failed: ",err,resp);
        return ;
      }
      console.log("I got all documents of the database",resp.rows);
    });

###Example2: get several docs by id

    var server = ncouch.server("http://localhost:5984/");
    var db = server.database("test");
    db.getAllDocs([keys: ["docid1","docid2"]], function(err, resp) {
      if ( err ) {
        console.log("Document retrieval failed: ",err,resp);
        return ;
      }
      console.log("I got two documents of the database",resp.rows);
    });

Documents: delete several documents in one go
---------------------------------------------

**db.deleteDocs(docs, [data,] callback)**

###Parameters

**docs**: array of documents to delete  
**data**: optionnal data to be passed (an object of keys => values)  
**callback**: a node style (err,resp) callback that will be called when the request is processed

###Example

    var server = ncouch.server("http://localhost:5984/");
    var db = server.database("test");
    db.getAllDocs([keys: ["docid1","docid2"]], function(err, resp) {
      if ( err ) {
        console.log("Document retrieval failed: ",err,resp);
        return ;
      }
      console.log("I got two documents of the database",resp.rows);
      db.deleteDocs(resp.rows, function(err, resp) {
        if ( err ) {
          console.log("failed to delete docs",err,resp);
          return;
        }
        console.log("documents deleted");
      });
    });

View: query a view
------------------

**db.view(name, [data,] callback)**

###Parameters

**name**: name of the view, composed by the name of the design document, a /, and the name of the view  
**data**: optionnal data to be passed (an object of keys => values)  
**callback**: a node style (err,resp) callback that will be called when the request is processed

###Example

    var server = ncouch.server("http://localhost:5984/");
    var db = server.database("test");
    db.view("artists/all", {reduce: false, include_docs: true}, function(err,resp) {
      if ( err ) {
        console.log("Error fetching the view: ",err,resp);
        return;
      }
      console.log("Got view results: ", resp.rows);
    });

List: query a list
------------------

**db.list(name, [data,] callback)**

###Parameters

**name**: name of the list, composed by the name of the design document, a /, the name of the view, a /, optionnally, the name of the design document containing the list function and a /, the name of the list function  
**data**: optionnal data to be passed (an object of keys => values)  
**callback**: a node style (err,resp) callback that will be called when the request is processed

###Example

    var server = ncouch.server("http://localhost:5984/");
    var db = server.database("test");
    db.list("artists/all/docs", {reduce: false, include_docs: true}, function(err,resp) {
      if ( err ) {
        console.log("Error fetching the list: ",err,resp);
        return;
      }
      console.log("Got list results: ", resp);
    });

Show: query a show
------------------

**db.show(name, [data,] callback)**

###Parameters

**name**: name of the list, composed by the name of the design document, a /, the name of the show function, a /, and optionnally, the id of the document to show  
**data**: optionnal data to be passed (an object of keys => values)  
**callback**: a node style (err,resp) callback that will be called when the request is processed

###Example

    var server = ncouch.server("http://localhost:5984/");
    var db = server.database("test");
    db.show("artist/public_profile/artist1", function(err,resp) {
      if ( err ) {
        console.log("Error fetching the show: ",err,resp);
        return;
      }
      console.log("Got show results: ", resp);
    });

Update: calling an update function
----------------------------------

See [http://wiki.apache.org/couchdb/Document_Update_Handlers](CouchDB Update API)

**db.updateDoc(name, data, callback)**

###Parameters

**name**: path of the update function, composed by the name of the design document, a /, the name of the update function, and optionnally, a / and the id of the document to update  
**data**: additionnal data to pass to the update function  
**callback**: a node style (err,resp) callback that will be called when the request is processed

###Example

    var server = ncouch.server("http://localhost:5984/");
    var db = server.database("test");
    db.update("artist/recommandation/artist1", {likes: 4}, function(err,resp) {
      if ( err ) {
        console.log("Error updating the document: ",err,resp);
        return;
      }
      console.log("Document updated: ");
    });

