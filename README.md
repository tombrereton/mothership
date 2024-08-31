# VM Design
- `dev-public` for exploration and testing
- `pdn-private` is the VM for housing permanent data, non exposed backends e.g. database, file storage, logging storage
- `pdn-public` is the VM for housing public applications e.g. web server, game server
- `pdn-public-personal` is the VM for housing personal application exposed to the internet e.g. seafile, nextcloud, monitoring dashboards
- `pdn


# Requirements
```sh
brew install ansible
brew install hudochenkov/sshpass/sshpass
```
