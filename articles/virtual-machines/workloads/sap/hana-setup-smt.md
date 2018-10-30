---
title: Hur du ställer in SMT server för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Hur du ställer in SMT server för SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d9fe644b7cc7d1a13cb9ed2f7016f25b3e346dfb
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233182"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Konfigurera SMT server för SUSE Linux
Stora instanser av SAP HANA har inte direkt anslutning till internet. Det är inte enkelt att registrera till exempel enheter med operativsystem-providern och för att ladda ned och installera uppdateringar. En lösning för SUSE Linux är att ställa in en SMT-server i virtuella Azure-datorer. Vara värd för den virtuella datorn i ett Azure-nätverk som är ansluten till den stora HANA-instansen. Med sådana en SMT-server kan den stora HANA-instansen enheten registrera och hämta uppdateringar. 

Mer dokumentation om SUSE finns i deras [prenumeration hanteringsverktyg för SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Förutsättningar för att installera en SMT-server som uppfyller uppgiften för HANA stora instanser är:

- Ett Azure-nätverk som är ansluten till HANA stora instans ExpressRoute-kretsen.
- Ett SUSE-konto som är associerad med en organisation. Organisationen bör ha en giltig SUSE-prenumeration.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Installera SMT server på virtuella Azure-datorer

Logga först in på den [SUSE Customer Center](https://scc.suse.com/).

Gå till **organisation** > **organisationens autentiseringsuppgifter**. I avsnittet, bör du hitta de autentiseringsuppgifter som krävs för att konfigurera SMT-servern.

Sedan kan installera en SUSE Linux-dator i Azure-nätverk. Om du vill distribuera den virtuella datorn, ta en bild för SLES 12 SP2 galleriet i Azure (Välj BYOS SUSE bild). I distributionsprocessen, inte definierar ett DNS-namn och inte använder statiska IP-adresser.

![Skärmbild av distribution av virtuella datorer för SMT-server](./media/hana-installation/image3_vm_deployment.png)

Den distribuerade virtuella datorn är mindre och fick den interna IP-adressen i Azure-nätverket av 10.34.1.4. Namnet på den virtuella datorn är *smtserver*. Efter installationen kan kontrolleras anslutningen till stora HANA-instansen enhet eller enheter. Beroende på hur organiserat namnmatchning, kan du behöva konfigurera lösning av enheter för stora HANA-instansen i etc/värdar för virtuella Azure-datorer. 

Lägg till en disk till den virtuella datorn. Du använder den här disken för att lagra uppdateringarna och startdisken själva kan vara för litet. Disken har här kan monteras /srv/www/htdocs, enligt följande skärmbild. En disk på 100 GB bör vara tillräckligt.

![Skärmbild av distribution av virtuella datorer för SMT-server](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Logga in på stora HANA-instansen enhet eller enheter, underhålla/etc/hosts och kontrollera om du kan nå den Azure-dator som ska köra SMT-server i nätverket.

Logga in på Azure-dator som ska köras SMT-servern efter denna kontroll. Om du använder putty för att logga in på den virtuella datorn, kör du denna sekvens av kommandon i bash-fönstret:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Starta om din bash för att aktivera inställningarna. Starta sedan YAST.

Anslut din virtuella dator (smtserver) till webbplatsen SUSE.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

När den virtuella datorn är ansluten till webbplatsen SUSE, installera smt-paket. Använd kommandot putty för att installera de smt-paket.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


Du kan också använda verktyget YAST för att installera de smt-paket. I YAST, går du till **programvaruunderhåll**, och Sök efter smt. Välj **smt**, som automatiskt växlar till yast2 smt.

![Skärmbild av SMT i YAST](./media/hana-installation/image5_smt_in_yast.PNG)


Acceptera valet för installation på smtserver. När installationen är klar går du till SMT-serverkonfigurationen. Ange organisationens autentiseringsuppgifter från SUSE kunden Center som hämtades tidigare. Ange också Azure VM-värdnamn som SMT-Serveradress. I den här demonstrationen har https://smtserver.

![Skärmbild av SMT-serverkonfiguration](./media/hana-installation/image6_configuration_of_smtserver1.png)

Testa nu om anslutningen till SUSE Customer Center fungerar. Den fungerar som du ser i följande skärmbild, i det här fallet demonstration.

![Skärmbild av testar anslutningen till SUSE kunden Center](./media/hana-installation/image7_test_connect.png)

Ange ett lösenord för databasen när SMT-installationen startar. Eftersom det är en ny installation, definierar du lösenordet som du ser i följande skärmbild.

![Skärmbild av definiera lösenordet för databas](./media/hana-installation/image8_define_db_passwd.PNG)

Nästa steg är att skapa ett certifikat.

![Skärmbild av skapa ett certifikat för SMT-server](./media/hana-installation/image9_certificate_creation.PNG)

I slutet av konfigurationen kan det ta några minuter att köra synkronisering-kontroll. Efter installationen och konfigurationen av SMT-server, bör du hitta directory lagringsplatsen under montera punkt /srv/www/htdocs /. Det finns även vissa underkataloger på lagringsplatsen. 

Starta om servern SMT och relaterade tjänster med följande kommandon.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Ladda ned paket på SMT-servern

När alla tjänster startas om, väljer du paket i SMT Management med hjälp av YAST. Paketet valet beror på en angiven operativsystemavbildning på stora HANA-instans-server. Alternativet paketet beror inte på SLES-version eller version av den virtuella datorn körs SMT-servern. Följande skärmbild visar ett exempel på val av skärmen.

![Skärmbild över att välja paket](./media/hana-installation/image10_select_packages.PNG)

Starta sedan den inledande kopian av SMT-server som du har konfigurerat väljer paketen. Den här kopian utlöses i gränssnittet med hjälp av kommandot smt-speglingen.

![Skärmbild av hämtar paket till SMT-servern](./media/hana-installation/image11_download_packages.PNG)

Paket som ska få kopieras till de kataloger som skapats under montera punkt /srv/www/htdocs. Den här processen kan ta en timme eller mer, beroende på hur många paket som du väljer. Eftersom den här processen är klar kan du flytta till SMT-klientkonfiguration. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Konfigurera SMT-klienten på stora HANA-instansen enheter

Klient- eller klienter är i det här fallet stora HANA-instansen enheter. Serverinstallation SMT kopieras skriptet clientSetup4SMT.sh till virtuella Azure-datorer. Kopia som skriptet över till stora HANA-instansen-enhet du vill ansluta till servern SMT. Starta skriptet med alternativet -h och ge namnet på din SMT-server som en parameter. I det här exemplet heter *smtserver*.

![Skärmbild av SMT-klienten](./media/hana-installation/image12_configure_client.PNG)

Det är möjligt att belastningen på certifikatet från servern och klienten lyckas, men registreringen misslyckas, enligt följande skärmbild.

![Skärmbild av klienten registreringsfel](./media/hana-installation/image13_registration_failed.PNG)

Om registreringen misslyckas kan du se [SUSE stöd för dokument](https://www.suse.com/de-de/support/kb/doc/?id=7006024), och det kör stegen som beskrivs.

> [!IMPORTANT] 
> Ange namnet på den virtuella datorn för server-namn (i det här fallet *smtserver*), utan att det fullständigt kvalificerade domännamnet. 

Kör följande kommando på den stora HANA-instansen enheten när du har kört dessa steg:

```
SUSEConnect –cleanup
```

> [!Note] 
> Vänta några minuter efter det steget. Om du kör clientSetup4SMT.sh direkt, kan du få ett felmeddelande.

Om det uppstår ett problem som du behöver åtgärda baserat på stegen i artikeln SUSE, startar du om clientSetup4SMT.sh på enheten för stora HANA-instansen. Nu den bör slutföras utan problem.

![Skärmbild av klienten registreringen lyckades](./media/hana-installation/image14_finish_client_config.PNG)

Du har konfigurerat SMT-klienten av den stora HANA-instansen enheten att ansluta till den SMT-server som du installerade i Azure-dator. Du kan nu ta 'zypper upp' eller 'zypper i ”installera uppdateringar av operativsystemet till stora HANA-instanser eller installera ytterligare paket. Du kan bara hämta uppdateringar som du laddade ner innan på SMT-servern.

## <a name="next-steps"></a>Nästa steg
- [HANA-Installation på HLI](hana-example-installation.md).











