

```yaml

- name: Record if container exists
  shell: docker inspect postgrescont
  register: has_container 

- name: Check if container exists
  fail: msg="The container does not exist"
  when: has_container.stderr.find('ERROR') != -1

```
