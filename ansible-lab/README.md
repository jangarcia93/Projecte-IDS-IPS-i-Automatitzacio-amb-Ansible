# Infraestructura d’Automatització (Ansible)

La infraestructura d'automatització es basa en **Ansible**, que permet gestionar i configurar diversos servidors de forma centralitzada.

Ansible s'executa dins un **contenidor Docker que actua com a node de control**, mentre que els servidors gestionats també s'executen com a contenidors Debian.

Aquesta configuració permet crear un laboratori fàcilment reproduïble i modular.

---

# Node de Control

El node de control Ansible s'executa dins un contenidor Docker basat en **Debian 12**.

Aquest node és responsable d'automatitzar la configuració dels servidors utilitzant **playbooks d’Ansible**.

Les seves funcions principals són:

- executar playbooks
- gestionar inventaris de hosts
- establir connexions SSH amb els nodes gestionats
- garantir que els sistemes mantinguin l'estat desitjat

L'ús de Docker permet:

- desplegament ràpid
- reproduïbilitat del laboratori
- separació del sistema host
- flexibilitat per modificar la infraestructura

---

# Nodes Gestionats

Els nodes gestionats representen servidors dins la infraestructura.

Cada node s'executa com un **contenidor Docker basat en Debian 12** i disposa d'un servidor **SSH** que permet la seva gestió des d'Ansible.

Els nodes gestionats permeten demostrar:

- instal·lació de paquets
- configuració automatitzada de serveis
- desplegament d'aplicacions web
- gestió d'usuaris i permisos
- aplicació de configuracions de sistema

---

# Inventari

Els nodes gestionats es defineixen dins del fitxer:

```
inventory/hosts
```

Exemple d'inventari utilitzat en el laboratori:

```
[clients]
managed-node-01 ansible_host=managed-node-01 ansible_user=ansible ansible_password=ansible ansible_become_password=ansible
managed-node-02 ansible_host=managed-node-02 ansible_user=ansible ansible_password=ansible ansible_become_password=ansible
```

Aquest inventari permet que Ansible gestioni múltiples nodes de forma centralitzada.

---

# Execució del Playbook

La configuració dels servidors es realitza executant el següent comandament des del node de control:

```
ansible-playbook -i /inventory/hosts /ansible/setup_web.yml
```

Aquest playbook aplica la configuració sobre tots els nodes definits al grup **clients**.

---

# Tasques Automatitzades

El playbook implementa diverses tasques que permeten demostrar diferents aspectes d'automatització.

---

## Aprovisionament

El sistema realitza automàticament les següents tasques d'aprovisionament:

- instal·lació de paquets base (`git`, `vim`, `curl`, `cron`)
- creació del grup `devops`
- creació de l'usuari `deploy`
- creació del directori `/opt/webapp`

Exemple de tasca Ansible:

```
- name: Crear grup devops
  group:
    name: devops
    state: present
```

---

## Desplegament d’Aplicacions

El laboratori desplega una aplicació web utilitzant **Nginx**.

Tasques realitzades:

- instal·lació del servidor web Nginx
- activació del servei
- desplegament d'una pàgina web personalitzada
- clonació d'un repositori Git amb contingut d'exemple

Exemple de tasca:

```
- name: Instal·lar nginx
  apt:
    name: nginx
    state: present
```

També es realitza la clonació d'un repositori Git dins del directori de treball.

```
- name: Clonar aplicació web des de Git
  git:
    repo: https://github.com/docker/awesome-compose.git
    dest: /opt/webapp/repo
```

---

## Gestió de Configuració

Ansible permet garantir que els sistemes mantinguin una configuració consistent.

Entre les tasques realitzades es troben:

- assegurar que el servei nginx està actiu
- desplegar fitxers de configuració
- mantenir l'estat dels serveis
- crear tasques programades de manteniment

Exemple de configuració de servei:

```
- name: Assegurar que nginx està actiu
  service:
    name: nginx
    state: started
    enabled: yes
```

---

## Seguretat Bàsica

El playbook també aplica configuracions bàsiques de seguretat.

Entre aquestes mesures es troben:

- desactivar el login SSH del root
- crear fitxers amb permisos segurs
- limitar l'accés administratiu

Exemple de configuració:

```
- name: Desactivar login root per SSH
  lineinfile:
    path: /etc/ssh/sshd_config
    regexp: '^PermitRootLogin'
    line: 'PermitRootLogin no'
```
---

# Execució Ràpida del Laboratori

Per iniciar la infraestructura Docker del laboratori:

```bash
docker compose up -d
```

Accedir al contenidor del node de control d’Ansible:

```bash
sudo docker exec -it ansible-control bash
```

Executar el playbook d'automatització:

```bash
ansible-playbook -i /inventory/hosts /ansible/setup_web.yml
```

Després de l'execució del playbook:

- els nodes gestionats disposaran d’un **servidor web Nginx instal·lat i actiu**
- es desplegarà una **pàgina web personalitzada**
- es configuraran **usuaris, directoris i serveis**
- s’aplicaran **configuracions bàsiques de seguretat**

Això demostra com Ansible pot automatitzar la configuració d’una infraestructura completa.

---
