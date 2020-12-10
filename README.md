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

## Future Expansion
