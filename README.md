# **DEVOPS - NODEPOP**

### Servidor que aloja 2 versiones de aplicación de servicio de venta de artículos de segunda mano llamado ***NODEPOP***.


## **Parte 1** - Servicio mediante **DNS**

#### **Se puede acceder a través de este [enlace dns](ec2-50-19-115-178.compute-1.amazonaws.com)**

La funcionalidad de esta parte es principalmente un *API* para el mantenimiento y creación de anuncios de compra o venta de artículos. Además, permite buscar a través de filtros por varios criterios. Como complemento, se pueden visualizar los anuncios a través del navegador, función presentada como *web*.

#### **Consumo de servicios**
- Para el consumo como *api*, se recomienda el uso de alguna aplicación para realizar peticiones *HTTP REST*, (ejemplo: *Postman*).
- Para el consumo como *web*, se puede emplear cualquier navegador. 

#### **1. Anuncio**
Cada **anuncio** está compuesto por los siguientes atributos:
- **name**: nombre del artículo. Tipo *String*.
- **sale**: indica si se vende o se busca. Tipo *Boolean*.
- **price**: precio de venta o precio máximo para búsqueda, en euros. Tipo *Number*.
- **image**: nombre del fichero de la imagen del artículo. Sólo admite una imagen. Tipo *String*.
- **tags**: etiquetas asociadas. Admite más de una. Tipo *array de String*.
*El usuario puede crear todos los tags que mejor se asocien al producto en cuestión.*  

#### **2. Guía de uso**
Las operaciones permitidas son las siguientes:
1. **Listar los anuncios** con posibilidad de paginación y filtros para buscar aquellos anuncios deseados. 
    Esta operación está disponible desde en la versión *web* como en *api*. Para la versión de *api*, se requiere estar autenticado.
2. **Listar los tags** de todos los anuncios existentes. Esta operación está disponible desde *web* y desde *api*.
3. **Crear un anuncio**. Operación disponible desde *api*. *Uso de **Postman**.*

##### **2.1. **NODEPOP** en **web****
Desde un navegador, las rutas de la aplicación, para las diferentes operaciones permitidas son:
1. **Listado de anuncios**: *ec2-50-19-115-178.compute-1.amazonaws.com*
    Los filtros se añaden conforme al apartado *2.3.* a partir de la ruta *ec2-50-19-115-178.compute-1.amazonaws.com?*.
2. **Listado de tags**: *ec2-50-19-115-178.compute-1.amazonaws.com/tags*

##### **2.2. **NODEPOP** como **API****
Desde la aplicación de peticiones (*Postman*), las rutas para las diferentes operaciones permitidas son:
0. **Autenticación** de usuario: tipo *GET* a *ec2-50-19-115-178.compute-1.amazonaws.com/api/authenticate*.
    Los parámetros a incluir en el *body* son:
    - **email**: cadena de texto con el email del usuario.
    - **password**: cadena de texto con la contraseña del usuario. 
    *Se dispone de un usuario por defecto cuyos datos son:*
    *- email: admin@example.com*
    *- password: 1234*
    Esta operación se requiere realizar previamente para poder obtener el *listado de anuncios mediante API*, la cual devuelve un *token* que será añadido en la cabecera con la propiedad *Authorization* al realizar la petición mencionada.
1. **Listado de anuncios**: tipo *GET* a *ec2-50-19-115-178.compute-1.amazonaws.com/api/advertisements*
    Los filtros se añaden conforme al apartado *2.3.* a partir de la ruta *[ec2-50-19-115-178.compute-1.amazonaws.com/api/advertisements?](ec2-50-19-115-178.compute-1.amazonaws.com/api/advertisements?)*.
    Para esta petición se requiere estar autenticado previamente, según se describe en el punto *2.2.0*
2. **Listado de tags**: tipo *GET* a *ec2-50-19-115-178.compute-1.amazonaws.com/api/tags*
3. **Creación de anuncio**: tipo *POST* a *ec2-50-19-115-178.compute-1.amazonaws.com/api/advertisements*, añadiendo todos 
    los atributos o valores del anuncio en el *body*. (La elección del formato del *body* debe ser *form-data*).
    Para la creación de un anuncio, los datos a introducir son: 
    - **name**: cadena de texto con el nombre del artículo.
    - **sale**: valor *true* para el caso de ser un artículo para ser vendido y *false* para el caso de ser un artículo que se busca.
    - **price**: cantidad expresada en números. Para el caso de venta, cantidad por la que se ofrece para vender y en el caso de ser buscado, sería la cantidad numérica máxima a pagar por el artículo.
    - **image**: tipo *file* de la imagen asociada al producto a añadir.*
    - **tags**: cadena de texto que representa una etiqueta relacionada como palabra clave para el artículo. Para añadir varios *tags* asociados, se añadiría una propiedad *tags* por cada tag deseado a asociar.

* Adicionalmente a la imagen asociada al producto, se genera una imagen de tipo thumbnail asociada a la misma.

##### **2.3. Filtros para **NODEPOP** en *web* y en *api***
Los filtros posibles son los que a continuación se describen, los cuales se añaden a partir de la ruta indicada para cada caso:
- **Tag**: se añade **tags=tag**, donde *tag* corresponde con el tag de interés.
    Adicionalmente, se pueden buscar por varios *tags*, para ello, por ejemplo, si se deseara buscar por 3 tags, sería **tags=tag1 tag2 tag3**, de forma que se pondrían los *tags* de interés separados por espacios.    
- **Precio**: se puede buscar por rango de precios o por un único valor, para cada caso sería:
    -- Para indicar un precio mínimo: **price=precioMínimo-**, donde *precioMínimo* es la cantidad mínima deseada, en euros.
    -- Para indicar un precio máximo: **price=-precioMáximo**, donde *precioMáximo* es la cantidad máxima deseada, en euros.
    -- Para indicar un rango de precios con un mínimo y un máximo: **price=*precioMínimo*-*precioMáximo***, donde *precioMínimo* corresponde con la cantidad mínima deseada y *precioMáximo* corresponde con la cantidad máxima deseada, ambas expresadas en números.
    -- Para indicar un único precio concreto: **price=*precio***, donde *precio* corresponde con la cantidad deseada, expresada en números.
- **Nombre**: permite buscar por tanto por el nombre concreto como el inicio del mismo, para ello se emplea **name=*nombre***, donde *nombre* corresponde con el nombre del artículo de interés.
- **Venta o Búsqueda**: para el caso en el que se desee el listado de anuncios disponibles para venta, sería **sale=*true*** y para el caso de obtener el listado de artículos disponibles para búsqueda, sería **sale=*false***.
- **Paginación**: para hacer uso de la paginación de un listado, se emplearían conjuntamente **limit=*númeroDeAnunciosMostrados*** y *skip=númeroDeAnunciosYaMostrados*, donde *númeroDeAnunciosMostrados* corresponden con el número de anuncios a mostrar en el listado deseado y *númeroDeAnunciosYaMostrados*, con el número de anuncios ya mostrados anteriormente, es decir, aquellos que se salta del listado obtenido.

Adicionalmente, se puede obtener un listado **ordenado** por alguno de las propiedades, en cuyo caso se usaría **sort=*propiedad***, donde *propiedad* puede ser alguno de los siguientes valores: name, price. Para indicar un orden inverso, sería **sort=-*propiedad***.

Para combinar los filtros y poder indicar varios seguidos, se emplea el carácter *&* entre cada uno de ellos.

#### **3. Ejemplo de uso**
Un **ejemplo** de obtención de listados de anuncios con varios filtros, para cada caso, podría ser:
- Para *web*: **ec2-50-19-115-178.compute-1.amazonaws.com?tags=mobile&sale=true&name=tab&price=50-&limit=2&skip=0&sort=price**
- Para *api*: **ec2-50-19-115-178.compute-1.amazonaws.com/api/advertisements?tags=mobile&sale=true&name=tab&price=50-&limit=2&skip=0&sort=price**

El listado obtenido estaría paginado y para el caso de ejemplo, correspondería con la primera página, en la que se mostraría un total de 2 anuncios (si los hubiera), con el o los tags deseados (separados por espacios), que se venden, con un nombre que empiece por *tab*, con un precio mínimo de 50 euros y ordenados por precio.

#### **4. Repositorio**
El servicio desplegado es el que se encuentra disponible en [este enlace](github.com/ainacarin/nodepop-pro).


## **Parte 2** - Servicio mediante **IP**

#### **Se puede acceder a través de este [enlace ip](50.19.115.178)**

La funcionalidad de esta parte es principalmente una *web* a la que se accede a través de una página de *login* y que después de acceder, ofrece un listado de anuncios y además, permite filtrar anuncios conforme a varios criterios, ver el detalle de cada anuncio, crear un nuevo anuncio, borrar un anuncio y para finalizar la sesión, un *logout*.
*Para acceder, se ofrece un usuario por defecto cuyos valores son:*
*- email: admin@example.com*
*- password: 1234* 

#### **1. Repositorios**
El servicio desplegado está compuesto de dos partes:
- **Parte *Frontend***: disponible en [este enlace](github.com/davidjj76/nodepop-react/tree/redux).
- **Parte *Backend***: disponible en [este enlace](github.com/davidjj76/nodepop-api).

*La web se presenta con un objetivo funcional, sin diseño asociado.*
