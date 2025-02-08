# Desplegar Un proyecto (Frontend) en una ec2

## Requisitos previos

### Archivo de plantilla YAML

Guarda la plantilla de CloudFormation en un archivo llamado [`deployment.yml`](deployment.yml)

## Pasos para desplegar el proyecto

### 1. Crear un par de claves (key pair)

Ejecuta los siguientes comandos para crear un par de claves y asignarle los permisos correctos:

```bash
# Crear el key pair
aws ec2 create-key-pair --key-name project_key_pair --query 'KeyMaterial' --output text > project_key_pair.pem

# Asignar permisos al archivo
chmod 400 project_key_pair.pem
```

### 2. Crear el stack en CloudFormation

- Sube el archivo [`deployment.yml`](https://github.com/jctrejosi/Cloud-Technologies-Class/blob/master/PERT-solver_CloudFormations/deployment.yml) al CloudShell.
- Ejecuta el siguiente comando para crear el stack usando tu archivo `deployment.yml`:

```bash
aws cloudformation create-stack \
  --stack-name projectStack \
  --template-body file://deployment.yml \
  --capabilities CAPABILITY_NAMED_IAM
```

### 3. Verificar el estado del Stack-cloudformation

Monitorea el progreso de la creación del stack:

```bash
aws cloudformation describe-stacks --stack-name projectStack --query "Stacks[0].StackStatus"
```

### 4. Obtener la IP pública de la Instancia

Una vez que el stack esté en estado `CREATE_COMPLETE` debemos obtener la IP pública de la Instancia

Obtener valores de las Instancias

```bash
aws ec2 describe-instances --query "Reservations[*].Instances[*].[InstanceId, Tags[?Key=='Name'].Value | [0], PublicIpAddress, State.Name]" --output table

```

### 5. Conectarse a la Instancia por SSH

Usa el archivo de clave privada (`project_key_pair.pem`) para conectarte a la Instancia:

```bash
ssh -i project_key_pair.pem admin@$PUBLIC_IP
```

### 6. Acceder a project en el navegador

Abre tu navegador y visita:

```bash
http://<$IP_PUBLICA>:3000
```

Ingresa la contraseña que copiaste en el paso anterior.

Sigue las instrucciones en pantalla para completar la configuración inicial de deployment.

### 7. Eliminar el Stack-cloudformation (opcional)

Si deseas eliminar el stack y todos los recursos asociados, ejecuta:

```bash
aws cloudformation delete-stack --stack-name projectStack
```

Verifica que el stack se haya eliminado correctamente:

```bash
aws cloudformation describe-stacks --stack-name projectStack
```

## Resumen de comandos principales

### Crear keyPair

```bash
aws ec2 create-key-pair --key-name project_key_pair --query 'KeyMaterial' --output text > project_key_pair.pem
chmod 400 project_key_pair.pem
```

### Crear Stack-cloudformation

```bash
aws cloudformation create-stack --stack-name projectStack --template-body file://deployment.yml --capabilities CAPABILITY_NAMED_IAM
```

### Estado del Stack-cloudformation

```bash
aws cloudformation describe-stacks --stack-name projectStack --query "Stacks[0].StackStatus"
```

### Ver detalles y eventos de Stack-cloudformation

```bash
aws cloudformation describe-stack-events --stack-name projectStack
```

### Eliminar Stack-cloudformation

```bash
aws cloudformation delete-stack --stack-name projectStack
```

### Estado de las Instancias

```bash
aws ec2 describe-instances --query "Reservations[*].Instances[*].[InstanceId, Tags[?Key=='Name'].Value | [0], PublicIpAddress, State.Name]" --output table
```

### Conectar a la Instancia

  ```bash
  ssh -i project_key_pair.pem admin@$INTANCE_PUBLIC_IP
  ```

### Ver logs de la Instancia

```bash
sudo cat /var/log/cloud-init-output.log
```

### Eliminar Instancias EC2

```bash
aws ec2 terminate-instances --instance-ids i-xxxxxxxxxxxxxxxxx
```

### Asignar IP pública a la Instancia

```bash
ALLOC_ID=$(aws ec2 allocate-address --query 'AllocationId' --output text) aws ec2 associate-address --instance-id $INSTANCE_ID --allocation-id $ALLOC_ID
```

### Obtener IP pública de Instancia

```bash
INSTANCE_ID=$(aws ec2 describe-instances --filters "Name=tag:Name,Values=Instanciaproject" --query "Reservations[0].Instances[0].InstanceId" --output text)
PUBLIC_IP=$(aws ec2 describe-instances --instance-ids $INSTANCE_ID --query "Reservations[0].Instances[0].PublicIpAddress" --output text)
echo $PUBLIC_IP
```

### Validar clave usada en la Instancia (Dentro de la Instancia)

```bash
aws ec2 describe-instances --instance-ids i-xxxxxxxxxxxxxxxxx --query "Reservations[0].Instances[0].KeyName" --output text
```
