# AGENTS.md

This is the documentation repository of the flow environment.  
  
This environement has the following features:  
  
- docker compose files for multiple purposes in n8n-workflows/docker-templates
- frontend forms for different services the n8n instance manages: preprocessing, inference, and evaluation.
- n8n workflows for the different tasks to import in the n8n UI.
- FastAPI microservices to run the different taks, like preprocessing and inference, called by the n8n workflows.

It is the intention you can use those instances just by creating proper .env files and run the specific docker compose files.

For the basic n8n-workflows your choice depends on your usecase:

- n8n-workflows/docker-templates/docker-compose.n8n.yml for generic use and an existing traefik proxy (n8n-workflows/docker-templates/docker-compose.traefik-proxy.yml to run that proxy server).
- n8n-workflows/docker-templates/docker-compose.n8n.local.yml for the local use without domains. The API calls in n8n run via docker network - so the services need to be in the same docker network or reachable via LAN. The workflows are atm designed to work with docker networks.
- n8n-workflows/docker-templates/docker-compose.mailserver.local.yml and n8n-workflows/docker-templates/docker-compose.local.yml as override, if you don't want to use the mailjet solution but a simple mailserver container with DKIM keys. For this you need to change the workflows and activate the E-Mail nodes.

For the frontend properly working, you need to set the webhook base ip/url in n8n-workflows/frontend-common/config.js. This webhook is the one provided by the n8n-workflows to trigger those workflows.

The services, which are called by the n8n-workflows have to be started via other docker compose files.
You can find the code of those in this GitHub repositories:

- n8n Workflows and Docker Compose files: <https://github.com/The-Flow-Project/n8n-workflows>
- Preprocessing service: <https://github.com/The-Flow-Project/service-trocr-preprocess>
- Inference/Evaluation service: <https://github.com/The-Flow-Project/service-trocr-inference>

## Conventions

- All variables are set in .env
- Workflows are imported by the admin via n8n interface
- In the simple variant, the admin needs a mailjet account, access to the domains DNS records, and add those credentials in n8n.
- In the mailserver variant, the admin needs access to the domains DNS records. And the outgoing port 25 needs to be open.
- The local variant is used in those cases:
  - If the ports 80 and 443 are not open in your network
  - You don't have a domain to run it with (then use a SMTP account to send emails - those nodes do not exist in the workflows)
  - For development / testing
- If you choose to run the URL based variant, you can set the domains/subdomains. Those should all have a DNS record (be aware of the update time of 48h)
- The admin can always change the URL/IPs of the endpoints for the API calls in the n8n workflows.

## Documentation

- Describe the basic structure, the requirements to run tge flow environment, required software (docker), and the required accesses / accounts.
- Describe the different use cases and what to run when starting one of those.
- Give some explanation, what n8n does and about it's pros.
- Do not use dashes in text!

###  Documentation structure

- **Flow-Project:** Landing Page, index.md

- **Getting Started:**
  - Installation: How to install the flow environment, getting_started/installation.md
  - Overview Environment: Technical insight, htr_workflow/n8n.md

- **Tutorials:**
  - Preprocessing a ZIP into a raw XML dataset: tutorials/preprocessing_zip_raw_xml.md
  - Preprocessing from a HuggingFace dataset: tutorials/preprocessing_hf_raw_xml.md
  - Preprocessing a line-based dataset: tutorials/preprocessing_line_based.md
  - Running inference: tutorials/inference.md
  - Evaluating results: tutorials/evaluation.md
  - Writing results back into raw XML: tutorials/write_raw_xml.md

- **Reference:**
  - Packages: packages.md
  - Services: services.md

- **Help:**
  - FAQ: help/faq.md
  - Contributing: help/contributing.md

The documentation is generetad via mkdocs in this repository:
<https://github.com/The-Flow-Project/flow-docs>
