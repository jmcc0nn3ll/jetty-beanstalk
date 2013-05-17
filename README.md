jetty-beanstalk
===============

Configuration for Amazon Beanstalk

NOTE: this configuration has been tested with:

* 32 bit Amazon Linux running Tomcat 7 


This repo is configured with three different Maven modules.

- jetty-beanstalk-overlay

   This is the collection of scripts that are required to wedge jetty into the normal beanstalk setup.  This module is intended to extract into an webapp to enable it for beanstalk usage with jetty.

- jetty-beanstalk-resources

   This generates and artifact of files that are downloaded by the configuration process and contains replacements for certain beanstalk files as well as various system level jetty configuration files like an updated jetty.sh script for the /etc/init.d setup.

- jetty-beanstalk-testapp
   
   An example webapp that shows both how to combine war file from another maven module with the jetty-beanstalk-overlay to produce a beanstalk enabled application bundle.  Also included in this is examples of how to alter jetty configuration for things like a customized start.ini file and the like.

Note: The test webapps needs access to a snapshot version of the test-jetty-webapp so it really serves as more of an example of how to layer your webapp with the bits required to customize your app for beanstalk and jetty.

How it works
--------------

Elastic beanstalk has a very powerful configuration mechanism so this integration taps into that to effectively rework the tomcat configuration and replace it with the bits required to make jetty run in its place.  Below is a walk through of what the various configuration files are doing and how the general flow of configuration on beanstalk happens.

There is an .ebextensions directory in your beanstalk application which contains all of the files requires to configure and customize your beanstalk and application combo.  Files that end in .config in this directory are processed in alphabetical order.  

* 00-java7.config : installs java 7 onto the beanstalk environment and makes it the default
* 10-tweak.config : not required, but changes the /opt/elasticbeanstalk directory to be readable making debugging easier
* 11-jetty.config : installs jetty9 into /opt/jetty-9 and removes unneeded distribution filesf
* 12-beanstalk.config : handles replaceing tomcat with jetty in many configuration files, configures logging and wires up system startup processes.  Some files in your .ebextensions direction are moved to replace files under /opt/elasticbeanstalk.

If you look in the .ebextensions of your application you should also see other jetty specific xml and ini files.  The final config file handles these as they are largely customization for your application.  

* 20-testapp.config : layers application specific configuration files into the jetty installation

The files in our example test webapp here enable various OPTIONS for libraries that need loaded, customize the root application being deployed and even deploy additional contexts like we do in our jetty distribution demo.  This is also the mechanism that you would use to wire up application specific things, for example if you needed additional software installed, customized directories made, etc.

A Note on Bluepill
------------------

Bluepill is used to manage the start and stop process of the app server.  This seems to be a problematic bit of software with a colored history and the version in use at the time of this writing is old.  When starting and stopping (or restarting) the appserver you may see error messages show up that the Server timed out getting a response or things like that.  These are red herrings and my experience is that jetty has started and stopped just fine, the pid file required shows up in a very timely fashion (under /var/run/jetty.pid) so do check that the app server has started, but please be aware there is a strangeness here that hasn't been sorted out yet.
