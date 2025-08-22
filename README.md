# Documentación del Proyecto

## Descripción



---

## Diseño de la Arquitectura

La solución se construyó sobre **Kubernetes** para orquestrar y administrar todos los servicios describen en este proyecto.

Con el fin de organizar mejor la infraestructura y separar responsabilidades, se definieron dos **Namespaces** dentro del clúster:  
- `publico`
- `privado`


---

### Recursos en el Namespace Público
Dentro de este namespace se ubican los componentes que deben ofrecer acceso externo, ya que actúan como intermediarios entre los clientes y los servicios internos.

#### Router

El **Router** se implementa a partir de una imagen base de **CentOS** y se configura utilizando **nftables**.  
Su función principal es redirigir el tráfico entrante hacia los distintos servicios internos. En este caso, el tráfico TCP y UDP en los puertos definidos se dirige a **FreeSWITCH**, mientras que las peticiones al puerto **TCP/8080** son redirigidas a **NGINX 1**, las del puerto **TCP/8081** hacia **NGINX 2**, y finalmente, el puerto **TCP/80** se encamina al **Ingress Controller**.


#### Ingress Controller

El **Ingress Controller** se implementa utilizando **Apache Server** y tiene como responsabilidad redirigir las peticiones HTTP en función de la URL solicitada.  
En esta configuración, cuando un cliente accede a `http://127.0.0.1:80/Nginx1`, la solicitud se dirige hacia **NGINX 1**, mientras que si accede a `http://127.0.0.1:80/Nginx2`, la petición es redirigida hacia **NGINX 2**.


---

### Recursos en el Namespace Privado
En este namespace se alojan los servicios internos que no deben ser accesibles directamente desde el exterior, garantizando así aislamiento y seguridad dentro del clúster.


#### NGINX 1
NGINX 1 es un servidor web simple que, al ser accedido, muestra el mensaje `"Hola NGINX1"`.  
Para su implementación en Kubernetes se realizaron los siguientes pasos:  
- Se creó un **Deployment** con una réplica del pod que corre NGINX 1, utilizando la imagen **nginx:alpine**.  
- Se definió un **Service de tipo ClusterIP** para permitir la comunicación interna dentro del clúster.  
- Se creó un **ConfigMap** para establecer la configuración del HTML que se servirá.  
- Este ConfigMap se montó como un **volumen** dentro del Deployment, de modo que el contenedor pueda leer y servir el contenido correcto del sitio web.


#### NGINX 2
NGINX 2 tiene la misma estructura y propósito que NGINX 1, mostrando el mensaje `"Hola NGINX2"`.  
Su implementación también incluyó:  
- **Deployment** con una réplica, utilizando la imagen **nginx:alpine**.  
- **Service de tipo ClusterIP**.  
- **ConfigMap** para la configuración del HTML.  
- Montaje del ConfigMap como **volumen** en el Deployment, garantizando que el contenedor pueda servir la página correctamente.

#### FreeSWITCH
FreeSWITCH es un servicio de telefonía IP que utiliza el protocolo **SIP (Session Initiation Protocol)** sobre los transportes **UDP** y **TCP**. Este servicio permite crear extensiones telefónicas, realizar llamadas entre ellas y simular un entorno de central telefónica. Es fundamental configurar correctamente los puertos requeridos para garantizar su funcionamiento dentro del clúster.

---

## ⚙️ Herramientas

Para implementar los servicios utilizamos **Helm**, que permite definir la infraestructura como código.  
Esto facilita el despliegue en kubernetes y ayuda a mantener la configuración organizada y reutilizable.

---

## 📞 FreeSWITCH

**FreeSWITCH** es una plataforma para telefonía IP.  
- Usa el protocolo **SIP** (Session Initiation Protocol)  
- Trabaja sobre **UDP** y **TCP**  

### Lo que haremos con FreeSWITCH
1. Crear extensiones SIP.  
2. Configurar un cliente softphone (hay opciones para iOS, Android y escritorio).  
3. Realizar llamadas de prueba entre extensiones.  

---

## 🔗 Recursos de Apoyo
- [Documentación oficial de FreeSWITCH](https://developer.signalwire.com/freeswitch/FreeSWITCH-Explained/Introduction/)  
- [Helm - sitio oficial](https://helm.sh/docs/)  
- [Kubernetes - documentación oficial](https://kubernetes.io/docs/)  
