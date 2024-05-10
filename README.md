# Considerações Gerais

Ter instalado no WINDOWS:
- Oracle VM Virtual BOX instalado
- Vagrant
- Visual Studio Code (Todos os arquivos / comandos foram criados/executados pelo terminal)

# Instação do Sonarqube

acessar a pasta Challenge\sonar
executar:
vagrant up
vagrant provision

acessar o sonar: localhost:9000
usuário: admin
senha: admin
* alterar a senha

Challenge\jenkins

# Instação do Jenkins

- acessar a pasta Challenge\jenkins e executar:
vagrant up
vagrant provision

- após instalação acessar via ssh
vagrant ssh

- copiar a senha do administrador: 
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
*copia a senha

- adicionar o usuário jenkins ao grupo docker
sudo usermod -aG docker jenkins

-acessar o jenkins: localhost:8080
cola a senha

- Instalar as extensões sugeridas

criar usuário jenkins/jenkins

- criar um trabalho
- criar pipeline
Nome: API_Comentario
- Pipeline
- Definition
Pileline script
- SCM
Git
-repositório
https://github.com/XXXX/API_Comentario.git
-Credentials:
-add

# instalar extensão do sonar no jenkins
- painel de controle
- gerenciar jenkins
- Plugins
- Extensões disponíveis
SonarQube Scanner
instalar

#configurar servidor do sonar no jenkins
- painel de controle
- gerenciar jenkins
- System
- SonarQube servers
Environment variables
Name: soanr-server
server URL: http://192.168.10.6:9000
- add Credentials
- kind
secret text
- Secret
copia o token do sonar
- id
secret-sonar

# Configuar a ferramenta do sonar-scanner no Jenkins

- painel de controle
- Gerenciar Jenkins
- Tools
- SonarQube Scanner
- Name
sonar-scanner
- SONAR_RUNNER_HOME
/opt/sonar-scanner

# Configurando o Nexus no Jenkins

acesse via ssh
docker volume create --name nexus-data
docker run -d -p 8081:8081 -p 8123:8123 --name nexus -v nexus-data:/nexus-data sonatype/nexus3
docker logs -f nexus
	Result Started Sonartype Nexus
	
# Criando usuário nexus

acessar via ssh o jenkins
docker exec -it nexus bash
cat /nexus-data/admin.password

altere a senha para nexus, usu´~ario admin
disable anonymous access

- server administration
- usermod
- create local user
id: jenkins
first name: jenkins
last name: jenkins
e-mail: jenkins@jenkins.com.br
password: jenkins
confirm password: jenkins
status: Active
roles: nx-admin

# Instalar o kuctl no jenkins
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
usermod -s /bin/bash jenkins
sudo su - jenkins
id
mkdir ~/.kube
vi ~/.kube/configs

acessar o manager (k3s) e copiar (cat /etc/rancher/k3s/k3s.yaml)


su -s /bin/bash jenkins
ID
kubectl

# Instalar o docker no jenkins


# criar repositório docker

- repository
- repositories
- create repository
docker (hosted)
Name: docker-repo
HTTP: 8123

# Configurar o Nexus no Jenkins
- painel de controle
- gerenciar jenkins
- System
- Propriedades globais
- Váriaveis de ambiente
- Adicionar

nome: NEXUS_URL
valor: 192.168.1.6:8123

- painel de controle
- gerenciar jenkins
- Credentials
- System
- Global credentials (unrestricted)
- Add Credentials
username: jenkins
password: jenkins
ID: nexus-user

# Instalar o Kubernets
- acessar a pasta Challenge\k3s e executar:
vagrant up

acesse via ssh
sudo su -
curl -sfL https://get.k3s.io | sh -s - --cluster-init --tls-san 192.168.10.2 --node-ip 192.168.10.2 --node-external-ip 192.168.10.2
service k3s status
kubectl get nodes
yum install git unzip telnet net-tolls -y
git clone https://github.com/ahmetb/kubectx /opt/kubectx
ln -s /opt/kubectx/kubectx /usr/local/bin/kubectx
ln -s /opt/kubectx/kubens /usr/local/bin/kubens

vi /etc/rancher/k3s/registries.yaml

mirrors:
  docker.io:
    endpoint:
      - "https://192.168.10.5:8123"
configs:
  "192.168.10.5:8123":
    auth:
      username: jenkins # this is the registry username
      password: jenkins # this is the registry password

systemctl restart k3s.service

