
# Utilisation du provider Docker avec Terraform

## Préparer l'environnement Terraform

Créez un répertoire pour votre projet Terraform :
```bash
mkdir terraform-docker-tp
cd terraform-docker-tp
```

Créez un fichier `main.tf` pour initialiser Terraform avec le provider Docker :

```hcl
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0"
    }
  }

  required_version = ">= 1.5.0"
}

provider "docker" {}
```

## Gérer les images Docker

Ajoutez une ressource pour télécharger une image Docker :

```hcl
resource "docker_image" "nginx_image" {
  name         = "nginx:latest"
  keep_locally = true
}
```

Initialisez Terraform :
```bash
terraform init
```

Appliquez la configuration pour télécharger l'image :
```bash
terraform apply
```

Vérifiez que l'image est téléchargée :
```bash
docker images
```

## Créer un conteneur

Ajoutez une ressource pour lancer un conteneur Nginx :

```hcl
resource "docker_container" "nginx_container" {
  name  = "nginx-container"
  image = docker_image.nginx_image.latest

  ports {
    internal = 80
    external = 8080
  }
}
```

Appliquez la configuration :
```bash
terraform apply
```

Vérifiez que le conteneur est lancé :
```bash
docker ps
```

Accédez à l'application dans un navigateur ou avec `curl` :
```bash
curl http://localhost:8080
```

## Ajouter un réseau Docker

Ajoutez un réseau personnalisé pour votre conteneur :
```hcl
resource "docker_network" "custom_network" {
  name = "custom-network"
}
```

Connectez le conteneur au réseau :
```hcl
resource "docker_container" "nginx_container" {
  name  = "nginx-container"
  image = docker_image.nginx_image.latest

  ports {
    internal = 80
    external = 8080
  }

  networks_advanced {
    name = docker_network.custom_network.name
  }
}
```

Appliquez la configuration :
```bash
terraform apply
```

Vérifiez que le réseau est créé et associé au conteneur :
```bash
docker network ls
docker network inspect custom-network
```

## Créer un volume Docker

Ajoutez un volume pour persister les données de Nginx :
```hcl
resource "docker_volume" "nginx_volume" {
  name = "nginx-data"
}
```

Montez le volume dans le conteneur :
```hcl
resource "docker_container" "nginx_container" {
  name  = "nginx-container"
  image = docker_image.nginx_image.latest

  ports {
    internal = 80
    external = 8080
  }

  networks_advanced {
    name = docker_network.custom_network.name
  }

  volumes {
    container_path = "/usr/share/nginx/html"
    volume_name    = docker_volume.nginx_volume.name
  }
}
```

Pour Windows :
```hcl
resource "docker_container" "nginx_container" {
  name  = "nginx-container"
  image = docker_image.nginx_image.latest

  ports {
    internal = 80
    external = 8080
  }

  networks_advanced {
    name = docker_network.custom_network.name
  }

  mounts {
    source = "C:/docker-data/nginx"
    target = "/usr/share/nginx/html"
    type   = "bind"
  }
}
```

Appliquez la configuration :
```bash
terraform apply
```

Vérifiez que le volume est attaché :
```bash
docker volume ls
docker volume inspect nginx-data
```

Ajoutez un fichier dans le volume pour tester la persistance :
```bash
echo "Hello Terraform!" > /var/lib/docker/volumes/nginx-data/_data/index.html
```

Rechargez l'application dans le navigateur :
```bash
curl http://localhost:8080
```

## Supprimer les ressources

Pour nettoyer l'environnement, supprimez toutes les ressources créées par Terraform :
```bash
terraform destroy
```
