# Ejecutando juegos de Windows en Mac

![](/Portada%20Game%20Porting%20toolkit.png)

Aquí puedes ver el video relacionado a este tutorial:
https://youtu.be/QGPmwzI18T8

Revisa las sesiones del WWDC23 de Apple sobre este tema:
https://developer.apple.com/videos/play/wwdc2023/10123/

## Requisitos

- MacOS Ventura (última versión 13.4) idealmente la macOS 14 Developer Beta 1 (Sonoma)
- Mac con procesador Apple Silicon (M1 en adelante), esta herramienta no funciona en Macs con procesadores Intel

## Command Line Tools for Xcode 15 Beta

Puedes descargar e instalar esta herramienta desde: https://developer.apple.com/download/all/?q=xcode%20command%20line%20tools%2015

Después de instalar verifica que se haya instalado correctamente y ejecutando desde la ruta default abriendo una Terminal y ejecutando:
~~~
xcode-select -p
~~~

Debe aparecer algo como esto:
~~~
/Library/Developer/CommandLineTools
~~~

Después cambia la ruta de ejecución a la de la beta de Comand Line Tools:
~~~
xcode-select -s /Applications/Xcode-beta.app/Contents/Developer
~~~

## Instala Rosetta 2

Rosetta 2 te permite ejecutar aplicaciones basadas en Intel en Apple Silicon. Teniendo en cuenta que la mayoría de los juegos para Windows están diseñados para Intel (arquitectura x86-64), necesitarás instalar Rosetta 2.

En tu terminal ejecuta:
~~~
softwareupdate --install-rosetta
~~~

## Instala Game Porting Toolkit Beta

Descárgalo desde https://developer.apple.com/download/all y ejecuta el emg. Montará un volumen en "/Volumes/Game Porting Toolkit-1.0".

Manten esta herramienta abierta todo el proceso

## Cambia la terminal de arquitectura

Ejecuta el siguiente comando para cambiar la ejecución de laterminal a Rosetta 2 bajo arquitectura Intel:
~~~
arch -x86_64 zsh
~~~


## Instala homebrew

A pesar de que ya lo tengas instalado, probablemente sea para arquitectura ARM. Es momento de instalarlo para la arquitectura x86 de Intel

Homebrew es un administrador de paquetes no oficial que muchos desarrolladores utilizamos para instalar diversas herramientas.

Para instalarlo, puedes consultar el manual de instalación en https://brew.sh. Necesitarás ejecutar el siguiente comando en la terminal:

~~~
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
~~~

Si ya tienes Homebrew instalado para Apple Silicon, esa versión se encuentra en **/opt/homebrew**. La versión para Intel se instalará en **/usr/local**. Por lo tanto, no entrarán en conflicto entre sí.

Sin embargo, si deseas indicar a esta sesión de terminal que utilice **/usr/local** en lugar de **/opt/homebrew**, debes modificar la variable de entorno PATH para esta sesión de terminal. Puedes hacerlo ejecutando el siguiente comando:
~~~
export PATH="/usr/local/bin:${PATH}"
~~~

Revisa que homebrew se haya instalado bien:
~~~
brew doctor
~~~

Y verifica si hay algún problema de permisos ejecutando lo siguiente:
~~~
sudo chown -R $(whoami) /usr/local/share/zsh /usr/local/share/zsh/site-functions
~~~

Instala los paquetes oficiales proporcionados por Apple para Homebrew.

~~~
brew tap apple/apple http://github.com/apple/homebrew-apple
~~~

Después instala lo siguiente:
~~~
brew -v install apple/apple/game-porting-toolkit
~~~

Este comando te puede demorar hasta 1 hora. En mi caso tardó 50 minutos (Macbook Pro 14" 2021 M1 Pro)

## Preparate para los juegos

Crea una carpeta que contendrá los juegos. En mi caso la llamé *Games*
~~~
mkdir ~/Games
~~~

Después de eso, decidí mantener todos los juegos de Battle.net en una carpeta bajo **~/Juegos/battle-net.**

Configura eso para la sesión actual de la terminal (comando para indicarle a Wine dónde se encuentran los juegos):

~~~
export WINEPREFIX=~/Games/battle-net
~~~

Prepara la carpeta para los juegos con slguiente comando:

~~~
`brew --prefix game-porting-toolkit`/bin/wine64 winecfg
~~~

Debería aparecer una ventana de "Configuración de Wine" en tu pantalla. Cambia la versión de Windows a Windows 10. Selecciona Aplicar y luego Aceptar para salir de winecfg.

Instala el directorio de la biblioteca Game Porting Toolkit en el directorio de bibliotecas de Wine.
~~~
ditto /Volumes/Game\ Porting\ Toolkit-1.0/lib/ `brew --prefix game-porting-toolkit`/lib/
~~~

Luego, copia todos los ejecutables requeridos a /usr/local/bin, de esta manera podrás acceder a ellos más adelante sin necesidad de adjuntar el volumen de Game Porting Toolkit.

~~~
cp /Volumes/Game\ Porting\ Toolkit-1.0/gameportingtoolkit* /usr/local/bin
~~~

Actualiza la versión de Windows en el registro de Wine para que coincida con la compilación esperada por la aplicación Battle.net.

~~~
`brew --prefix game-porting-toolkit`/bin/wine64 reg add 'HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion' /v CurrentBuild /t REG_SZ /d 19042 /f
`brew --prefix game-porting-toolkit`/bin/wine64 reg add 'HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion' /v CurrentBuildNumber /t REG_SZ /d 19042 /f
`brew --prefix game-porting-toolkit`/bin/wineserver -k
~~~

## Instala la app de Battle net

Descarga la aplicación Battle.net para Windows desde https://www.blizzard.com/download/.

Suponiendo que ya lo has descargado en tu carpeta ~/Descargas, verifica si Wine también lo tiene:

~~~
ls ~/Games/battle-net/drive_c/users/crossover/Downloads/
~~~

ejecuta lo siguiente para instalar la app de Battle net de Windows

~~~
gameportingtoolkit ~/Games/battle-net ~/Games/battle-net/drive_c/users/crossover/Downloads/Battle.net-Setup.exe
~~~

Deberías ver la ventana de la aplicación Battle.net en tu pantalla. Inicia sesión con tu cuenta de Blizzard e instala Diablo IV. Una vez instalado, podrás iniciar el juego.

Listo, recuerda compartir este tutorial, darle like al video y darle ⭐  a este repo.

Recuerda que esta es una herramienta para desarrolladores y pueda haber fallas, recuerda no apoyar la piratería y recuerda que esto no es brujería, es tecnología. 🪄 


![Imagen generada con el creador de imagenes de Bing](/_c8584c34-c994-4794-b6b4-37a748407abe.jpeg)
*Imagen generada con el creador de imagenes de Bing*



