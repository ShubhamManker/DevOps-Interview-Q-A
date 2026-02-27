Interview Questions & Answers for Docker --

1) What is Docker?
Docker is a platform to package and run applications in containers so they behave consistently across environments (dev/test/prod).

2) What is a container?
A container is a running instance of an image—an isolated process with its own filesystem, network interfaces, etc.

3) What is an image?
An image is a static template (layers + metadata) used to create containers.

4) Container vs VM?
VMs virtualize hardware and include full guest OS; containers share the host kernel and are lighter and faster to start.

5) What is Docker Engine?
It’s the runtime that builds and runs containers: daemon (dockerd), REST API, CLI.

6) What is Docker Desktop?
A packaged Docker environment for Windows/macOS (and Linux) including UI, engine, and helpful tooling.

7) What is a Dockerfile?
A text file describing how to build an image (base image, copy files, run commands, default start command).

8) What is a Docker registry?
A storage service for images (e.g., Docker Hub, private registry). You push/pull images from it.

9) What does “immutable infrastructure” mean in Docker context?
Instead of modifying servers, you build a new image and redeploy containers, improving repeatability.

10) What is a Docker layer?
Most Dockerfile instructions create layers; layers are cached to speed builds.

11) What is .dockerignore used for?
To exclude files from build context (faster builds, fewer secrets included).

12) What is the “build context”?
The directory content sent to the builder when you run docker build .—keep it minimal.

13) Explain FROM.
Defines the base image and starts a build stage. Dockerfile must begin with FROM.

14) Explain RUN.
Executes commands during build (e.g., install packages) and creates a new layer.

15) Explain COPY vs ADD.
COPY copies local files/directories. ADD can also fetch URLs and auto-extract archives—use COPY unless you need ADD.

16) Explain CMD vs ENTRYPOINT.

CMD provides default command/args (easy to override).
ENTRYPOINT sets the executable; CMD often supplies default args.

17) What does WORKDIR do?
Sets working directory for subsequent instructions and runtime.

18) What does EXPOSE do?
Documents the port the container listens on; does not publish it automatically.

19) What does ENV do?
Sets environment variables used at build/runtime.

20) What does USER do and why is it important?
Sets non-root user for subsequent steps and runtime, reducing risk.

21) How do you build an image?
docker build -t myapp:1.0 .

22) How do you tag an image?
docker tag myapp:1.0 registry.example.com/myapp:1.0

23) How do you push/pull an image?
docker push … and docker pull …

24) What is multi-stage build and why use it?
Multiple FROM stages; copy only necessary artifacts into final stage to reduce size/attack surface.

25) Name 5 Dockerfile best practices.
Use small trusted base images, multi-stage builds, pin versions, use .dockerignore, run as non-root, minimize layers/cleanup.

26) How do you reduce image size?
Use slim/alpine base, multi-stage builds, remove caches, avoid build tools in final image.

27) How do you pin image versions safely?
Use explicit tags (e.g., ubuntu:24.04) or digests for reproducibility.

28) What are “dangling images” and how do you clean them?
Untagged intermediate layers. Use docker image prune.

29) How do you run a container?
docker run IMAGE (pulls if needed).

30) What does -d do?
Runs container in detached mode (background).

31) What does --name do?
Assigns a readable container name.

32) What does --rm do?
Auto-removes container when it exits (good for one-off jobs).

33) How do you list containers?
docker ps (running), docker ps -a (all).

34) How do you stop/start/restart a container?
docker stop, docker start, docker restart.

35) How do you delete a container?
docker rm <container> (use -f to force remove).

36) How do you view container logs?
docker logs -f <container>

37) How do you execute a command inside a running container?
docker exec -it <container> sh (or bash if available)

38) How do you inspect container configuration?
docker inspect <container> (JSON details: env, mounts, networks, etc.)

39) How do you check resource usage?
docker stats (CPU/mem/net I/O).

40) How do you copy files to/from a container?
docker cp <container>:<path> <dest> and vice versa.

41) What is the default Docker network?
Typically bridge network for local containers.

42) Difference between bridge and host networking?

bridge: NAT + port publishing
host: container shares host network namespace (no port mapping needed)

43) How do you publish ports?
-p HOST:CONTAINER (e.g., -p 8080:80).

44) What’s the difference between EXPOSE and -p?
EXPOSE documents intent; -p actually publishes ports.

45) How do containers communicate by name?
On a user-defined bridge network, Docker DNS resolves service/container names.

46) What is --link and should you use it?
Legacy mechanism; prefer user-defined networks.

47) How do you list Docker networks?
docker network ls

48) How do you create and use a custom network?
docker network create mynet then docker run --network mynet …

49) How do you troubleshoot “connection refused” between containers?
Check port binding, container listening address (0.0.0.0), network membership, firewall rules.

50) How do you assign a static IP?
Create subnet with docker network create --subnet … and run with --ip … (use sparingly).

51) What is a volume?
Docker-managed persistent storage independent of container lifecycle.

52) What is a bind mount?
Mounts a host path into container (great for local dev, riskier in prod).

53) When use volume vs bind mount?
Volume for production persistence/portability; bind mount for local iteration or controlled host paths.

54) How do you create a named volume?
docker volume create mydata

55) How do you mount a volume?
docker run -v mydata:/var/lib/app …

56) What happens if you remove a container—do volumes get removed?
Not unless you remove them explicitly (or use compose down with volume removal options).

57) How do you back up a volume?
Run a helper container that mounts the volume and tars its content to a bind mount.

58) Common pitfall with bind mounts?
Permissions mismatch between host user and container user; fix with correct UID/GID or --chown during copy/build.

59) What is Docker Compose?
Tool to define and run multi-container apps via compose.yml (services, networks, volumes).

60) docker compose up vs start?
up creates (and starts) services; start starts existing stopped containers created earlier.

61) Difference: docker compose stop vs docker compose down?
stop stops containers but does not remove them.
down stops and removes containers and networks (and optionally volumes/images).

62) How do you rebuild images with Compose?
docker compose up --build

63) How do you scale a service?
docker compose up --scale web=3 (works well for stateless services).

64) How do you pass environment variables in Compose?
Use environment: or env_file:; prefer secrets for sensitive values.

65) How do you view logs for Compose services?
docker compose logs -f or docker compose logs -f <service>

66) How do you remove everything including volumes?
docker compose down -v (or --volumes).

67) Why is running containers as root risky?
If compromised, root inside container increases blast radius; misconfig + kernel issues can lead to host impact. Prefer non-root and least privilege.

68) How do you run as non-root?
Create a user in Dockerfile and set USER, or use docker run -u.

69) What is Docker “rootless mode”?
Runs daemon and containers without root privileges, reducing risk of daemon/runtime vulnerabilities.

70) List 6 container hardening steps.
Use minimal base images, run as non-root, don’t mount /var/run/docker.sock, drop capabilities, read-only FS where possible, scan images & rebuild frequently.
