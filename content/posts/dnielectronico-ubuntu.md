+++
title = "DNI Electrónico en Ubuntu 24.04"
author = ["Javier de la Puente"]
date = 2024-06-02
tags = ["ubuntu"]
draft = false
+++

El Dni electrónico esta diseñado para funcionar tanto en Windows, GNU/Linux y como en MacOS.

Probablemente la distribución de Linux más famosa es Ubuntu. La última versión de esta distribución
es la 24.04. Esta distribución es amigable y funciona perfectamente, además de tener una fuerte apuesta
por la seguridad. Por desgracia, esta apuesta por la seguridad en su sistema de paquetes snap a veces
genera alguna dificultad. Este es el caso del DNI electrónico. Este post explica como
poder utilizar tanto el DNI electrónico con el navegador [Google Chrome](https://www.google.com/intl/es_es/chrome/) y con [AutoFirma](https://firmaelectronica.gob.es/Home/Descargas) utilizando
tanto un lector electrónico como [DNIeRemote con Android](https://www.dnielectronico.es/PortalDNIe/PRF1_Cons02.action?pag=REF_1015&id_menu=65).


## DNIe. Aplicaciones oficiales. {#dnie-dot-aplicaciones-oficiales-dot}

El primer paso por tanto es instalar todas las utilidades. Estas son:

-   Tarjetas criptográficas: <https://www.dnielectronico.es/PortalDNIe/> -&gt; Área de descargas -&gt; GNU/Linux y MacOS -&gt;

Software para las distribuciones Linux.

-   Descargar e instalar la última versión para Ubuntu e instalarlo con  `sudo dpkg --install <fichero.deb>`.
-   DNIeRemote: <https://www.dnielectronico.es/PortalDNIe/>]] -&gt; Área de descargas -&gt; DNIeRemote -&gt;

Instalable para DNIe Remote Setup para linux 64 bits.

-   Descargar el fichero zip. Descomprimirlo e instalarlo con `sudo dpkg --install <fichero.deb>`.

Después de instalar cada paquete puede ser necesario ejecuar `sudo apt install --fix-broken`.

Los siguientes paquetes permiten gestionar los servicios de seguridad y tarjetas criptográficas:

```text
sudo apt install -y pcscd pcsc-tools
sudo apt install libnss3-tools
```


## Instalación de Google Chrome. {#instalación-de-google-chrome-dot}

Para Google Chrome, crearemos una librería de NSS (Network Security Services) con los
siguientes comanddos:

```text
cd "${HOME}"
mkdir -p ~/.pki/nssdb
certutil -d ~/.pki/nssdb -N --empty-password
certutil -d "sql:${HOME}/.pki/nssdb" -L

chmod 700 ~/.pki/nssdb
modutil -add "DNIe" -dbdir "sql:${HOME}/.pki/nssdb" -libfile /usr/lib/libpkcs11-dnie.so -force
modutil -add "DNIeRemote" -dbdir "sql:${HOME}/.pki/nssdb" -libfile /usr/local/lib/libdnieremotepkcs11.so
modutil -list -dbdir "sql:${HOME}/.pki/nssdb"
```

A partir de aquí, solo es necesario instalar Chrome. El fichero .deb de 64 bits se
puede descargar en <https://www.google.com/intl/es_es/chrome/>, e instalar con
un comando similar a `dpkg --install google-chrome-stable_current_amd64.deb`.

En este punto, la aplicación de Google Chrome se podrá utilizar con el DNI electrónico,
tanto como tarjeta criptográfica como con DNIeRemote con la aplicación de Android.


## Autofirma. {#autofirma-dot}

La segunda aplicación necesaria para utilizar el DNI electrónico es [Autofirma](https://firmaelectronica.gob.es/Home/Descargas.html). Esta utilidad
es usada por la administración electrónica para multitud de procesos.

El primer paso para su instalación es instalar una máquina virtual de Java. Esto se puede
realizar con el comando `sudo apt install default-jre`.

Autofirma se puede descargar desde la página <https://firmaelectronica.gob.es/Home/Descargas.html>.
Una vez descargada la versión de Debian para Linux y descomprimido el fichero zip, se
instalará con el comando =sudo dpkg --install &lt;fichero aufirma.deb&gt;.

Autofirma utiliza la librería de NSS de Firefox. Firefox por defecto viene instalado
en Ubuntu como aplicación Snap. Firefox debe ser abierto al menos una vez y después
cerrado antes de utilizar los comandos para poder utilizar DNIeRemote.

Con Snap, el directorio local de configuración de Firefox es `~/snap/firefox/common/.mozilla/firefox`.
Dentro de este directorío está el fichero `profiles.ini`, que contiene la ruta a la
configuración por defecto de Firefox.

Este fichero contendrá la ruta en el atributo `Path` en la sección con el atributo `Name=Default`.
Por lo tanto, el directorio será algo similar a `~/snap/firefox/common/.mozilla/firefox/aaaaaaaa.default`.

Una vez idenficado este directorio, será necesario ejecutar los siguientes comandos (sustituyendo
el directorio por defecto de Firefox por el correcto):

```text
certutil -d   "sql:${HOME}/snap/firefox/common/.mozilla/firefox/c81c22m8.default"  -L
modutil -list -dbdir "sql:${HOME}/snap/firefox/common/.mozilla/firefox/aaaaaaaa.default"
modutil -add "DNIeRemote" -dbdir "sql:${HOME}/snap/firefox/common/.mozilla/firefox/aaaaaaaa.default"  -libfile /usr/local/lib/libdnieremotepkcs11.so
/usr/local/lib/libdnieremotepkcs11.so
```

En este momento Aufofirma debería funcionar tanto con un lector de tarjetas como con la aplicación
DNIeRemote.


## Otras Referencias Similares {#otras-referencias-similares}

<https://www.vidaxp.com/tecnologia/como-configurar-dnie-ubuntu-chrome-firefox/>
<https://bandaancha.eu/foros/dnie-debian-ubuntu-mint-chromium-chrome-1747958>
