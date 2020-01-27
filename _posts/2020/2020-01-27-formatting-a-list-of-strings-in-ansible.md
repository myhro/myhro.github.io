---
date: 2020-01-27
layout: post
title: Formatting a list of strings in Ansible
...

My [Kubernetes Ansible setup][ansible-k8s] - which is, to this date, still the easiest way to bootstrap an on-premises Kubernetes cluster - has a task that installs the packages tied to a specific Kubernetes version. When using it with an Ansible version newer than the one used when it was written, I was bothered by the following deprecation warning:

{% raw %}
```
[DEPRECATION WARNING]: Invoking "apt" only once while using a loop via
squash_actions is deprecated. Instead of using a loop to supply multiple items
and specifying `name: "{{ item }}={{ kubernetes_version }}-00"`, please use
`name: '{{ kubernetes_packages }}'` and remove the loop. This feature will be
removed in version 2.11. Deprecation warnings can be disabled by setting
deprecation_warnings=False in ansible.cfg.
```
{% endraw %}

This warning a bit misleading. It's clear that `item`, which comes from the `kubernetes_packages` variable used in a `with_items` option, is just one part of the equation. The package name is being interpolated with its version, glued together with other characters (`=` and `-00`) that will produce something like `kubectl=1.17.2-00`. Changing it to `kubernetes_packages` isn't enough. The process of replacing this in-place interpolation by a proper list, as Ansible wants, can be achieved in some ways like:

- Write down a list that interpolates hard-coded package names with version, like: {% raw %}``kubectl={{ kubernetes_version }}-00``{% endraw %}. The problem is that this pattern has to be repeated for every package.
- Find a way do generate this list dynamically, by applying the interpolation to every item of the `kubernetes_packages` list.

Repetition isn't always bad, but I prefer to avoid it here. The latter option can be easily achieved in any programming language with functional constructs, like JavaScript, which offers a `map()` array method that accepts a function (here, an [arrow function][arrow-functions]) as the argument and returns another array:

```javascript
let pkgs = ['kubelet', 'kubectl', 'kubeadm'];

let version = '1.17.2';

pkgs.map(p => `${p}=${version}-00`);
(3) ["kubelet=1.17.2-00", "kubectl=1.17.2-00", "kubeadm=1.17.2-00"]
```

Python, the language in which Ansible is written, offers a `map()` function which accepts a function (here, a [lambda expression][lambda]) and a list as arguments. The object it returns can then be converted to a list:

```python
In [1]: pkgs = ['kubelet', 'kubectl', 'kubeadm']

In [2]: version = '1.17.2'

In [3]: list(map(lambda p: '{}={}-00'.format(p, version), pkgs))
Out[3]: ['kubelet=1.17.2-00', 'kubectl=1.17.2-00', 'kubeadm=1.17.2-00']
```

That's be supposed to be similarly easy in Ansible, given that [Jinja][jinja], its template language, offers a [`format()` filter][jinja-format]. The problem is that [it does not - and will not - support combining `format()` and `map()` filters][jinja-issue]. Another way to do the same would be to use the `format()` filter in a list comprehension, but that's also not supported. But not all hope is lost, as Ansible supports [additional regular expression filters][ansible-regex], like `regex_replace()`. It can be used in many different ways, but here we will use it for [doing a single thing][map-concat]: concatenate the package name with a suffix made of another string concatenation operation. This way, the following task:

{% raw %}
```yaml
- name: install packages
  apt:
    name: "{{ item }}={{ kubernetes_version }}-00"
  with_items: "{{ kubernetes_packages }}"
```
{% endraw %}

Is equivalent to:

{% raw %}
```yaml
- name: install packages
  apt:
    name: "{{ kubernetes_packages |
      map('regex_replace', '$', '=' + kubernetes_version + '-00') |
      list
    }}"
```
{% endraw %}

The key is that the `'$'` character [matches the end of the string][regex], so replacing it is akin to concatenating two strings. The `list` filter in the end is needed because, just like the equivalent Python built-in function, the Jinja `map()` filter also returns a generator. This object then needs to be converted to a list, otherwise, it would result in errors like `No package matching '<generator object do_map at 0x10bbedba0>' is available`, given that its string representation will be used as the package name.


[ansible-k8s]: https://github.com/myhro/ansible-k8s
[ansible-regex]: https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html#regular-expression-filters
[arrow-functions]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions
[jinja-format]: https://jinja.palletsprojects.com/en/2.10.x/templates/#format
[jinja-issue]: https://github.com/pallets/jinja/issues/545
[jinja]: https://jinja.palletsprojects.com/
[lambda]: https://docs.python.org/3/tutorial/controlflow.html#lambda-expressions
[map-concat]: https://stackoverflow.com/q/49697899
[regex]: https://en.wikipedia.org/wiki/Regular_expression#POSIX_basic_and_extended
