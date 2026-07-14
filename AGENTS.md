This repository is for RKE2 + Rancher automation with Ansible and GitOps for custom/imported downstream clusters.

  Allowed scope:
  - Ansible playbooks to prepare VMs, install RKE2, install Rancher, and register custom/imported clusters.
  - GitOps/Kustomize/Argo CD/Fleet manifests for Rancher objects and downstream cluster post-configuration.
  - Integrations directly related to Kubernetes, RKE2, Rancher, cert-manager, External Secrets, Argo CD, and Kustomize.

  Out of scope unless explicitly requested:
  - Replacing RKE2 with another Kubernetes distribution.
  - Introducing Terraform, custom Helm charts, operators, or new controllers.
  - Creating generic platforms, dashboards, apps, or pipelines unrelated to the RKE2/Rancher flow.
  - Adding dependencies without justification and validation.

  ## graphify

  This project has a knowledge graph at graphify-out/ with god nodes, community structure, and cross-file relationships.
  
  When the user types `/graphify`, use the installed graphify skill or instructions before doing anything else.
  
  Rules:
  - For codebase questions, first run `graphify query "<question>"` when graphify-out/graph.json exists. Use `graphify path "<A>" "<B>"` for relationships and `graphify explain "<concept>"` for focused concepts. These return a scoped subgraph, usually much smaller than GRAPH_REPORT.md or raw grep output.
  - Dirty graphify-out/ files are expected after hooks or incremental updates; dirty graph files are not a reason to skip graphify. Only skip graphify if the task is about stale or incorrect graph output, or the user explicitly says not to use it.
  - If graphify-out/wiki/index.md exists, use it for broad navigation instead of raw source browsing.
  - Read graphify-out/GRAPH_REPORT.md only for broad architecture review or when query/path/explain do not surface enough context.
  - After modifying code, run `graphify update .` to keep the graph current (AST-only, no API cost).

  ## Response Style

  - Respond directly, in English, with only the necessary detail.
  - Do not repeat obvious context.
  - Do not over-explain simple commands.
  - For small changes, provide a short summary and the validations executed.
  - Avoid broad brainstorming when the user asks for execution or a concrete decision.
  - If an answer depends on a version, API, CRD, or external behavior, check official documentation before making claims.

  ## Token Efficiency

  - Use Graphify before broad repository searches when `graphify-out/graph.json` exists.
  - Prefer `rg`, `git diff`, `git status`, `kubectl kustomize`, `ansible-playbook --syntax-check`, and targeted file reads.
  - Do not read large files in full when a focused search is enough.
  - Do not paste long command outputs into the chat; summarize the relevant result.
  - Do not open external documentation unless needed. Open only official pages directly related to the issue.

  ## Do Not Invent

  - Do not invent APIs, CRDs, YAML fields, Ansible modules, versions, or flags.
  - If official sources or repository evidence are not sufficient, say clearly: “I did not find enough evidence.”
  - When making an inference, label it as an inference.
  - Do not present examples as production-ready without validation.
  - Do not create real secrets, tokens, kubeconfigs, certificates, or registration commands.
  - Use explicit placeholders such as `CHANGE_ME` only when necessary.

  ## Infrastructure Rules

  - Treat changes to Ansible, Kubernetes, Rancher, Argo CD, Fleet, cert-manager, and External Secrets as production-impacting changes.
  - Before changing files, understand the existing repository flow.
  - Preserve the current pattern:
    - Ansible for VMs and bootstrap.
    - GitOps/Kustomize for Kubernetes/Rancher resources.
    - Real inventories ignored by git.
  - For custom clusters on empty VMs, remember that GitOps creates the Rancher object, but Ansible runs the registration command on the VMs.
  - For imported clusters, GitOps can create the Rancher registration object, but the generated manifest still has to be applied to the downstream cluster.

  ## Required Validations

  Depending on the type of change, run:

  - Ansible:
    - `ansible-playbook --syntax-check <playbook>`
    - `ansible-inventory --list` when inventory changes
  - Kustomize/GitOps:
    - `kubectl kustomize <path>`
    - local YAML parsing
  - Kubernetes/Rancher manifests:
    - validate with available local tools
    - do not apply to a real cluster unless explicitly requested
  - Always:
    - `git diff --check`
    - `graphify update .` after structural changes

  If a validation cannot be executed, state why.

  ## Official Sources

  Prefer these official documentation sources:

  - RKE2: https://docs.rke2.io/
  - Rancher Manager: https://ranchermanager.docs.rancher.com/
  - cert-manager: https://cert-manager.io/docs/
  - Ansible: https://docs.ansible.com/
  - External Secrets Operator: https://external-secrets.io/latest/
  - Argo CD: https://argo-cd.readthedocs.io/en/stable/
  - Kubernetes: https://kubernetes.io/docs/home/
  - Kustomize: https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/