observable
==========

implementation of SplObserver + SplSubject with support for named events.

one, other, both
----------------

Depending on which interfaces are declared, unneeded methods will do nothing.  For example:

* In a class which `implements SplObserver`, the `update` method will work normally, but `attach`, `detach`, and `notify` will do nothing at all.
* In a class which `implements SplSubject`, `attach`, `detach`, and `notify` will work, but `update` will do nothing.
* A class which `implements SplObserver, SplSubject` will have use for all methods.

events and event groups
-----------------------

`observable` supports subscriptions/notifications on named events.  Event names are dot-delimited strings; a trailing dot will also match all sub-names.  The default event is "*" (meaning "every|any event" - the is the "normal" usage that the SPL interfaces intended).  Observe (no pun intended):

    <?php
      # this code...                                 # subscribes observer to...
      #----------------------------------------------#-------------------------------------------
      $subject->attach( $observer1,"hello.world" );  # "hello.world"
      $subject->attach( $observer2,"hello.mom" );    # "hello.mom"
      $subject->attach( $observer3,"hello." );       # "hello" _and_ all sub-events
      $subject->attach( $observer4,"hello" );        # "hello"
      $subject->attach( $observer5 );                # _all_ events ("normal" SPL usage)

      # this code...                                 # triggers update() on...
      #----------------------------------------------#-------------------------------------------
      $subject->notify( "hello" );                   # $observer3, $observer4, $observer5
      $subject->notify( "hello.world" )              # $observer1, $observer3, $observer5
      $subject->notify( "hello.world.again" )        # $observer5
      $subject->notify();                            # _all_ observers ("normal" SPL usage)
