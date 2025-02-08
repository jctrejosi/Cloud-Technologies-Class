# Comandos usados en cloudFormation

## Crear Stack

```bash
aws cloudformation create-stack --stack-name your_stack_name --template-body file://deployment.yml --capabilities CAPABILITY_NAMED_IAM
```

## Ver id del Stack

```bash
aws cloudformation describe-stacks --stack-name your_stack_name --query "Stacks[0].StackId" --output text
```

## Verificar estado del Stack

```bash
aws cloudformation describe-stacks --stack-id your_stack_id --query "Stacks[0].StackStatus"
```

## Ver todos los Stacks

```bash
aws cloudformation list-stacks --query "StackSummaries[*].[StackId, StackName, StackStatus]" --output table
```

## Ver detalles y eventos del Stack

```bash
aws cloudformation describe-stack-events --stack-id your_stack_id
```

## Eliminar Stack

```bash
aws cloudformation delete-stack --stack-id your_stack_id
```
