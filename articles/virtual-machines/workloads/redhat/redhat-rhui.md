---
title: Uppdateringsinfrastruktur för Red Hat | Microsoft Docs
description: Läs mer om Red Hat-Uppdateringsinfrastruktur för Red Hat Enterprise Linux på begäran-instanser i Microsoft Azure
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
ms.openlocfilehash: dc4762cbda5ad2877d2d69953d2514dea17c8b46
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368901"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Uppdateringsinfrastruktur för Red Hat för på begäran Red Hat Enterprise Linux-datorer i Azure
 Med [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) kan moln leverantörer, till exempel Azure, spegla innehåll i Red Hat-värdbaserade databaser, skapa anpassade databaser med Azure-särskilt innehåll och göra det tillgängligt för slutanvändarens virtuella datorer.

Red Hat Enterprise Linux (RHEL) betala per användning (PAYG) avbildningar kommer förkonfigurerade för att komma åt Azure RHUI. Ingen ytterligare konfiguration krävs. Hämta de senaste uppdateringarna genom att köra `sudo yum update` när RHEL-instansen är klar. Den här tjänsten ingår som en del av RHEL PAYG-programvaruavgifter.

Mer information om RHEL-avbildningar i Azure, inklusive publicerings-och bevarande principer, finns [här](./redhat-images.md).

Information om Red Hat support-principer för alla versioner av RHEL finns på sidan [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata) .

> [!IMPORTANT]
> RHUI är endast avsett för PAYGO-avbildningar (pay-on-go). Systemet måste kopplas till RHSM eller satellit för att kunna ta emot uppdateringar för anpassade och gyllene avbildningar, även kallade "att ta din egen prenumeration" (BYOS). Mer information finns i [artikeln om Red Hat](https://access.redhat.com/solutions/253273) .


## <a name="important-information-about-azure-rhui"></a>Viktig information om Azure RHUI

* Azure RHUI är en uppdaterings infrastruktur som stöder alla RHEL PAYG-VM: ar som skapats i Azure. Detta hindrar dig inte från att registrera dina virtuella PAYG RHEL-datorer med prenumerations hanteraren eller satellit eller någon annan källa med uppdateringar, men om du gör det med en PAYG VM kommer den att leda till indirekt dubbel fakturering. Se följande punkt för mer information.
* Åtkomst till Azure som värd-RHUI ingår i priset för RHEL PAYG-avbildningen. Om du avregistrerar en PAYG RHEL virtuell dator från Azure som värd-RHUI konverterar som den virtuella datorn inte till en bring-your-own-license (BYOL) typ av virtuell dator. Om du registrerar samma virtuella dator med en annan uppdaterings källa kan du ådra dig _indirekta_ dubbla avgifter. Du debiteras för första gången för avgiften för Azure RHEL-programvara. Du debiteras den andra gången för Red Hat-prenumerationer som köpts tidigare. Om du ständigt behöver använda en annan uppdaterings infrastruktur än Azure-värdbaserade RHUI bör du överväga att registrera dig för att använda [RHEL BYOS-avbildningarna](./byos.md).

* RHEL SAP PAYG-avbildningar i Azure (RHEL för SAP, RHEL for SAP HANA och RHEL for SAP Business Applications) är anslutna till dedikerade RHUI-kanaler som finns kvar på den särskilda RHEL-lägre versionen som krävs för SAP-certifiering.

* Åtkomst till Azure-värdbaserade RHUI är begränsad till de virtuella datorerna inom [IP-intervallen för Azure-datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Om du är proxy är alla VM-trafik via en lokal nätverksinfrastruktur, du kan behöva konfigurera användardefinierade vägar för RHEL PAYG virtuella datorer till Azure-RHUI. I så fall måste användardefinierade vägar läggas till för _alla_ RHUI-IP-adresser.


## <a name="image-update-behavior"></a>Beteende för avbildnings uppdatering

Från och med april 2019 erbjuder Azure RHEL-avbildningar som är anslutna till EUS-databaser (Extended Update Support) som standard och RHEL avbildningar som är anslutna till vanliga (icke-EUS) databaser som standard. Mer information om RHEL-EUS finns i dokumentation om och [EUs](https://access.redhat.com/articles/rhel-eus)i Red Hats [versions livs cykel](https://access.redhat.com/support/policy/updates/errata) . Standard beteendet för `sudo yum update` varierar beroende på vilken RHEL-avbildning som du har tilldelat från, eftersom olika avbildningar är anslutna till olika lagrings platser.

För en fullständig avbildnings lista kör `az vm image list --publisher redhat --all` med hjälp av Azure CLI.

### <a name="images-connected-to-non-eus-repositories"></a>Avbildningar som är anslutna till icke-EUS-databaser

Om du etablerar en virtuell dator från en RHEL-avbildning som är ansluten till icke-EUS-lagringsplatser, uppgraderas du till den senaste RHEL-versionen när du kör `sudo yum update`. Om du till exempel etablerar en virtuell dator från en RHEL 7,4 PAYG-avbildning och kör `sudo yum update`, slutar du med en RHEL 7,7-dator (den senaste lägre versionen i RHEL7-serien).

Avbildningar som är anslutna till icke-EUS-lagringsplatser kommer inte att innehålla ett lägre versions nummer i SKU: n. SKU: n är det tredje elementet i URN (fullständigt namn på avbildningen). Till exempel är alla följande avbildningar kopplade till icke-EUS-databaser:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Observera att SKU: erna antingen är 7-LVM eller 7-RAW. Den lägre versionen anges i versionen (fjärde elementet i URN) för de här avbildningarna.

### <a name="images-connected-to-eus-repositories"></a>Bilder som är anslutna till EUS-databaser

Om du etablerar en virtuell dator från en RHEL-avbildning som är ansluten till EUS-lagringsplatserna, uppgraderas du inte till den senaste RHEL-versionen när du kör `sudo yum update`. Detta beror på att avbildningarna som är anslutna till EUS-lagringsplatser också är versions låsta till sin speciella lägre version.

Bilder som är anslutna till EUS-lagringsplatser kommer att innehålla ett lägre versions nummer i SKU: n. Till exempel är alla följande avbildningar kopplade till EUS-databaser:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS och version – lås RHEL virtuella datorer

EUS-databaser (Extended Update Support) är tillgängliga för kunder som vill låsa sina RHEL-VM: ar till en viss RHEL-mindre version efter etablering av den virtuella datorn. Du kan konfigurera den virtuella RHEL-datorn till en viss del version genom att uppdatera databaserna så att de pekar på de utökade support databaserna för uppdatering. Du kan också återställa EUS-versions låsnings åtgärden.

>[!NOTE]
> EUS stöds inte för RHEL-tillägg. Det innebär att om du installerar ett paket som vanligt vis är tillgängligt från RHEL extra-kanalen, kan du inte göra det på EUS. Den Red Hat extra produkt livs cykeln beskrivs [här](https://access.redhat.com/support/policy/updates/extras/).

När detta skrivs är EUS-supporten avslutad för RHEL < = 7,4. Se avsnittet "Red Hat Enterprise Linux längre support tillägg" i [Red Hat-dokumentationen](https://access.redhat.com/support/policy/updates/errata/) för mer information.
* RHEL 7,4 EUS-support upphör 31 augusti 2019
* RHEL 7,5 EUS-support upphör 30 april 2020
* RHEL 7,6 EUS-support upphör den 31 oktober 2020
* RHEL 7,7 EUS-support upphör 30 augusti 2021

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Växla en virtuell RHEL-dator till EUS (versions låsning till en speciell del version)
Använd följande instruktioner för att låsa en RHEL VM till en viss del version (kör som rot):

>[!NOTE]
> Detta gäller endast för RHEL-versioner som EUS är tillgängligt för. När detta skrivs, omfattar detta RHEL 7,2-7.7. Mer information finns på sidan [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata) .

1. Inaktivera icke-EUS databaser:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Lägg till EUS databaser:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Lås variabeln releasever (kör som root):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Ovanstående instruktion kommer att låsa den lägre RHEL-versionen till den aktuella del versionen. Ange en speciell del version om du vill uppgradera och låsa till en senare version som inte är den senaste. `echo 7.5 > /etc/yum/vars/releasever` kommer till exempel att låsa RHEL-versionen till RHEL 7,5

1. Uppdatera din virtuella RHEL-dator
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Ändra tillbaka en RHEL-VM till icke-EUS (ta bort ett versions lås)
Kör följande som rot:
1. Ta bort releasever-filen:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Inaktivera EUS-databaser:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. Konfigurera RHEL VM
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. Uppdatera din virtuella RHEL-dator
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>IP-adresser för servrarna som RHUI

RHUI är tillgänglig i alla regioner där RHEL-avbildningar på begäran är tillgängliga. Den innehåller för närvarande alla offentliga regioner som visas på sidan [Azure status Dashboard](https://azure.microsoft.com/status/) , Azure amerikanska myndigheter och Microsoft Azure Tyskland regioner.

Om du använder en nätverks konfiguration för att ytterligare begränsa åtkomsten från virtuella RHEL PAYG-datorer måste du kontrol lera att följande IP-adresser är tillåtna för att `yum update` ska fungera beroende på vilken miljö du befinner dig i:


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


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Uppdatera har upphört att gälla RHUI-klientcertifikat på en virtuell dator

Om du använder en äldre RHEL VM-avbildning, till exempel RHEL 7,4 (avbildnings-URN: `RedHat:RHEL:7.4:7.4.2018010506`), kommer du att uppleva anslutnings problem till RHUI på grund av ett nu utgånget SSL-klientcertifikat. Felet som visas kan se ut som _"SSL-peer avvisade certifikatet som förfallet"_ eller _"fel: det går inte att hämta metadata för databas (repomd. xml) för databasen:... Verifiera sökvägen och försök igen_. Du kan åtgärda det här problemet genom att uppdatera RHUI-klient paketet på den virtuella datorn med hjälp av följande kommando:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Om du kör `sudo yum update` kan även klient certifikats paketet uppdateras (beroende på din RHEL-version), trots att "upphört SSL-certifikat" fel visas för andra databaser. Om den här uppdateringen lyckas, ska normal anslutning till andra RHUI-lagringsplatser återställas så att du kan köra `sudo yum update` korrekt.

Om du stöter på ett 404-fel när du kör en `yum update`kan du prova följande för att uppdatera yum-cachen:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Felsök anslutningsproblem till Azure RHUI
Om du får problem med att ansluta till Azure RHUI från Azure RHEL PAYG-VM, gör du följande:

1. Kontrollera konfigurationen för virtuell dator för Azure RHUI-slutpunkten:

    1. Kontrol lera om `/etc/yum.repos.d/rh-cloud.repo`-filen innehåller en referens till `rhui-[1-3].microsoft.com` i `baseurl` i `[rhui-microsoft-azure-rhel*]`-avsnittet i filen. I annat fall använder du den nya Azure-RHUI.

    1. Om den pekar på en plats med följande mönster `mirrorlist.*cds[1-4].cloudapp.net`, krävs en konfigurations uppdatering. Du använder den gamla VM-ögonblicksbilden och du måste du uppdatera den så att den pekar till den nya Azure-RHUI.

1. Åtkomst till Azure-värdbaserade RHUI är begränsad till virtuella datorer inom [IP-intervallen för Azure-datacenter](https://www.microsoft.com/download/details.aspx?id=41653).

1. Om du använder den nya konfigurationen har verifierat att den virtuella datorn ansluter från Azure-IP-adressintervall och fortfarande inte kan ansluta till Azure RHUI, filen ett supportärende med Microsoft eller Red Hat.

### <a name="infrastructure-update"></a>Infrastruktur uppdatering

I September 2016 kommer distribuerade vi en uppdaterade Azure RHUI. I April 2017 stänga vi av den gamla Azure-RHUI. Om du har använt RHEL PAYG-bilder (eller deras ögonblicksbilder) från September 2016 eller senare måste ansluter du automatiskt till den nya Azure-RHUI. Om du har dock äldre ögonblicksbilder på dina virtuella datorer kan behöva du manuellt uppdatera konfigurationen för att komma åt Azure-RHUI enligt beskrivningen i följande avsnitt.

De nya Azure RHUI-servrarna distribueras med [azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). I Traffic Manager, en enda slutpunkt (rhui-1.microsoft.com) kan användas av alla virtuella datorer, oavsett region.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Manuell uppdateringsproceduren för att använda Azure RHUI-servrar
Den här proceduren tillhandahålls endast för referens. RHEL PAYG-avbildningar har redan rätt konfiguration för att ansluta till Azure RHUI. För att manuellt uppdatera konfigurationen för att använda Azure RHUI-servrarna, gör du följande:

- För RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- För RHEL 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

- För RHEL 8:
    1. Skapa en konfigurations fil:
        ```bash
        vi rhel8.config
        ```
    1. Lägg till följande innehåll i konfigurations filen:
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
* Om du vill skapa en Red Hat Enterprise Linux virtuell dator från en Azure Marketplace PAYG-avbildning och använda Azure-värdbaserade RHUI går du till [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Om du vill veta mer om Red Hat-bilderna i Azure går du till [dokumentations sidan](./redhat-images.md).
* Information om Red Hat support-principer för alla versioner av RHEL finns på sidan [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata) .
