---
description: Steps to Build Reviactyl Agent (GO)
---

# Building Agent

{% hint style="warning" %}
Do **not** run the following steps on your production nodes.
{% endhint %}

Agent is written in Go. This makes it very easy to modify and compile it on your own, and distribute your own binaries. This guide will cover the steps necessary to build it yourself.

It will not, however, explain where to look for certain aspects of Agent and which changes are necessary to achieve specific results. Knowledge of the Go language is required if you want to modify it.

Building Go programs is very easy, and the same also applies to Agent. Go is cross-platform, but Agent only supports Linux at the moment. The easiest way to compile it for Linux is to run the commands on a Linux machine.

### Build Requirements

An up to date version of Go is required to compile Agent. The minimum version can be found at the top of the [go.mod](https://github.com/reviactyl/agent/blob/develop/go.mod) file. See the [official instructions](https://golang.org/doc/install) for help with installing Go.

### Building

Execute the following command in your local clone of the repository to compile Agent into a binary.

```bash
go build
```

### Install the new binary

{% hint style="info" %}
**Root Required**\
Some the following commands require root permissions. Prepend them with `sudo` if you are not logged in as root.
{% endhint %}

{% stepper %}
{% step %}
### Backup the current installation of wings

```sh
mv /usr/local/bin/agent /usr/local/bin/wings-agent
```
{% endstep %}

{% step %}
### Place the new binary in /usr/local/bin

```shellscript
cp ./agent /usr/local/agent
```
{% endstep %}

{% step %}
### Restart Agent

```shellscript
systemctl restart agent
```
{% endstep %}
{% endstepper %}

### Troubleshooting

If the wings service does not start properly, you can try to start Wings in a console window.

```shellscript
agent --debug
```

Remember to stop the system service before, and re-enable it afterwards.

```shellscript
systemctl stop agent
systemctl start agent
```
