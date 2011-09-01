asyncmongo
==========

AsyncMongo is an asynchronous library for accessing mongo 
which is built on the tornado ioloop.

Installation
------------

Installing: `pip install asyncmongo`

Installing form github: `pip install -e git://github.com/bitly/asyncmongo.git#egg=asyncmongo`

Installing from source: `git clone git://github.com/bitly/asyncmongo.git; cd asyncmongo; python setup.py install`

Usage
-----
    import tornado.web
    import tornado.ioloop
    import asyncmongo

    class MainHandler(tornado.web.RequestHandler):
        @tornado.web.asynchronous
        def get(self):
            print 'debug'
            db.users.find({"user_id" : 1}, callback=self._on_response)

        def _on_response(self, response, error):
            if error:
                self.write(str(error))
            self.write(str(response))
            self.finish()

    if __name__ == "__main__":
        application = tornado.web.Application([
                (r"/?", MainHandler)
                ])
        application.listen(8888)
        db = asyncmongo.Client(pool_id="mydb",
                               host='staff.mongohq.com',
                               port=10025,
                               mincached=30,
                               maxcached=30,
                               maxconnections=30,
                               dbname='dbname', 
                               dbuser='username',
                               dbpass='password')
        tornado.ioloop.IOLoop.instance().start()

About
-----

Features not supported: some features from pymongo are not currently implemented. i.e.: directly 
interfacing with indexes, dropping collections, and retrieving results in batches instead of all at once. 
(asyncmongo's nature means that no calls are blocking regardless of the number of results you are retrieving)

Warning: If you need authentication it is advisable to use a fully cached connection pool. As of now when connections are created the first database request will be processed before the authentication callbacks have fully executed. The connection will however be fully created and authenticated and subsequent requests made with that connection will succeed. If someone wants to complete the last mile that would be great. What I have here works for me at the moment. 

Requirements
------------
The following two python libraries are required

* [pymongo](http://github.com/mongodb/mongo-python-driver) version 1.9+ for bson library
* [tornado](http://github.com/facebook/tornado)

Issues
------

Please report any issues via [github issues](https://github.com/bitly/asyncmongo/issues)
