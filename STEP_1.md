# Step 1: Creazione di un Custom Execution Environment

Questo README descrive lo Step 1 del modulo formativo per la creazione di un Custom Execution Environment (EE) in Ansible Automation Platform. Segue le istruzioni del modulo e documenta i file utilizzati per completare l'attività.

## Obiettivo

Creare un nuovo Execution Environment che includa una collection a scelta da Ansible Galaxy (in questo caso `ansible.posix`) e utilizzare questa collection in un playbook che esegue una semplice operazione: la creazione di un file remoto.

---

## Struttura dei file utilizzati

### 1. `execution-environment.yml`

Questo file specifica le dipendenze necessarie per costruire l'Execution Environment. In questo caso, richiama un file separato `requirements.yml` per definire le collections.

```yaml
---
version: 1
dependencies:
  galaxy: requirements.yml
```

### 2. `requirements.yml`

In questo file vengono definite le collection da Ansible Galaxy da includere nell'Execution Environment. Per lo Step 1 è stata scelta la collection `ansible.posix`.

```yaml
---
collections:
  - name: ansible.posix
```

### 3. `playbook.yml`

Un semplice playbook che sfrutta la collection `ansible.posix`. Esegue la creazione di un file remoto nella directory `/tmp`.

```yaml
---
- name: Creazione di un file remoto
  hosts: localhost
  connection: local
  tasks:
    - name: Creare un file remoto
      file:
        path: /tmp/nuovo_file.txt
        state: touch
```

---

## Procedura seguita

1. **Creazione del Custom Execution Environment**
Utilizzando il comando `ansible-builder`, è stato creato l'Execution Environment con le dipendenze specificate.
```bash
ansible-builder create
ansible-builder build --container-runtime podman --tag my-ee
```

2. **Esecuzione del Playbook**
Una volta creato l'Execution Environment, il playbook è stato eseguito specificando l'immagine creata (`my-ee`).
```bash
ansible-playbook -i localhost, \
-e ansible_execution_environment_image=my-ee \
playbook.yml
```

---

## Requisiti

- Ansible Automation Platform installato
- `ansible-builder` configurato
- Conoscenza di base di Podman e di come configurare un EE

---

## Risultato atteso

Dopo l'esecuzione del playbook, viene creato un file vuoto chiamato `nuovo_file.txt` nella directory `/tmp`.
