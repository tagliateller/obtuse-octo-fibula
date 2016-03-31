# Start
vagrant up --no-provision
vagrant provision

# Fehler

TASK: [shell getent ahostsv4 {{ openshift.common.hostname }} | head -n 1 | awk '{ print $1 }'] *** 
ok: [master]
ok: [node2]
ok: [node1]

TASK: [Warn user about bad openshift_hostname values] ************************* 
fatal: [master] => Traceback (most recent call last):
  File "/usr/local/lib/python2.7/dist-packages/ansible/runner/__init__.py", line 586, in _executor
    exec_rc = self._executor_internal(host, new_stdin)
  File "/usr/local/lib/python2.7/dist-packages/ansible/runner/__init__.py", line 789, in _executor_internal
    return self._executor_internal_inner(host, self.module_name, self.module_args, inject, port, complex_args=complex_args)
  File "/usr/local/lib/python2.7/dist-packages/ansible/runner/__init__.py", line 1036, in _executor_internal_inner
    result = handler.run(conn, tmp, module_name, module_args, inject, complex_args)
  File "/usr/local/lib/python2.7/dist-packages/ansible/runner/action_plugins/pause.py", line 103, in run
    tcflush(sys.stdin, TCIFLUSH)
error: (25, 'Inappropriate ioctl for device')


FATAL: all hosts have already failed -- aborting

PLAY RECAP ******************************************************************** 
           to retry, use: --limit @/home/robert/vagrant.retry

localhost                  : ok=12   changed=0    unreachable=0    failed=0   
master                     : ok=22   changed=5    unreachable=0    failed=1   
node1                      : ok=22   changed=5    unreachable=0    failed=1   
node2                      : ok=22   changed=5    unreachable=0    failed=1   

Ansible failed to complete successfully. Any error output should be
visible above. Please fix these errors and try again.
robert@c3p0:~/workspace/openshift-ansible$ 
