# Docker Fundamentals 

## Learning Objectives 

- Explain the purpose of Docker images and containers. 
- Run and manage a Docker container using the CLI. 
- Insepct a running container with `docker logs` and `docker exec`. 
- Pull images from Docker Hub and work with tags. 

## Docker CLI: Essential Commands 

### Key Commands: 

- `docker pull <image>`: Pull an image from a registry (e.g., Docker Hub). 
- `docker images`: List all Docker images available on your machine. 
- `docker run <image>`: Create and start a container from an image. 
- `docker ps`: List running containers 
- `docker ps- a`: LIst all containers, including stopped ones. 

## Hands-On: Running Your First Docker Commands 

### **Step 1**: Pulling an Image

To download an image, use the `docker pull` command. This pulls an iamge from Docker Hub if it's not already on your machine. 

```bash
docker pull nginx
``` 

**Expected Output:**

``` 
using default tag: latest
latest: Pull form library/nginx
...
Status: Download newer image for nginx:latest
```

### **Step 2**: Listing images

After pulling, list all iamges on your system using: 

```bash 
docker images
```

You should see the `nginx` image along with ites details like repository, tag, image ID, and size. 

### **Step 3**: Running a Container 

Now, let's start a container from this image: 

```bash 
docker run -d -p 8080:80 nginx
```

**What this command does**: Runs an `nginx` container in detached mode (`d`), mapping the container's port 80 to port 8080 on your machine. 

### **Step 4**: Checking Running Containers 

Verify that your container is running by listing all active containers. 

```bash
docker ps
```

The output shows a container ID, the image (`nginx`), the command that started it, and the status. 

### **Step 5**: Looking Inside a Running Container

A container is not a black box. Two commands answer most fo the "why is this not doing what I expect?"  questions, and you will reach for them constantly.

First, see what the container has printed to its output: 

```bash
docker logs <container-id>
```

Next, running a command ***inside*** the continaer. This one lists the directory `nginx` serves files from: 

```bash 
docker exec <container-id> ls /usr/share/nginx/html
```

For more than one command, ask for an interactive shell instead with `-it` (**i**nteractive + **t**ty) and leave it with `exit`: 

```bash 
docker exec -it <container-id> /bin/bash
```

Inside the shell you are in the container's filesystem, not your own - a good way to confirm that the files you expected actually made into the image. 

> `docker exec` needs a ***running*** container - `docker logs` does not
> This distinction is the whole diagnostic trick. Try to `exec` into a comtainer that has stopped and Docker refuses: 
> ```bash
> Error response from daemon: container ... is not running
> ```
> But `docker logs` still works on a stopped container, and still shows everything it printed on the way down. So when something is wrong: 
> - **The container exited ->** `docker logs`. It is the only one of the two that can well you why, and the reason is almost always in the last few lines.
> - **The container is running but behaving wrong ->** `docker exec`. Go in and check what is actually there.

### **Step 6**: Stopping and Removing Containers

Once you're done, stop and remove the container to free up resources. 

```bash 
docker stop <container-id>
docker rm <container-id>
```

Replace `<container-id>` with the actual ID shown in `docker ps`.

## Working with Tags in Docker Images

Images on Docker Hub often come with tags to indicate different versions. The `:latest` tag is applied by default if you don't specify a version, but specific tags help manage versions more reliably. 

**Example**: 

```bash
docker pull nginx:1.19
```

This command pulls a specific version of `nginx` tagged as `1.19`. Use `docker images` to verify the version.

## Summary 

These fundamentals are essential for working efficiently with Docker in any environment, as they form the foundation for building, testing, and deploying applications. 