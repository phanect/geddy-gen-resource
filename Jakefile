var path = require('path')
  , fs = require('fs')
  , cwd = process.cwd()
  , utilities = require('utilities')
  , genutils = require('geddy-genutils')
  , exec = require('child_process').exec
  , genDirname = __dirname;

var ns = 'resource';

// Load the basic Geddy toolkit
genutils.loadGeddy();
var utils = genutils.loadGeddyUtils();

// Tasks
task('default', function() {
  var self = this;
  var t = jake.Task['resource:create'];
  t.reenable();
  t.invoke.apply(t, Array.prototype.slice.call(arguments));
});

namespace(ns, function() {
  // Creates a resource with a model, controller and a resource route
  task('create', {async: true}, function (name) {
    if (!genutils.inAppRoot()) {
      fail('You must run this generator from the root of your application.');
      return;
    }

    if (!name) {
      fail('No resource name specified.');
      return;
    }

    var appPath = process.cwd();
    var modelProperties = Array.prototype.slice.call(arguments, 1);

    var modelsDir = path.join(appPath, 'app', 'models');

    // check for geddy-gen-controller and geddy-gen-model
    testGenController();

    function testGenController()
    {
      try {
        require.resolve('geddy-gen-controller');
      } catch(error) {
        console.log('Unable to find geddy-gen-controller. Will install it now ...');

        exec('npm install geddy-gen-controller -g', function(error) {
          if (error) {
            fail('Error during installation. Please try to install it manually using "npm install geddy-gen-controller -g".');
            return;
          }

          testGenModel();
        });
      } finally {
        testGenModel();
      }
    }

    function testGenModel()
    {
      try {
        require.resolve('geddy-gen-model');
      } catch(error) {
        console.log('Unable to find geddy-gen-model. Will install it now ...');

        exec('npm install geddy-gen-model -g', function(error) {
          if (error) {
            fail('Error during installation. Please try to install it manually using "npm install geddy-gen-model -g".');
            return;
          }

          createResource();
        });
      } finally {
        createResource();
      }
    }

    function createResource()
    {
      genutils.runGen('geddy-gen-model', [name].concat(modelProperties),
        genutils.runGen.bind(genutils, 'geddy-gen-controller', [name, '--resource'], complete)
      );
    }
  });

  task('help', function() {
    console.log(
      fs.readFileSync(
        path.join(__dirname, 'help.txt'),
        {encoding: 'utf8'}
      )
    );
  });

  desc('Clears the test temp directory.');
  task('clean', function() {
    console.log('Cleaning temp files ...');
    var tmpDir = path.join(__dirname, 'test', 'tmp');
    utilities.file.rmRf(tmpDir, {silent:true});
    fs.mkdirSync(tmpDir);
  });

  desc('Copies the test app into the temp directory.');
  task('prepare-test-app', function() {
    console.log('Preparing test app ...');
    jake.cpR(
      path.join(__dirname, 'test', 'geddy-test-app'),
      path.join(__dirname, 'test', 'tmp'),
      {silent: true}
    );
    console.log('Test app prepared.');
  });
});

testTask('Resource', ['resource:clean', 'resource:prepare-test-app'], function() {
  this.testFiles.exclude('test/helpers/**');
  this.testFiles.exclude('test/fixtures/**');
  this.testFiles.exclude('test/geddy-test-app');
  this.testFiles.exclude('test/tmp/**');
  this.testFiles.include('test/**/*.js');
});