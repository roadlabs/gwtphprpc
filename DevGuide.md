# What it is ? #
  * The code generator is developed in Java (see dependencies below).
  * It generates some Java and PHP code using Velocity.
  * The client/serveur protocol between GWT client and PHP back-end is JSON-RPC (v1).
  * The JSON-RPC PHP server code (for JSON-RPC serialization/unserialization) is partly based upon the http://jsonrpcphp.org/ code (available as GPL). I say parly because I had to modify it and to add some more code to manage complex object structures as parameters and result from remote methods.

# Dependencies #
The generator depends on the following libraries:
  * jdom.jar: version 1.1 of the [JDOM](http://www.jdom.org/) interface for manipulating XML files
  * velocity-1.6.4.jar: the [Velocity](http://velocity.apache.org/) templating engine
  * commons-collections-3.2.1.jar: [jakarta commons collections](http://commons.apache.org/) libraries (required by Velocity)
  * commons-io-1.4.jar: [jakarta commons IO](http://commons.apache.org/) used for a bit of file system manipulations
  * commons-lang-2.5.jar: [jakarta commons lang](http://commons.apache.org/) which contains the very useful StringUtils

Obviously there is also a dependency with ANT (since the entry point to the generator is a Task).

# Templates and libraries #
The velocity templates as well as the helper classes (both Java and PHP ones) are directly inserted into the classpath of the generator (and thus distributed in the "phpgen.jar").