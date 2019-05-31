---
title: Uppdateringsinfrastruktur för Red Hat | Microsoft Docs
description: Läs mer om Red Hat-Uppdateringsinfrastruktur för Red Hat Enterprise Linux på begäran-instanser i Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: jeconnoc
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/28/2019
ms.author: borisb
ms.openlocfilehash: e950a92925e77fa05708d2af3e04e7991243f613
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357754"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Uppdateringsinfrastruktur för Red Hat för på begäran Red Hat Enterprise Linux-datorer i Azure
 [Uppdateringsinfrastruktur för Red Hat](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) gör att cloud-leverantörer, till exempel Azure för spegling av Red Hat-värdbaserade databasinnehåll, skapa anpassade databaser med Azure-specifika innehåll och gör den tillgänglig för slutanvändaren virtuella datorer.

Red Hat Enterprise Linux (RHEL) betala per användning (PAYG) avbildningar kommer förkonfigurerade för att komma åt Azure RHUI. Ingen ytterligare konfiguration krävs. För att få de senaste uppdateringarna, köra `sudo yum update` när din RHEL-instans är klar. Den här tjänsten ingår som en del av RHEL PAYG-programvaruavgifter.

Mer information om RHEL-avbildningar i Azure, inklusive publicering och principer för kvarhållning är tillgänglig [här](./rhel-images.md).

Information om principer för Red Hat-support för alla versioner av RHEL kan hittas på den [livscykel för Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) sidan.

## <a name="important-information-about-azure-rhui"></a>Viktig information om Azure RHUI
* Azure RHUI stöder för närvarande endast den senaste mindre versionen i varje RHEL-serien (RHEL6 eller RHEL7). Om du vill uppgradera en RHEL VM-instans som är anslutna till RHUI till den senaste minor-versionen, kör `sudo yum update`.

    Exempel: Om du vill etablera en virtuell dator från en RHEL 7.4 PAYG-avbildning och kör `sudo yum update`, att avslutas med en RHEL 7.6 virtuell dator (de senaste mindre versionen i RHEL7-serien).

    För att undvika det här beteendet kan du behöva skapa en egen avbildning enligt beskrivningen i den [skapa och ladda upp en Red Hat-baserad virtuell dator för Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artikeln. Måste du ansluta den till en annan uppdatering-infrastruktur ([direkt till Red Hat innehåll servrar](https://access.redhat.com/solutions/253273) eller en [Red Hat satellit server](https://access.redhat.com/products/red-hat-satellite)).

* Åtkomst till Azure som värd-RHUI ingår i priset för RHEL PAYG-avbildningen. Om du avregistrerar en PAYG RHEL virtuell dator från Azure som värd-RHUI konverterar som den virtuella datorn inte till en bring-your-own-license (BYOL) typ av virtuell dator. Om du har registrerat samma virtuella dator med en annan källa för uppdateringar kan roamingavgifter _indirekt_ dubbelklicka avgifter. Du debiteras för första gången för avgiften för Azure RHEL-programvara. Du debiteras den andra gången för Red Hat-prenumerationer som köpts tidigare. Om du behöver konsekvent att använda en uppdateringsinfrastruktur än Azure-värdbaserade RHUI kan du skapa och distribuera dina egna avbildningar (BYOL-typ). Den här processen beskrivs i [skapa och ladda upp en Red Hat-baserad virtuell dator för Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* RHEL SAP PAYG-avbildningar i Azure (RHEL for SAP, RHEL for SAP HANA och RHEL for SAP Business Applications) är anslutna till RHUI kanaler som finns kvar på den specifika RHEL lägre versionen som krävs för SAP-certifiering.

* Åtkomst till Azure-värdbaserade RHUI är begränsad till de virtuella datorerna inom de [Azure datacenter IP-adressintervall](https://www.microsoft.com/download/details.aspx?id=41653). Om du är proxy är alla VM-trafik via en lokal nätverksinfrastruktur, du kan behöva konfigurera användardefinierade vägar för RHEL PAYG virtuella datorer till Azure-RHUI.

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS och version-låsning RHEL-datorer
Vissa kunder vill låsa sin virtuella RHEL-datorer till en viss RHEL mindre version. Du kan version Lås din RHEL VM till en viss mindre version genom att uppdatera databaser så att den pekar till stöd för utökat-databaser. Använd följande instruktioner för att låsa en RHEL VM till en viss mindre version:

>[!NOTE]
> Detta gäller endast för RHEL-versioner som EUS är tillgänglig. Detta omfattar RHEL 7.2-7.6 när detta skrivs. Mer information finns på den [livscykel för Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) sidan.

1. Inaktivera lagringsplatser för icke-EUS:
    ```bash
    sudo yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Lägg till EUS lagringsplatser:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Lås releasever variabeln:
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Instruktionen ovan låses den mindre RHEL-versionen till den aktuella lägre versionen. Ange en specifik mindre version om du vill uppgradera och låsa till en senare mindre version som inte är senast. Till exempel `echo 7.5 > /etc/yum/vars/releasever` låser den RHEL-versionen med RHEL 7.5

1. Uppdatera din virtuella RHEL-dator
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>IP-adresser för servrarna som RHUI

RHUI är tillgänglig i alla regioner där RHEL-avbildningar på begäran är tillgängliga. Omfattar för närvarande alla offentliga regioner som visas på den [Azure statusinstrumentpanelen](https://azure.microsoft.com/status/) sida, Azure US Government och Microsoft Azure Tyskland-regioner.

Om du använder en nätverkskonfiguration för att ytterligare begränsa åtkomst från RHEL PAYG virtuella datorer, se till att följande IP-adresser tillåts för `yum update` ska fungera beroende på den miljö som du använder:


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

## <a name="azure-rhui-infrastructure"></a>Azure RHUI Infrastructure


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Uppdatera har upphört att gälla RHUI-klientcertifikat på en virtuell dator

Om du använder en äldre RHEL VM-avbildning, till exempel RHEL 7.4 (bild-URN: `RedHat:RHEL:7.4:7.4.2018010506`), du kommer att uppleva anslutningsproblem till RHUI på grund av ett utgångna SSL-klientcertifikat. Fel som du ser kan se ut _”SSL-peer avvisade ditt certifikat som upphört att gälla”_ eller _”fel: Det går inte att hämta metadata för databasen (repomd.xml) för databasen:... Kontrollera sökvägen och försök igen ”_ . Uppdatera RHUI-klientpaketet på den virtuella datorn med följande kommando för att lösa problemet:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Du kan också köra `sudo yum update` kan också uppdatera klientpaketet för certifikat (beroende på din RHEL-version), trots de ”utgånget SSL-certifikat” fel visas för andra databaser. Om den här uppdateringen lyckas normala anslutning till andra RHUI-databaser ska återställas, så att du kommer att kunna köra `sudo yum update` har.

Om du får ett 404-fel när du kör en `yum update`, prova följande för att uppdatera din yum-cache:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Felsök anslutningsproblem till Azure RHUI
Om du får problem med att ansluta till Azure RHUI från Azure RHEL PAYG-VM, gör du följande:

1. Kontrollera konfigurationen för virtuell dator för Azure RHUI-slutpunkten:

    a. Kontrollera om den `/etc/yum.repos.d/rh-cloud.repo` filen innehåller en referens till `rhui-[1-3].microsoft.com` i den `baseurl` av den `[rhui-microsoft-azure-rhel*]` i filen. I annat fall använder du den nya Azure-RHUI.

    b. Om den pekar på en plats med följande mönster `mirrorlist.*cds[1-4].cloudapp.net`, krävs en konfigurationsuppdatering. Du använder den gamla VM-ögonblicksbilden och du måste du uppdatera den så att den pekar till den nya Azure-RHUI.

1. Åtkomst till Azure-värdbaserade RHUI är begränsad till virtuella datorer inom den [Azure datacenter IP-adressintervall](https://www.microsoft.com/download/details.aspx?id=41653).

1. Om du använder den nya konfigurationen har verifierat att den virtuella datorn ansluter från Azure-IP-adressintervall och fortfarande inte kan ansluta till Azure RHUI, filen ett supportärende med Microsoft eller Red Hat.

### <a name="infrastructure-update"></a>Uppdatering av infrastrukturen

I September 2016 kommer distribuerade vi en uppdaterade Azure RHUI. I April 2017 stänga vi av den gamla Azure-RHUI. Om du har använt RHEL PAYG-bilder (eller deras ögonblicksbilder) från September 2016 eller senare måste ansluter du automatiskt till den nya Azure-RHUI. Om du har dock äldre ögonblicksbilder på dina virtuella datorer kan behöva du manuellt uppdatera konfigurationen för att komma åt Azure-RHUI enligt beskrivningen i följande avsnitt.

De nya Azure RHUI-servrarna distribueras med [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). I Traffic Manager, en enda slutpunkt (rhui-1.microsoft.com) kan användas av alla virtuella datorer, oavsett region.

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

## <a name="next-steps"></a>Nästa steg
* Skapar en Red Hat Enterprise Linux virtuell dator från en Azure Marketplace PAYG-avbildning och kan använda Azure-värdbaserade RHUI går du till den [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Om du vill veta mer om Red Hat-bilder i Azure kan du gå till den [dokumentationssidan](./rhel-images.md).
* Information om principer för Red Hat-support för alla versioner av RHEL kan hittas på den [livscykel för Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) sidan.
