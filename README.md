Automator task and example gruntfile for Pixelmator conversions using grunt watch

Based on http://www.mrspeaker.net/2013/07/29/pixelmator-save-project-reload/

Example usage with `grunt`:


**Gruntfile.js**

```js
var pxm2png = require('pxm2png');
var path = require('path');
module.exports = function(grunt) {
  grunt.initConfig({
    watch: {
      pxm: {
        files   : ['**/*.pxm'],
        tasks   : ['pxm2png'],
        options : { spawn: false },
        modified: []
      }
    }
  });
 
  grunt.registerTask('pxm2png', 'convert Pixelmator files to pngs', function() {
    var tool = path.join(__dirname, 'workflow/pxm2png/pxm2png.app');
    var target = path.join(__dirname, grunt.config("pxm.modified")[0]);
    grunt.log.writeln('Converting pxm file: ' + target);
    var cp = grunt.util.spawn({
      cmd: 'open', args: [ pxm2png.app, '--args', target ]
    }, function (err, res, code) {
      if (err) throw err.stack;
      if (code == 0) return;
      grunt.log.writeln("Error converting .pxm file:", target, res.stderr);
    });
  });
 
  // Set the pxm file changed to be retrieved by pxm2png task
  grunt.event.on('watch', function(action, filepath) {
    if (grunt.file.isMatch('**/*.pxm', filepath)) {
      grunt.config("pxm", { modified: [filepath] })
    }
  });
 
  // Load plugin
  grunt.loadNpmTasks('grunt-contrib-watch');
 
  // Default task(s).
  grunt.registerTask('default', []);
};
```
