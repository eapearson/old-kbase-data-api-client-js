**Version: 0.1.0**

Last modified: 23 November 2015

https://travis-ci.org/kbase/data_api_js_clients.svg?branch=develop

# JavaScript API Wrappers for the Data API

The JavaScript Data API is, like the Python API, custom wrapper code on top of the JavaScript library generated by Thrift. The source code for these wrappers is in `src/js/`.

## Download

### JS API repository

Get the repository from github using the `git clone` command (or some UI equivalent):

        git clone https://github.com/kbase/data_api_js_clients.git

### Core Data API repository
 
You will also need to check out a version of the data API 'core'. For right now, you should just check out the head of the development branch. Later, we will have tagged releases and you should check out a release that aligns with the version of this API. To future-proof the directory names, we will use a convention of `core-{tag_or_version}` for naming the cloned directory, so the command becomes the following:

    git clone -b develop https://github.com/kbase/data_api.git core-develop
    
For now it is recommended you use this directory name and clone the repository inside the top level of this repository. If you choose another location, you **must** modify the value of the variable `corepath` inside `Gruntfile.js` to your path. You also must substitute this directory wherever you see "core-develop" used in the examples below.

## Installation

In the following instructions, it will be assumed unless otherwise stated that the shell is currently operating from the top-level directory of the github repository, i.e. from the same directory in which you find this README. 

1. Change to the `core-{version}` directory for the core Data API repository.

     1a. Follow the instructions in the README for installation. This will include creating and activating a Python virtual environment. The scripts installed by this process will be used below to start the Data API Thrift services.

     1b. Important! Make sure you have checked out the git "submodule" that contains the test data. This will be
     required for running the JavaScript tests later.
     
         cd core-develop; git submodule init; git submodule update

2. Install/update JavaScript libraries. We use Node Package Manager (npm) and Bower.

    a. Change to the  directory and run the Node Package Manager (npm). 
    This downloads and installs a bunch of things, so it will take a couple of 
    minutes the first time, and create hundreds of lines of output.

        npm install

    If you have run `npm install` before, and there is nothing to do, you may just see a warning like "npm WARN package.json kbase-data-api-js@0.0.1 No repository field.". This is normal and harmless and you can ignore it.
    
4. Build JavaScript library. Repeat this step every time you make changes to the local code. This will also automatically run `bower update` for you.
    
        grunt build
    
    If this works, the last line of the Grunt output will say `Done, without errors.`
    
## Testing

All the JavaScript tests and tools, unless otherwise stated, should be run from the same directory as this README.

We use [Karma](https://karma-runner.github.io) to run the JavaScript code. Tests run against the following browsers/platforms:
    * chrome
    * safari  --- EEK! not working yet!!
    * firefox

The Karma configuration is under `test/karma.conf.js`. You shouldn't need to modify it, except perhaps for the "port:" setting if the default port conflicts with something else on your system.

Tests themselves are in `test/spec` and are usually named `test_<type>_api.js`, where "<type>" is the API name, e.g., `test_taxon_api.js`.

### Running the tests

* Make sure the version of Karma and the proxy server ("corsproxy") that was installed by grunt is the first one in your PATH. As usual for PATH modifications, you need to do this once per shell instance; the easiest way to automate this is put it in your startup files (e.g., "$HOME/.bashrc"). The command below should be run from the top-level of the cloned repository.

        export PATH=`pwd`/node_modules/karma-cli/bin/karma:`pwd`/node_modules/corsproxy/bin:$PATH
    
      
* Run the corsproxy on port 8000

        CORSPROXY_PORT=8000 corsproxy &
    
    The output should look something like this:
        
        151123/114601.036, [log,info], data: CORS Proxy running at: http://localhost:8000

 
* Run the Data API service of choice -- here, Taxon -- using the configuration in `data_api-test.cfg`

        data_api_start_service.py --config data_api-test.cfg --kbase_url core-develop/test --service taxon &
        
    You can add `-v` for very verbose messages to stderr. The last 2 lines of the output (without `-v`), will look like this:

        2015-11-23 11:47:50,890 [INFO] Starting service: port=9101, PID=36560
        2015-11-23 11:47:51,030 [INFO] Starting the server...

* Now you can run the Karma tests

        karma start test/karma.conf.js

    If this works, you should see lines like this at the end of the output:
    
        Chrome 46.0.2490 (Mac OS X 10.11.1): Executed 2 of 2 SUCCESS (4.083 secs / 4.087 secs)
        Firefox 41.0.0 (Mac OS X 10.11.0): Executed 2 of 2 SUCCESS (3.466 secs / 3.467 secs)
        TOTAL: 4 SUCCESS
    
### TravisCI

For development, we also use TravisCI. The configuration for the TravisCI tests is in the usual place, `.travis.yml` in the top-level directory. If any of the above steps don't work, you can look there and see if it's doing something different; if so, then that's probably the _real_ procedure and this document is out of date (please [report it](http://kbase.us/contact-us/) as a bug!).
