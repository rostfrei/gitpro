# gitpro

A lightweight Git command wrapper that automatically manages SSH keys and configurations for multiple repositories, enabling seamless use of different identities (e.g., work and personal) without manual key switching.

## Purpose

gitpro simplifies Git operations across projects with varying authentication requirements. It detects repository URLs in Git commands and applies the appropriate SSH key and user settings, eliminating the need to manually configure SSH agents or local Git configs for each repository.

## Background

gitpro is a direct consequence solving the problem discussed in [this gist](https://gist.github.com/rahularity/86da20fe3858e6b311de068201d279e3) about managing multiple SSH keys for different GitHub accounts on a single machine.

Inspiration for gitpro comes from [multigit](https://github.com/gdsoumya/multigit) by gdsoumya, a similar CLI tool for managing multiple git accounts with SSH key authentication.

## Features

- **Automatic SSH Key Selection**: Dynamically sets `GIT_SSH_COMMAND` based on repository URL patterns.
- **Profile-Based Configuration**: Supports multiple profiles (e.g., work, personal) defined in a `.gitpro` config file.
- **Remote Command Support**: Handles `clone`, `pull`, `push`, and `fetch` commands with URL detection.
- **Fallback Behavior**: Runs standard Git commands unmodified if no configuration or URL is detected.
- **Local Config Application**: Applies user email and name locally for cloned repositories.
- **Flexible Config Location**: Prioritizes repository-specific `.gitpro` over global `~/.gitpro`.

## Installation

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/your-username/gitpro.git
   cd gitpro
   ```

2. **Make Executable**:
   ```bash
   chmod +x gitpro
   ```

3. **Optional: Add to PATH**:
   - Move `gitpro` to a directory in your `PATH` (e.g., `/usr/local/bin`).
   - Or create an alias: `alias git='path/to/gitpro'`.

4. **Prerequisites**:
   - Python 3.x
   - Git
   - SSH keys configured for your repositories

## Configuration

Create a `.gitpro` file in your home directory (`~/.gitpro`) or in specific repository roots for per-project overrides.

### Example Configuration

```ini
[default]
sshKeyPath = "~/.ssh/id_rsa"
email = "default@example.com"
userName = "Default User"

[work]
sshKeyPath = "~/.ssh/id_rsa_work"
email = "work@company.com"
userName = "Work User"
matchingRepos = ["github\\.com[:/]company/.*", "github\\.com[:/]work-org/.*"]

[personal]
sshKeyPath = "~/.ssh/id_rsa_personal"
email = "personal@example.com"
userName = "Personal User"
matchingRepos = ["github\\.com[:/]your-username/.*"]
```

- **Sections**: Define profiles (e.g., `default`, `work`).
- **Keys**:
  - `sshKeyPath`: Path to the SSH private key (expanded with `~`).
  - `email`: Git user email.
  - `userName`: Git user name.
  - `matchingRepos`: List of regex patterns to match repository URLs (optional, falls back to `default`).
- **Priority**: Repository-specific `.gitpro` takes precedence over global.

## Usage

Use `gitpro` as a drop-in replacement for `git`:

```bash
gitpro [git-command [args]]
```

If no command is provided, it runs plain `git`.

- For commands with URLs (e.g., `clone`), the wrapper detects the URL and applies the matching profile.
- For remote commands in existing repos (e.g., `pull`), it uses the origin remote URL.
- Non-remote commands (e.g., `status`, `log`) run unmodified.

### Examples

1. **Clone a Repository**:
   ```bash
   gitpro clone git@github.com:company/project.git
   ```
   - Selects the `work` profile if the URL matches, sets SSH key, clones, and applies local user config.

2. **Pull in an Existing Repository**:
   ```bash
   gitpro pull
   ```
   - Uses the origin URL to select the profile and sets the appropriate SSH key.

3. **Push Changes**:
   ```bash
   gitpro push origin main
   ```
   - Applies the profile based on the repository's origin URL.

4. **Status Check**:
   ```bash
   gitpro status
   ```
   - Runs `git status` without modifications.

## Troubleshooting

- **Permission Denied**: Ensure the selected SSH key is loaded (`ssh-add ~/.ssh/your-key`) and matches the repository's requirements.
- **No Configuration Found**: Verify `.gitpro` exists in the repo root or `~/.gitpro`.
- **URL Not Detected**: For custom remotes, ensure the URL is provided in the command args.

## Contributing

Contributions are welcome! Please submit issues or pull requests on GitHub.

## License

This project is licensed under the MIT License.