# Terminology

**Panel** — This refers to Reviactyl Panel itself, and is what allows you to add additional nodes and servers to the system.

**Node** — A node is a physical machine that runs an instance of Wings.

**Agent** — _(Formerly Known as "Wings")_ The daemon service written in Go that interfaces with Docker and the Panel to provide secure access for controlling servers via the Panel.

**Server** — In this case, a server refers to a running instance that is created by the panel. These servers are created on nodes, and you can have multiple servers per node.

**Docker** — Docker is a platform that lets you separate the application from your infrastructure into isolated, secure containers.

**Docker Image** — A Docker image contains everything needed to run a containerized application. (e.g. Java for a Minecraft Server).

**Container** — Each server will be running inside an isolated container to enforce hardware limitations (such as CPU and RAM) and avoid any interference between servers on one node. These are created by Docker.

**Nest** — Each nest is usually used as a specific game or service, for example: Minecraft, Teamspeak or Terraria and can contain many eggs.

**Egg** — Each egg is usually used to store the configuration of a specific type of game, for example: Vanilla, Spigot or Bungeecord for Minecraft.

**Image** — A curated collection of core docker images that can be used with Reviactyl's Egg system.

### Simple Setup Diagram

```mermaid
flowchart TD
    Panel --> Node1["Node #1"]
    Panel --> Node2["Node #2"]
    Panel --> Noden["Node #n"]

    Node1 --> Server1["Server #1"]
    Node1 --> Server2["Server #2"]
    Node1 --> Server3["Server #3"]
    Node1 --> Servern["Server #n"]
```

### Advanced Setup Diagram

{% hint style="info" %}
**Agent and Panel in same machine**\
Yes, it's possible to run Agent and Panel on the same machine.
{% endhint %}

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>
