# grafana-git-sync-demo

Hey there! This is a demo showing how to use [Grafana Git Sync](https://grafana.com/docs/grafana/latest/observability-as-code/provision-resources/git-sync-setup/) and [grafanactl](https://github.com/grafana/grafanactl).

> [!NOTE]
> This documentation reflects the state of Git Sync as of May 2025. As this feature is actively evolving, some instructions may become outdated quickly. Please refer to the [official Grafana documentation](https://grafana.com/docs/grafana/latest/observability-as-code/provision-resources/git-sync-setup/) for the most up-to-date information.

> [!WARNING]
> Git Sync is currently in an experimental phase and may have limitations or breaking changes. While it represents Grafana's first step toward comprehensive Observability as Code, we don't recommend using it in production or critical environments.

## Target Audience

This demo is designed for:

- **DevOps Engineers** looking to implement Observability as Code practices.
- **SREs** wanting to version control their monitoring dashboards.
- **Grafana Users** interested in automating dashboard management.
- **Teams** seeking to improve collaboration on dashboard development.
- **Anyone** curious about Grafana's Git Sync feature and its capabilities.

## Video Recordings

For a visual (but muted :P) walkthrough, you can watch the following recordings:

- [Step 1: Establishing GitHub Connection](https://github.com/MissingRoberto/grafana-git-sync-demo/raw/refs/heads/main/assets/videos/git-sync-demo-step-1-connect.mp4).
- [Step 2: Use Pull Request to Edit your Dashboards](https://github.com/MissingRoberto/grafana-git-sync-demo/raw/refs/heads/main/assets/videos/git-sync-demo-step-2-pull-request.mp4).
- [Step 3: Edit Dashboard from Github](https://github.com/MissingRoberto/grafana-git-sync-demo/raw/refs/heads/main/assets/videos/git-sync-demo-step-3-github-com.mp4).
- [Step 4: Edit Dashboard with grafanactl](https://github.com/MissingRoberto/grafana-git-sync-demo/raw/refs/heads/main/assets/videos/git-sync-demo-step-4-grafanactl.mp4).

## Requirements

- A terminal (you've got this!).
- Image rendering up and running. Check out the [Image Rendering documentation](https://grafana.com/docs/grafana/latest/setup-grafana/image-rendering/).
- Grafana nightly build with Git Sync enabled. Follow the [Git Sync documentation](https://grafana.com/docs/grafana/latest/observability-as-code/provision-resources/git-sync-setup/) and make sure webhooks and image rendering are set up so pull request comments and instant sync work smoothly.
- An empty repository to push and pull changes.
- A PAT (Personal Access Token) with the right permissions for your repository, including webhook events and pull requests. See [Create a Github Access Token](https://grafana.com/docs/grafana/latest/observability-as-code/provision-resources/git-sync-setup/#create-a-github-access-token).
- Some dashboards and folders already created in your Grafana instance.
- [grafanactl](https://grafana.github.io/grafanactl/installation/) installed.

**Note**: If you don't have an existing environment or want to quickly try out the demo, you can set up a demo environment using [Docker](https://www.docker.com/) and [Ngrok](https://ngrok.com/). Follow these steps:

1. Configure the necessary environment variables for NGROK. You can export them in your terminal session:

   ```bash
   export NGROK_AUTH_TOKEN=<your-ngrok-auth-token>
   export NGROK_SUBDOMAIN=<your-desired-subdomain>
   ```

2. Execute the following command to start the demo environment:

   ```bash
   docker-compose up
   ```

## Intro

[Git Sync](https://grafana.com/docs/grafana/latest/observability-as-code/provision-resources/) is Grafana's solution for implementing [Observability as Code (OaC)](https://grafana.com/docs/grafana/latest/observability-as-code/). It enables bi-directional synchronization between your Grafana dashboards and a Git repository, allowing you to:

- Version control your dashboards and other Grafana resources
- Review changes through pull requests with visual previews
- Automate dashboard deployments
- Collaborate with team members using familiar Git workflows
- Maintain a single source of truth for your observability stack

This demo will walk you through setting up Git Sync and using it to manage your Grafana dashboards effectively.

## Step 1: Migrate Dashboards to GitHub

![](./assets/gifs/git-sync-demo-step-1-connect.gif)

Here's how to migrate your existing dashboards to GitHub and enable bi-directional sync between Grafana and GitHub:

- In Grafana:
  - Go to `Dashboards` and check out your existing dashboards and folders.
- In GitHub:
  - Open your repository on `github.com` to see what's inside.
- In Grafana, go to `Administration/provisioning` to set up your first repository:
  - Click in `Configure Git Sync`.
  - Paste your PAT token and enter your repository details. Click `Choose what to synchronize`.
  - Grafana will show you how many dashboards are in your instance and ask if you want to migrate them to GitHub.
  - Select the migration option to sync all dashboards `Sync all resources from external storage` and start the process by click in `Synchronize with external storage` and then `Begin synchronization`.
  - Wait for the migration to complete. Grafana will tell you how many dashboards were successfully migrated.
  - Click on `Choose additional settings`.
  - Enable the `Enable dashboards previews in pull requests` option to have dashboard preview screenshots on pull requests.
  - Click `Finish`.
- In GitHub:
  - Refresh your repository to see a new `grafana` directory containing your migrated dashboards.
  - Look at the folder structure and files.
  - Open a dashboard file to peek at its contents.
- In Grafana:
  - Open the GitHub connection by clicking `View`.
  - Check out the repository status page (e.g., dashboard count, recent jobs, resources tab, and files tab).

That's it! Your Grafana instance is now connected to GitHub with Git Sync, keeping everything in sync.

## Step 2: Use Pull Requests for Your Dashboards

![](./assets/gifs/git-sync-demo-step-2-pull-request.gif)

You can edit dashboards in Grafana and submit changes as pull requests in GitHub before they go live:

- In Grafana:
  - Go to `Dashboards`.
  - Open a dashboard.
  - You will see a purple `<->` badge indicating it's now managed by Git Sync.
  - Make some changes and click on `Save`.
  - A new drawer will pop up for provisioned dashboards with new saving options.
  - Write a commit message.
  - Select `Push to new branch` to create a branch for your changes.
  - Click `Save`.
  - You will be redirected to the dashboards page and see a banner indicating this is a preview and not yet live in Grafana.
  - The banner will offer an `Open Pull Request` button, click on it.
  - A new tab will be opened for you to open a pull request in Github.
- In GitHub:
  - Open the pull request.
  - Wait a few seconds for the Grafana integration to comment on the pull request with before-and-after previews of your dashboard changes.
  - Enjoy the view of the side-to-side comparison.
  - Click on the preview link to see the new dashboard preview render in Grafana.
  - See also that you have a link to go back to your original PR.
  - Edit the dashboard again and save it to the pull request branch which would be selected automatically for you.
  - Close the tab.
  - See the new comment with the new side-by-side comparison.
  - Merge the pull request.
- In Grafana:
  - Go to your dashboard (or refresh) to see the changes were applied automatically.

## Step 3: Edit a Dashboard from Github

![](./assets/gifs/git-sync-demo-step-3-github-com.gif)

- In Github,
  - Go to the dashboard you previously modified (or another one).
  - Edit the title of a panel (e.g. "Modified From Github").
  - Save and push to the branch.
- In Grafana,
  - Go to that dashboard (or refresh).
  - See that changes are now reflected.

## Step 4: Edit a Dashboard Using `grafanactl`

> `grafanactl` is a command-line tool that helps you manage Grafana resources like dashboards, folders, and datasources. The tool integrates with Git Sync, allowing you to manage your dashboards through both the Grafana UI and command line, giving you flexibility in how you work with your dashboards.

![](./assets/gifs/git-sync-demo-step-4-grafanactl.gif)

Here is how you can edit dashboards using `grafanactl` and see the changes reflected in both GitHub and Grafana:

- In your terminal:

  - First, explore the available commands:

    ```console
    grafanactl -h
    Usage:
    grafanactl [command]

    Available Commands:
    completion  Generate the autocompletion script for the specified shell
    config      View or manipulate configuration settings
    help        Help about any command
    resources   Manipulate Grafana resources

    Flags:
    -h, --help            help for grafanactl
        --no-color        Disable color output
    -v, --verbose count   Verbose mode. Multiple -v options increase the verbosity (maximum: 3).
        --version         version for grafanactl

    Use "grafanactl [command] --help" for more information about a command.
    ```

    This will show you all available options and commands for the CLI tool.

  - Pull the latest resources from Grafana:

    ```console
    grafanactl resources pull
    ✔ 6 resources pulled
    ```

    This command synchronizes your local environment with your Grafana instance, downloading all dashboards and other resources.

  - List all available dashboards and folders:

    ```console
    $ grafanactl resources get folders
    KIND     NAME                                       NAMESPACE
    Folder   demodeeperfolder-xkamwnofnmtnog45gzwhbw9   default
    Folder   demofolder-gmw2mvkigdeajzkygpy7wvlnwbyy0   default

    $ grafanactl resources get dashboards
    KIND        NAME      NAMESPACE
    Dashboard   adg5vbj   default
    Dashboard   admfz74   default
    Dashboard   adn5mxb   default
    ```

    This will display a list of all dashboards with their IDs, which you'll need for editing.

  - Find out the dashboard ID by looking at the file or browser URL.
  - Edit a specific dashboard by peeking at the id:

    ```console
    grafanactl resource edit dashboard <ID>
    # You will be able to edit the dashboard using your default terminal editor
    ```

    Replace `<ID>` with the actual dashboard ID from the previous step. This will open your default editor where you can make changes to the dashboard JSON.

- In GitHub:

  - Navigate to your repository.
  - Check the commit history to see your dashboard changes.
  - Review the changes in the dashboard JSON file.

- In Grafana:
  - Open your dashboards section.
  - Verify that your changes have been automatically synchronized.

---

🎉 Excellent work! You've now mastered Git Sync for Grafana dashboards and can confidently manage your dashboards through multiple interfaces - the Grafana UI, GitHub, and the command line. This setup enables collaborative dashboard development with proper version control and review processes. 🎉

Thank you for following along with this guide. You're now part of an elite group who keep their dashboards version-controlled and collaborative. Happy dashboarding! 🚀
