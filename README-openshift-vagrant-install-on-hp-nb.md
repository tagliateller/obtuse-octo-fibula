* Installation OpenShift Origin 
** Vagrant auf lokalem NB

./openshift-ansible

vagrant up --no-provision
vagrant provision

PLAY RECAP ******************************************************************** 
localhost                  : ok=21   changed=0    unreachable=0    failed=0   
master                     : ok=335  changed=64   unreachable=0    failed=0   
node1                      : ok=101  changed=29   unreachable=0    failed=0   
node2                      : ok=101  changed=29   unreachable=0    failed=0   

** TODO
*** Hochfahren - geht das ?
*** Tutorials abarbeiten siehe paas/tutorials
*** ManageIQ -> Monitoring klären
