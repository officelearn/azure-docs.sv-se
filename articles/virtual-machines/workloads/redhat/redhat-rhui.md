---
title: Red Hat Update Infrastruktur | Microsoft-dokument
description: Lär dig mer om Red Hat Update-infrastruktur för Red Hat Enterprise Linux-instanser på begäran i Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: aa9fd230f59b5e46576e78beb0436c85449d3c5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256920"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update-infrastruktur för virtuella virtuella datorer med Red Hat Enterprise Linux på begäran i Azure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) gör det möjligt för molnleverantörer, till exempel Azure, att spegla Red Hat-värdbaserat databasinnehåll, skapa anpassade databaser med Azure-specifikt innehåll och göra det tillgängligt för virtuella slutanvändare.

Payg-avbildningar (Red Hat Enterprise Linux) pay-as-you-go (PAYG) är förkonfigurerade för att komma åt Azure RHUI. Ingen ytterligare konfiguration behövs. Om du vill hämta `sudo yum update` de senaste uppdateringarna körs du när RHEL-instansen är klar. Denna tjänst ingår som en del av RHEL PAYG programvaruavgifter.

Ytterligare information om RHEL-avbildningar i Azure, inklusive publicerings- och bevarandeprinciper, finns [här](./redhat-images.md).

Information om Supportpolicyer för Red Hat för alla versioner av RHEL finns på [red hat Enterprise Linux life cycle-sidan.](https://access.redhat.com/support/policy/updates/errata)

> [!IMPORTANT]
> RHUI är endast avsett för pay-as-you-go (PAYG) bilder. För anpassade och gyllene bilder, även känd som bring-your-own-subscription (BYOS), måste systemet kopplas till RHSM eller Satellit för att få uppdateringar. Se [Red Hat artikeln](https://access.redhat.com/solutions/253273) för mer information.


## <a name="important-information-about-azure-rhui"></a>Viktig information om Azure RHUI

* Azure RHUI är uppdateringsinfrastrukturen som stöder alla virtuella RHEL PAYG-datorer som skapats i Azure. Detta hindrar dig inte från att registrera dina VIRTUELLA PAYG RHEL-datorer med Subscription Manager eller Satellite eller annan uppdateringskälla, men om du gör det med en PAYG-virtuell dator leder det till indirekt dubbelfakturering. Se följande punkt för mer information.
* Åtkomst till den Azure-värdbaserade RHUI ingår i avbildningspriset för RHEL PAYG. Om du avregistrerar en PAYG RHEL-virtuell dator från den Azure-värdbaserade RHUI som inte konverterar den virtuella datorn till en BYOL-typ (bring-your-own-license) av vm. Om du registrerar samma virtuella dator med en annan uppdateringskälla kan du ådra dig _indirekta_ dubbla avgifter. Du debiteras första gången för Azure RHEL-programvaruavgiften. Du debiteras andra gången för Red Hat-prenumerationer som köptes tidigare. Om du konsekvent behöver använda en annan uppdateringsinfrastruktur än Azure-värd-RHUI, överväg att registrera dig för att använda [RHEL BYOS-avbildningarna](./byos.md).

* RHEL SAP PAYG-avbildningar i Azure (RHEL för SAP, RHEL för SAP HANA och RHEL för SAP Business Applications) är anslutna till dedikerade RHUI-kanaler som finns kvar på den specifika RHEL-delversionen som krävs för SAP-certifiering.

* Åtkomst till Azure-värdbaserade RHUI är begränsad till virtuella datorer inom [Azure datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). Om du proxyerar all vm-trafik via en lokal nätverksinfrastruktur kan du behöva konfigurera användardefinierade vägar för RHEL PAYG-virtuella datorer för att komma åt Azure RHUI. Om så är fallet måste användardefinierade vägar läggas till för _alla_ RHUI IP-adresser.


## <a name="image-update-behavior"></a>Beteende för bilduppdatering

Från och med april 2019 erbjuder Azure RHEL-avbildningar som är anslutna till EUS-databaser (Extended Update Support) som standard och RHEL-avbildningar som är anslutna till de vanliga (icke-EUS-databaserna) som standard. Mer information om RHEL EUS finns i Red Hats [versions livscykeldokumentation](https://access.redhat.com/support/policy/updates/errata) och [EUS-dokumentation](https://access.redhat.com/articles/rhel-eus). Standardbeteendet `sudo yum update` för varierar beroende på vilken RHEL-avbildning du har etablerat från, eftersom olika bilder är anslutna till olika databaser.

Om du vill ha `az vm image list --publisher redhat --all` en fullständig avbildningslista kör du med Azure CLI.

### <a name="images-connected-to-non-eus-repositories"></a>Bilder som är anslutna till databaser som inte är EUS-databaser

Om du etablerar en virtuell dator från en RHEL-avbildning som är ansluten till databaser som inte `sudo yum update`är EUS-databaser uppgraderas du till den senaste RHEL-delversionen när du kör . Om du till exempel etablerar en virtuell dator från en RHEL 7.4 PAYG-avbildning och kör, `sudo yum update`hamnar du med en virtuell RHEL 7.7 -dator (den senaste delversionen i RHEL7-familjen).

Bilder som är anslutna till databaser som inte är EUS-databaser innehåller inte ett delversionsnummer i SKU.Images that are connected to non-EUS-repositories will not contain a minor version number in the SKU. SKU är det tredje elementet i URN (bildens fullständiga namn). Alla följande bilder bifogas till exempel till databaser som inte är EUS:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Observera att SKU:erna är antingen 7-LVM eller 7-RAW. Delversionen anges i versionen (fjärde elementet i URN) av dessa bilder.

### <a name="images-connected-to-eus-repositories"></a>Bilder som är anslutna till EUS-databaser

Om du etablerar en virtuell dator från en RHEL-avbildning som är ansluten till EUS-databaser `sudo yum update`uppgraderas du inte till den senaste RHEL-delversionen när du kör . Detta beror på att de bilder som är anslutna till EUS-arkiv också är versionslåst till deras specifika delversion.

Bilder som är anslutna till EUS-databaser innehåller ett mindre versionsnummer i SKU: et. Alla följande bilder bifogas till exempel till EUS-databaser:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS och versionslåsning av virtuella RHEL-datorer

Extended Update Support (EUS) databaser är tillgängliga för kunder som kanske vill låsa sina RHEL virtuella datorer till en viss RHEL-mindre version efter etablering av den virtuella datorn. Du kan versionslåsa din virtuella RHEL-dator till en viss delversion genom att uppdatera databaserna så att de pekar på de utökade supportdatabaserna för utökad uppdatering. Du kan också ångra EUS-versionslåsningen.

>[!NOTE]
> EUS stöds inte på RHEL Extras. Det innebär att om du installerar ett paket som vanligtvis är tillgängligt från RHEL Extras-kanalen kan du inte göra det under eus-systemet. Red Hat Extras Produktlivscykel beskrivs [här](https://access.redhat.com/support/policy/updates/extras/).

I skrivande stund har EUS-stödet upphört för RHEL <= 7.4. Mer information finns i avsnittet "Red Hat Enterprise Linux Longer Support Add-Ons" i [Red Hat-dokumentationen.](https://access.redhat.com/support/policy/updates/errata/)
* RHEL 7.4 EUS-stöd upphör 31 augusti 2019
* RHEL 7.5 EUS-stöd upphör 30 april 2020
* RHEL 7.6 EUS-stöd upphör 31 oktober 2020
* RHEL 7.7 EUS-stöd upphör 30 augusti 2021

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Växla en virtuell RHEL-dator till EUS (versionslås till en viss delversion)
Använd följande instruktioner för att låsa en virtuell RHEL-dator till en viss mindre version (kör som root):

>[!NOTE]
> Detta gäller endast för RHEL-versioner för vilka EUS är tillgängligt. I skrivande stund omfattar detta RHEL 7.2-7.7. Mer information finns på [Red Hat Enterprise Linux Life Cycle-sidan.](https://access.redhat.com/support/policy/updates/errata)

1. Inaktivera reposer som inte är EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Lägg till EUS-repor:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Lås `releasever` variabeln (kör som rot):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Ovanstående instruktion låser RHEL-den mindre frigöringen till den aktuella mindre utgåvan. Ange en specifik mindre version om du vill uppgradera och låsa till en senare mindre version som inte är den senaste. Till exempel `echo 7.5 > /etc/yum/vars/releasever` låser rhel-versionen till RHEL 7.5

1. Uppdatera din virtuella RHEL-dator
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Växla tillbaka en virtuell RHEL-dator till icke-EUS (ta bort ett versionslås)
Kör följande som root:
1. Ta `releasever` bort filen:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Inaktivera EUS-repor:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. Konfigurera virtuell RHEL-dator
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. Uppdatera din virtuella RHEL-dator
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>IPs för RHUI-innehållsleveransservrarna

RHUI är tillgängligt i alla regioner där RHEL on-demand-bilder finns tillgängliga. Den innehåller för närvarande alla offentliga regioner som anges på [azure-statusinstrumentpanelen,](https://azure.microsoft.com/status/) Azure US Government och Microsoft Azure Germany.It currently includes all public regions listed on the Azure status dashboard page, Azure US Government, and Microsoft Azure Germany regions.

Om du använder en nätverkskonfiguration för att ytterligare begränsa åtkomsten från virtuella RHEL `yum update` PAYG-datorer kontrollerar du att följande IPs tillåts att arbeta beroende på vilken miljö du befinner dig i:


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="azure-rhui-infrastructure"></a>Azure RHUI-infrastruktur


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Uppdatera utgånget RHUI-klientcertifikat på en virtuell dator

Om du använder en äldre RHEL VM-avbildning, till exempel RHEL `RedHat:RHEL:7.4:7.4.2018010506`7.4 (image URN: ), uppstår anslutningsproblem med RHUI på grund av ett nu utgånget TLS/SSL-klientcertifikat. Felet som visas kan se ut som _"SSL-peer avvisade certifikatet som utgånget"_ eller _"Fel: Det går inte att hämta databasmetadata (repomd.xml) för databasen: ... Kontrollera sökvägen och försök igen"_. För att lösa problemet uppdaterar du RHUI-klientpaketet på den virtuella datorn med följande kommando:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Alternativt kan `sudo yum update` körningen också uppdatera klientcertifikatpaketet (beroende på din RHEL-version), trots "utgångna SSL-certifikat"-fel som du ser för andra databaser. Om den här uppdateringen lyckas bör normal anslutning till andra RHUI-databaser återställas, `sudo yum update` så att du kan köras.

Om du stöter på ett 404-fel när du kör en `yum update`kan du prova följande för att uppdatera yum-cachen:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Felsöka anslutningsproblem till Azure RHUI
Om du får problem med att ansluta till Azure RHUI från din virtuella Azure RHEL PAYG-dator gör du så här:

1. Kontrollera vm-konfigurationen för Azure RHUI-slutpunkten:

    1. Kontrollera om `/etc/yum.repos.d/rh-cloud.repo` filen innehåller en `rhui-[1-3].microsoft.com` referens `baseurl` till `[rhui-microsoft-azure-rhel*]` i avsnittet i filen. Om den gör det använder du den nya Azure RHUI.

    1. Om den pekar på en plats `mirrorlist.*cds[1-4].cloudapp.net`med följande mönster krävs en konfigurationsuppdatering. Du använder den gamla ögonblicksbilden av den virtuella datorn och du måste uppdatera den så att den pekar på den nya Azure RHUI.

1. Åtkomst till Azure-värdbaserade RHUI är begränsad till virtuella datorer inom [Azure datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).

1. Om du använder den nya konfigurationen, har verifierat att den virtuella datorn ansluter från Azure IP-intervallet och fortfarande inte kan ansluta till Azure RHUI, fil ett supportärende med Microsoft eller Red Hat.

### <a name="infrastructure-update"></a>Uppdatering av infrastruktur

I september 2016 distribuerade vi en uppdaterad Azure RHUI. I april 2017 stängde vi av den gamla Azure RHUI. Om du har använt RHEL PAYG-avbildningarna (eller deras ögonblicksbilder) från september 2016 eller senare ansluter du automatiskt till den nya Azure RHUI. Om du däremot har äldre ögonblicksbilder på dina virtuella datorer måste du uppdatera konfigurationen manuellt för att komma åt Azure RHUI enligt beskrivningen i ett följande avsnitt.

De nya Azure RHUI-servrarna distribueras med [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). I Traffic Manager kan en enda slutpunkt (rhui-1.microsoft.com) användas av vilken virtuell dator som helst, oavsett region.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Manuell uppdateringsprocedur för att använda Azure RHUI-servrar
Detta förfarande tillhandahålls endast som referens. RHEL PAYG-avbildningar har redan rätt konfiguration för att ansluta till Azure RHUI. Så här uppdaterar du konfigurationen manuellt för att använda Azure RHUI-servrar:

- För RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- För RHEL 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

- För RHEL 8:
    1. Skapa en konfigurationsfil:
        ```bash
        vi rhel8.config
        ```
    1. Lägg till följande innehåll i konfigurationsfilen:
        ```bash
        [rhui-microsoft-azure-rhel8]
        name=Microsoft Azure RPMs for Red Hat Enterprise Linux 8
        baseurl=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-2.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-3.microsoft.com/pulp/repos/microsoft-azure-rhel8
        enabled=1
        gpgcheck=1
        gpgkey=https://rhelimage.blob.core.windows.net/repositories/RPM-GPG-KEY-microsoft-azure-release sslverify=1
        ```
    1. Spara filen och kör följande kommando:
        ```bash
        dnf --config rhel8.config install 'rhui-azure-rhel8'
        ```
    1. Uppdatera din virtuella dator
        ```bash
        sudo dnf update
        ```


## <a name="next-steps"></a>Nästa steg
* Om du vill skapa en Red Hat Enterprise Linux-virtuell dator från en Azure Marketplace PAYG-avbildning och använda Azure-värdbaserade RHUI går du till [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Mer information om Red Hat-avbildningarna i Azure finns på [dokumentationssidan](./redhat-images.md).
* Information om Supportpolicyer för Red Hat för alla versioner av RHEL finns på [red hat Enterprise Linux life cycle-sidan.](https://access.redhat.com/support/policy/updates/errata)
