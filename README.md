README.md
1)	Release template 
$ bosh init release grozda 
grozda/
grozda/blobs
grozda/src
grozda/config
grozda/config/blobs.yml
grozda/jobs
grozda/packages

2)	Store source code 
mkdir src/interview
cd src/interview    ( Store compiled file !!!) 
git checkout https://github.com/benlaplanche/interview-timer


3)	Create package 
$ bosh generate package interview

create  packages/interview
create  packages/interview/packaging
create  packages/interview/pre_packaging
create  packages/interview/spec

4)	fill in spec: 
Open /interview/spec
---
name: interview

files:
  - interview/timer-linux

5)	fill in package 
set -e
echo "Starting interview timer..."
./interview/timer-
6)	Generate Job
$ bosh generate job myjob
create  jobs/myjob
create  jobs/myjob/templates
create  jobs/myjob/spec
create  jobs/myjob/monit

7)	Config file & templates 
myjob_ctl.erb
#!/bin/bash

JOB=myjob
RUN_DIR=/var/vcap/sys/run/myjob
LOG_DIR=/var/vcap/sys/log/myjob
CONF_DIR=/var/vcap/jobs/$JOB/config
APACHECTL=/var/vcap/packages/interview
APSTART=/var/vcap/sys/log/myjob/start.log

case $1 in

  start)
    echo "About to start"
    ;;

  stop)
    echo "Stopping it now"
    ;;

  *)
  echo "Usage: ${JOB}_ctl {start|stop}" ;;
esac
exit 0

8)	Monit script :
check process myjob
  with pidfile /var/vcap/sys/run/myjob/httpd.pid
  start program "/var/vcap/jobs/myjob/bin/myjob_ctl start" with timeout 60 seconds
  stop program "/var/vcap/jobs/myjob/bin/myjob_ctl stop" with timeout 60 seconds
  group vcap

9)	Spec update 
---
name: myjob

templates:
  myjob_ctl.erb: bin/myjob_ctl
packages:
  - interview
files:
  - interview/linux-timer


10)	$ bosh create release –force

11)	$ bosh upload release
