# Tips for docker

## Docker commands tips

### Update docker container parameters
```
# Check the policy
docker inspect --format '{{.HostConfig.RestartPolicy.Name}}' [container name]

# Change the restart policy
docker update --restart always [container name]
```

### Cloning an existing container
```
# Check running containers
docker ps
```
And then, clone it with commit as an image
```
# The commit command clone the container as an image
docker commit [id of the existing container] [name of the generated image]
```
A new container can be run from the existing image
```
docker run -d 
  --name [container image] 
  --mount source=[host path],target=[container path] 
  [name of the image generated previously]
```

### Find a standard docker volume

```
docker inspect -f "{{ .Mounts }}" convertigo$i | grep -oP '([0-9a-z]){20,}' | head -1
```

### Copy the content from a docker volume to an other one

```
docker run --rm -i -u root -t -v [source volume]:/from -v [destination volume]:/to [alpine image] sh -c "cp -avr /from/* /to"
```

## Linux tips

### Looking for listening ports

```
# list of listening ports
sudo lsof -i -P -n | grep -i listen

# list of docker related listening ports
sudo lsof -i -P -n | grep -i listen | grep -i docker
```

### Looking for all container names

```
# Check all running container names
docker ps --format "{{ .Names }}"
```

### Looking for a docker volume with the standard naming

```
docker inspect -f "{{ .Mounts }}" [container name] | grep -oP '([0-9a-z]){20,}' | head -1
# -o : display match regex only
# -P : perl regex (-e is not working)
```

## Ansible tips

```
# Check all running container names
- name: Check all containers in the server
  # Check all containers running in the server
  command: docker ps --format "{{ '{{' }} .Names {{ '}}' }}"
  register: containers_list
  become: yes
  become_user: docker
```
