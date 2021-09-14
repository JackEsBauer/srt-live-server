Introduction
============

Credits go to Edward-Wu's srt-server implemetation

Added string token system in url

for example:

srt://[your.sls.ip]:[port]]?streamid=[streamid]/[application]/[stream_name][token=<your generate token>]
if the token parameter is presented, the on_event url will include this token in order for you to validate it in your own system.
aka: token=<valid token> send a 200 for ok, <invalid> send 403 for rejected (POST METHOD)

Requirements
============

please install the SRT first, refer to SRT(https://github.com/Haivision/srt) for system enviroment.
SLS can only run on OS based on linux, such as mac, centos or ubuntu etc.

Compile
=======

$ sudo make

bin file is generated in subdir of 'bin'.

Directivies
===========

about the config file, please see the wiki:
https://github.com/Edward-Wu/srt-live-server/wiki/Directives

Usage
=====

$ cd bin

1.help information
------------------
$ ./sls -h

2.run with default config file
------------------------------
$ ./sls -c ../sls.conf

Test
====

SLS only supports the MPEG-TS format streaming. 

1.test with ffmpeg
------------------

you can push camera live stream by FFMPEG.Please download ffmpeg sourcecode from https://github.com/FFmpeg/FFmpeg, then compile FFMPEG with --enable-libsrt. 

srt library is installed in folder /usr/local/lib64.
if "ERROR: srt >= 1.3.0 not found using pkg-config" occured when compiling FFMPEG, please check the ffbuild/config.log file and follow its instruction to resolve this issue. in most cases it can be resolved by the following command:
export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig:/usr/local/lib64/pkgconfig

if "error while loading shared libraries: libsrt.so.1" occured, please add srt library path to file '/etc/ld.so.conf' as the default path, then refresh by comand /sbin/ldconfig with root.


use ffmpeg to push camera stream with SRT(on my mac):

$ ./ffmpeg -f avfoundation -framerate 30 -i "0:0" -vcodec libx264  -preset ultrafast -tune zerolatency -flags2 local_header  -acodec libmp3lame -g  30 -pkt_size 1316 -flush_packets 0 -f mpegts "srt://[your.sls.ip]:8080?streamid=uplive.sls.com/live/test"


play the SRT stream with ffplay:

./ffplay -fflags nobuffer -i "srt://[your.sls.ip]:8080?streamid=live.sls.com/live/test"


2.test with OBS
---------------

the OBS supports srt protocol to publish stream when version is later than v25.0. you can use the following url:
srt://[your.sls.ip]:8080?streamid=uplive.sls.com/live/test
whith custom service.

3.test with srt-live-client
---------------------------

there is a test tool in sls, which can be used performance test because of no codec overhead but main network overhead. the slc can play a srt stream to a ts file, or push a ts file to a srt stream.

push ts file as srt url:

cd bin

./slc -r srt://[your.sls.ip]:8080?streamid=uplive.sls.com/live/test -i [the full file name of exist ts file]

play srt url

./slc -r srt://[your.sls.ip]:8080?streamid=live.sls.com/live/test -o [the full file name of ts file to save]

4.use sls with docker
---------------------------
please refer to :https://hub.docker.com/r/ravenium/srt-live-server

Note:
=====

1.SLS refer to the RTMP url format(domain/app/stream_name), example: www.sls.com/live/test. The url of SLS must be set in streamid parameter of SRT, which will be the unique identification a stream.

2.How to distinguish the publisher and player of the same stream? In conf file, you can set parameters of domain_player/domain_publisher and app_player/app_publisher to resolve it. Importantly, the two combination strings of domain_publisher/app_publisher and domain_player/app_player must not be equal in the same server block.

3.I supply a simple android app for test sls, your can download from https://github.com/Edward-Wu/liteplayer-srt

ReleaseNote
============



