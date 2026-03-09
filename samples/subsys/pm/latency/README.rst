.. zephyr:code-sample:: pm-latency
   :name: Demonstrate usage of the PM policy latency APIs
   :relevant-api: subsys_pm_sys_policy

   Demonstrates usage of the PM policy latency APIs.

Overview
********

This sample demonstrates Zephyr's power management policy latency APIs
using a simulated device and application-level latency constraints.

Using :c:func:`pm_policy_latency_request_add`, latency constraints are
registered, and both applications and drivers can prevent the system from
entering power states whose exit latency would violate their timing requirements.
The sample also shows how to subscribe to latency changes with
:c:func:`pm_policy_latency_changed_subscribe`.


The application performs the following sequence:

#. Sleeps with no latency constraint. The System enters ``RUNTIME_IDLE``,
   ``SUSPEND_TO_IDLE``, and ``STANDBY``.
#. Adds a 30ms application-level constraint and repeats the sleeps.
   ``STANDBY``, with an exit latency of 30ms remains reachable.
#. Opens a test device that registers its own 20ms constraint. The combined minimum
   is now 20ms and ``STANDBY`` is blocked.
#. Updates the application constraint to 10ms via :c:func:`pm_policy_latency_request_update`
   and now only ``RUNTIME_IDLE`` is reachable.
#. Restores the application constraint to 30ms and closes the test device,
   which removes its 20ms constraint with :c:func:`pm_policy_latency_request_remove`.
#. Removes all constraints and ``STANDBY`` is now reachable again.

Requirements
************

This sample runs on ``native_sim`` which requires a Linux host.

Building and Running
********************

Build and run the sample as follows:

.. zephyr-app-commands::
   :zephyr-app: samples/subsys/pm/latency
   :host-os: unix
   :board: native_sim
   :goals: build run
   :compact:

Sample Output
=============

.. code-block:: console

   *** Booting Zephyr OS build zephyr-vX.Y.Z ***
   [00:00:00.000,000] <inf> app: Sleeping for 1.1 seconds, we should enter RUNTIME_IDLE
   [00:00:00.000,000] <inf> soc_pm: Entering RUNTIME_IDLE{0}
   [00:00:01.110,000] <inf> app: Sleeping for 1.2 seconds, we should enter SUSPEND_TO_IDLE
   [00:00:01.110,000] <inf> soc_pm: Entering SUSPEND_TO_IDLE{0}
   [00:00:02.320,000] <inf> app: Sleeping for 1.3 seconds, we should enter STANDBY
   [00:00:02.320,000] <inf> soc_pm: Entering STANDBY{0}
   [00:00:03.630,000] <inf> app: Setting latency constraint: 30ms
   [00:00:03.630,000] <inf> app: Latency constraint changed: 30ms
   [00:00:03.630,000] <inf> app: Sleeping for 1.1 seconds, we should enter RUNTIME_IDLE
   [00:00:03.630,000] <inf> soc_pm: Entering RUNTIME_IDLE{0}
   [00:00:04.740,000] <inf> app: Sleeping for 1.2 seconds, we should enter SUSPEND_TO_IDLE
   [00:00:04.740,000] <inf> soc_pm: Entering SUSPEND_TO_IDLE{0}
   [00:00:05.950,000] <inf> app: Sleeping for 1.3 seconds, we should enter STANDBY
   [00:00:05.950,000] <inf> soc_pm: Entering STANDBY{0}
   [00:00:07.260,000] <inf> app: Opening test device
   [00:00:07.260,000] <inf> dev_test: Adding latency constraint: 20ms
   [00:00:07.260,000] <inf> app: Latency constraint changed: 20ms
   [00:00:07.260,000] <inf> app: Sleeping for 1.1 seconds, we should enter RUNTIME_IDLE
   [00:00:07.260,000] <inf> soc_pm: Entering RUNTIME_IDLE{0}
   [00:00:08.370,000] <inf> app: Sleeping for 1.2 seconds, we should enter SUSPEND_TO_IDLE
   [00:00:08.370,000] <inf> soc_pm: Entering SUSPEND_TO_IDLE{0}
   [00:00:09.580,000] <inf> app: Sleeping for 1.3 seconds, we should enter SUSPEND_TO_IDLE
   [00:00:09.580,000] <inf> soc_pm: Entering SUSPEND_TO_IDLE{0}
   [00:00:10.890,000] <inf> app: Updating latency constraint: 10ms
   [00:00:10.890,000] <inf> app: Latency constraint changed: 10ms
   [00:00:10.890,000] <inf> dev_test: Latency constraint changed: 10ms
   [00:00:10.890,000] <inf> app: Sleeping for 1.1 seconds, we should enter RUNTIME_IDLE
   [00:00:10.890,000] <inf> soc_pm: Entering RUNTIME_IDLE{0}
   [00:00:12.000,000] <inf> app: Sleeping for 1.2 seconds, we should enter RUNTIME_IDLE
   [00:00:12.000,000] <inf> soc_pm: Entering RUNTIME_IDLE{0}
   [00:00:13.210,000] <inf> app: Sleeping for 1.3 seconds, we should enter RUNTIME_IDLE
   [00:00:13.210,000] <inf> soc_pm: Entering RUNTIME_IDLE{0}
   [00:00:14.520,000] <inf> app: Updating latency constraint: 30ms
   [00:00:14.520,000] <inf> app: Latency constraint changed: 20ms
   [00:00:14.520,000] <inf> dev_test: Latency constraint changed: 20ms
   [00:00:14.520,000] <inf> app: Closing test device
   [00:00:14.520,000] <inf> dev_test: Removing latency constraint
   [00:00:14.520,000] <inf> app: Latency constraint changed: 30ms
   [00:00:14.520,000] <inf> dev_test: Latency constraint changed: 30ms
   [00:00:14.520,000] <inf> app: Sleeping for 1.1 seconds, we should enter RUNTIME_IDLE
   [00:00:14.520,000] <inf> soc_pm: Entering RUNTIME_IDLE{0}
   [00:00:15.630,000] <inf> app: Sleeping for 1.2 seconds, we should enter SUSPEND_TO_IDLE
   [00:00:15.630,000] <inf> soc_pm: Entering SUSPEND_TO_IDLE{0}
   [00:00:16.840,000] <inf> app: Sleeping for 1.3 seconds, we should enter STANDBY
   [00:00:16.840,000] <inf> soc_pm: Entering STANDBY{0}
   [00:00:18.150,000] <inf> app: Removing latency constraints
   [00:00:18.150,000] <inf> app: Latency constraint changed: none
   [00:00:18.150,000] <inf> app: Finished, we should now enter STANDBY
   [00:00:18.150,000] <inf> soc_pm: Entering STANDBY{0}

References
**********

* :ref:`Power States Policy <pm-policy-power-states>`
