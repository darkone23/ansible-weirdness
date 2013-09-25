This just documents some weird global scoping issues in ansible roles and includes in general.

It looks like templates aren't agressively evaluated, so while priority still holds, sometimes when you pass along 'foo' it won't evaluate and you'll wind up using someone elses value.

What you probably want is ansible to blow up and tell you that you are referencing undefined variables.
In this case, that doesn't happen!

    $ ansible-playbook -i hosts.ini site.yaml

    PLAY [local] ****************************************************************** 
    
    GATHERING FACTS *************************************************************** 
    ok: [localhost]
    
    TASK: [debug msg="Running bar: { foo: foo from dependency when bar was 'bar from dependency', bar: bar from dependency }"] *** 
    ok: [localhost] => {"item": "", "msg": "Running bar: { foo: foo from dependency when bar was 'bar from dependency', bar: bar from dependency }"}
    
    TASK: [debug msg="Running foo: { foo: leaked bar bar's $E(RET, bar: foo's bar }"] *** 
    ok: [localhost] => {"item": "", "msg": "Running foo: { foo: leaked bar bar's $E(RET, bar: foo's bar }"}
    
    TASK: [debug msg="Running bar: { foo: bar's foo, bar: leaked foo bar's foo }"] *** 
    ok: [localhost] => {"item": "", "msg": "Running bar: { foo: bar's foo, bar: leaked foo bar's foo }"}
    
    PLAY RECAP ******************************************************************** 
    localhost                  : ok=4    changed=0    unreachable=0    failed=0  

Here, we see vars from the role 'bar' leaked into the global playbook scope, and made available to the role 'foo'.   

In the role dependency, when foo depends on bar, it attempts to use its value of foo when defining bar for the include, but because of the lack of agressive evaluation we wind up with it evaluating with the value of '{{ foo }}' instead, which has a different meaning to bar.

I think this is caused by 'call by name' template evaluation instead of 'call by value' -- Could we force aggresive evaluation of things like lookup plugins, templated vars, filters, and company? 
