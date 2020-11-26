%title: Vagrant - Kubernetes
%author: xavki


# Vagrant : cluster kubernetes avec kubespray


<br>


* objectifs :
		* disposer rapidement/facilement d'un k8s local
		* apprendre vagrant
		* découvrir comment installer k8s
		* avoir une pseudo prod (haproxy)
		* scripter (vagrant et bash)


<br>


* fonctionnement par étapes pédagogiques et courtes


<br>


* représentation de l'infrastructure

```
                     +----------------+          +----------------+
                     | Srv Deployment +----------> Srv Haproxy    |
              +-----------------------+          +----------------+
              |                  |
              |                  +----------------------v
   +----------v-----+     +------v---------+     +----------------+
   | Srv Kworker    |     | Srv Kworker    |     | Srv Kmaster    |
   +----------------+     +----------------+     +----------------+
```

--------------------------------------------------------------------------------------

# Vagrant : cluster kubernetes avec kubespray


<br>


* principe de flux pour kubernetes

```
                           +----------------+
                           | Rq Cliente     |
                           +----------------+
                                   |
                           +-------v--------+       Port6443
              Port80       | Srv Haproxy    +-------------+
                           +----------------+             |
               +-------------------+                      |
               |                   |                      |
    +----------v-----+     +-------v--------+     +-------v--------+
    | Srv Kworker    |     | Srv Kworker    |     | Srv Kmaster    |
    +----------------+     +----------------+     +----------------+
```

--------------------------------------------------------------------------------------

# Vagrant : cluster kubernetes avec kubespray


<br>


* stockage via nfs et objectif wordpress derrière nginx controller

```
                           +----------------+
                           | Rq Cliente     |
                           +----------------+
                                   |
                           +-------v--------+       Port6443
              Port80       | Srv Haproxy    +-------------+
                           +----------------+             |
               +-------------------+                      |
               |                   |                      |
    +----------v-----+     +-------v--------+     +-------v--------+
    | Srv Kworker    |     | Srv Kworker    |     | Srv Kmaster    |
    +----------------+     +-------------^--+     +----------------+
        |                                |
        |                                |
        |   +----------------------+     |
        |   |                      |     |
        +---+  SrvDeployment-------------+
            |              | NFS   |
            +----------------------+
```

<br>


* possibilité d'installer wordpress

Haproxy > ingress > services > pods (WP/BDD)
