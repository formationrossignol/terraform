# Pour mettre en place l’environnement du TP :

Vous pouvez utiliser Powershell en mode admin

## Installer Chocolatey :

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString(''))
```

## Installer Terraform :

```bash
choco install terraform
```

## Installer Kind (Kubernetes in Docker)

```bash
choco install kind
```

## Installer Helm

```bash
choco install kubernetes-helm
```
