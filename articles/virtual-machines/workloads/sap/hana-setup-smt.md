---
title: Hur du installationsprogrammet SMT server för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Hur du ställer in SMT server för SAP HANA på Azure (stor instans).
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
ms.openlocfilehash: f387c1afe88f2bba476309b2e2e01942d2b7ae5b
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982633"
---
# <a name="setting-up-smt-server-for-suse-linux"></a>Ställa in SMT server för SUSE Linux
Stora SAP HANA-instanser har inte direkt anslutning till Internet. Därför är det inte enkelt att registrera till exempel enheter med OS-providern och för att ladda ned och tillämpa korrigeringar. Om SUSE Linux, en lösning kan vara att ställa in en SMT-server i en Azure VM. Medan den virtuella Azure-datorn måste finnas i ett virtuellt Azure nätverk, som är ansluten till den stora HANA-instansen. Med sådana en SMT-server kan den stora HANA-instansen enheten registrera och ladda ned korrigeringar. 

SUSE innehåller en större vägledning om deras [prenumeration hanteringsverktyg för SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Som villkor för installation av SMT-server som uppfyller uppgiften för stora HANA-instansen behöver du:

- Ett virtuellt Azure nätverk som är ansluten till HANA stora instans ER-kretsen.
- Ett SUSE-konto som är associerad med en organisation. Medan organisationen behöver ha en giltig SUSE-prenumeration.

## <a name="installation-of-smt-server-on-azure-vm"></a>Installation av SMT-server på Azure VM

I det här steget ska installera du SMT-server i en Azure VM. Det första måttet är att logga in på den [SUSE Customer Center](https://scc.suse.com/).

När du är inloggad, gå till organisation--> organisationens autentiseringsuppgifter. I avsnittet bör du hitta de autentiseringsuppgifter som krävs för att konfigurera SMT-servern.

Det tredje steget är att installera en SUSE Linux-dator i Azure VNet. Om du vill distribuera den virtuella datorn, ta en bild för SLES 12 SP2 galleriet i Azure (Välj BYOS SUSE bild). I distributionsprocessen, inte definierar ett DNS-namn och inte använder statiska IP-adresser som visas i den här skärmbilden

![distribution av virtuella datorer för SMT-server](./media/hana-installation/image3_vm_deployment.png)

Den distribuerade virtuella datorn har en mindre VM och fick den interna IP-adressen i Azure VNet för 10.34.1.4. Namnet på den virtuella datorn var smtserver. Efter installationen kan kontrollerades anslutningen till stora HANA-instans enhet(er). Beroende på hur du organiserat namnmatchning du kan behöva konfigurera lösning av enheter för stora HANA-instansen i etc/värdar för den virtuella Azure-datorn. Lägg till ytterligare en disk till den virtuella datorn som ska användas för att lagra uppdateringarna. Startdisken själva kan vara för litet. I det fallet visas fick disken monteras /srv/www/htdocs enligt följande skärmbild. En disk på 100 GB bör vara tillräckligt.

![distribution av virtuella datorer för SMT-server](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Logga in på stora HANA-instansen enhet(er), underhålla/etc/hosts och kontrollera om du kan nå den virtuella Azure-datorer som ska köra SMT-server i nätverket.

När den här kontrollen görs har, måste du logga in på Azure VM som ska köras SMT-servern. Om du använder putty för att logga in på den virtuella datorn som du behöver köra den här sekvensen av kommandon i bash-fönstret:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Starta om din bash för att aktivera inställningarna när du kör dessa kommandon. Starta sedan YAST.

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


Du kan också använda YAST verktyget för att installera de smt-paket. Gå till programvaruunderhåll och Sök efter smt i YAST. Välj smt växla automatiskt till yast2 smt enligt nedan

![SMT i yast](./media/hana-installation/image5_smt_in_yast.PNG)


Acceptera valet för installation på smtserver. När du har installerat kan du gå till SMT-serverkonfigurationen och ange organisationens autentiseringsuppgifter från SUSE kunden Center som hämtades tidigare. Ange också Azure VM-värdnamn som SMT-Serveradress. I det här exemplet var https://smtserver som visas i nästa bilderna.

![Konfigurationen av SMT-server](./media/hana-installation/image6_configuration_of_smtserver1.png)

Du måste testa om anslutningen till SUSE Customer Center fungerar som nästa steg. Den fungerar som du ser i följande bilderna i fallet demonstration.

![Testa ansluta till SUSE Customer Center](./media/hana-installation/image7_test_connect.png)

När SMT installationen startar, måste du ange ett lösenord för databasen. Eftersom det är en ny installation som du behöver definiera lösenordet som du ser i nästa bilderna.

![Ange lösenordet för databas](./media/hana-installation/image8_define_db_passwd.PNG)

I nästa interaktion som du har är när ett certifikat skapas. Gå igenom dialogrutan som visas nästa och steget bör fortsätta.

![Skapa certifikat för SMT-server](./media/hana-installation/image9_certificate_creation.PNG)

Det kan finnas vissa antal minuter som används i steg i ”Kör kontroll av synkronisering” i slutet av konfigurationen. Efter installationen och konfigurationen av SMT-server, bör du hitta directory lagringsplatsen under montera punkt /srv/www/htdocs/plus vissa underkataloger på lagringsplatsen. 

Starta om servern SMT och relaterade tjänster med följande kommandon.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-of-packages-onto-smt-server"></a>Hämtningen av paketen på SMT-servern

Välj paket i SMT-hantering med Yast när alla tjänster har startats om. Paketet valet beror på OS-avbildning av stora HANA-instans-server och inte på SLES-version eller -versionen av SMT-servern. Ett exempel på val av skärmen visas nedan.

![Välj paket](./media/hana-installation/image10_select_packages.PNG)

När du är klar med det paket valet av måste du starta den inledande kopian av SMT-server som du har konfigurerat väljer paketen. Den här kopian har utlösts i gränssnittet med hjälp av kommandot smt-speglingen enligt nedan


![Ladda ned paket till SMT-servern](./media/hana-installation/image11_download_packages.PNG)

Som du ser ovan, bör paket som ska kopieras till de kataloger som skapats under montera punkt /srv/www/htdocs. Den här processen kan ta en stund. Beroende på hur många paket som du väljer, det kan ta upp till en timme eller mer.
Du måste flytta till SMT-klientkonfiguration som den här processen är klar. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Konfigurera SMT-klienten på stora HANA-instansen enheter

Klienterna är i det här fallet de stora HANA-instansen. Serverinstallation SMT kopierade skriptet clientSetup4SMT.sh på den virtuella Azure-datorn. Kopia som skriptet över till stora HANA-instansen-enhet du vill ansluta till servern SMT. Starta skriptet med alternativet -h och ge den som parameter namnet på din SMT-server. I det här exemplet smtserver.

![Konfigurera SMT-klienten](./media/hana-installation/image12_configure_client.PNG)

Det kan finnas ett scenario där belastningen på certifikatet från servern och klienten lyckades men registreringen misslyckades enligt nedan.

![Registrering av klienten misslyckas](./media/hana-installation/image13_registration_failed.PNG)

Om registreringen misslyckas kan du läsa följande [SUSE stöd för dokument](https://www.suse.com/de-de/support/kb/doc/?id=7006024) och kör stegen som beskrivs det.

> [!IMPORTANT] 
> Du måste ange namnet på den virtuella datorn i det här fallet smtserver, utan att det fullständigt kvalificerade domännamnet som servernamn. Bara den virtuella datorn namnet fungerar. 

När dessa steg har utförts, måste du köra följande kommando på den stora HANA-instansen enheten

```
SUSEConnect –cleanup
```

> [!Note] 
> I våra tester hade vi alltid Vänta några minuter efter det steget. Körs omedelbart-clientSetup4SMT.sh efter de korrigerande åtgärderna som beskrivs i artikel SUSE avslutades med meddelanden att certifikatet inte giltigt ännu. Väntar på vanligtvis 5 – 10 minuter och kör clientSetup4SMT.sh avslutades i en lyckad klientkonfiguration.

Om du råkade ut för problem som du behöver åtgärda baserat på stegen i artikeln SUSE, måste du starta om clientSetup4SMT.sh på stora HANA-instansen enheten igen. Nu bör den slutföras utan problem som visas nedan.

![Klienten har registrerats](./media/hana-installation/image14_finish_client_config.PNG)

Det här steget ska du har konfigurerat SMT-klienten av den stora HANA-instansen enheten att ansluta mot den SMT-server som du installerade i Azure-VM. Du kan nu dra 'zypper upp' eller 'zypper i ”om du installerar korrigeringsfiler för operativsystem till stora HANA-instanser eller installera ytterligare paket. Det är underförstått att du bara kan få korrigeringsprogram som du laddade ner innan på SMT-servern.

**Nästa steg**
- Se [HANA-Installation på HLI](hana-example-installation.md).











