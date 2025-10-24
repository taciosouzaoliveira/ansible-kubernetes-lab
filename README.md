# Laboratório Kubernetes Automatizado com Vagrant e Ansible

Este repositório contém um ambiente de laboratório para estudos de Kubernetes, projetado para ser provisionado de forma automática. Ele é ideal para quem está se preparando para certificações como **LFCS (Linux Foundation Certified Sysadmin)** e **CKA (Certified Kubernetes Administrator)**.

O projeto demonstra a evolução de uma automação simples com Shell Script para uma solução de Infraestrutura como Código (IaC) robusta e profissional utilizando Ansible.

<p align="center">
  <img src="Crontrol Plane Imagem.png" alt="Arquitetura do Cluster Kubernetes" width="800">
</p>

## Estrutura do Repositório

Este projeto está dividido em duas versões, representando uma jornada de aprimoramento técnico:

### 1. `v1-vagrant-shell-script/`
A abordagem inicial. Utiliza um `Vagrantfile` para criar as máquinas virtuais e um único `script.sh` para instalar e configurar todos os componentes.
* **Prós:** Rápido de prototipar.
* **Contras:** Não é idempotente, difícil de manter e não é escalável.

### 2. `v2-vagrant-ansible/`
A versão profissional. Utiliza o **Ansible** para provisionar e configurar os nós do cluster de forma declarativa, garantindo consistência e reutilização.
* **Prós:** Idempotente, legível, modular (usando roles) e facilmente escalável.
* **Contras:** Requer conhecimento prévio de Ansible.

## Pré-requisitos

Para executar este laboratório, você precisa ter as seguintes ferramentas instaladas em sua máquina host:

* [Vagrant](https://www.vagrantup.com/downloads)
* [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
* Um provedor de virtualização para o Vagrant, como:
    * [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
    * [Libvirt/KVM](https://vagrant-libvirt.github.io/vagrant-libvirt/) (Recomendado para usuários Linux)

## Como Utilizar (Versão Ansible)

1.  **Clone o repositório:**
    ```bash
    git clone https://github.com/taciosouzaoliveira/ansible-kubernetes-lab
   
    ```

2.  **Acesse o diretório da versão com Ansible:**
    ```bash
    cd ansible-kubernetes-lab
    ```

3.  **Inicie o ambiente:**
    ```bash
    vagrant up
    ```
    O Vagrant irá criar 1 Control Plane e 2 Worker Nodes. Em seguida, ele acionará o playbook do Ansible para configurar o ambiente, instalando `containerd`, `kubeadm`, `kubelet` e `kubectl` em todos os nós.

4.  **Acesse o Control Plane para iniciar o cluster:**
    ```bash
    vagrant ssh k8s-control-plane
    ```

5.  **Dentro do Control Plane, inicialize o cluster Kubernetes:**
    ```bash
    sudo kubeadm init --pod-network-cidr=10.244.0.0/16
    ```
    > **Nota:** Ao final da execução, o `kubeadm` fornecerá o comando `kubeadm join` com um token. Copie este comando para usar nos worker nodes.

6.  **Instale um CNI (Container Network Interface) como o Flannel:**
    ```bash
    # Ainda no Control Plane
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
    kubectl apply -f [https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml](https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml)
    ```

7.  **Junte os Worker Nodes ao cluster:**
    Acesse cada worker (`vagrant ssh k8s-worker-1`, `vagrant ssh k8s-worker-2`) e execute o comando `kubeadm join` que você copiou no passo 5.

## Agradecimentos

A evolução deste projeto foi impulsionada pela colaboração da comunidade. Agradecimentos especiais a:
* **Bruno dos Santos**, pela excelente sugestão de migrar a lógica de provisionamento para **Ansible**.
* **Marcelo Baptista**, pela importante correção técnica de usar o termo padrão da indústria, **"Control Plane"**.

