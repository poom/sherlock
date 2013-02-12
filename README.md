Sherlock
========

_Sherlock_ is a PHP (>=5.3.0) client for [ElasticSearch](http://www.elasticsearch.org/).  _Sherlock_ can be used to search and manage ElasticSearch clusters.


Installation via Composer
-------------------------
The recommended method to install _Sherlock_ is through [Composer](http://getcomposer.org).

1. Add ``sherlock/sherlock`` as a dependency in your project's ``composer.json`` file:

        {
            "require": {
                "polyfractal/sherlock": "master"
            }
        }

2. Download and install Composer:

        curl -s http://getcomposer.org/installer | php

3. Install your dependencies:

        php composer.phar install

4. Require Composer's autoloader

    Composer also prepares an autoload file that's capable of autoloading all of the classes in any of the libraries that it downloads. To use it, just add the following line to your code's bootstrap process:

        require 'vendor/autoload.php';

You can find out more on how to install Composer, configure autoloading, and other best-practices for defining dependencies at [getcomposer.org](http://getcomposer.org).


Manual Installation
-------------------
_Sherlock_ can be installed even if you don't use Composer.  This feature is not finished yet, unfortunately.  Please check back soon.

Usage
-----
The library interface is still under flux...this section will be updated once _Sherlock_ has been fleshed out a bit more.

```php
   require 'vendor/autoload.php';

   //The Sherlock object manages cluster state, builds queries, etc
   $sherlock = new Sherlock();

   //Add a node to our cluster.  Sherlock can optionally autodetect nodes given one starting seed
   $sherlock->addNode('localhost', 9200);

   //Build a new search request
   $request = $sherlock->search();

   //Set the index, type and from/to parameters of the search
   $request->index("test")->type("tweet")->from(0)->to(10);

   //Set the query to a term query, execute and display results
   $request->query($sherlock->query()->Term()->field("message")->term("ElasticSearch"));

   $response = $request->execute();

   echo "Took: ".$response->took."\r\n";
   echo "Hits: ".count($response)."\r\n";

   foreach($response as $hit)
   {
      echo $hit['score'].' - '.$hit['source']['message']."\r\n";
   }


   //Let's try a more advanced query now:
   $must = $sherlock->query()->Term()
                             ->field("message")
                             ->term("ElasticSearch");

   $should = $sherlock->query()->Match()
                               ->field("author")
                               ->query("Zachary Tong")
                               ->boost(2.5);

   $should_not = $sherlock->query()->Term()
                                   ->field("message")
                                   ->term("Solr")

    $request->query($sherlock->query()->Bool->must($must)
                                            ->should($should)
                                            ->must_not($must_not));

```


Philosophy
----------
_Sherlock_ aims to continue the precendent set by ElasticSearch: work out of the box with minimal configuration and provide a simple interface.

_Sherlock's_ API uses a "Fluid" interface that relies heavily on method chaining and type hinting for brevity.  The developer should never need to stop and look up a class name to instantiate, or dig through docs to remember which class accepts which arguments.

Secondary to the interface comes _Sherlock_ developer sanity: reduce code as much as possible.  Rather than write a million getter/setter functions to expose ElasticSearch's various parameters, _Sherlock_ relies heavily upon templates, auto-generated class stubs, magic methods and PHPdoc.

