# Setting Up a Scalable Jenkins CI/CD Environment with Podman

## Prerequisites

- Installed and operational Podman
- Network access
- Console access

## 1. Creating a Dedicated Network

First, create a separate network for Jenkins-related containers:

```sh
podman network create n_jenkins
```

## 2. Setting Up the Jenkins Pod

Create a new pod named `itcafe`:

```sh
podman pod create itcafe
```

## 3. Deploying the Jenkins Container

To ensure data persistence, create a volume for Jenkins data:

```sh
podman volume create jenkins_home
```

Then, create the Jenkins container within the `itcafe` pod:

```sh
podman create --pod itcafe --restart=on-failure \
  -v jenkins_home:/var/jenkins_home \
  --name jenkins --publish 8080:8080 \
  --network n_jenkins \
  --publish 50000:50000 \
  docker.io/jenkins/jenkins:lts-jdk17
```

Start the pod:

```sh
podman pod start itcafe
```

## 4. Initializing the Jenkins Controller

- Access the Jenkins Web UI at: `http://localhost:8080`
- Retrieve the initial admin password:

  ```sh
  podman exec jenkins \
    cat /var/jenkins_home/secrets/initialAdminPassword
  ```

- Use this password to log in and install the suggested plugins.

## 5. Adding Build Agents

To enhance scalability, add build agents:

1. In Jenkins, navigate to `Manage Jenkins` > `Nodes` > `New Node`.
2. Name the node, select "Permanent Agent," and set the remote root directory to `/home/jenkins/agent`.
3. Save the configuration to create an offline agent.
4. Obtain the agent's secret key from its configuration page.
5. Run the inbound-agent container:

   ```sh
   podman run --pod itcafe \
     -d --rm --name agent1 \
     --init docker.io/jenkins/inbound-agent \
     -url http://localhost:8080 \
     -secret <secret_code> \
     -name Agent1
   ```

6. Repeat these steps to add more agents as needed.

## 6. Creating Backups

To back up the Jenkins setup:

```sh
podman kube generate itcafe --filename jenkins.yml
```

Restore the setup using:

```sh
podman kube play jenkins.yml
```

For a detailed walkthrough, refer to the original article: [How To Create A Scalable Jenkins CI/CD Environment Using Containers?](https://tomsitcafe.com/2023/12/05/how-to-create-a-scalable-jenkins-ci-cd-environment-using-containers/)

