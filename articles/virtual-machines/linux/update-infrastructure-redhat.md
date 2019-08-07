---
title: Uppdateringsinfrastruktur för Red Hat | Microsoft Docs
description: Läs mer om Red Hat-Uppdateringsinfrastruktur för Red Hat Enterprise Linux på begäran-instanser i Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: efc76616151776bc2f766f92ff9503413c6037d0
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774276"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Uppdateringsinfrastruktur för Red Hat för på begäran Red Hat Enterprise Linux-datorer i Azure
 [Uppdateringsinfrastruktur för Red Hat](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) gör att cloud-leverantörer, till exempel Azure för spegling av Red Hat-värdbaserade databasinnehåll, skapa anpassade databaser med Azure-specifika innehåll och gör den tillgänglig för slutanvändaren virtuella datorer.

Red Hat Enterprise Linux (RHEL) betala per användning (PAYG) avbildningar kommer förkonfigurerade för att komma åt Azure RHUI. Ingen ytterligare konfiguration krävs. För att få de senaste uppdateringarna, köra `sudo yum update` när din RHEL-instans är klar. Den här tjänsten ingår som en del av RHEL PAYG-programvaruavgifter.

Mer information om RHEL-avbildningar i Azure, inklusive publicerings-och bevarande principer, finns [här](./rhel-images.md).

Information om Red Hat support-principer för alla versioner av RHEL finns på sidan [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata) .

## <a name="important-information-about-azure-rhui"></a>Viktig information om Azure RHUI
* Azure RHUI är en uppdaterings infrastruktur som stöder alla RHEL PAYG-VM: ar som skapats i Azure. Detta hindrar dig inte från att registrera dina virtuella PAYG RHEL-datorer med prenumerations hanteraren eller satellit eller någon annan källa med uppdateringar, men om du gör det med en PAYG VM kommer den att leda till indirekt dubbel fakturering. Se följande punkt för mer information.
* Åtkomst till Azure som värd-RHUI ingår i priset för RHEL PAYG-avbildningen. Om du avregistrerar en PAYG RHEL virtuell dator från Azure som värd-RHUI konverterar som den virtuella datorn inte till en bring-your-own-license (BYOL) typ av virtuell dator. Om du har registrerat samma virtuella dator med en annan källa för uppdateringar kan roamingavgifter _indirekt_ dubbelklicka avgifter. Du debiteras för första gången för avgiften för Azure RHEL-programvara. Du debiteras den andra gången för Red Hat-prenumerationer som köpts tidigare. Om du ständigt behöver använda en annan uppdaterings infrastruktur än Azure-värdbaserade RHUI bör du överväga att registrera dig för att använda [RHEL BYOS](https://aka.ms/rhel-byos)-avbildningarna.
* Standard beteendet för RHUI är att uppgradera den virtuella RHEL-datorn till den senaste lägre versionen när `sudo yum update`du kör.

    Exempel: Om du vill etablera en virtuell dator från en RHEL 7.4 PAYG-avbildning och kör `sudo yum update`, att avslutas med en RHEL 7.6 virtuell dator (de senaste mindre versionen i RHEL7-serien).

    Du kan undvika det här problemet genom att växla till [utökade uppdateringar Support kanaler](#rhel-eus-and-version-locking-rhel-vms) eller skapa en egen avbildning enligt beskrivningen i artikeln [skapa och ladda upp en Red Hat-baserad virtuell dator för Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) . Om du skapar en egen avbildning måste du ansluta den till en annan uppdaterings infrastruktur ([direkt till Red Hat-innehålls leverans servrar](https://access.redhat.com/solutions/253273) eller en [Red Hat satellit-Server](https://access.redhat.com/products/red-hat-satellite)).



* RHEL SAP PAYG-avbildningar i Azure (RHEL för SAP, RHEL for SAP HANA och RHEL for SAP Business Applications) är anslutna till dedikerade RHUI-kanaler som finns kvar på den särskilda RHEL-lägre versionen som krävs för SAP-certifiering.

* Åtkomst till Azure-värdbaserade RHUI är begränsad till de virtuella datorerna inom de [Azure datacenter IP-adressintervall](https://www.microsoft.com/download/details.aspx?id=41653). Om du är proxy är alla VM-trafik via en lokal nätverksinfrastruktur, du kan behöva konfigurera användardefinierade vägar för RHEL PAYG virtuella datorer till Azure-RHUI.

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS och version – lås RHEL virtuella datorer
Vissa kunder kan vilja låsa sina virtuella RHEL-datorer till en viss RHEL-mindre version. Du kan konfigurera den virtuella RHEL-datorn till en viss del version genom att uppdatera databaserna så att de pekar på de utökade support databaserna för uppdatering. Du kan också återställa EUS-versions låsnings åtgärden.

>[!NOTE]
> EUS stöds inte för RHEL-tillägg. Det innebär att om du installerar ett paket som vanligt vis är tillgängligt från RHEL extra-kanalen, kan du inte göra det på EUS. Den Red Hat extra produkt livs cykeln beskrivs [här](https://access.redhat.com/support/policy/updates/extras/).

När detta skrivs är EUS-supporten avslutad för RHEL < = 7,3. Se avsnittet "Red Hat Enterprise Linux längre support tillägg" i [Red Hat-dokumentationen](https://access.redhat.com/support/policy/updates/errata/) för mer information.
* RHEL 7,4 EUS-support upphör 31 augusti 2019
* RHEL 7,5 EUS-support upphör 30 april 2020
* RHEL 7,6 EUS-support upphör den 31 oktober 2020

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Växla en virtuell RHEL-dator till EUS (versions låsning till en speciell del version)
Använd följande instruktioner för att låsa en RHEL VM till en viss del version (kör som rot):

>[!NOTE]
> Detta gäller endast för RHEL-versioner som EUS är tillgängligt för. När detta skrivs, omfattar detta RHEL 7,2-7.6. Mer information finns på sidan [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata) .

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
    > Ovanstående instruktion kommer att låsa den lägre RHEL-versionen till den aktuella del versionen. Ange en speciell del version om du vill uppgradera och låsa till en senare version som inte är den senaste. `echo 7.5 > /etc/yum/vars/releasever` Kommer till exempel att låsa din RHEL-version till RHEL 7,5

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

## <a name="azure-rhui-infrastructure"></a>Azure RHUI-infrastruktur


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Uppdatera har upphört att gälla RHUI-klientcertifikat på en virtuell dator

Om du använder en äldre RHEL VM-avbildning, till exempel RHEL 7,4 (avbildnings-urn `RedHat:RHEL:7.4:7.4.2018010506`:), kommer du att uppleva anslutnings problem till RHUI på grund av ett nu utgånget SSL-klientcertifikat. Felet som visas kan se ut som _"SSL-peer avvisade certifikatet som förfallet"_ eller _"fel: Det går inte att hämta metadata för databas (repomd. xml) för databasen:... Verifiera sökvägen och försök igen_. Du kan åtgärda det här problemet genom att uppdatera RHUI-klient paketet på den virtuella datorn med hjälp av följande kommando:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Om du kör `sudo yum update` kan du också uppdatera klient certifikat paketet (beroende på din RHEL-version), trots att "utgången SSL-certifikat" fel visas för andra lagrings platser. Om den här uppdateringen lyckas, ska normal anslutning till andra RHUI-lagringsplatser återställas så att du kan köra `sudo yum update` den.

Om du stöter på ett 404-fel när `yum update`du kör en kan du prova följande för att uppdatera yum-cachen:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Felsök anslutningsproblem till Azure RHUI
Om du får problem med att ansluta till Azure RHUI från Azure RHEL PAYG-VM, gör du följande:

1. Kontrollera konfigurationen för virtuell dator för Azure RHUI-slutpunkten:

    1. Kontrollera om den `/etc/yum.repos.d/rh-cloud.repo` filen innehåller en referens till `rhui-[1-3].microsoft.com` i den `baseurl` av den `[rhui-microsoft-azure-rhel*]` i filen. I annat fall använder du den nya Azure-RHUI.

    1. Om den pekar på en plats med följande mönster `mirrorlist.*cds[1-4].cloudapp.net`, krävs en konfigurationsuppdatering. Du använder den gamla VM-ögonblicksbilden och du måste du uppdatera den så att den pekar till den nya Azure-RHUI.

1. Åtkomst till Azure-värdbaserade RHUI är begränsad till virtuella datorer inom den [Azure datacenter IP-adressintervall](https://www.microsoft.com/download/details.aspx?id=41653).

1. Om du använder den nya konfigurationen har verifierat att den virtuella datorn ansluter från Azure-IP-adressintervall och fortfarande inte kan ansluta till Azure RHUI, filen ett supportärende med Microsoft eller Red Hat.

### <a name="infrastructure-update"></a>Infrastruktur uppdatering

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
* Om du vill veta mer om Red Hat-bilderna i Azure går du till [dokumentations sidan](./rhel-images.md).
* Information om Red Hat support-principer för alla versioner av RHEL finns på sidan [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata) .
