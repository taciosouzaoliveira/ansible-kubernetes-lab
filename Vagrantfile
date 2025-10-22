# Vagrantfile

Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu2204"
  config.vm.box_check_update = false

  config.vm.provider :libvirt do |libvirt|
    libvirt.cpu_mode = "host-passthrough"
  end

  # Control Plane Node
  config.vm.define "k8s-control-plane" do |control_plane|
    control_plane.vm.hostname = "k8s-control-plane"
    control_plane.vm.network "private_network", ip: "192.168.56.10"
    control_plane.vm.provider :libvirt do |libvirt|
      libvirt.memory = 4096
      libvirt.cpus = 2
    end
  end

  # Worker Nodes
  (1..2).each do |i|
    config.vm.define "k8s-worker-#{i}" do |worker|
      worker.vm.hostname = "k8s-worker-#{i}"
      worker.vm.network "private_network", ip: "192.168.56.#{i + 10}"
      worker.vm.provider :libvirt do |libvirt|
        libvirt.memory = 2048
        libvirt.cpus = 2
      end
    end
  end
  
  # Provisionador Ansible
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"

    # --- INÍCIO DA CORREÇÃO ---
    # Mapeia as VMs para os grupos do Ansible que o playbook.yml espera
    ansible.groups = {
      "control_plane" => ["k8s-control-plane"],
      "workers"       => ["k8s-worker-1", "k8s-worker-2"]
    }
    # --- FIM DA CORREÇÃO ---
  end
end
