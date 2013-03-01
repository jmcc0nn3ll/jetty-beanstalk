jetty-beanstalk
===============

Configuration for Amazon Beanstalk

This repo is configured with three different Maven modules.

 - jetty-beanstalk-overlay

   This is the collection of scripts that are required to wedge jetty into the normal beanstalk setup.  This module is intended to extract into an webapp to enable it for beanstalk usage with jetty.

- jetty-beanstalk-resources

   This generates and artifact of files that are downloaded by the configuration process and contains replacements for certain beanstalk files as well as various system level jetty configuration files like an updated jetty.sh script for the /etc/init.d setup.

- jetty-beanstalk-testapp
   
   An example webapp that shows both how to combine war file from another maven module with the jetty-beanstalk-overlay to produce a beanstalk enabled application bundle.  Also included in this is examples of how to alter jetty configuration for things like a customized start.ini file and the like.

