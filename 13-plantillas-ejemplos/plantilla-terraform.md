# 🏗️ Plantilla de Infraestructura como Código (Terraform)

## 1. Objetivo
Proveer la estructura base modular en **Terraform** para aprovisionar la infraestructura requerida por **Fixia** (redes VPC, clústeres de cómputo y bases de datos relacionales).

---

## 2. Estructura de Proyecto Terraform
```text
terraform/
├── main.tf           # Definición principal de proveedores y recursos
├── variables.tf      # Declaración de variables de entrada
├── outputs.tf        # Valores de salida exportados
├── terraform.tfvars  # Asignación de valores por entorno (dev/prod)
└── modules/
    ├── vpc/          # Módulo de red (Subnets, Gateways, Route Tables)
    └── k3s_cluster/  # Módulo para aprovisionamiento de nodos K3s
```

---

## 3. Código Base (`main.tf`)

```hcl
terraform {
  required_version = ">= 1.6.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }

  backend "s3" {
    bucket         = "fixia-terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "fixia-terraform-locks"
  }
}

provider "aws" {
  region = var.aws_region
  default_tags {
    tags = {
      Environment = var.environment
      Project     = "Fixia"
      ManagedBy   = "Terraform"
    }
  }
}

module "vpc" {
  source   = "./modules/vpc"
  cidr_block = var.vpc_cidr
  env        = var.environment
}
```
