# ankra-host-agent

Release binaries of the Ankra host agent: the small service that lets a Linux host (a VM or a bare-metal machine)
receive deployments from [Ankra](https://ankra.io) pipelines.

The agent connects out to the Ankra platform over HTTPS and pulls its work. Nothing connects in: no inbound port, no
SSH key, and no deploy credential in CI. When a pipeline's `deploy` stage releases to the host's environment, the agent
fetches the release by digest, verifies it, runs its install and health checks as an unprivileged user, and restores
the previous release if the health check fails.

## Install

Register a host with the Ankra CLI, as root on the host:

```sh
ankra targets join-token create --environment production | \
  ankra targets register --environment production --name "$(hostname -s)" --token-stdin
```

`ankra targets register` downloads the binary for the host's architecture from this repository's releases, verifies it
against `SHA256SUMS`, registers the host, and installs `ankra-host-agent.service`.

## Release assets

Each release carries:

- `ankra-host-agent-linux-amd64`
- `ankra-host-agent-linux-arm64`
- `ankra-host-agent.service`
- `SHA256SUMS`

This repository holds releases only. The source is developed in Ankra's agent repository and published here by its
release pipeline: it pushes the assets on a short-lived `release-assets/v<version>` branch together with the tag
`v<version>`, and this repository's [publish workflow](.github/workflows/publish-release.yml) verifies them against
`SHA256SUMS`, creates the release and deletes the branch.
