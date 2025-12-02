# CI-CD-Workflow-Docker
This is a simple Github Workflow intended to work with apps using Docker compose hosted on a remote server. On commit to a chosen branch, this workflow foes the following:

1. Test rebuilding all Docker images on the Github runner. If successful...
2. SSH into your server
3. `git pull`
4. Rebuild Docker images
5. Shut down all associated Docker-compose containers
6. Restart all containers

Usage:

Set up the following on your server:
- `git clone` your repo to a specific path
- Ensure your repo has docker compose set up with a `docker-compose.yml` file
- [Optional, but recomended for security]: Create a seperate user for the Github runner on your server. It should have the bare minimum permissions to run this action. These include access to Git and Docker for the repo folder. Avoid giving it `sudo` access if possible.
- Generate an ssh key pair for the Github runner to use. It is reccomended that this is different from the one you or any other users use. Add the public key to `authorized_keys` for the Github Runner user on the server.

This workflow makes use of environments (ex. `PROD`, `DEV`) on your repo. If you haven't already, create an environment in Github.

Set the following environment variables in Github:
- `APP_PATH`: The filepath to your local repo and Docker root (if these are not the same location, you can change the docker-compose file used in `DOCKER_COMPOSE_CMD`). Ex. `/opt/DOCKER_APP`
- `BRANCH`: The branch name associated with the given environment (used for local `git pull`, etc.) Ex. `main`
- `DOCKER_COMPOSE_CMD`: The base-level command to run for Docker compose. Do not include any sub-commands like `build` or `run`. Do not include any options for those commands either like `-d` (`-d` in particular is added automatically when running). Ex. `docker compose -f docker-compose.yml`
- `SSH_HOST`: The server address or IP address for the SSH host. Ex. `web.myserver.com`
- `SSH_USER`: The username to use when accessing the server. (Prepended to `SSH_HOST` like so: `SSH_USER@SSH_HOST`. This workflow assumes port 22.) Ex. `ghrun`

Set the following environment secrets in Github:
- `SSH_PRIVATE_KEY`: The full text of the private key used to log into the server. Include the start and end text `-----BEGIN OPENSSH PRIVATE KEY----- ... -----END OPENSSH PRIVATE KEY-----`

Once all is set up, add this action to your own repo and try committing! It may take a few tries to get everything set up right on the server, so ideally try it on a dev branch first where you can make a lot of little commits.
