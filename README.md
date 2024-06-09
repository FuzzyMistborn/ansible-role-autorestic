# Autorestic/Restic Installer

An ansible role to install and configure [restic](https://github.com/restic/restic) and [autorestic](https://github.com/cupcakearmy/autorestic).  Inspiration/basis for the role goes to [@IronicBadger](https://github.com/IronicBadger/infra/tree/master/roles/ktz-autorestic) and also to [@ItsNotGoodName](https://github.com/ItsNotGoodName/ansible-role-autorestic) for improvements in the installer/config copying.

Install with `ansible-galaxy install fuzzymistborn.autorestic`

## Features

- Installation and configuration of `restic` and `autorestic` GO binaries.
- Copying/updating `autorestic` config file.
- Updating binaries if there is an update and version is not pinned.

## Configuration

This role has a number of variables that can be configured.

Additionally, you can pin a specific version of either binary with `autorestic_pinned_ver` or `restic_pinned_ver`.  By default the role fetches and installs the latest available version, and will run the update command if the binary is already present every time the role is run.  You can disable this by pinning to a specific version.  Here's an example if you wanted to set the version.

```yaml
autorestic_download_latest_ver: false
autorestic_pinned_ver: 1.2.0
restic_download_latest_ver: false
restic_pinned_ver: 0.12.1
```
By setting a pinned version, the updater commands will not run and a version will only be pulled if the installed version does not match the pinned version.

Other variables that can be changed:

```yaml
autorestic_config_user: root
autorestic_config_yaml: CHANGEME  # autorestic configuration in yaml
autorestic_config_path: "{{ autorestic_user_directory }}/.autorestic.yml"
autorestic_config_mode: 0600
autorestic_config_owner: "{{ autorestic_config_user }}"
autorestic_config_group: "{{ autorestic_config_user }}"

autorestic_distro: linux_amd64
restic_distro: linux_amd64
```
The other variables, like `restic_gh_url`, `restic_install_directory`, etc I do not recommend changing unless you want to customize the install.

See the release pages for [autorestic](https://github.com/cupcakearmy/autorestic/releases) and [restic](https://github.com/restic/restic/releases/) to find the correct distribution for your install.

### Example for Autorestic Config File

Autorestic configuration is copied to the root home directory by default. It can be changed to another user's home directory with the `autorestic_config_user` variable.

Below is an example that shows all the available options.

```yaml
autorestic_config_yaml:
  locations:
    docker:
      from: '/opt/docker'
      to:
        - local
        - b2_docker
  backends:
    local:
      type: local
      path: /backup
      key: 123
    b2_docker:
      type: s3
      path: 'b2_backend_url'
      key: b2_password
      env:
        AWS_ACCESS_KEY_ID: 1234
        AWS_SECRET_ACCESS_KEY: 1234abc
```
For additional documentation, please see the [official docs](https://autorestic.vercel.app/).

## Github API

This role utilizes the GitHub API to determine the latest release available.  By default, the role utilizes unauthenticated requests, which are [limited by GitHub](https://docs.github.com/en/rest/overview/resources-in-the-rest-api#rate-limiting) to 60 requests per hour.  Requests are associated with the originating IP address.  For most usecases, this is not an issue.  However, you may find yourself rate limited.  If you authenticate, you can make 5,000 requests per hour.

To authenticate, you must obtain a [Personal Access Token](https://github.com/settings/tokens/new).  The token does not need any scopes selected.  Then add the following variables:

```
github_api_user: fuzzymistborn
github_api_pass: YOUR_TOKEN
github_api_auth: yes
```

That's it!

## To Do

- ~~Add cronjob variables (?) for basic tasks (backup, forget, etc).~~
  - Don't think I will implement.  Too personalized and too much variety in how to do.  Will keep trying to think up a way.
- ~~Find a way to pin restic even if updating autorestic.~~

### If you appreciate my work, please consider buying me a beer (or coffee, or whatever)
[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/E1E5796VZ)
