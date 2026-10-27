# xtree

A Bash script for \*nix that combines the functions of `tree`, `ls -l`, `du`, and `getfacl`. It shows a colourised directory tree with per-entry sizes and can also show permissions, owner/group, modification times and ACLs. It can export the result to CSV.

```
admin@kiosk:~/.dotfiles-nixish$ xtree -L 2 -ptuaf -r .
  /\
 /  \
/____\
Permissions Owner::Group Size Last Modified Name
drwxr-xr-x admin::admin 548K 2026-10-27 11:58:05 .
├── -rw-r--r-- admin::admin 12K 2026-10-27 11:58:05 CHANGELOG.md
├── -rw-r--r-- admin::admin 36K 2026-10-27 11:58:05 LICENSE
├── -rw-r--r-- admin::admin 20K 2026-10-27 11:58:05 README.md
├── drwxr-xr-x admin::admin 12K 2026-10-27 11:58:05 config
│   ├── -rw-r--r-- admin::admin 4.0K 2026-10-27 11:58:05 bashrc_append.sh
│   ├── -rw-r--r-- admin::admin 4.0K 2026-10-27 11:58:05 zshrc_append.sh
│   ├── Error: ./config/.[!.]* does not exist
│   └── Error: ./config/..?* does not exist
├── -rwxr-xr-x admin::admin 68K 2026-10-27 11:58:05 install.sh
├── drwxr-xr-x admin::admin 8.0K 2026-10-27 11:58:05 packages
│   ├── -rw-r--r-- admin::admin 4.0K 2026-10-27 11:58:05 package_list.txt
│   ├── Error: ./packages/.[!.]* does not exist
│   └── Error: ./packages/..?* does not exist
├── drwxr-xr-x admin::admin 84K 2026-10-27 13:48:01 scripts
│   ├── -rw-r--r-- admin::admin 64K 2026-10-27 11:58:05 system-setup.sh
│   ├── -rwxr-xr-x admin::admin 16K 2026-10-27 13:48:01 xtree.sh
│   ├── Error: ./scripts/.[!.]* does not exist
│   └── Error: ./scripts/..?* does not exist
├── drwxr-xr-x admin::admin 304K 2026-10-27 13:48:01 .git
│   ├── -rw-r--r-- admin::admin 4.0K 2026-10-27 13:48:01 FETCH_HEAD
│   ├── -rw-r--r-- admin::admin 4.0K 2026-10-27 11:58:05 HEAD
│   ├── -rw-r--r-- admin::admin 4.0K 2026-10-27 13:48:01 ORIG_HEAD
│   ├── drwxr-xr-x admin::admin 4.0K 2026-10-27 11:58:04 branches
│   ├── -rw-r--r-- admin::admin 4.0K 2026-10-27 11:58:05 config
│   ├── -rw-r--r-- admin::admin 4.0K 2026-10-27 11:58:04 description
│   ├── drwxr-xr-x admin::admin 64K 2026-10-27 11:58:04 hooks
│   ├── -rw-r--r-- admin::admin 4.0K 2026-10-27 13:48:01 index
│   ├── drwxr-xr-x admin::admin 12K 2026-10-27 11:58:05 info
│   ├── drwxr-xr-x admin::admin 36K 2026-10-27 11:58:05 logs
│   ├── drwxr-xr-x admin::admin 120K 2026-10-27 13:48:01 objects
│   ├── -rw-r--r-- admin::admin 4.0K 2026-10-27 11:58:05 packed-refs
│   ├── drwxr-xr-x admin::admin 32K 2026-10-27 11:58:05 refs
│   ├── -rw-r--r-- admin::admin 4.0K 2026-10-27 11:58:05 shallow
│   ├── Error: ./.git/.[!.]* does not exist
│   └── Error: ./.git/..?* does not exist
└── Error: ./..?* does not exist


```

## Requirements

- `bash`, plus the standard coreutils: `ls`, `du`, `stat`, `awk`, `sed`, `cut`, `tr`. It uses `stat -c`, which is GNU stat syntax.
- `getfacl` (from the `acl` package). You only need it for `-A`.
- `tput` is optional. If it's missing, the script falls back to raw ANSI colour codes.

## Install

```sh
curl -fsSL https://raw.githubusercontent.com/entropytwo-ai/xtree/main/xtree.sh -o ~/.local/bin/xtree
chmod +x ~/.local/bin/xtree
```

## Usage

```
xtree [options] /path/to/directory
```

| Flag | Description |
|------|-------------|
| `-f` | Include files (by default only directories are listed) |
| `-L depth` | Maximum traversal depth (default `3`) |
| `-p` | Show permissions |
| `-t` | Show last-modified date/time |
| `-u` | Show `owner::group` |
| `-a` | Include hidden files and directories |
| `-C file` | Write the output to a CSV file instead of the terminal |
| `-s scheme` | Colour scheme: `default`, `alternative`, `christmas`, `halloween`, `ghost` |
| `-r` | Pick a random colour scheme |
| `-A` | Show ACL entries for paths that have them (a `+` in the permissions; use with `-p`) |

Examples:

```sh
xtree -L 2 /var                 # directory sizes, two levels deep
xtree -fpu -L 1 ~               # files too, with permissions and owner::group
xtree -fptuA -C report.csv /srv # full detail exported to CSV
```

To turn colours off, set `NO_COLOR=1` or `TERM=dumb`.

## Relationship to dotfiles-nixish

This repo is the main place where xtree is developed. A copy lives in [`dotfiles-nixish`](https://github.com/entropytwo-ai/dotfiles-nixish) at `scripts/xtree.sh`, where `nix/xtree.nix` packages it. **That copy is kept in sync automatically.** Each push to `main` here that changes `xtree.sh` runs [`.github/workflows/sync-to-dotfiles.yml`](.github/workflows/sync-to-dotfiles.yml), which commits the new version straight to dotfiles-nixish's `main`. Make changes here, not in dotfiles-nixish, because the sync will overwrite edits made there.

The workflow needs a repository secret named `DOTFILES_SYNC_TOKEN`. It must be a fine-grained personal access token with **Contents: Read and write** on `entropytwo-ai/dotfiles-nixish`. You can also run the sync by hand from the Actions tab (*Run workflow*).

## License

GPL-3.0. See [LICENSE](LICENSE).
