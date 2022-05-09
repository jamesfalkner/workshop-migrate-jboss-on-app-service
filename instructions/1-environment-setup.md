# 1 - Set up your environment

This workshop uses GitPod to provide a pre-configured development environment with Java, JBoss, and the Red Hat VS Code extensions ready-to-go! This section will guide you through the process of setting up a GitPod account and workspace.

## 1.1 - GitPod Setup

1. Fork [the workshop repository](https://github.com/Azure-Samples/workshop-migrate-jboss-on-app-service) into your personal GitHub account.
2. Go to [https://gitpod.io/](https://gitpod.io/) and create an account. You can use the Single-Sign-On to create a GitPod account from your GitHub account.

    ![Log into GitPod with GitHub](../img/gitpod-login-prompt.png)

3. On the next screen, select Poject blade and then click on **New Project**

    ![Select new project](../img/gitpod-new-project-prompt.png)

4. Select the Project from the drop down menu.

    ![Select the project from the drop down menu](../img/select-the-project.png)

5. Next, select **Authorize**

    ![Click authorize](../img/gitpod-authorize-prompt.png)

6. The next screen will ask which account(s) you want to authorize the application in. Select **your personal account**, which should be first in the list.

   ![Select personal account](../img/gitpod-choose-account-prompt.png)

7. Then select your fork of the workshop repository, this will give GitPod permissions to read and write to your repo. Click **Install**.

   ![Install app](../img/gitpod-select-repository-prompt.png)

8. On the next screen, select the **workshop-migrate-jboss-on-app-service** project.

    ![Select workshop-migrate-jboss-on-app-service](../img/gitpod-select-proejct.png)

9. Then select your personal account as the team to instantiate the project into.

    ![Click your personal account](../img/gitpod-select-team.png)

10. Lastly, click **New workspace**. This will start the dev container and takes about one minute.

    ![Click New Workspace](../img/gitpod-start-workspace-01.png)

11. It will take some time to create the workspace.

    ![workspace creation](../img/gitpod-start-workspace-02.png)

Once the workspace launches, you will have a cloud-based VS Code IDE!

## 1.2 - Sign into Azure

The exercises in this workshop will involve creating and configuring resources from the Azure CLI and Azure Portal. The GitPod workspace already has the Azure CLI installed, but you will have to sign in from the CLI.

1. Open the VS Code terminal in GitPod by going to the existing `bash` terminal:

    ![Open terminal](../img/0-terminal.png)

2. Run the following command to start the authentication flow.

    ```bash
    az login
    ```

    Follow the instructions in the terminal output to login in.

3. To confirm your CLI is authenticated, run the following command. This will output summary information about your Azure Subscription.

    ```bash
    az account show
    ```

> If you couldn't authenticate using the browser window, you can log in using your username and password directly in the command, `az login -u johndoe@contoso.com -p verySecretPassword`. This only works if your account does **not** have 2FA enabled.

## 1.2.1 - Locate Resources Name

* **Azure Portal:**

    To login to [Azure Portal](https://www.portal.azure.com/) . 

    ![Azure Portal](../img/0-azure-portal-login.png)

    Use the credential provided by Cloudlabs to Login.

    ![cloudlabs-creds](../img/0-cloudlabs-creds.png)
    
    Go to [Azure Portal](https://portal.azure.com/) and search **Resource Groups**.

    ![Resource Groups](../img/0-ResourceGroup.png)
    
    Select **Resource Groups**, click on the only resource group name you see (`JBossonapp-xxxx`) and Note down the following values. (*You will be using the value further in the exercise*)

    * **Resource Group Name** : Your resource group name should look like `Jbossonapp-XXXXXXX`
    * **Subscription ID** : Shown in the Overview page for the Resource Group
    * **Location** : Shown in the Overview page for the Resource Group
    * **ASE Webapp Name** : Look at the list of resources, and there should be one `App Service` resource created for you, and the name should look like `redhattestXXXXXXX`


    **TIP💡:** To know your resource group location code. Run the following command.

  

    ```bash
    az group show --name <Resource Group Name>
    ```



    ![Resource Groups](../img/0-aio.png)



## 1.3 - Configure the workspace

Let's set some environment variables for later use. Press `F1` to open the command search window, and type `settings` into the search box, then select **Preferences: Open Workspace Settings (JSON)**. This will open a mostly empty file:

![Preferences](../img/0-prefs.png)

Replace the entire file with the below content, and then replace the placeholder values in `[]` with your unique values. Note that some of these must be globally unique, so consider adding your name or initials to them.

```jsonc
{
    "terminal.integrated.env.linux": {
        // Provide the value which you noted down earlier
        "SUBSCRIPTION_ID": "[Your Azure Subscription ID]",

        //Provide the value which you noted down earlier
        //it should come like redhattestxxxxxxxx
        "ASE_WEBAPP_NAME": "[Paste the webapp name here]", 

        // these must be unique to you.
        "DB_SERVER_NAME": "[Unique-value]-postgres-database",
        "WEBAPP_NAME": "[Unique-value]-webapp",
        //Provide the value which you noted down earlier.
        "RESOURCE_GROUP": "[Resource-group-name]", 
        
        // the locatio should looks like eastus, westus, eastus2
        "LOCATION": "[Resource-group-location]",   

        // these are OK to be hard-coded
        "SERVICE_PRINCIPAL_NAME": "jboss-ase-sp",
        "DB_USERNAME": "cooladmin",
        "DB_PASSWORD": "EAPonAzure1"
    }
}
```

Save the file, then close your existing bash Terminal since it will not have these new settings (careful not to close the others!):

![Save and close terminal](../img/0-bash-kill.png)

Next, open a new Terminal using the `＋` button and confirm the values are correct by running this command in the new Terminal:

```sh
for var in $(cat $GITPOD_REPO_ROOT/.vscode/settings.json \
    | grep -v '//' \
    | jq -r '."terminal.integrated.env.linux"
    | keys | join(" ")') ; do 
        val=$(eval echo \$$var); echo $var = $val
done
```

![Preferences](../img/0-env-test.png)

You should see the same values you entered. Now each new Terminal you open will have these settings. Some of the commands you run later in the workshop will reference these variables.

```
**Warning⚠️:**  If you still see placeholder values instead of the values you entered into the JSON file, ensure that the file is saved by clicking into the file, and using `CTRL-S` (or `CMD-S` on a Mac), then close the newly-opened Terminal and open a new one and try the above command again until it shows correct values.
```
```
**Tip💡:** You can view the progress of your deployments in the Azure Portal by navigating to your resource group, and clicking on the **Deployments** tab.
```

## 1.4 Workflow Integration Permission - Github


GitHub now separates out ‘workflow’ repo permissions from ‘repo’ permissions. To enable the GitPod workspace to access your GitHub repo, you must grant the workspace permission to access your repo.

Please follow the following steps to enable the permission. 

1. After adding the Repo to workspace. Proceed to [Integration](https://gitpod.io/integrations) window.

2. Click on 3 dots alongside with Github Option.

    ![Integration](../img/0-github.png)

3. Click on edit permission to open the permission screen.

    ![Edit Permission](../img/0-github-edit.png)

4. Check the Repo and Workflow permission in the dialog box, and update the permission.

![Edit Permission](../img/0-github-permission.png)

5. Authorize github to confirm the permissions. 

![Edit Permission](../img/0-github-authorize.png)

<br>

*Congratulations!* Your GitPod workspace is now ready to go. Proceed to the next section
<br>

---
