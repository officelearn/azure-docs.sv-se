---
title: Konfigurera SMT-server för SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Konfigurera SMT-server för SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 699a8a69621642d07d3547c07bb20c0d32ca7686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616981"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Konfigurera SMT-server för SUSE Linux
Stora instanser av SAP HANA har inte direkt anslutning till Internet. Det är inte en enkel process att registrera en sådan enhet hos operativsystemets leverantör och att hämta och tillämpa uppdateringar. En lösning för SUSE Linux är att konfigurera en SMT-server på en virtuell Azure-dator. Vara värd för den virtuella datorn i ett virtuellt Azure-nätverk som är anslutet till DEN STORA HANA-instansen. Med en sådan SMT-server kan HANA Large Instance-enheten registrera och hämta uppdateringar. 

Mer information om SUSE finns i deras [prenumerationshanteringsverktyg för SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Förutsättningarna för att installera en SMT-server som uppfyller uppgiften för STORA HANA-instanser är:

- Ett virtuellt Azure-nätverk som är anslutet till HANA Large Instance ExpressRoute-kretsen.
- Ett SUSE-konto som är associerat med en organisation. Organisationen bör ha en giltig SUSE-prenumeration.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Installera SMT-server på en virtuell Azure-dator

Logga först in på [SUSE-kundcentret](https://scc.suse.com/).

Gå till**Autentiseringsuppgifter för organisationsorganisation** **Organization** > . I det avsnittet bör du hitta de autentiseringsuppgifter som krävs för att konfigurera SMT-servern.

Installera sedan en virtuell SUSE Linux-dator i det virtuella Azure-nätverket. Om du vill distribuera den virtuella datorn tar du en SLES 12 SP2-galleriavbildning av Azure (välj BYOS SUSE-avbildning). Definiera inte ett DNS-namn i distributionsprocessen och använd inte statiska IP-adresser.

![Skärmbild av distribution av virtuella datorer för SMT-server](./media/hana-installation/image3_vm_deployment.png)

Den distribuerade virtuella datorn är mindre och fick den interna IP-adressen i det virtuella Azure-nätverket på 10.34.1.4. Namnet på den virtuella datorn är *smtserver*. Efter installationen kontrolleras anslutningen till HANA Large Instance-enheten eller enheterna. Beroende på hur du ordnade namnmatchning kan du behöva konfigurera upplösningen för HANA-stora instansenheter i etc/hosts på den virtuella Azure-datorn. 

Lägg till en disk på den virtuella datorn. Du använder den här disken för att lagra uppdateringarna och själva startdisken kan vara för liten. Här fick disken monterad på /srv/www/htdocs, som visas i följande skärmdump. En disk på 100 GB bör räcka.

![Skärmbild av distribution av virtuella datorer för SMT-server](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Logga in på HANA Large Instance-enheten eller enheterna, underhåll /etc/hosts och kontrollera om du kan nå den virtuella Azure-datorn som ska köra SMT-servern över nätverket.

Efter den här kontrollen loggar du in på den virtuella Azure-datorn som ska köra SMT-servern. Om du använder putty för att logga in på den virtuella datorn kör du den här sekvensen av kommandon i bash-fönstret:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Starta om bash för att aktivera inställningarna. Starta sedan YAST.

Anslut din virtuella dator (smtserver) till SUSE-platsen.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

När den virtuella datorn är ansluten till SUSE-platsen installerar du smt-paketen. Använd följande kommandot putty för att installera smt-paketen.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


Du kan också använda YAST-verktyget för att installera smt-paketen. I YAST går du till **Software Maintenance**och söker efter smt. Välj **smt**, som växlar automatiskt till yast2-smt.

![Skärmbild av SMT i YAST](./media/hana-installation/image5_smt_in_yast.PNG)


Acceptera valet för installation på smtservern. När installationen är klar går du till SMT-serverkonfigurationen. Ange organisationsbehörigheten från SUSE-kundcentret som du hämtade tidigare. Ange också ditt värdnamn för den virtuella Azure-datorn som SMT-server-URL:en. I den här demonstrationen\/är det https: /smtserver.

![Skärmbild av SMT-serverkonfiguration](./media/hana-installation/image6_configuration_of_smtserver1.png)

Testa nu om anslutningen till SUSE Customer Center fungerar. Som du ser i följande skärmdump, i detta demonstrationsfall, fungerade det.

![Skärmbild av testanslutning till SUSE Customer Center](./media/hana-installation/image7_test_connect.png)

När SMT-installationen har startat anger du ett databaslösenord. Eftersom det är en ny installation bör du definiera lösenordet som visas i följande skärmbild.

![Skärmbild av att definiera lösenord för databasen](./media/hana-installation/image8_define_db_passwd.PNG)

Nästa steg är att skapa ett certifikat.

![Skärmbild av hur du skapar ett certifikat för SMT-server](./media/hana-installation/image9_certificate_creation.PNG)

I slutet av konfigurationen kan det ta några minuter att köra synkroniseringskontrollen. Efter installationen och konfigurationen av SMT-servern bör du hitta katalogreponyren under monteringspunkten /srv/www/htdocs/. Det finns också några underkataloger under repo. 

Starta om SMT-servern och dess relaterade tjänster med dessa kommandon.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Hämta paket till SMT-servern

När alla tjänster har startats om väljer du lämpliga paket i SMT Management med yast. Paketvalet beror på operativsystemavbildningen på HANA Large Instance-servern. Paketvalet beror inte på SLES-versionen eller versionen av den virtuella datorn som kör SMT-servern. Följande skärmbild visar ett exempel på urvalsskärmen.

![Skärmbild av att välja paket](./media/hana-installation/image10_select_packages.PNG)

Starta sedan den första kopian av de valda paketen till den SMT-server som du konfigurerade. Den här kopian utlöses i skalet med kommandot smt-mirror.

![Skärmbild av hämtning av paket till SMT-server](./media/hana-installation/image11_download_packages.PNG)

Paketen ska kopieras till de kataloger som skapats under monteringspunkten /srv/www/htdocs. Den här processen kan ta en timme eller mer, beroende på hur många paket du väljer. När den här processen är klar flyttar du till SMT-klientkonfigurationen. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Konfigurera SMT-klienten på HANA Large Instance-enheter

Klienten eller klienterna i det här fallet är HANA-enheter för stora instanser. SMT-serverinstallationen kopierade skriptet clientSetup4SMT.sh till den virtuella Azure-datorn. Kopiera skriptet till den STORA HANA-instansenhet som du vill ansluta till SMT-servern. Starta skriptet med alternativet -h och ange namnet på SMT-servern som en parameter. I det här exemplet är namnet *smtserver*.

![Skärmbild av konfiguration av SMT-klienten](./media/hana-installation/image12_configure_client.PNG)

Det är möjligt att belastningen av certifikatet från servern av klienten lyckas, men registreringen misslyckas, som visas i följande skärmdump.

![Skärmbild av klientregistreringsfel](./media/hana-installation/image13_registration_failed.PNG)

Om registreringen misslyckas läser du [SUSE-supportdokumentet](https://www.suse.com/de-de/support/kb/doc/?id=7006024)och kör stegen som beskrivs där.

> [!IMPORTANT] 
> Ange namnet på den virtuella datorn (i det här fallet *smtserver)* utan det fullständiga domännamnet för servernamnet. 

När du har kört de här stegen kör du följande kommando på HANA Large Instance-enheten:

```
SUSEConnect –cleanup
```

> [!Note] 
> Vänta några minuter efter det steget. Om du kör clientSetup4SMT.sh omedelbart kan du få ett felmeddelande.

Om du stöter på ett problem som du behöver åtgärda baserat på stegen i SUSE-artikeln startar du om clientSetup4SMT.sh på HANA Large Instance-enheten. Nu bör det avslutas framgångsrikt.

![Skärmbild avlyckades med klientregistrering](./media/hana-installation/image14_finish_client_config.PNG)

Du har konfigurerat SMT-klienten för HANA Large Instance-enheten för att ansluta till SMT-servern som du installerade på den virtuella Azure-datorn. Du kan nu ta "zypper up" eller "zypper in" för att installera operativsystemuppdateringar till HANA Stora instanser, eller installera ytterligare paket. Du kan bara få uppdateringar som du har hämtat tidigare på SMT-servern.

## <a name="next-steps"></a>Nästa steg
- [HANA Installation på HLI](hana-example-installation.md).











