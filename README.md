# Ansible RoboShop

Ansible automation for deploying the **RoboShop** microservices e-commerce application across multiple EC2 instances. This repo provisions and configures each backend service, database, and the frontend using individual playbooks and systemd unit files.

## What This Does

RoboShop is a microservices-based e-commerce demo application. Each service (catalogue, cart, shipping, payment, user, frontend) and supporting datastore (MySQL, MongoDB, Redis, RabbitMQ) runs on its own host. This repo automates the setup of each component using Ansible.

## Project Structure

| File | Purpose |
|------|---------|
| `inventory.ini` | Defines target hosts/groups for each service |
| `roboshop.yaml` | Main playbook that orchestrates the full deployment |
| `practice.yaml` | Scratch/practice playbook used during development |
| `catalogue.yaml` | Deploys the Catalogue service |
| `cart.yaml` | Deploys the Cart service |
| `shipping.yaml` | Deploys the Shipping service |
| `payment.yaml` | Deploys the Payment service |
| `user.yaml` | Deploys the User service |
| `frontend.yaml` | Deploys the Nginx-based frontend |
| `mysql.yaml` | Sets up the MySQL database |
| `mongodb.yaml` | Sets up MongoDB |
| `redis.yaml` | Sets up Redis |
| `rabbitmq.yaml` | Sets up RabbitMQ |
| `*.repo` (`mongo.repo`, `rabbitmq.repo`) | Yum repo definitions for installing MongoDB and RabbitMQ |
| `*.service` (`cart.service`, `catalogue.service`, `payment.service`, `shipping.service`, `user.service`) | systemd unit files for running each Node.js service as a managed service |
| `nginx.conf` | Nginx configuration for the frontend reverse proxy |
| `ec2_output.json` | Output/metadata from EC2 instance provisioning, used to populate inventory |

## Prerequisites

- Ansible installed on the control node (`pip install ansible` or `dnf install ansible`)
- SSH access to all target EC2 instances, with keys configured
- Target hosts listed in `inventory.ini` under the correct group names
- Python installed on all managed nodes (required by Ansible)

## Usage

1. Update `inventory.ini` with your target hosts' IPs or hostnames under the appropriate groups.
2. Verify connectivity to all hosts:

   ```bash
   ansible all -i inventory.ini -m ping
   ```

3. Run the full deployment:

   ```bash
   ansible-playbook -i inventory.ini roboshop.yaml
   ```

4. Or run an individual service playbook:

   ```bash
   ansible-playbook -i inventory.ini catalogue.yaml
   ```

## Notes

- Each service playbook installs dependencies, deploys the corresponding `.service` file to `/etc/systemd/system/`, and starts/enables the service via `systemd`.
- Database playbooks (`mysql.yaml`, `mongodb.yaml`, `redis.yaml`, `rabbitmq.yaml`) handle repo setup, installation, and basic configuration for each datastore.
- `nginx.conf` is deployed as part of `frontend.yaml` to proxy requests to the backend services.

## Status

This is a personal learning/practice project for DevOps automation (Ansible + AWS EC2), part of a broader RoboShop deployment exercise also covering Terraform, Docker, and EKS in separate repos.
