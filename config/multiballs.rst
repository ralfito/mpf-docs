multiballs:
===========

*Config file section*

+----------------------------------------------------------------------------+---------+
| Valid in :doc:`machine config files </config/instructions/machine_config>` | **YES** |
+----------------------------------------------------------------------------+---------+
| Valid in :doc:`mode config files </config/instructions/mode_config>`       | **YES** |
+----------------------------------------------------------------------------+---------+

The ``multiballs:`` section of your config is where you configure multiballs.

Here's an example which contains several different multiball configs. (In the
real world, you'd probably only have one multiball for each mode.)

.. code-block:: yaml

   multiballs:
       add_a_ball:
           ball_count: 1
           ball_count_type: add
           shoot_again: 30s
           enable_events: mb4_enable
           disable_events: mb4_disable
           start_events: mb4_start
           stop_events: mb4_stop

       quick_2_ball:
           ball_count: 2
           ball_count_type: total
           shoot_again: 20s
           start_events: mb11_start
           ball_locks: bd_lock

       release_all_locked_balls:
           ball_count: current_player.lock_mb6_locked_balls
           ball_count_type: add
           shoot_again: 20s
           start_events: mb12_start
           ball_locks: bd_lock

       quick_add_2_ball:
           ball_count: 2
           ball_count_type: add
           shoot_again: 0
           start_events: mb6_start
           ball_locks: bd_lock

You can use the following settings for each multiball:

ball_count: (Required)
~~~~~~~~~~~~~~~~~~~~~~
Single value, type: ``integer``.

The number of balls this multiball should eject (and maintain during shoot again period). Note: It may eject more balls
when using locks but only ball_count balls will be maintained during shoot again.

Note that you can use a :doc:`placeholder value </config/instructions/placeholders>`
for this setting.

ball_locks:
~~~~~~~~~~~
List of one (or more) values, each is a type: string name of a ``ball_locks:`` device. Default: ``None``

Use those devices first when ejecting balls to the playfield on multiball start. On start all balls from all
locks will be ejected (maybe more than ball_count). If there are not enough balls in the lock more balls will be
requested to the source_playfield.

debug:
~~~~~~
Single value, type: ``boolean`` (Yes/No or True/False). Default: ``False``

See the :doc:`documentation on the debug setting </config/instructions/debug>`
for details.

disable_events:
~~~~~~~~~~~~~~~
One or more sub-entries, either as a list of events, or key/value pairs of
event names and delay times. (See the
:doc:`/config/instructions/device_control_events` documentation for details
on how to enter settings here.

Default: ``None``

Events in this list, when posted, disable this multiball. When disabled,
the other events (like start and add a ball) do not work. If this multiball
is in a mode config, then it will also be disabled when the mode it's in stops.

enable_events:
~~~~~~~~~~~~~~
One or more sub-entries, either as a list of events, or key/value pairs of
event names and delay times. (See the
:doc:`/config/instructions/device_control_events` documentation for details
on how to enter settings here.

Default: ``None``

Events in this list, when posted, enable this multiball. Note that enabling a
multiball is not the same as starting it, but the other events (like to start
the multiball or, or add a ball, etc.) do not work unless this multiball is enabled.

Note that if you do not add any ``enable_events:`` (which is the default), this
multiball will be automatically enabled when the mode it's in starts.

reset_events:
~~~~~~~~~~~~~
One or more sub-entries, either as a list of events, or key/value pairs of
event names and delay times. (See the
:doc:`/config/instructions/device_control_events` documentation for details
on how to enter settings here.

Default: ``machine_reset_phase_3, ball_starting``

Event(s) that reset this multiball, which means they disable it as well as
disabling shoot again and resetting the ball add counts to 0.

shoot_again:
~~~~~~~~~~~~
Single value, type: ``time string (ms)`` (:doc:`Instructions for entering time strings) </config/instructions/time_strings>` . Default: ``10s``

Specifies a time period for "shoot again" which is a sort of automatic ball save for
multiballs. The timer will start when this multiball starts, and any balls that
drain during this time will be re-added into play.

source_playfield:
~~~~~~~~~~~~~~~~~
Single value, type: string name of a ``ball_devices:`` device. Default: ``playfield``

The name of the playfield (from the ``playfields:`` section of your machine config
that this multiball will add balls to. You don't have to worry about this unless
you have multiple playfields that you're managing separately (which is rare, usually
only in head-to-head type games).

start_events:
~~~~~~~~~~~~~
One or more sub-entries, either as a list of events, or key/value pairs of
event names and delay times. (See the
:doc:`/config/instructions/device_control_events` documentation for details
on how to enter settings here.

Default: ``None``

Events in this list, when posted, start the multiball. Note that these events will
only have an effect if this multiball is enabled.

stop_events:
~~~~~~~~~~~~
One or more sub-entries, either as a list of events, or key/value pairs of
event names and delay times. (See the
:doc:`/config/instructions/device_control_events` documentation for details
on how to enter settings here.

Default: ``None``

Events in this list, when posted, stop the multiball. If there are multiball balls
on the playfield, there's nothing that can be done about that (unless you want to
disable the flippers). However stopping the multiball will cut off the "shoot again"
period.

add_a_ball_events:
~~~~~~~~~~~~~~~~~~

.. versionadded:: 0.33

One or more sub-entries, either as a list of events, or key/value pairs of
event names and delay times. (See the
:doc:`/config/instructions/device_control_events` documentation for details
on how to enter settings here.

Default: ``None``

Events in this list, when posted, will add one ball into play. Posting an event
multiple times will add one ball for each time the event is posted.

This is useful for "add-a-ball" functionality (which you can combine with a
counter and/or conditional events if you want to cap how many total balls can
be added into play).

start_or_add_a_ball_events:
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. versionadded:: 0.33

One or more sub-entries, either as a list of events, or key/value pairs of
event names and delay times. (See the
:doc:`/config/instructions/device_control_events` documentation for details
on how to enter settings here.

Default: ``None``

Events in this list, when posted, will either start the multiball, or, if it's
started, will add another ball.

ball_count_type:
~~~~~~~~~~~~~~~~

.. versionadded:: 0.31

Set this to either ``total`` or ``add``. Default is ``total``.

This setting controls the behavior of how the multiball calculates the number of
balls it should add into play. Adjusting this setting is useful when you have
multiple (or stacked) multiballs and you want to control how the combined counts
work.

*total*
   Means the ``ball_count:`` setting will provide a target for the total number of
   balls that should be in play when this multiball starts. So if this multiball
   has a ``ball_count: 3``, and it starts when 2 balls are live on the playfield,
   then this multiball will only add 1 more ball to bring the total to 3.

*add*
   Means that the ``ball_count:`` setting will specify the number of balls that are
   added into play on top of whatever number of balls are already in play. So if this
   multiball is set to ``ball_count: 2`` and there are already 2 balls in play, then
   this multiball will add 2 more balls for a total of 4 balls live.
