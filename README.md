# Laboratorio-de-Despliegue-de-Infraestructura-Windows-Server-2022-y-Active-Directory
Este repositorio contiene la documentación paso a paso para la instalación, configuración e integración de un entorno de dominio corporativo utilizando Windows Server 2022 como Controlador de Dominio y un cliente Windows 11 Pro, aislados en un entorno virtualizado sobre VMware Workstation.
1. Arquitectura de Red del Laboratorio
Para garantizar el aislamiento de las pruebas, se utilizó un segmento de red virtualizado.

Tipo de red: LAN Segment (laboratorio)

Controlador de Dominio (DC):

Sistema Operativo: Windows Server 2022 Datacenter

Nombre de equipo: DC-MARIO

IP Estática: 192.168.10.10

Máscara de subred: 255.255.255.0

Puerta de enlace: 192.168.10.1

DNS Preferido: 192.168.10.10

Cliente:

Sistema Operativo: Windows 11 Pro

Nombre de equipo: CLMARIO01

IP Estática: 192.168.10.20

Máscara de subred: 255.255.255.0

DNS Preferido: 192.168.10.10

2. Fase de Implementación
Configuración del Rol de Servicios de Dominio (AD DS)
Se procedió con la instalación del rol de Active Directory y la posterior promoción del servidor. Se optó por la opción de Agregar un nuevo bosque asignando el nombre de dominio raíz mario.local.

Tras resolver las comprobaciones de requisitos previos (asegurando el direccionamiento IPv4 estático), se completó la instalación y el reinicio automático del sistema para consolidar el nuevo rol.

3. Fase de Integración y Troubleshooting (Resolución de Problemas)
Durante el proceso de unión del cliente Windows 11 al dominio, se presentó un error de conectividad donde el cliente no lograba resolver el nombre de dominio mario.local.

Para diagnosticar el fallo, se ejecutaron las siguientes pruebas:

Prueba ICMP (Ping): El cliente respondía correctamente a la dirección IP del servidor (192.168.10.10).

Resolución mediante archivo local: Se editaron los registros del archivo hosts en el cliente de manera temporal, mapeando la IP del servidor tanto a mario.local como al nombre NetBIOS corto (mario). Esto habilitó la comunicación ICMP por nombre, pero el asistente de Windows persistía en el error al realizar las consultas RPC/LDAP del proceso de unión.

Resolución del problema raíz
Al realizar una auditoría visual minuciosa de la configuración de red en el cliente Windows 11, se detectó un error tipográfico (typo) en los parámetros estáticos introducidos en la tarjeta de red. El campo Servidor DNS preferido se había configurado erróneamente como 192.160.10.10 en lugar de apuntar a la IP real del servidor DNS, que es 192.168.10.10.

Una vez corregido el direccionamiento DNS en el adaptador de red de Windows 11, el tráfico de autenticación se canalizó de manera correcta a través del segmento LAN hacia el controlador de dominio. La solicitud de credenciales administrativas se ejecutó de inmediato, completando la unión al dominio mario.local con éxito.

4. Verificación y Administración del Entorno
Una vez reiniciado el cliente, se realizaron las siguientes tareas de validación en la consola del servidor:

Verificación del objeto de equipo: En la consola Usuarios y equipos de Active Directory, dentro del contenedor nativo Computers, se verificó la correcta incorporación de la cuenta de equipo de CLMARIO01.

Estructura organizativa corporativa: Con el fin de estructurar el directorio de acuerdo con las buenas prácticas, se creó una nueva Unidad Organizativa (OU) denominada Empleados.

Aprovisionamiento de identidades: Dentro de la OU Empleados, se dio de alta una nueva cuenta de usuario asignando el nombre Mario mr..

Finalmente, se validó el entorno iniciando sesión de forma local en el puesto de trabajo de Windows 11 utilizando las credenciales del nuevo usuario del dominio recién aprovisionado, confirmando la correcta comunicación del protocolo Kerberos y la carga del perfil móvil corporativo en la red virtual.
