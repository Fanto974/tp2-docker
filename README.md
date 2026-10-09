# TP DevOps Correction Docker

1. Les testcontainers permettent de créer des vrais conteneurs docker et qui les détruisent automatiquement à la fin des tests
2. Pour plus de sécurité car les répos github peuvent être vus, clonés, etc... Les variables sécurisées elles, sont chiffrées.
3. Parce que sans cela les 2 jobs démarrent en parallèle et les images docker seraient alors construites même si les tests échouent. Avec needs on attend de voir si les tests sont validés et si ils le sont alors on crés et publie les images.
4. Parce que c'est plus facile à récupérer sans tout recompiler ou avoir le code. De plus ca permet que tout le monde utilise la même image et ca permet de rolleback si jamais y a un problème.


# tp3-docker
### 3.1 Inventaire et commandes de base

**Inventaire** (`ansible/inventories/setup.yml`)
- `all.vars` : variables communes à tous les hôtes
- `all.children.prod` : groupe `prod` contenant notre serveur. Les groupes permettent de cibler un sous-ensemble de machines.

**Commandes**
| Commande | Rôle |
| `ansible all -i inventories/setup.yml -m ping` | Vérifie la connexion SSH et la présence de Python sur les hôtes. |
| `ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"` | Récupère les facts liés à la distribution de l'OS. |
| `ansible all -i inventories/setup.yml -m apt -a "name=apache2 state=absent" --become` | Garantit qu'apache2 est désinstallé, avec les droits root (`--become`). |

**playbook.yml**
- `hosts: all` : cible tous les hôtes de l'inventaire.
- `gather_facts: true` : collecte les facts, nécessaires pour `distribution_release`, utilisé dans l'URL du dépôt Docker.
- `become: true` : exécute les tâches en root (installation de paquets, gestion de services).
- `roles` : délègue l'installation de Docker au rôle `docker`.

**Réseau**
```yaml
community.docker.docker_network:
  name: "{{ network_name }}"
  state: present
```
Crée le réseau Docker partagé, qui permet aux conteneurs de se joindre par leur nom.

**Paramètres communs aux conteneurs**
| Paramètre | Rôle |
| `name` | Nom du conteneur, qui sert aussi de nom DNS sur le réseau Docker. |
| `image` | Image DockerHub à lancer. |
| `state: started` | Garantit que le conteneur existe et tourne. |
| `restart_policy: always` | Redémarrage automatique. |
| `networks` | Rattache le conteneur au réseau `{{ network_name }}`. |

### Est-il sûr de déployer automatiquement chaque nouvelle image ?
Non car il n'y aucune validation humaine, toute image poussée part en prod directementy compris un bug qui passe les tests
Pour sécuriser tout ca il faudrai déployer seulement après des tests réussis et relus avec validation manuelle.
