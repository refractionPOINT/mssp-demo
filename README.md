# LimaCharlie MSSP Demo Repo
Example repository demonstrating an MSSP setup with LimaCharlie.

## Prerequisite Steps
1. Create a special CI/CD User on LC to use for orchestrating.
1. Create a User API key for the CI/CD User.

## Repo Structure
```
configs
configs/global
configs/global/all.yaml
configs/global/rules.yaml
configs/global/outputs.yaml
configs/global/fim.yaml
configs/global/artifact.yaml
configs/global/exfil.yaml
configs/customers/
configs/customers/cus_1
configs/customers/cus_1/main.yaml
configs/customers/cus_1/custom_rules.yaml
configs/customers/cus_2
configs/customers/cus_2/main.yaml
configs/customers/cus_2/custom_rules.yaml
configs/customers/cus_2/custom_fim.yaml
configs/customers/cus_3
configs/customers/cus_3/main.yaml
configs/customers/cus_3/custom_rules.yaml
```

The `global` directory contains all configs that are global to all customers.

Each `customers` subdirectory contains any specific customizations needed per customer. The `main.yaml` file is the core file used in the `limacharlie sync` CLI invocation.

## Adding a New Customer
1. Add the CI/CD User to the new organization.
1. Edit the following files and add the relevant commands based on previous customers:
  1. `.github/workflows/deploy.yaml`
  1. `.github/workflows/dry_run.yaml`

## Future Expansion
1. Add Replay jobs on new D&R rules PR to test rule changes.