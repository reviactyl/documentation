# Updating Agent

Upgrading Agent is a painless process and should take less than a minute to complete.

{% stepper %}
{% step %}
## Download the Updated Binary

First, download the updated agent binary into `/usr/local/bin`. You will need to stop Agent briefly. _Your running servers will **not** be affected._

```bash
systemctl stop agent
curl -L -o /usr/local/bin/agent "https://github.com/reviactyl/agent/releases/latest/download/agent_linux_$([[ "$(uname -m)" == "x86_64" ]] && echo "amd64" || echo "arm64")"
chmod u+x /usr/local/bin/agent
```
{% endstep %}

{% step %}
## Restart Process

Finally, restart the agent process. Your running servers will not be affected and any open connections to the instance will re-connect automatically.

```bash
systemctl restart agent
```
{% endstep %}
{% endstepper %}

_Told ya! It's easy and straight forward_!
