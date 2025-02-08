# Clase de tecnologías en la nube (con AWS-Academy)

Este repositorio contiene diversas implementaciones y documentación relacionadas con el laboratorio de AWS-Formation.

Cada 📂Projects/"subcarpeta" representa un tema específico y contiene su respectiva documentación y archivos de despliegue.

## Tabla de contenidos

- [Proyectos](#proyectos)
  - [📂 Despliegue de Jenkins en CloudFormation](#-despliegue-de-jenkins-en-cloudformation)
  - [📂 Despliegue de proyectos web con Docker en CloudFormation](#-despliegue-de-proyectos-web-con-docker-en-cloudformation)
- [Recomendaciones](#recomendaciones)
- [Requisitos](#requisitos)
- [Cómo utilizar este repositorio](#cómo-utilizar-este-repositorio)
- [Cómo realizar contribuciones](#cómo-realizar-contribuciones)
- [Recursos adicionales](#recursos-adicionales)

## Proyectos

### 📂 [Despliegue de Jenkins en CloudFormation](./Jenkins_CloudFormations)

- 📄 [Documentación](./Projects/Jenkins_CloudFormations/doc.md)
- 🚀 [Archivo de despliegue](./Projects/Jenkins_CloudFormations/deployment.yaml)

### 📂 [Despliegue de proyectos web con Docker en CloudFormation](./PERT-solver_CloudFormations)

- 📄 [Documentación](./Projects/PERT-solver_CloudFormations/doc.md)
- 🚀 [Archivo de despliegue](./Projects/PERT-solver_CloudFormations/deployment.yaml)

## Resumen de comandos

- [CloudFormation](./Commands/cloudFormations.md)
- [EC2](./Commands/instanceEC2.md)

## Recomendaciones

- Cada sección donde se copie ejecuciones de código en el bash, debes tener presente las variables nombradas como $NOMBRE_VARIABLE.

## Requisitos

- Acceso a una cuenta en un proveedor de nube compatible (AWS, AWS-Formations).

## Cómo utilizar este repositorio

1. Navega a la subcarpeta del tema que te interese.
2. Revisa la documentación incluida para comprender la implementación.
3. Usa el archivo de despliegue para ejecutar el servicio en la nube.

## Cómo realizar contribuciones

Si deseas contribuir, por favor sigue estos pasos:

1. Realiza un fork del repositorio.
2. Crea una rama con tu nueva corrección ó funcionalidad:

   ```bash
   git checkout -b hotfix/nombre_de_la_corrección
   ```

   *Si es la creación de una nueva funcionalidad, comienza el nombre de la rama con "feature/" en lugar de "hotfix/"*

3. Realiza tus cambios y súbelos:

   ```bash
   git commit -m "mensaje con descripción de la funcionalidad" && git push origin feature/nombre_de_la_funcionalidad
   ```

4. Crea un Pull Request a la rama "master" para revisión.

## Recursos adicionales

1. [Repositorio del profesor del curso](https://github.com/cesarpalacios)
2. [Cómo crear una solicitud de incorporación de cambios (PR)](https://docs.github.com/es/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request)
3. [Cómo trabajar con ramas en git usando el método de Gitflow workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)
4. [Curso de git en YouTube por midudev](https://www.youtube.com/watch?v=niPExbK8lSw&t=358s&ab_channel=midulive)
5. [Curso de git en freecodecamp](https://www.freecodecamp.org/espanol/news/aprende-git-y-github-curso-desde-cero/)
6. [Manual de debian/comandos básicos para el manejo de subsistemas](https://www.debian.org/doc/manuals/debian-reference/debian-reference.es.pdf)
7. [Comandos en linux pdf (Quick refrence Linux Administrarion)](./References/Linux%20Administration.pdf)
