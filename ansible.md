---
layout: page
title: Ansible
permalink: /Ansible
---

## YAML
List in YAML 
```
languages:
	- python
	- golang
	- c++
```
Dictionaries
```
book:
	name: learning Ansible
	author: someone
	brief: Getting started with Ansible
```
dictionaries and list as part of each other
```
  - foo:
      name: Foo Bar
      job: Awesome Administrator
      languages:
        - perl
        - python
  - learn:
      name: Learn Moar
      job: Sr Awesome Administrator
      languages:
        - ansible
        - ruby
        - awk
```
abbreviated forms of list and dictionary
```
  foo: {name: Foo Bar, job: Awesome Administrator, skill: High}
  foods: ['bacon', 'pizza', 'steak', 'blueberries']
```
New line '|' vs Single line '>' 
```
  with_newlines: |
    the text is going
    to look
    exactly like this

  single_line: >
    this text is
    going to present as a
    single line
```
## Ansible/Tower Terminologies 
Inventories: An inventory is a collection of hosts managed by Tower
