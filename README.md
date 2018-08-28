# JENNIFER Extension Architecture

![이미지](https://raw.githubusercontent.com/jennifersoft/jennifer-extension-manuals/master/res/img/view_server_extension/infographic_en.png)

# JENNIFER Adapter Tutorial

This repository contains Adapter development guides and sample code for creating JENNIFER View Server Adapters

## Guides 

* [Adapter Developer Korean Guide](./README_ko.md)
* [Adapter Developer English Guide](./README_en.md)

## Getting started

You can use this repo as a base of your adapter or a quick start method. 

 1. git clone https://github.com/jennifersoft/jennifer-view-adapter-tutorial.git 
 2. cd jennifer-view-adapter-tutorial
 3. mvn install


 ## Example Code 
You can find sample code for the 3 Adapters type provided by JENNIFER in the src folder: 

* [TransactionAdapter.java](./src/main/java/com/aries/tutorial/TransactionAdapter.java): Example of receiving Real-Time data from X-View

* [EventAdapter.java](./src/main/java/com/aries/tutorial/EventAdapter.java): Example of receiving data related to an EVENT occurrence

* [LoginAdapter.java](./src/main/java/com/aries/tutorial/LoginAdapter.java): Example of using LoginAdapter for user authentication
