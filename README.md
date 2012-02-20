# irssi-zeromq

## Motivation
I have long been using [Irssi][irssi] running in a screen as my IRC client of
choice. Lately I have also been putting [ZNC][znc] in between to also be able
to connect from my iPhone, iPad and nice graphical OSX clients. But somehow I
always end up going back to irssi. The one thing I am missing though is the
instant notification when something happens which might need my attention. The
nice things you get with GUI clients like bouncing dock icons, ping sounds and
growl notifications. I know there are a lot of [solutions][notify] for this,
but I kind of didn't feel like constantly parsing a logfile over SSH to get
notifications.

And I REALLY wanted to play with [ZeroMQ][zeromq].

## Overview
irssi-zeromq is an irssi plugin, inspired by [fnotify][fnotify], which
subscribes to irssi events and distributes them via ZeroMQ pub/sub.
Notification handlers just subscribe to the socket, get notified about events
and can be written in any language. If you have irssi running on a remote
system, just change the socket to use TCP and your golden.

## Example subscribers
Writing messages to stdout with Python:

    import sys
    import zmq

    context = zmq.Context()
    socket = context.socket(zmq.SUB)
    socket.connect ("ipc://irssi.ipc")
    socket.setsockopt(zmq.SUBSCRIBE, 'irssi')

    print socket.recv()

Display a growl notification with Ruby:

    require 'rubygems'
    require 'ffi-rzmq'

    context = ZMQ::Context.new(1)
    subscriber = context.socket(ZMQ::SUB)
    subscriber.connect("ipc://irssi.ipc")
    subscriber.setsockopt(ZMQ::SUBSCRIBE, "irssi")

    s = ''
    subscriber.recv_string(s)
    system("growlnotify -t irssi -m \"#{s}\"")

I think you get the idea. Now it's super easy to write notification subscribers
in any language you like. Go forth and subscribe.

## Requirements
- the perl ZeroMQ module from CPAN

## Todo:
- emit the actual irssi event as pub channel
- emit more events

## Meta
- [Bugs][bugs]

## Thanks
- Thorsten Leemhuis for fnotify
- ZeroMQ for being awesome


[irssi]: http://irssi.org
[znc]: http://wiki.znc.in/ZNC
[notify]: http://matthewhutchinson.net/2010/8/21/irssi-screen-fnotify-and-growl-on-osx
[fnotify]: https://gist.github.com/542141
[zeromq]: http://www.zeromq.org/
[bugs]: https://github.com/mrtazz/irssi-zeromq/issues
