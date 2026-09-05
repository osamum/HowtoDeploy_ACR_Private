# Step 3: Sign in to ACR and Push an Image from the Jump Box

Connect to the Azure Container Registry (ACR) deployed in the private network environment from the Jump Box machine on the same virtual network.

The Jump Box must be prepared before you perform this task.

## Preparation

The `az acr login` command requires both the Docker CLI and Docker daemon on the machine where it runs. Therefore, you must install Docker on the Jump Box machine.

Review the paid and free licensing terms for [Docker Desktop](https://docs.docker.com/desktop/setup/install/windows-install/). If you are eligible to use it, follow the linked instructions to [install Docker Desktop](https://docs.docker.com/desktop/setup/install/windows-install/#install-docker-desktop-on-windows) on the Jump Box machine, and then start the Docker service.

If you can use Docker Desktop, skip the next section and proceed to [Sign in to ACR and push an image](#sign-in-to-acr-and-push-an-image).

### If Docker Desktop is unavailable

If you cannot use Docker Desktop because of licensing or other restrictions, install [Windows Subsystem for Linux (WSL)](https://learn.microsoft.com/en-us/windows/wsl/), install Docker Engine in WSL, and run the Docker daemon there.

Follow these steps.

\[**Steps**▶️\]

1. Use Bastion to sign in to the Jump Box machine, and open a terminal.

2. Run the following command to verify that [nested virtualization](https://learn.microsoft.com/en-us/windows-server/virtualization/hyper-v/enable-nested-virtualization) is available.

    ```powershell
    Get-ComputerInfo -Property HyperV*
    ```

    If the output includes `HyperVisorPresent : True`, Hyper-V is available. If the value is `False`, stop here and change the Jump Box virtual machine size to one that supports nested virtualization, such as `Standard D2lds v5`.

3. Install WSL.

    ```powershell
    wsl --install
    ```

4. Install Ubuntu as the Linux distribution.

    ```powershell
    wsl --install -d Ubuntu
    ```

    When prompted during installation, set an administrator password.

    After the installation is complete, close and reopen the terminal window.

5. Run the following command and verify that the prompt switches to Bash.

    ```powershell
    bash
    ```

    If the prompt does not switch, restart the Jump Box machine.

6. With the terminal running Bash, install the Azure CLI in WSL.

    Run the following [command](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-linux?view=azure-cli-latest&pivots=apt#option-1-install-with-one-command).

    ```bash
    curl -fsSL 'https://azurecliprod.blob.core.windows.net/$root/deb_install.sh' | sudo bash
    ```

    After the installation is complete, restart the terminal, switch the prompt to Bash, and run the following command.

    ```bash
    which az
    ```

    Verify that the command returns `/usr/bin/az`.

7. Install Docker Engine in WSL.

    Docker Engine can be installed in several ways. This guide uses the [convenience script](https://docs.docker.com/engine/install/ubuntu/#install-using-the-convenience-script) described in the official Docker documentation.

    Run the following commands to install Docker Engine.

    ```bash
    curl -fsSL https://get.docker.com -o get-docker.sh
    sudo sh get-docker.sh
    ```

    Then complete the instructions through **step 3** in the following document.

    * [**Manage Docker as a non-root user**](https://docs.docker.com/engine/install/linux-postinstall/)

8. Verify that the terminal is running Bash, and then sign in to Azure by using the following command.

    ```bash
    az login
    ```

    Verify that you can sign in successfully.

The Jump Box is now ready to sign in to Azure Container Registry (ACR).

<br>

## Sign in to ACR and push an image

From the Jump Box terminal, sign in to Azure Container Registry (ACR) and push a sample container image.

Follow these steps.

\[**Steps**▶️\]

1. Sign in to Azure Container Registry (ACR).

    ```bash
    az acr login --name <ACR_NAME>
    ```

2. Pull the hello-world image from Microsoft Container Registry.

    ```bash
    docker pull mcr.microsoft.com/hello-world:latest
    ```

3. List the pulled images and verify that the image is present.

    ```bash
    docker images
    ```

4. Before pushing the image to the registry, use the [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) command to tag the image with the fully qualified name of the registry login server.

    Because `Unsecure` was selected for \[Domain name label scope\] when ACR was deployed, tag the image as follows.

    ```bash
    docker tag mcr.microsoft.com/hello-world:latest <ACR_NAME>.azurecr.io/hello-world:v1
    ```

5. Use the [docker push](https://docs.docker.com/engine/reference/commandline/push/) command to push the image to the registry instance. This example creates a hello-world repository containing the hello-world:v1 image.

    ```bash
    docker push <ACR_NAME>.azurecr.io/hello-world:v1
    ```

6. After the push is complete, access the Azure portal from a web browser on the Jump Box, and verify that the hello-world:v1 image appears in the ACR repository.

    ![The hello-world:v1 image pushed to ACR](img/EN-ACR_Repos.png)

    If an error occurs when displaying the repository list even though you are accessing it from the Jump Box, try assigning the following roles to the account you are currently using under \[Access control (IAM)\] for the ACR instance.

    * `Container Registry Repository Catalog Lister`
    * `Container Registry Repository Contributor`

You have now signed in to the deployed ACR and pushed a container image.

For more information, see the following document.

* [**Quickstart: Create an Azure container registry using the Azure portal**](https://learn.microsoft.com/en-us/azure/container-registry/container-registry-get-started-portal?tabs=azure-cli)

<br>

👈 [**Step 2: Deploy ACR in a Private Network Environment**](en-ex02.md)

---

🏚️ [Back to README](README.md)
