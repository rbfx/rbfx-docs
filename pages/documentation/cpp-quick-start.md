Rebel Fork Qick Start for C++ developers
========================================

## Create a project from template

A good starting point for a game would be a sample project.

You can create a new repository based on a template repository on GitHub in the following way:

* Go to the template repository's page on GitHub. In this case, the template repository is https://github.com/rbfx/sample-project.

* Click the "Use this template" button on the repository's page.
![Img](/images/github/use-this-template.jpg)

* On the next page, specify the name for your new repository, a description (optional), and the settings for your repository.

* Choose whether to make the repository public or private.

* Click the "Create repository from template" button to create the repository.

Your new repository will be created with the same files and directories as the template repository, and you can start using it right away. Note that the new repository will not be connected to the template repository, and any changes made to the new repository will not affect the template repository.

## Clone repository

> **Note**: Not everyone is familiar with git. If you are then you already know what to do and can skip this section.

You should have the repository from the previous section avaliable in your github account. If not - you can use https://github.com/rbfx/sample-project instead.

To clone a git repository you need a git client. Here is a list of resources where you can get a command line git client: https://git-scm.com/downloads

To get the clone URL of a GitHub repository, follow these steps:

* Go to the GitHub repository you want to clone.

* Look for the "Code" button on the repository's page, usually located in the top-right corner.

* Click the "Code" button, and a dropdown will appear with the clone URL for the repository.

* Copy the URL by clicking the "Copy to clipboard" button next to the URL, or by manually selecting and copying the URL.
![Img](/images/github/code-clone.jpg)

> **Important**: Sample project references to the rbfx engine code as a submodule. "Download ZIP" won't work for you.


### Clonning with command line

Don't forget to restart your console after git client installation, otherwise it may not be found in the path.

Cloning the main repository: This is done using the git clone command followed by the URL of the main repository.
```shell
git clone https://github.com/my-org/my-game.git
```

Initializing and updating the submodules: After cloning the main repository, you need to initialize the submodules and update them to the latest version. To do this, you can use the git submodule init and git submodule update commands.
```shell
cd my-game
git submodule init
git submodule update
```

This will clone all the submodules in the main-repo repository. You can also specify the --recursive option while cloning the main repository to initialize and update the submodules automatically.
```shell
git clone --recursive https://github.com/my-org/my-game.git
```

Note that submodules are independent Git repositories, so any changes you make to a submodule will not be reflected in the main repository until you push the changes to the submodule's repository and update the reference in the main repository.

### Clonning with Git GUI

There are multiple GUI applications for Git, some of them free.

Here is a guide for Git Extensions as an example. You can find similar options in any Git GUI application.

You can get Git Extension from the web site https://gitextensions.github.io/.
Open the "Download" section and click on the latest release installer, for example GitExtensions-4.0.2.16100-25100ec1f.msi (the version may be different). It may ask you to download and install a certain version of Microsoft Windows Desktop Runtime.

When you run the application it will settings window. You can set it up later, for now you can just click "On" to open the main window.

There are two options to clone repository:
![Img](/images/github/git-ext-clone.jpg)

### Clone repository

Let's take a look into generic "Clone repository" first.

![Img](/images/github/git-ext-clone-repository.jpg)
Fill in where you want to clone the game repository. "Initialize all submodules" checkbox allows you to fetch the engine source code along with the game source code.

When clonned you should get an overview on your repository:
![Img](/images/github/git-ext-repository.jpg)

### Clone GitHub repository

First you need to generate a GitHub token following one of the links on a setup page. The token gives the Git Extension application access to your GitHub account.
![Img](/images/github/git-ext-github-token.jpg)

Then you should be able to see list of your repositories, including the game repository you created from the template.
![Img](/images/github/git-ext-clone-github.jpg)
Fill in required information and click "Clone" button.

You are not done yet. The previous step only clonned the game source code but you still need the engine submodule. You need to find the rbfx submodule and click "Update" in the context menu.
![Img](/images/github/git-ext-submodule-update.jpg)

Now you are ready configure and run the game.

## Configure

**...To be continued...**