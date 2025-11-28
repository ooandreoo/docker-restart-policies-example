## Restart *no*: (DEFAULT)

Docker container won't restart after it's stopped

---

## Restart *unless-stopped* policy:

`$ docker container run -d --name testing-restart --restart unless-stopped nginx`
f69561d075f4c2a91d56693ca7fa6b0385b24ca704abca13cbec54bcb3a33a3d

`$ docker container ls`
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
f69561d075f4   nginx     "/docker-entrypoint.…"   6 seconds ago   Up 6 seconds   80/tcp    testing-restart

`$ docker exec -it testing-restart bash`

`root@f69561d075f4:/# /etc/init.d/nginx stop`

`$ docker container ls`
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS         PORTS     NAMES
f69561d075f4   nginx     "/docker-entrypoint.…"   27 seconds ago   Up 2 seconds   80/tcp    testing-restart

`$ docker container ls`
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS          PORTS     NAMES
f69561d075f4   nginx     "/docker-entrypoint.…"   About a minute ago   Up 54 seconds   80/tcp    testing-restart

`$ docker exec -it testing-restart bash`
root@f69561d075f4:/# /etc/init.d/nginx stop

`$ docker container ls`
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS        PORTS     NAMES
f69561d075f4   nginx     "/docker-entrypoint.…"   About a minute ago   Up 1 second   80/tcp    testing-restart

---

## Restart *on-failure* policy:

For this example, we first build a docker image which is located at on-failure folder.
So, we first move to that folder and start executing the following commands:

`$ docker build -t python-failure .`
[+] Building 1.4s (8/8) FINISHED                                                                     docker:default
 => [internal] load build definition from Dockerfile                                                           0.0s
 => => transferring dockerfile: 115B                                                                           0.0s
 => [internal] load metadata for docker.io/library/python:3                                                    0.9s
 => [internal] load .dockerignore                                                                              0.0s
 => => transferring context: 2B                                                                                0.0s
 => [1/3] FROM docker.io/library/python:3@sha256:edf6433343f65f94707985869aeaafe8beadaeaee11c4bc02068fca52dce  0.0s
 => => resolve docker.io/library/python:3@sha256:edf6433343f65f94707985869aeaafe8beadaeaee11c4bc02068fca52dce  0.0s
 => [internal] load build context                                                                              0.0s
 => => transferring context: 135B                                                                              0.0s
 => CACHED [2/3] WORKDIR /usr/src/app                                                                          0.0s
 => [3/3] COPY . .                                                                                             0.0s
 => exporting to image                                                                                         0.2s
 => => exporting layers                                                                                        0.1s
 => => exporting manifest sha256:830db4f728f1ba3949e12470c282270948069f970890b1a108f37b6d82e06f98              0.0s
 => => exporting config sha256:69c383585faf844fca0adddb1a03040a5c88a0ec52d9324d06cdc5a6f6e950df                0.0s
 => => exporting attestation manifest sha256:2497398fe99d2ab4c387b7a194ae49ecaf7cb7ae42b5224939656e7fef1e81b8  0.0s
 => => exporting manifest list sha256:76d298414dc548c47a75a4d386983d8fef60212ed337732581852951f3c2a434         0.0s
 => => naming to docker.io/library/python-failure:latest                                                       0.0s
 => => unpacking to docker.io/library/python-failure:latest                                                    0.0s

`$ docker run -d --name python-failure-container --restart on-failure python-failure`
c550491b1f1452bab2a98f027993a12bacddeac3ec221a75e5358d0ee57acb72

`$ docker container ls`
CONTAINER ID   IMAGE            COMMAND              CREATED         STATUS                                  PORTS     NAMES
c550491b1f14   python-failure   "python ./main.py"   3 seconds ago   Restarting (1) Less than a second ago             python-failure-container

`$ docker container ls`
CONTAINER ID   IMAGE            COMMAND              CREATED         STATUS         PORTS     NAMES
c550491b1f14   python-failure   "python ./main.py"   6 seconds ago   Up 2 seconds             python-failure-container

`$ docker container ls`
CONTAINER ID   IMAGE            COMMAND              CREATED         STATUS         PORTS     NAMES
c550491b1f14   python-failure   "python ./main.py"   9 seconds ago   Up 2 seconds             python-failure-container

`$ docker container ls`
CONTAINER ID   IMAGE            COMMAND              CREATED          STATUS                                  PORTS     NAMES
c550491b1f14   python-failure   "python ./main.py"   10 seconds ago   Restarting (1) Less than a second ago             python-failure-container

`$ docker container ls`
CONTAINER ID   IMAGE            COMMAND              CREATED          STATUS         PORTS     NAMES
c550491b1f14   python-failure   "python ./main.py"   14 seconds ago   Up 3 seconds             python-failure-container

`$ docker container ls`
CONTAINER ID   IMAGE            COMMAND              CREATED          STATUS                  PORTS     NAMES
c550491b1f14   python-failure   "python ./main.py"   15 seconds ago   Up Less than a second             python-failure-container

---

## Restart *always* policy:

It will always restart, even if docker is restarted. That last part is what
makes it particularly useful and different.

`$ docker container run -d --name immortal-container --restart always nginx`
1eb8a5cbd0d85e0eba2f7689b94c1b664acdde3559a4e65aa88c7581d88acad0

`$ docker ps`
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
1eb8a5cbd0d8   nginx     "/docker-entrypoint.…"   18 seconds ago   Up 17 seconds   80/tcp    immortal-container

`$ docker exec -it immortal-container bash`

`root@1eb8a5cbd0d8:/# /etc/init.d/nginx stop`

`$ docker ps`
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS        PORTS     NAMES
1eb8a5cbd0d8   nginx     "/docker-entrypoint.…"   50 seconds ago   Up 1 second   80/tcp    immortal-container

`$ docker stop immortal-container`
immortal-container

`$ docker ps`
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

`$ sudo systemctl restart docker`
[sudo] password for xxxxx:

`$ docker ps`
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS         PORTS     NAMES
1eb8a5cbd0d8   nginx     "/docker-entrypoint.…"   About a minute ago   Up 2 seconds   80/tcp    immortal-container
