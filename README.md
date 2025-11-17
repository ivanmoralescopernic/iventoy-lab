# Entorn de Servidor PXE amb iVentoy i Vagrant

Aquest projecte configura un entorn de proves complet per a [iVentoy](https://www.iventoy.com/), un potent servidor PXE, utilitzant [Vagrant](https://www.vagrantup.com/) i [VirtualBox](https://www.virtualbox.org/). L'objectiu és automatitzar la creació d'un servidor iVentoy i un client de xarxa llest per arrencar, facilitant així les proves d'instal·lació de sistemes operatius a través de la xarxa.

## Característiques

*   **Automatització completa**: Amb unes poques comandes, es creen i configuren dues màquines virtuals: un servidor iVentoy i un client PXE.
*   **Entorn aïllat**: Les màquines virtuals operen en una xarxa privada, evitant conflictes amb la teva xarxa local.
*   **Fàcil accés**: La interfície web d'iVentoy és accessible directament des del navegador del teu ordinador a `http://localhost:26000`.
*   **Sincronització de fitxers**: La carpeta local `iventoy/` es sincronitza automàticament amb la màquina virtual del servidor, permetent afegir o canviar ISOs de forma senzilla.

## Requisits Previst

Abans de començar, assegura't de tenir instal·lat el següent programari:
*   [Vagrant](https://developer.hashicorp.com/vagrant/downloads)
*   [VirtualBox](https://www.virtualbox.org/wiki/Downloads)

## Com Utilitzar

El procés es divideix en quatre passos clars que s'han de seguir en ordre.

### Pas 1: Preparar els Fitxers ISO

Col·loca totes les imatges ISO dels sistemes operatius que vulguis arrencar per xarxa dins de la carpeta `iventoy/iso/`.
iventoy/
└── iso/
└── el-teu-sistema-operatiu.iso
code
Code
### Pas 2: Iniciar el Servidor iVentoy

Obre un terminal a l'arrel del projecte (on es troba el `Vagrantfile`) i executa la següent comanda per aixecar **només** la màquina virtual del servidor:

vagrant up iventoy

Aquesta comanda crearà la VM iventoy-server, la configurarà, sincronitzarà la carpeta iventoy/ i iniciarà el servei web d'iVentoy.

### Pas 3: Activar el Servei PXE a la Interfície Web

Aquest pas és crucial. El servei web d'iVentoy està actiu, però el servidor PXE (que permet l'arrencada per xarxa) s'ha d'iniciar manualment.
Obre el teu navegador web a l'ordinador amfitrió.
Ves a l'adreça: http://localhost:26000.
A la interfície d'iVentoy, busca l'adreça IP del servidor (192.168.56.10) i fes clic al botó d'inici (icona de "Play") per activar el servei PXE.
![alt text](https://www.iventoy.com/en/assets/images/screenshot/select_ip.png)
Un cop l'estat canviï a "Running", el servidor estarà llest per acceptar clients.
### Pas 4: Iniciar el Client PXE
Un cop el servidor PXE estigui actiu (després de fer clic a "Play"), torna al teu terminal i executa la següent comanda per iniciar la màquina client:

vagrant up pxeclient

Hauries de veure com la consola de la VM client s'obre i comença a arrencar per xarxa, rebent una adreça IP del servidor iVentoy i mostrant el menú d'arrencada amb les ISOs que has preparat.

### Gestió de l'Entorn
Pots utilitzar les següents comandes de Vagrant per gestionar les màquines virtuals:

vagrant status: Mostra l'estat actual de les VMs (iventoy i pxeclient).

vagrant halt: Atura les màquines virtuals de forma segura.

vagrant destroy -f: Elimina completament les màquines virtuals i tots els seus recursos.

### Estructura del Directori iventoy

L'estructura del directori iventoy ha de ser la següent per al correcte funcionament del projecte:
```bash

iventoy/
├── data/       # Dades de configuració d'iVentoy
├── doc/        # Documentació i llicències
├── iso/        # ★ COL·LOCA LES TEVES IMATGES .ISO AQUÍ ★
├── iventoy.sh  # Script d'execució
├── lib/        # Llibreries necessàries
├── log/        # Fitxers de registre
└── user/       # Scripts d'instal·lació desatesa
