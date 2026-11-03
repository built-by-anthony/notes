# Images

## Learning Objectives

- Explain the purpose and structure of a Dockerfile. 
- Write a basic Dockerfile for your application. 
- Build a Docker image from a Dockerfile using the Docker CLI. 
- Use tags to version and organize custom images. 

## What is a Dockerfile?

A **Dockerfile** is a text file with a set of instructions that Docker reads to automatically build an image. These instructions define the base image, environment configuration, dependencies, and application code necessary for the containerized application. Each instruction creates a new **layer**, allowing Docker to cache steps and speed up builds. 

## Structure of a Dockerfile 

Dockerfiles typically follow this structure: 

- `FROM`: Base image
- `WORKDIR`: Working directory inside the container 
- `COPY`: Copy files from your project into the container
- `Run`: Run commands to install dependencies or set up the environment
- `Expose`: Document the port the app will use 
- `CMD`: Command to start the application, which arguments to `docker run` can replace 
- `ENTRYPOINT`: Command that always runs, with arguments to `docker run` appended to it

## Hands-On: Writing Your Dockerfile

### Example: Python (Flask)

```Dockerfile
# Step 1: Use the official Python image as the base 
FROM python:3.13-slim

# Step 2: Set the working directory inside the container
WORKDIR /app

# Step 3: Copy requirements.txt and isntall dependencies
COPY requirements.txt . 
RUN pip install --no-cache-dir -r requirements.txt

# Step 4: Copy the resot of the application files 
COPY . . 

# Step 5: Expose the port the app will use
EXPOSE 8080

# Step 6: Drop from root to an unprivileged user
RUN useradd -m appuser && chown -R appuser /app
USER appuser

# Step 7: Start the application with gunicorn
CMD ["gunicorn", "--bind", "0.0.0.0:8080", "app:app"]
```

## Building and Tagging an Image 

### Step 1: Build the Image

From the terminal in the project directory 

```bash
docker build -t <my-flask_app> .
```

This command: 

- Tags the image as `my-flask-app`
- Uses the current directory (`.`) as the build context

**Expected Output**

Docker should show output for each layer and end with something like: 

```bash
Successfully tagged my-flask-app:latest
```

### Step 2: Verify the Image 

Run: 

```bash
docker images
```

Look for `my-flask-app` with the `latest` tag. 

## Running Your Custom Image 

Start a container: 

```bash
docker run -p 8080:8080 my-flask-app
```

This maps port 8080 in the container to port 8080 on your host. Access the app at: 

```
http://localhost:8080
```

## Updating and Rebuilding the Image 

### Step 1: Update Your Code 

Make changes to your application code. 

### Step 2: Rebuild the Docker Image

```bash
docker build -t my-flask-app .
```

### Step 3: Rerun the Container 

```bash
docker run -p 8080:8080 my-flask-app
```

### Step 4: Clean Up Containers

If you didn't name the container when running it, you'll need to stop and remove it using its **container ID**. 

1. First, find the container ID: 

```bash
docker ps
```

2. Then stop and remove it: 

```bash
docker stop <container-id>
docker rm <container-id> 
```

3. Or do both in one command: 


```bash
docker rm -f <container-id>
```

> ***Replace `container-id` with the actual ID shown in the `docker ps` output***

## Summary 

In this lesson you: 
- Wrote a Dockerfile for your application
- Built and tagged a Docker image 
- Ran a containerized version of the app 
- Learned how to update and rebuild the image
- Cleaned up containers using container IDs
