role bug: dependencies in absolute/relative paths & role dependencies break

from project a:

    ansible-playbook -i ../hosts.ini site.yaml
    
    PLAY [local] ****************************************************************** 
    
    GATHERING FACTS *************************************************************** 
    ok: [localhost]
    
    TASK: [debug msg="Running role b..."] ***************************************** 
    ok: [localhost] => {"item": "", "msg": "Running role b..."}
    
    TASK: [debug msg="Running role a..."] ***************************************** 
    ok: [localhost] => {"item": "", "msg": "Running role a..."}
    
    PLAY RECAP ******************************************************************** 
    localhost                  : ok=3    changed=0    unreachable=0    failed=0

from project b:

    ansible-playbook -i ../hosts.ini site.yaml
    ERROR: cannot find role in /home/egghead/projects/python/ansible-weirdness/project-b/roles/b or /home/egghead/projects/python/ansible-weirdness/project-b/b
