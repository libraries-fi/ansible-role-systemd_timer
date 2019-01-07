systemd_timer
=========

Create systemd timers for recurring tasks.

Requirements
------------

None.

Role Variables
--------------

Only one variable is required, *systemd_timer_jobs*, that takes an array of objects.

Job properties:
* name: **mandatory** name of the timer and related unit.
* on_calendar: **mandatory** Timer calendar event: https://www.freedesktop.org/software/systemd/man/systemd.time.html#Calendar%20Events
* job: List of bash script strings. This or exec_start is **mandatory**.
* exec_start: Path to executable and arguments. Passed to Systemd unit ExecStart. This or job is **mandatory**.
* working_directory: Unit working directory.
* persistent: Boolean, if timer is run after host has been down and deadline has passed, default is true.
* user: Unit user, default is root.
* dynamic_user: Boolean, use dynamic user instead of user, see: systemd.exec(5). Default is false.
  Use this if you want to skip creating an user and no special permissions are needed.
* runtime_max_sec: Configure timeout for running the service, default is 'ininite'.
* restart: Optional restarting for failing job. Object.
    * timeout: Timeout between restarts, must be specified in seconds.
    * tries: How many times service is tried to run, before giving up.

Dependencies
------------

None.

Example
----------------
```yaml
- role: libraries-fi.systemd-timer
  systemd_timer_jobs:
    - name: "task_using_jobs"
      on_calendar: "*-*-* 00:15:00"
      job:
        - cd foo
        - cp bar bar2
    - name: "task_using_exec"
      on_calendar: "*-*-* 00/3:59:00"
      user: "www-data"
      exec_start: "run_thing.sh"
      working_directory: "/some_path"
    - name: "task_with_restarting"
      on_calendar: "*-*-* 00:00/10:00"
      restart: # If task fails, it is retried 3 times in 1 minute.
        interval: 60
        tries: 3
      job:
        - cd foo
        - cp bar bar2
    - name: "task_with_time_limit"
      on_calendar: "*-*-* 00/3:59:00"
      runtime_max_sec: 60
      user: "www-data"
      exec_start: "run_thing.sh"
      working_directory: "/some_path"
```

License
-------

MIT

Author Information
------------------

Libraries.fi
