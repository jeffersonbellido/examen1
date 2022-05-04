# examen1
1er primer examen MODULO 4

PARA LA PARTE DE KVM:

1.- En una consola de PowerShell (en modo administrador se debe correr) con la
    maquina virtual apagada.
      
      Set-VMProcessor -VMName UbuntuServer -ExposeVirtualizationExtensions $true

2.- Verificar que la maquina virtual tiene las extensiones de virtualización
    asistida por Hardware.
      
      egrep -c '(vmx|svm)' /proc/cpuinfo

3.- Debe salir mayor que cero Luego como siempre se deben actualizar toda la distro.

      sudo apt update
      sudo apt upgrade

4.- Luego se puede instalar KVM con el comando:
      
      sudo apt -y install qemu-kvm libvirt-daemon bridge-utils virtinst libvirt-daemon-system
      
5.- Y luego las herramientas de administración:

      sudo apt -y install virt-top libguestfs-tools libosinfo-bin qemu-system virt-manager

6.- Por último debemos estar seguros que el modulo de red este activo
       
      sudo modprobe vhost_net
      lsmod | grep vhos
      vhost_net 32768 0
      vhost 49152 1 vhost_net
      tap 24576 1 vhost_net
      echo vhost_net | sudo tee -a /etc/modules

PARA EL USO DE TERRAFORM

1. Instalar Terraform:
      
       sudo apt install wget curl unzip
       TER_VER=`curl -s https://api.github.com/repos/hashicorp/terraform/releases/latest | grep tag_name | cut -d: -f2 | tr -d \"\,\v | awk '{$1=$1};1'`
       wget https://releases.hashicorp.com/terraform/${TER_VER}/terraform_${TER_VER}_linux_amd64.zip

2.- Una vez que, el archivo este en la maquina virtual, es necesario copiar el binario en /usr/local/bin:
       
       unzip terraform_${TER_VER}_linux_amd64.zip
       Archive: terraform_1.1.8_linux_amd64.zip
       inflating: terraform
       sudo mv terraform /usr/local/bin/
       
3. Uso de Terraform para crear una máquina virtual KVM con una imagen de cloud, y la ayuda de cloud init.
       
       mkdir -p ~/projects/terraform
       cd ~/projects/terraform

4.- Se deben crear tres archivos : 

       Primer archivo main.tf   
       El segundo archivo libvirt.tf
       Y el último archivo es cloud_init.cfg
      
5.- Por último para conseguir la imagen de cloud de Ubuntu Xenial :
       
       wget https://cloud-images.ubuntu.com/xenial/current/xenial-server-cloudimg-amd64-disk1.img
       
6.- Una vez descargado la imagen de Xenial se debe escribir los siguientes comandos:
        
        terraform init 
        terraform plan
        terraform apply
        terraform destroy
        
NOTA: POSIBLEMENTE SE PRESENTE PROBLEMAS EN EL LOGIN PERO LA MEJOR MANERA DE LOGIARSE ES CREANDO UN KEY COMO IDENTIFICADOR DE USUARIO Y ESTA NUEVA KEY COPIARLA EN EL ARCHIVO cloud_init.cfg 
       
       ssh-keygen

          Generating public/private rsa key pair.
          Enter file in which to save the key (/home/jefferson/.ssh/id_rsa):
          Your public key has been saved in /home/jefferson/.ssh/id_rsa.pub
          The key fingerprint is:
          SHA256:dBhDli7sh/5MR3E+ue9Wp/zWvmTnzDxFKo5b2KjbL9k jorge@c1
          The key's randomart image is:
          +---[RSA 3072]----+
          |       .=.       |
          |       ..+       |
          |     . .o o .    |
          |      o... + .  .|
          |     . oS . +  o |
          |      o .. +.o. +|
          |     . .. +=+o ==|
          |      .o ++.E.**+|
          |       .=.o+.oo=X|
          +----[SHA256]-----+
          
Esto crea el archivo ~/.ssh/id_rsa.pub y para copiar el contenido se puede usar re-direccionamiento:

        cat ~/.ssh/id_rsa_pub >> cloud_init.cfg
