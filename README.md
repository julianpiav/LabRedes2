# Lab #1 -  Redes IPv6 en coexistencia con Redes IPv4

## Presentado por: Julián (Alvarado + Durán + Pinilla).

## CONTEXTO DEL PROBLEMA
Un cliente de tipo empresarial le solicita un servicio de instalación de redes a la empresa Julianes SAS. Este cliente tiene como necesidad montar una red en Bogotá y otra en Madrid con direccionamiento IPV6, las cuales sean capaces de comunicarse a traves de INTERNET con direccionamiento IPV4 y acceder a los servicios de unos servidores. La red en Bogotá requiere ser segmentada entre clientes, empleados, accesorios como impresoras junto con servidores y telefonos IP. Para la red de Madrid, al igual que en la de Bogotá, también debe ser segmentada entre clientes, empleados, accesorios como impresoras junto con servidores y telefonos IP. 
Por otro lado, además de montar dicha topología, el cliente también requiere que se haga una administración de los servicios que se pueden acceder desde las diferentes redes. En la red Intranet Bog todos los usuarios deben poder acceder a los servicios web a través del protocolo HTTPS (puerto 443) salvo los usuarios de la red "Invitados" que accederan por HTTP(puerto 80). Para la red de Madrid, los usuarios de la red "invitados" deben estar restringidos de acceder a cualquier servicio web y los que hagan parte de la empresa conectarse a través de HTTPS(puerto 443). Ademas se debera implementar un servicio de migracion capaz de comunicar las LANs de Bogota, Madrid y el DMZ configurados con direccionamiento IPV6. El cliente solicita que el montaje sea con la topología evaluada de acuerdo con su capital, siendo la siguiente:
![image](https://github.com/julianpiav/LabRedes2/assets/88839459/01f01176-a013-4e5a-b1c2-a6bf69c258d1)


## MONTAJE

### PROCEDIMIENTO y RESPUESTA DE PREGUNTAS.
1. Primero que todo, se monta la topologia en cisco, presentada por el cliente.


2. Para las intranet de la empresa, nos dan disponibilidad el espacio de direccionamiento, Red “Intranet BOG”: 2001:1200:A3::/48 y Red “Intranet MAD”: 2001:1200:B3::/48, y nos exigen realizar el subneteo para las siguientes 5 VLANS; Internos, Invitados, Servidores e Impresoras, VOIP y La nativa.
Con el método aprendido en clase y un análisis que pueda satisfacer a nuestros clientes, llegamos a la conclusión que las VLANs tendrán una asignación de direccionamiento dependiendo a su número identificador de VLAN, además de asignar sus puertos correspondientes a cada VLAN.

![Imagen de WhatsApp 2023-09-30 a las 10 43 44_f44f414a](https://github.com/julianpiav/LabRedes2/assets/88839459/cda7e9ce-1a41-4b15-957d-29dce2a22fb5)



4. Se configuraron los switches y Routers para la cominucacion de dispositivos entre VLANs, al usar las notas de clase y la documentacion de Cisco [14].
Esta configuracion consistia principalmente en las claves para los modos privilegiados, el nombre y el mensaje del dispositivo y la creacion de las VLANs con su nombre
A cada una de estas VLANs se les asigna unos determinados puertos del Switch y su modo, siendo de acceso, o para las VLAN nativa, truncado.
Para el router, se crea una interfaz que se asocia en al puerto conectado al switch de nuestra LAN, y unas subinterfacez con la encapsulation dot1q, para su comunicacion y distribucion entre VLANs en interfaces entre los Switches y los routers, que se asocian a cada VLAN de nuestras Redes LAN.
Ademas Se activa el enrutamiento con el protocolo EIGRP ##(su "identificador" de conexion), que debe ser el mismo para todos los routers y se declara las redes que esten conectadas a cada uno de los Routers con una IP especifica.

#### ¿Qué método(s) de asignación se debe(n) configurar?¿En qué terminales se deben configurar los servicios requeridos?
5.  Se requiere tanto asignación dinámica como estática. En la red en la cuál están los servidores DNS y HTTP es recomendable usar una asignación estática, ya que dentro de la topología y el problema planteado no se contempla la necesidad de  como si ocurre tanto en las redes de Bogotá y Madrid, en las cuales existe hasta una subred para los invitados, lo cuál indica que lo más óptimo esa hacer uso de la asignación dinámica a través de DHCP. Por esta razon implementamos tambien el servidor DHCP en la Intranet de Madrid, realizando una leve modificacion a la topoligía.

En el servidor WEB se alojo la Pagina de los Julianes con las especificaciones del cliente, siendo: www.jdagjcdvjapa.com, esta pagina y su dominio es gestionada por el Servidor DNS.

Los usuarios de la Intranet Bogotaáefectivamente Ingresan a la pagina WEB por el protocolo HTTPs(puerto 433) y no por el protocolo HTTP(puerto 80) (PC1) :

Y verificamos por paquetes que se hace una solicitud HTTPS por el puerto 443:


Mientras que los invitados ingresan viceversa:


Y los Invitados de la intranet Madrid no pueden entrar por ninguno de los 2 protocolos

Estas especificaciones fueron solicitadas por el cliente


#### Finalmente, los usuarios de la empresa en la Intranet Madrid sólo deben acceder al servidor Web por el puerto 443. ¿Qué servicio IPv4 se debe configurar?

6.Se configura el servicio de ACL(Access Control List) el cual nos permite permitir o denegar el paso de paquetes desde una red hacia otra, o por ip's específicas de dispositivos.

#### ¿Dónde se deben ubicar los ACLs?
7. Los ACLs se ubicaron en los routers de ISP_Bogota y ISP_Madrid, ya que estos routers son los mas cercanos a los posibles destinos de los mensajes Externos y tambien porque se maneja este protocolo a nivel de IPv4, Siendo una configuracion ACL estandar.

#### Enlace “Trunks”, IEEE 802.1q. ¿En qué interfaces se deben configurar OSPF o EIGRP (no RIP) y/o rutas estáticas y redistribución entre protocolos? Analice los requerimientos de red.
8. Se configuró el protocólo EIGRP en  todas las interfaces de los routers y multilayer switch, las rutas estaticas solo las definimos en Los servidores WEB y DNS.

#### ¿Qué servicio(s) de migración se debe(n) implementar para permitir el acceso al servidor Web instalado en el DMZ configurado completamente en IPv6?

### RESULTADOS Y ANÁLISIS
#### Analisis
#### Compare y explique las tablas de enrutamiento IPv4 e IPv6 de los routers ISP_BOG e ISP_ESP. ¿Qué información relevante arrojan? ¿Qué criterio utilizan para escoger la mejor ruta?


### Marco Teorico

* Red LAN: "Se conoce como red LAN (siglas del inglés: Local Área Network, que traduce Red de Área Local) a una red informática cuyo alcance se limita a un espacio físico reducido, como una casa, un departamento o a lo sumo un edificio" [1].
* VLAN: "Las VLAN o también conocidas como «Virtual LAN» nos permite crear redes lógicamente independientes dentro de la misma red física, haciendo uso de switches gestionables que soportan VLANs para segmentar adecuadamente la red" [2]. Básicamente permiten segmentar una misma red apartir de un Switch, y tener distintas subredes.
* IPv4: "(Internet Protocol versión 4) es el formato de dirección estándar que permite que todas las máquinas en Internet se comuniquen entre sí. IPv4 se escribe como una cadena de dígitos de 32 bits y una dirección IPv4 se compone de cuatro números entre 0 y 255, separados por puntos" [3].
* Subneteo: "Hace referencia a la subdivisión de una red en varias subredes. El subneteo permite a los administradores de red, por ejemplo, dividir una red empresarial en varias subredes sin hacerlo público en Internet. Esto se traduce en que el router que establece la conexión entre la red e Internet se especifica como dirección única, aunque puede que haya varios hosts ocultos. Así, el número de hosts que están a disposición del administrador aumenta considerablemente" [4].
* HTTP: "Es el nombre de un protocolo el cual nos permite realizar una petición de datos y recursos, como pueden ser documentos HTML" [5].
* DNS: "Corresponde a las siglas en inglés de "Domain Name System", es decir, "Sistema de nombres de dominio". Este sistema es básicamente la agenda telefónica de la Web que organiza e identifica dominios. estas siendo protocoles de la capa de aplicación en el proceso de encapsulamiento y des encapsulamiento de datos" [6].
* DHCP: "(Protocolo de configuración dinámica de host) o también conocido como «Dynamic Host Configuration Protocol« , asignando las direcciones lógicas de cada dispositivo, al ser la capa de red en el modelo TCP/IP" [7].
* WAN: "Una red de área amplia (WAN) es la tecnología que conecta entre sí a las oficinas, los centros de datos, las aplicaciones en la nube y el almacenamiento en la nube" [8].
* ACL: "Una lista de control de acceso (ACL) de red permite o deniega el tráfico entrante o saliente específico en el nivel de subred. Puede usar la ACL de red predeterminada para su VPC o puede crear una ACL de red personalizada para su VPC con reglas similares a las reglas de sus grupos de seguridad para agregar una capa de seguridad adicional a su VPC" [9].
* EIGRP: "EIGRP (Enhanced Interior Gateway Routing Protocol) es un protoco- lo de enrutamiento propietario de Cisco, concebido para que funcione en aquellas redes implementadas solamente con equipos marca Cisco" [10].


Dispositivos Utilizados:

- PC-PT.
- Server-PT.
- Switch (2960).
- Router (2811).
- Printer
- IP Phone
- Multilayer Switch


## AGENDA DEL PROYECTO

### Jueves 14 de Septiembre:
Análisis de la topología a montar, lectura y entendimiento del documento, repartición básica de las tareas.
Alvarado: Servidores DNS y Web.
Durán: Configuración de routers.
Pinilla: Montaje básico de la INTRANET BOG e INTRANET MAD con VLANs Multilayer-switchy .

### Viernes 22 de Agosto:
Reporte de avances y corrección de errores de conexión. Socialización de posibles soluciones.
Alvarado: Configuración del ACL y protocolos OSPF o EIGRP
Durán: Migracion a través de Tunneling 
Pinilla: Configuración del enrutamiento de routers

### Domingo 30 de Septiembre:
Corrección final de errores y grabación del vídeo.

### REFERENCIAS
* [1] Fragmento tomado de: "https://concepto.de/red-lan/#ixzz7xxuZ6gL7"
* [2] Fragmento tomado de: "https://www.redeszone.net/tutoriales/redes-cable/vlan-tipos-configuracion/"
* [3] Fragmento tomado de: "https://www.avg.com/es/signal/ipv4-vs-ipv6#:~:text=IPv4%20(Internet%20Protocol%20versi%C3%B3n%204,y%20255%2C%20separados%20por%20puntos."
* [4] Fragmento tomado de: "https://www.ionos.es/digitalguide/servidores/know-how/subnetting-como-funcionan-las-subredes/"
* [5] Fragmento tomado de: "https://developer.mozilla.org/es/docs/Web/HTTP/Overview"
* [6] Fragmento tomado de: "https://developer.mozilla.org/es/docs/Glossary/TCP"
* [7] Fragmento tomado de: "https://www.redeszone.net/tutoriales/internet/que-es-protocolo-dhcp/"
* [8] Fragmento tomado de: "https://aws.amazon.com/es/what-is/wan/#:~:text=Una%20red%20de%20%C3%A1rea%20amplia%20(WAN)%20es%20la%20tecnolog%C3%ADa%20que,el%20almacenamiento%20en%20la%20nube."
* [9] Fragmento tomado de: "https://docs.aws.amazon.com/es_es/vpc/latest/userguide/vpc-network-acls.html"
* [10] Fragmento tomado de: "https://libros.univalle.edu.co/index.php/programaeditorial/catalog/download/53/4/185?inline=1#:~:text=DE%20ENCAMINAMIENTO%20IP-,EIGRP%20(Enhanced%20Interior%20Gateway%20Routing%20Protocol)%20es%20un%20protoco%2D,solamente%20con%20equipos%20marca%20Cisco."
* [11] " Configuracion de Inter-VLAN Routing (Multilayer Switch) - Parte 2 " - https://www.youtube.com/watch?v=VOX3gNV0Q58&ab_channel=JorgeArmijo
* [12] "Configuracion Vlan con routers + eigrp" - https://www.youtube.com/watch?v=MY6wPJD_Y7E
* [13] "Configuring IP Addressing and IP Services Features. Cisco Documentation"
* [14] "Default static route" - https://study-ccna.com/default-static-route/
