%title: Vagrant - Kubernetes
%author: xavki


# Vagrant : cluster kubernetes avec kubespray

## Prompt Vagrant


<br>


* objectifs :
		* disposer rapidement/facilement d'un k8s local
		* apprendre vagrant
		* découvrir comment installer k8s
		* avoir une pseudo prod (haproxy)
		* scripter (vagrant et bash)


<br>



```
# Set some variables

	ingressNginx = ""
	wordpress = ""
	wordpressUrl = "wordpress.kub"
  etcHosts = ""

	# Check ingress controller
	case ARGV[0]
		when "provision", "up"
  	print "Do you want nginx as ingress controller (y/n) ?\n"
  	ingressNginx = STDIN.gets.chomp
  	print "\n"

  if ingressNginx == "y"
	  print "Do you want a wordpress in your kubernetes cluster (y/n) ?\n"
  	wordpress = STDIN.gets.chomp
  	print "\n"

		# check if wordpress
  	if wordpress == "y"
 			print "Which url for your wordpress ?"
  		wordpressUrl = STDIN.gets.chomp
     	unless wordpressUrl.empty? then wordpressUrl else 'wordpress.url' end
		end
	end
	else
  	# do nothing
	end
```


```
	# some settings for common server (not for haproxy)
  common = <<-SHELL
  sudo apt update -qq 2>&1 >/dev/null
  sudo apt install -y -qq git vim tree net-tools telnet git python3-pip sshpass nfs-common 2>&1 >/dev/null
  curl -fsSL https://get.docker.com -o get-docker.sh 2>&1
  sudo sh get-docker.sh 2>&1 >/dev/null
  sudo usermod -aG docker vagrant
  sudo service docker start
  sudo echo "autocmd filetype yaml setlocal ai ts=2 sw=2 et" > /home/vagrant/.vimrc
  sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/g' /etc/ssh/sshd_config
  sudo systemctl restart sshd
  SHELL
```


```
			#for haproxy
      if node[:type] == "haproxy"
				cfg.vm.provision :shell, :path => "install_haproxy.sh"
			end

			# for all servers in cluster (need docker)
      if node[:type] == "kub"
				cfg.vm.provision :shell, :inline => common
			end

			# for the deploy server
      if node[:type] == "deploy"
				cfg.vm.provision :shell, :inline => common
				cfg.vm.provision :shell, :path => "install_kubespray.sh", :args => ingressNginx
        if wordpress == "y"
          cfg.vm.provision :shell, :path => "install_nfs.sh"
          cfg.vm.provision :shell, :path => "install_wordpress.sh", :args => wordpressUrl
				end
			end
```
