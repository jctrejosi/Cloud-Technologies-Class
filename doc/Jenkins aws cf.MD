# Desplegar Jenkins en AWS usando CloudFormation

## Requisitos previos

### Archivo de plantilla YAML

Guarda la plantilla de CloudFormation en un archivo llamado [`jenkins.yml`](https://github.com/jctrejosi/cloud-tecnology/blob/master/jenkins.yml).

## Pasos para desplegar Jenkins

### 1. Crear un par de claves (key pair)

Ejecuta los siguientes comandos para crear un par de claves y asignarle los permisos correctos:

```bash
# Crear el key pair
aws ec2 create-key-pair --key-name jenkins_key_pair --query 'KeyMaterial' --output text > jenkins_key_pair.pem

# Asignar permisos al archivo
chmod 400 jenkins_key_pair.pem
```

### 2. Crear el stack en CloudFormation

- Sube el archivo [`jenkins.yml`](https://github.com/jctrejosi/cloud-tecnology/blob/master/jenkins.yml) al CloudShell.
- Ejecuta el siguiente comando para crear el stack usando tu archivo `jenkins.yml`:

```bash
aws cloudformation create-stack \
  --stack-name JenkinsStack \
  --template-body file://jenkins.yml \
  --capabilities CAPABILITY_NAMED_IAM
```

### 3. Verificar el estado del stack

Monitorea el progreso de la creación del stack:

```bash
aws cloudformation describe-stacks --stack-name JenkinsStack --query "Stacks[0].StackStatus"
```

Ver los eventos del stack para más detalles:

```bash
aws cloudformation describe-stack-events --stack-name JenkinsStack
```

### 4. Obtener la IP pública de la instancia

Una vez que el stack esté en estado `CREATE_COMPLETE` debemos obtener la IP pública de la instancia

Obtener estado de instancias

```bash
aws ec2 describe-instances --query "Reservations[*].Instances[*].[InstanceId, Tags[?Key=='Name'].Value | [0], PublicIpAddress, State.Name]" --output table

```

Asignar IP pública a la instancia

```bash
ALLOC_ID=$(aws ec2 allocate-address --query 'AllocationId' --output text) aws ec2 associate-address --instance-id $INSTANCE_ID --allocation-id $ALLOC_ID
```

### 5. Conectarse a la instancia por SSH

Usa el archivo de clave privada (`jenkins_key_pair.pem`) para conectarte a la instancia:

```bash
ssh -i jenkins_key_pair.pem ubuntu@$PUBLIC_IP
```

### 6. Obtener la contraseña inicial de Jenkins

Dentro de la instancia, ejecuta el siguiente comando para obtener la contraseña inicial de Jenkins:

```bash
sudo docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

Copia la contraseña que se muestra.

### 7. Acceder a Jenkins en el navegador

Abre tu navegador y visita:

```bash
http://<IP_PUBLICA>:8080
```

Ingresa la contraseña que copiaste en el paso anterior.

Sigue las instrucciones en pantalla para completar la configuración inicial de Jenkins.

### 8. Eliminar el stack (opcional)

Si deseas eliminar el stack y todos los recursos asociados, ejecuta:

```bash
aws cloudformation delete-stack --stack-name JenkinsStack
```

Verifica que el stack se haya eliminado correctamente:

```bash
aws cloudformation describe-stacks --stack-name JenkinsStack
```

## Resumen de comandos principales

### Crear keyPair

```bash
aws ec2 create-key-pair --key-name jenkins_key_pair --query 'KeyMaterial' --output text > jenkins_key_pair.pem
chmod 400 jenkins_key_pair.pem
```

### Crear stack

```bash
aws cloudformation create-stack --stack-name JenkinsStack --template-body file://jenkins.yml --capabilities CAPABILITY_NAMED_IAM
```

### Estado del stack

```bash
aws cloudformation describe-stacks --stack-name JenkinsStack --query "Stacks[0].StackStatus"
```

### Estado de las instancias

```bash
aws ec2 describe-instances --query "Reservations[*].Instances[*].[InstanceId, Tags[?Key=='Name'].Value | [0], PublicIpAddress, State.Name]" --output table
```

### Conectar a la instancia

  ```bash
  ssh -i jenkins_key_pair.pem admin@$PUBLIC_IP
  ```

### Ver logs de la instancia

```bash
sudo cat /var/log/cloud-init-output.log
```

### Obtener contraseña de jenkins

  ```bash
  sudo docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
  ```

### Eliminar Stack

```bash
aws cloudformation delete-stack --stack-name JenkinsStack
```

### Eliminar instancias EC2

```bash
aws ec2 terminate-instances --instance-ids i-xxxxxxxxxxxxxxxxx
```

### Validar clave usada en la instancia

```bash
aws ec2 describe-instances --instance-ids i-xxxxxxxxxxxxxxxxx --query "Reservations[0].Instances[0].KeyName" --output text
```

### Obtener IP pública de instancia

```bash
INSTANCE_ID=$(aws ec2 describe-instances --filters "Name=tag:Name,Values=instanciaJenkins" --query "Reservations[0].Instances[0].InstanceId" --output text)
PUBLIC_IP=$(aws ec2 describe-instances --instance-ids $INSTANCE_ID --query "Reservations[0].Instances[0].PublicIpAddress" --output text)
echo $PUBLIC_IP
```
