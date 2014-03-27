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

`observable` supports subscriptions/notifications on named events.  Event names are dot-delimited strings; a trailing dot will also match all sub-names.  The default event is "`all`" —meaning "every|any event" _(this is analagous to the "normal" usage that the SPL interfaces intended)_.  Observe (no pun intended):

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

mini-docs
---------

_for **SplObserver**:_
<ul>
    <li>
        <p><b>update()</b><br>
            <code>void update( \SplSubject $subject )</code><br>
            see the <a href=http://php.net/splobserver.update>SPL documentation</a>.  
        <p><code>configurable</code> uses <code>update</code> as an update dispatcher.  
            The implementing class should define a "handler" method for each event it needs to support, 
            and must also define a "catch-all" method for other event notifications.  
            The naming convention/signature for handlers are as follows:<br>
            <code>void _update_{name of event}( \SplSubject $subject, $event )</code><br>
            <code>void _update_all( \SplSubject $subject, $event )</code><br>
    </li>
</ul>

_for **SplSubject**:_
<ul>
    <li>
        <p><b>attach()</b><br>
            <code>void attach( \SplObserver $observer [, $event] )</code><br>
            In addition to the <a href=http://php.net/splsubject.attach>SPL documentation</a>,
            allows observers to subscribe to specific events or event groups.
        <p>param <b>$event</b><br>
            The event (group) to subscribe to.  
            If omitted, the observer will recieve notifications on _all_ events.  
            Event names are arbitrary, dot-delimited strings.  
            Event names may be grouped in hierarchies; 
            a trailing dot will match all sub-named events.  
            The special event name "<code>all</code>" will match _all_ events.
    </li>
    <li>
        <p><b>detach</b><br>
            <code>void detach( \SplObserver $observer [, $event] )</code><br>
            In addition to the <a href=http://php.net/splsubject.detach>SPL documentation</a>, 
            allows un-subscribing from specific events or event groups.  
            If all of an observer's events are detached, the observer will be detached as well.
        <p>param <b>$event</b><br>
            See the description for <code>$event</code> (above).
    </li>
    <li>
        <p><b>notify</b><br>
            <code>void notify( [$event] )</code><br>
            In addition to the <a href=http://php.net/splsubject.notify>SPL documentation</a>, 
            allows sending notifications for specific events.
        <p>param <b>$event</b><br>
            See the description for <code>$event</code> (above).
    </li>
</ul>
