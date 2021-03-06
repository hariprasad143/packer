@options = {
  name: '',
  autostart: false,
  box: '',
  box_url: '',
  provider: {
    memory: 4096,
    cpus: 2,
    linked_clone: true,
    nested_virtualization: true,
  },
  network: {
    bridge: ENV['VAGRANT_NETWORK_BRIDGE'],
  },
  synced_folder: {
    smb_username: ENV['VAGRANT_SMB_USERNAME'],
    smb_password: ENV['VAGRANT_SMB_PASSWORD'],
  }
}

Vagrant.configure('2') do |config|
  vms(config, 'w10e')
  vms(config, 'w10e-dc')

  vms(config, 'w16s')
  vms(config, 'w16s-dc')
  vms(config, 'w16s-iis')
  vms(config, 'w16s-sql14d')
  vms(config, 'w16s-vs10p')
  vms(config, 'w16s-vs15c')
  vms(config, 'w16s-vs15p')
  vms(config, 'w16s-vs17c')
  vms(config, 'w16s-vs17p')
end

def vms(config, name, options = {})
  vm(config, options.merge(name: "#{name}-build", box: "packer-#{name}", box_url: "file://build/#{name}"))
  vm(config, options.merge(name: "#{name}-publish", box: "gusztavvargadr/#{name}"))
end

def vm(config, options = {})
  options = @options.merge(options)

  config.vm.define options[:name], autostart: options[:autostart] do |vm|
    vm.vm.box = options[:box]

    vm.vm.provider 'hyperv' do |hyperv, override|
      hyperv.memory = options[:provider][:memory]
      hyperv.cpus = options[:provider][:cpus]
      hyperv.differencing_disk = options[:provider][:linked_clone]
      hyperv.enable_virtualization_extensions = options[:provider][:nested_virtualization]

      override.vm.box_url = "#{options[:box_url]}/hyperv-sysprep/output/vagrant.box" unless options[:box_url].to_s.empty?
      override.vm.network 'public_network', bridge: options[:network][:bridge]
      override.vm.synced_folder '.', '/vagrant', smb_username: options[:synced_folder][:smb_username], smb_password: options[:synced_folder][:smb_password]
    end

    vm.vm.provider 'virtualbox' do |virtualbox, override|
      virtualbox.memory = options[:provider][:memory]
      virtualbox.cpus = options[:provider][:cpus]
      virtualbox.linked_clone = options[:provider][:linked_clone]

      override.vm.box_url = "#{options[:box_url]}/virtualbox-sysprep/output/vagrant.box" unless options[:box_url].to_s.empty?
    end
  end
end
