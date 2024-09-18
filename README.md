

# actions-sync-workflows

This repository contains workflows to automate the GitHub Actions-Sync tool[^1] for the syncing of public GitHub Actions into a GitHub Enterprise Server(GHES) instance that doesn't have GitHub Connect enabled.

## Features

- **Automation**: Automatically (for a GHES instance with internet connectivity) sync public selected GitHub Actions to a GHES instance
- **Consistency**: Keep all GHES actions current, and easily add new actions

## How-to Steps

1. Create a new, empty repository on your GHES instance named `actions-sync-workflows`.
2. Clone this repo to your local machine with `git clone https://github.com/JohnBreault/actions-sync-workflows.git`.
3. `cd actions-sync-workflows`
4. `git remote rename origin upstream`
5. `git remote add origin <your GHES-repo-url.git>`
6. `git push origin main` # may ask you to login to your GHES instance

### Create a Token
You'll need to create a GitHub token on your GHES instance[^2] with the follwoing permissions:
- `repo`
- `workflow`
- `site_admin` (If you want the tool to create new orgs for you as mentioned below)

Add the token to `<your-GHES-repo-url` settings as `ACTIONS_SYNC`[^3]

### repo-name-list.txt
This text file is currently populated with some test repositories. Edit this file on the GHES repo you just created. Actions repositories are added to repo-name-list.txt in the same manner as they are laid out in the documentation for the actions-sync tool[^1]

####Examples:
Using the example of `github.com/azure/powershell`...

- If you want the `owner/repository` on your GHES instance to match that of the public repository (i.e. `<your-GHES-repo-url/azure/powershell>`), add it to [repo-name-list.txt](/repo-name-list.txt) as `upstream_owner/upstream_repo` (`azure/powershell`).
> [!NOTE]
> This method will create a new GHES org name `azure` and requires a token permission with `site_admin` permission along with `repo` and `workflow`

- If you are wanting to add this action to an existing org (i.e. `actions`), you'll need to create the [repo-name-list.txt](/repo-name-list.txt) entry like this, `upstream_owner/upstream_repo:destination_owner/destination_repo` (i.e. `azure/powershell:actions/powershell`).
> [!NOTE]
> You will need to make yourself an owner of the organization that you are syncing the actions repositories to; in the case above, you'll need to be owner of the `actions` org on your ghes instance[^4}. 

### [Actions-Sync-Sync](/.github/workflows/actions-sync-sync.yml) Workflow for GHES with Internet Connectivity
This workflow syncs the repositories listed in [repo-name-list.txt](/repo-name-list.txt) directly to your GHES instance by performing `actions-sync sync`[^1]

### [Actions-Sync-Pull](/.github/workflows/actions-sync-pull.yml) Workflow for GHES without Internet Connectivity
This workflow creates a workflow artifact of the repositories listed in [repo-name-list.txt](/repo-name-list.txt) called `Repo cache and actions-sync tool artifact.zip`. The artifact will need to be transferred to a machine on the same network as your offline GHES instance.

1. Download the workflow artifact[^5]
1. Transfer to a computer with connectivity to your GHES instance
1. Extract the `.zip` file that contains your actions to be synced, `cache.tar.gz`, and the latest release of the GitHub Actions-Sync tool[^1] (`actions-sync.tar.gz`)
1. Extract both of the `.tar.gz` files with `tar -xvf <filename>`
1. `cd` into the newly extracted `actions-sync/bin` directory and run the command
   ```
   ./actions-sync push \
    --cache-dir "../../cache" \
    --destination-token "<your-GHES-personal-access-token>" \
    --destination-url "<your-GHES-repo-url"


## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## References

[^1]: [GitHub Actions-Sync tool](https://github.com/actions/actions-sync)

[^2]: [creating a personal access token](https://docs.github.com/en/enterprise-server@latest/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-personal-access-token-classic)

[^3]: [creating secrets for a repository](https://docs.github.com/en/actions/security-for-github-actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-a-repository)

[^4]: [managing your role in an organization owned by your enterprise](https://docs.github.com/en/enterprise-server@3.14/admin/managing-accounts-and-repositories/managing-organizations-in-your-enterprise/managing-your-role-in-an-organization-owned-by-your-enterprise#managing-your-role-with-the-enterprise-settings)

[^5]: [Downloading workflow artifacts](https://docs.github.com/en/actions/managing-workflow-runs-and-deployments/managing-workflow-runs/downloading-workflow-artifacts)
