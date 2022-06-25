Common Role
=========

Common configs for both master Nodes and worker Nodes.

Role Variables
--------------

The vars are in `vars/main.yml`:

- **base_packages**: Basic packages needed in first steps.
- **k8s_packages**: Kubernetes packages that are available after its repo has added.
- **google_cloud_public_sign_key_url**
- **google_cloud_public_sign_key_path**: Path that we want the google sign key store there.
- **kubernetes_repo**: Kubernetes packages' repo.
