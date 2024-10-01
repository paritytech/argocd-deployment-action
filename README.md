# ArgoCD Deploy with Teleport Auth

This GitHub Action allows you to deploy applications to ArgoCD with Teleport authentication. It's designed to be used in CI/CD pipelines for seamless deployments to environments secured by Teleport.

## Features

- Deploys applications to ArgoCD
- Authenticates with Teleport for secure access
- Configurable for different environments and applications

## Inputs

| Input                   | Description                                                                              | Required | Default                                 |
| ----------------------- | ---------------------------------------------------------------------------------------- | -------- | --------------------------------------- |
| `environment`           | Deployment environment (e.g., parity-stg, parity-prod)                                   | Yes      |                                         |
| `tag`                   | Docker image tag to deploy. Format: paritytech-identity-backend:v1.2.3                   | Yes      |                                         |
| `app_name`              | ArgoCD application name you're going to deploy (e.g., parityio, identity-backend, etc)   | Yes      |                                         |
| `app_packages`          | ArgoCD application packages (comma separated). Default is same as app_name               | Yes      |                                         |
| `argocd_server`         | ArgoCD server URL (e.g. argocd-prod.teleport.parity.io or argocd-stg.teleport.parity.io) | Yes      |                                         |
| `argocd_timeout`        | ArgoCD sync timeout in seconds                                                           | No       | 300                                     |
| `argocd_auth_token`     | ArgoCD auth token (provided by devops team)                                              | Yes      |                                         |
| `teleport_token`        | Teleport token for authentication (Request devops team to provide - individual per app)  | Yes      |                                         |
| `teleport_app_name`     | Teleport application name (argocd-prod, argocd-stg, etc)                                 | Yes      |                                         |
| `teleport_proxy_server` | Teleport proxy server address                                                            | No       | teleport.parity.io:443                  |
| `teleport_bin`          | Teleport binary version                                                                  | No       | teleport-v16.0.3-linux-amd64-bin.tar.gz |
| `argocd_version`        | ArgoCD CLI version                                                                       | No       | v2.11.4                                 |

## Secrets

The action requires the following secret:

- `ARGOCD_AUTH_TOKEN`: Authentication token for ArgoCD

## Usage

To use this action in your workflow, you can add the following step:

```yaml
- name: Deploy to ArgoCD
  uses: paritytech/argocd-deployment-action@main
  with:
    environment: "parity-stg" # or 'parity-prod', adjust as needed
    tag: "v1.2.3" # or sha. the full path is defined in ./helm
    app_name: "your-app-name" # application name in ArgoCD
    app_packages: "your-app-name1,your-app-name2" # how your chart is called, could be several apps
    argocd_server: ${{ vars.ARGOCD_SERVER }} # this is the argocd server, different per env
    teleport_token: token-for-teleport # ask devops team to provide. Usually the same for all envs.
    teleport_app_name: "${{ vars.ARGOCD_APP_NAME }}" # or argocd-prod, argocd-stg, argocd-chains etc, depending where you app is deployed
    argocd_auth_token: ${{ secrets.ARGOCD_AUTH_TOKEN }} # devops team should provide this & set as secret in github (env specific)
```

## Notes

- The `teleport_token` is not a secret and should be provided by the DevOps team. It's usually the same for all environments.
- The `secrets.ARGOCD_AUTH_TOKEN` should be set as a secret in your GitHub repository settings and is environment-specific.
- The `vars.ARGOCD_SERVER` and `vars.ARGOCD_APP_NAME` can be set as variables in your GitHub repository settings if they differ between environments.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more details.
