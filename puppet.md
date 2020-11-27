# show filebucket files
```shell
puppet filebucket list --local
# oder
find /opt/puppetlabs/puppet/cache/clientbucket -type f -name paths -exec sort {} + | uniq -c | sort -nr
```

# acceptance testing
```
# Gemfile
group :system_tests do             
  gem 'beaker',       :require => false
  gem 'beaker-rspec', :require => false
  gem 'beaker-vagrant', :require => false
  gem 'beaker-docker', :require => false             
  gem 'beaker-puppet_install_helper', :require => false
end 

# spec/spec_helper_acceptance.rb
require 'beaker-rspec'
require 'beaker/puppet_install_helper'

run_puppet_install_helper

RSpec.configure do |c|
  proj_root = File.expand_path(File.join(File.dirname(__FILE__), '..'))

  c.formatter = :documentation

  c.before :suite do
    hosts.each do |host|
      copy_module_to(host, :source => proj_root, :module_name => 'test')
      on host, puppet('module install puppetlabs-stdlib'),
        {:acceptable_exit_codes => [0]}
    end
  end
end

# spec/acceptance/nodesets/docker.yml 
HOSTS:
  ubuntu-16-04:
    platform: ubuntu-16.04-x64
    image: ubuntu:16.04
    hypervisor: docker
CONFIG:
  type: foss
  
# spec/acceptance/test_spec.rb 
require 'spec_helper_acceptance'

describe 'spacewalk::server' do
  let(:manifest) {
    <<-EOS
include test
EOS
  }
  it 'should apply without errors' do
    apply_manifest(manifest, :catch_failures => true)
  end

  it 'should apply a second time without changes' do
    @result = apply_manifest(manifest)
    expect(@result.exit_code).to be_zero
  end
end

# BEAKER_PUPPET_COLLECTION=puppet pdk bundle exec rake beaker:docker
# 
``` 

# local module execution
```bash
puppet apply --modulepath ./modules --hiera_config ./hiera.yml -e 'include my_module'
```
