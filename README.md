# acs-cluster-local
Free local alfresco inside a cluster.

Los repositorios que he desarrollado son un sistema basado en contenedores que utiliza la plataforma de código abierto Alfresco para la gestión de contenidos empresariales. Este sistema está diseñado para ser desplegado en un clúster de Kubernetes y consta de tres nodos, un panel de control (control panel) y dos nodos de trabajo (workers).

El control panel es responsable de coordinar y gestionar la infraestructura de Kubernetes, así como de controlar la distribución de tareas y la asignación de recursos en el clúster. Actúa como punto centralizado de administración y monitoreo de todo el sistema.

Los nodos de trabajo (workers) son los encargados de ejecutar los contenedores de Alfresco. Cada nodo de trabajo aloja un conjunto de contenedores que contienen instancias de Alfresco, permitiendo así una alta disponibilidad y escalabilidad del sistema. Estos contenedores se ejecutan en entornos aislados, lo que garantiza una mayor seguridad y confiabilidad.

Además, los contenedores de Alfresco están configurados para utilizar volúmenes persistentes. Esto significa que los datos generados y almacenados por Alfresco, como documentos, metadatos y configuraciones, se guardarán de forma duradera en estos volúmenes, incluso si los contenedores se reinician o migran a otros nodos. Esto asegura que los datos críticos de la aplicación se conserven de manera segura y no se pierdan en caso de fallos o reinicios.

Kubernetes es una plataforma de orquestación de contenedores de código abierto que se utiliza para gestionar y escalar aplicaciones en entornos de producción. Proporciona una forma eficiente y automatizada de desplegar, administrar y escalar aplicaciones en contenedores, lo que permite a los equipos de desarrollo y operaciones trabajar de manera más eficiente y confiable.

Kubernetes se basa en la idea de agrupar contenedores relacionados en unidades lógicas llamadas "pods". Estos pods son programables, escalables y autónomos, y representan una unidad de implementación que se puede replicar y distribuir en varios nodos de un clúster. Los nodos son máquinas físicas o virtuales en las que se ejecutan los contenedores.

Uno de los beneficios clave de Kubernetes es su capacidad para manejar la escalabilidad y la disponibilidad de las aplicaciones de manera automática. Puede aumentar o disminuir el número de réplicas de un pod según la demanda de la aplicación, lo que garantiza un rendimiento óptimo y una respuesta rápida a medida que los requisitos cambian.

Por último, es importante destacar que los repositorios que se proporcionan para generar un clúster de Alfresco en Kubernetes se ejecutarán de forma local. Si bien existe la posibilidad de exponer el clúster en Internet, es importante tener en cuenta que el enfoque principal de este proyecto no es la disponibilidad pública en línea.

Agradezco sinceramente la oportunidad de asistirte en tu proyecto y ayudarte a comprender mejor el fascinante mundo de los contenedores, Docker, Kubernetes y Alfresco. Valoraré mucho tu colaboración y espero que mi trabajo haya sido de utilidad para ti. ¡Te deseo mucho éxito en tus futuras exploraciones y proyectos!.

Repositorios que me han ayudado a completar mi objetivo:
- https://github.com/Alfresco/acs-deployment
- https://github.com/aborroy/alfresco-installer

Tutoriales que he seguido para guiarme un poco más con mi objetivo:
- https://docs.alfresco.com/content-services/community/install/containers/docker-compose/
- https://www.youtube.com/watch?v=Lg49CoY8yl4

Entiendo y aprecio sinceramente tu enfoque responsable y respetuoso al proporcionar información valiosa sobre un proyecto interesante e innovador. Quiero dejar claro que mi intención al compartir los repositorios es exclusivamente la de compartir conocimientos y contribuir de manera positiva, sin ninguna intención de robar o malutilizar la información ofrecida.

Los repositorios que he compartido han sido de gran ayuda para alcanzar mi objetivo, ya que contienen información relevante sobre la implementación de Docker Compose en Kubernetes. Quiero destacar que esta información ha sido investigada y recopilada por mí mismo, demostrando mi compromiso con el aprendizaje y la búsqueda de soluciones.

Valorando la importancia de actuar de manera ética e íntegra, me enorgullece mantener una postura responsable al garantizar el uso adecuado de la información proporcionada. Mi objetivo principal es compartir conocimientos y ayudar a otros a comprender mejor el mundo de los dockers, contenedores, Kubernetes y otros conceptos relacionados.

Agradezco la oportunidad de participar en este proyecto y espero que mi contribución te sea de utilidad.

# REQUISITOS NECESARIOS PARA QUE TE FUNCIONE COMO A MI

- Sistema Operativo (S.O.) = Windows 10 
- Versión = 22H2, concretamente la 22000.1936 
- Memória Física = 16 GB RAM
- CPU = Intel(R) Core(TM) i7-9700 CPU @ 3.00GHz 

# APLICACIONES USADAS

- Docker Desktop (Dashboard visual + ajustes generales, para facilitar la creación y administración de contenedores)
- Chocolatey (Para instalar otros comandos)
- Kind ("Kubernetes in docker", para generar clústeres)
- kubectl (Controla y administra clústeres de Kubernetes eficientemente)
- Powershell (Usar los comandos necesarios)
