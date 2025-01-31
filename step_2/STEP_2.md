# Step 2

In questo step, abbiamo creato un nuovo Execution Environment (EE) personalizzato con una versione specifica di Python e verificato la versione all'interno di un playbook Ansible.

## Struttura dei file utilizzati

- **`execution_environment.yml`**: Definisce la configurazione dell'EE, specificando l'immagine di base e le dipendenze.
- **`bindep.txt`**: Contiene le dipendenze di sistema necessarie per installare la versione desiderata di Python.
- **`requirements.txt`**: Elenca i pacchetti Python richiesti.
- **`playbook.yml`**: Esegue il playbook per stampare la versione di Python in uso all'interno dell'EE.

---

## Configurazione dell'Execution Environment

### **`execution_environment.yml`**
```yaml
---
version: 1
build_arg_defaults:
  EE_BASE_IMAGE: "registry.access.redhat.com/ubi9/python-39"

dependencies:
  system: bindep.txt
  python: requirements.txt
```

- **EE_BASE_IMAGE**: Usiamo `ubi9/python-39` per assicurarci che l'EE utilizzi Python 3.9.
- **dependencies**:
  - `bindep.txt` per le dipendenze di sistema.
  - `requirements.txt` per i pacchetti Python richiesti.

### **`bindep.txt`**
```yaml
python3.9
python3.9-devel
```

Queste dipendenze assicurano che Python 3.9 venga installato nel container.

### **`requirements.txt`**
```yaml
requests
numpy
```

Aggiungiamo alcune librerie Python di esempio.

---

## Creazione dell'EE

Per creare e costruire l'EE personalizzato, eseguire i seguenti comandi:
```bash
ansible-builder create
ansible-builder build --container-runtime podman --tag my-ee-python
```

- `ansible-builder create`: Genera il contesto di build.
- `ansible-builder build`: Costruisce l'EE con Podman e lo tagga come `my-ee-python`.

Per verificare che l'immagine sia stata creata correttamente, eseguire:
```bash
podman images
```

---

## Esecuzione del Playbook

### **`playbook.yml`**
```yaml
---
- name: Verifica della versione custom di Python
  hosts: localhost
  connection: local
  tasks:
    - name: Stampare la versione di Python
      command: python --version
      register: python_version

    - name: Mostrare la versione di Python
      debug:
        msg: "Versione di Python in uso: {{ python_version.stdout }}"
```

Per eseguire il playbook con l'EE personalizzato:
```bash
ansible-playbook -i localhost, \
  -e ansible_python_interpreter=/usr/bin/python3 \
  -e ansible_execution_environment_image=my-ee-python \
  playbook.yml
```

Se tutto è andato a buon fine, dovresti vedere un output simile a:
```bash
TASK [Mostrare la versione di Python] ******************************************
ok: [localhost] => {
    "msg": "Versione di Python in uso: Python 3.9.21"
}
```
