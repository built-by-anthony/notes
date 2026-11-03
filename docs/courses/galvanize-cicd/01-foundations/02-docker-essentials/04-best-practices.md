# Best Practices

## Learning Objectives

- Define the role of environment variables in Docker.
- Set environment variables securely in Dockerfiles and through the CLI.
- Use `.env` files with Docker to manage multiple variables. 
- Follow best pratices for securely managing sensitive data in Docker. 

## What are Environment Variables? 

**Environment variables** are key-value pairs that store configuration data outside of your application code. They allow you to change how an application behaves without altering the codebase itself, making applications more flexible across environments like development, testing, and production.  

In Docker, environment variables are crucial for keeping container configurations dynamic and adaptable. They let you specify configuration values at runtime, enabling easy changes to settings like database URLs, API endpoints, and other environment-specific information. 

## Setting Environment Variables in Dockerfiles 

One way to set environment variables in Docker is by defining them directly in a Dockerfile using the `ENV` keyword. This approach is useful for setting default values, especially for non-sensitive data. 

**Example**

```Dockerfile
# Dockerfile 
FROM amazoncorretto:17
ENV SPRING_PROFILES_ACTIVE=production
ENV DB_URL=jdbc:mysql://database:3306/mydb
```

In this example, `SPRING_PROFILES_ACTIVE` and `DB_URL` are environment variables that will be available to the application running inside the continer. 

> **Note**: The `ENV` instruction is not suitable for sensitive data (such as passwords or API keys) because Dockerfiles are often shraed or stored in version control. Instead, use `ENV` for non-sensitive default values. 

## Passing Environment Variables at Runtime 

To set or override environment variables at runtime, use the `-e` flag with the `docker run` command. This approach allows you to configure dynamically, passing in variables without modifying the Dockerfile. 

```Dockerfile 
docker run -e SPRING_PROFILE_ACTIVE=development -e DB_URL=jdbc:mysql://localhost:3306/mydb my-spring-app
```

This method is ideal for non-sensitive values that may need to vary between environments. For example, the `SPRING_PROFILES_ACTIVE` variable could be set to `development`, `staging`, or `production` as needed.

## Using `.env` Files for Multiple Variables 

When dealing with multiple environment variables, a `.env` file can help manage them more efficiently. `.env` files are plain text files that contain key-value pairs, allowing you to store multiple variables in one place. 

### Example `.env` file

```.env
SPRING_PROFILE_ACTIVE=production 
DB_URL=jdbc:mysql://database:3306/mydb
API_KEY=super-secret-key
```

To use a `.env` file with Docker, pass it to the `docker run` command with the `--env-file` option: 

```bash
docker run --env-file .env my-spring-app
```

### Advantages of `.env` files:

- **Separation of Configuration**: Keeps your environment variables separate from the application code, following the 12-factor app principles. 

- **Ease of Management**: Allows you to create different `.env` files for each environment (e.g., `.env.dev`, `.env.prod`) and specify the one you need at runtime.

- **Security**: While `.env` files should be kept out of version control, this method prevents sensitive data from being hardcoded in Dockerfiles.

## Security and Sensitive Information 

Sensitive data, such as passwords, API keys, and database credentials, should never be hardcoded in Dockerfiles or `.env` files that are version controlled. Here are best practices for securely managing sensitive data: 

### Avoid hardcoding Sensitive Data

- **Dockerfile**: Don't store sensitive data in `ENV` instructions within Dockerfiles. This information can be exposed if the image is shared or pushed to a public registry, 

- **`.env` Files**: Avoid committing `.env` files with sensitive data to version control. Add them to `.gitignore` to prevent accidental commits. 

### Using Docker Secrets (Advanced)

For production environments, consider using **Docker Secrets**, which provides a secure way to store and manage sensitive data. Docker Secrets is a feature of Docker Swarm and allows encrypted secrets to be passed into containers only at runtime.

**Example**: 

```bash
echo "super-secret-key" | docker secret create api_key_secret - 
```

**Accessing the Secret in a Container**: Secrets are injected into containers as files in `/run/secrets`, which you can then reference from within the application. 

```bash
docker service create --name my-app --secret api_key_secret my-spring-app
```

Docker secrets ensure that sensitive information is stored separately from environment variables, reducing the risk of exposure. 

### Using External Secret Management Tools 

In cloud environments, secret management tools like **AWS Secrets Manager, HashiCorp Vault,** and **Azure Key Vault** offer a robust way to manage sensitive data outside of Docker. These tools allow you to access secrets securely from your containers without storing them in Dockerfiles or `.env` files. 

## Best Practices Summary 

- 1. **Use Environment Variables for Configuration, Not Core Logic**: Environment variables should be used for configuration values rather than core application logic. This approach improves portability and reduces complexity. 

- 2. **Keep `.env` Files Out of Version Control**: Add `.env` files to `.gitignore` to prevent them from being committed to version control. 

- 3. **Default to Non-Sensitive Values in Dockerfiles**: Use `ENV` instructions only for non-sensitive defaults in Dockerfiles. For sensitive data, rely on runtime methods or secrets management. 

- 4. **Organize Environment Variables by Environment**: Create separate .`env` files for different environments (e.g., `env.dev`, `.env.prod`). This approach simplifies environment-specific configuration management. 

- 5. **Use Docker Secrets or External Tools for Sensitive Data**: For production deployments, use Docker Secrets or external secret management tools to handle sensitive data securely. 

## Summary

You learned best practices for managing environment variables in Docker, including using Dockerfiles, the `-e` flag, and `.env` files. You also explored methods for securing sensitive data, such as Docker Secrets and external xecret management tools. 