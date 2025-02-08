# Comandos usados en instancias EC2

## Crear un par de claves (KeyPair) para conectar con ssh

```bash
aws ec2 create-key-pair --key-name jenkins_key_pair --query 'KeyMaterial' --output text > jenkins_key_pair.pem
chmod 400 jenkins_key_pair.pem
```

## Ver estado de instancias EC2

```bash
aws ec2 describe-instances --query "Reservations[*].Instances[*].[InstanceId, Tags[?Key=='Name'].Value | [0], PublicIpAddress, State.Name]" --output table
```

## Conectarse a una instancia EC2

```bash
ssh -i jenkins_key_pair.pem admin@$INSTANCE_PUBLIC_IP
```

## Eliminar instancias EC2

```bash
aws ec2 terminate-instances --instance-ids i-xxxxxxxxxxxxxxxxx
```

## Asignar IP pública a la instancia

```bash
ALLOC_ID=$(aws ec2 allocate-address --query 'AllocationId' --output text)
aws ec2 associate-address --instance-id $INSTANCE_ID --allocation-id $ALLOC_ID
```

## Obtener IP pública de la instancia

```bash
INSTANCE_ID=$(aws ec2 describe-instances --filters "Name=tag:Name,Values=InstanciaJenkins" --query "Reservations[0].Instances[0].InstanceId" --output text)
PUBLIC_IP=$(aws ec2 describe-instances --instance-ids $INSTANCE_ID --query "Reservations[0].Instances[0].PublicIpAddress" --output text)
echo $PUBLIC_IP
```

## Validar clave ssh usada en la instancia

```bash
aws ec2 describe-instances --instance-ids i-xxxxxxxxxxxxxxxxx --query "Reservations[0].Instances[0].KeyName" --output text
```
