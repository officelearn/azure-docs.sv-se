---
title: Uppgradering på plats av Red Hat Enterprise Linux avbildningar på Azure
description: Hitta steg för att utföra en uppgradering på plats från Red Hat Enterprise 7. x-bilder till senaste 8. x-version
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: beede74134affeb3ee0d4bdd20d5da3b4c5e6eda
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566630"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux uppgraderingar på plats

Den här artikeln innehåller stegvisa instruktioner om hur du utför en uppgradering på plats från Red Hat Enterprise Linux 7 till Red Hat Enterprise Linux 8 med hjälp av verktyget "Leapp" i Azure. Under uppgraderingen på plats ersätts det befintliga RHEL 7-operativsystemet av RHEL 8-versionen.

>[!Note] 
> SQL Server på Red Hat Enterprise Linux-erbjudanden stöder inte uppgradering på plats på Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Vad som ska förväntas under uppgraderingen
Systemet startas om några gånger under uppgraderingen och är normalt. Den senaste omstarten kommer att uppgradera den virtuella datorn till den senaste mindre versionen av RHEL 8. 

Uppgraderings processen kan ta var som helst från 20 minuter till några timmar, beroende på flera faktorer som VM-storlek och antalet paket som har installerats i systemet.

## <a name="preparations-for-the-upgrade"></a>Förberedelser för uppgradering
Uppgradering på plats är det som rekommenderas av Red Hat och Azure, vilket gör det möjligt för kunder att uppgradera systemet till nästa högre version. Innan du utför uppgraderingen här är några saker som du bör känna till och ta hänsyn till. 

>[!Important] 
> Ta en ögonblicks bild av avbildningen innan du utför uppgraderingen.

>[!NOTE]
> Kommandona i den här artikeln måste köras med rot kontot

* Se till att du använder den senaste versionen av RHEL 7, som för närvarande är RHEL 7,9. Om du använder en låst version och inte kan uppgradera till RHEL 7,9 kan du använda [stegen här för att växla till en icke-EUs-lagringsplats](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Kör kommandot nedan för att se hur din uppgradering går och om den ska slutföras. Kommandot ska generera en fil under "/var/log/leapp/leapp-report.txt" som förklarar processen och vad som görs och om uppgraderingen är möjlig eller inte
    ```bash
    leapp preupgrade --no-rhsm
    ```
* Se till att serie konsolen fungerar som detta möjliggör övervakning under uppgraderings processen.

* Aktivera SSH-rot åtkomst i `/etc/ssh/sshd_config`
    1. Öppna filen `/etc/ssh/sshd_config`
    1. Sök efter "#PermitRootLogin ja"
    1. Ta bort ' # ' för att ta bort kommentar

## <a name="steps-for-performing-the-upgrade"></a>Steg för att utföra uppgraderingen

Utför de här stegen noggrant. Vi rekommenderar att du testar uppgraderingen på en test dator innan du kör produktions instanser.

1. Utför en yum-uppdatering för att hämta de senaste klient paketen.
    ```bash
    yum update -y
    ```

1. Installera leapp-client-Package.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. Använd filen leapp-data. tar. gz med repomap.csv och pes-events.jspå, som finns i [RedHat-portalen](https://access.redhat.com/articles/3664871)och extrahera dem. 
    1. Hämta filen.
    1. Extrahera innehållet och ta bort filen med följande kommando:
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. Lägg till "svars fil" för "Leapp".
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. Genomför Leapp-uppgraderingen.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  När `leapp upgrade` kommandot har slutförts startar du om systemet manuellt för att slutföra processen. Systemet startas om ett par gånger då det inte är tillgängligt. Övervaka processen med hjälp av serie konsolen.

1.  Verifiera att uppgraderingen har slutförts.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. Ta bort rot-SSH-åtkomst när uppgraderingen är klar.
    1. Öppna filen `/etc/ssh/sshd_config`
    1. Sök efter "#PermitRootLogin ja"
    1. Lägg till kommentaren ' # '

1. Starta om sshd-tjänsten för att ändringarna ska börja gälla
    ```bash
    systemctl restart sshd
    ```

## <a name="common-issues"></a>Vanliga problem
Detta är några av de vanliga instanserna som antingen är `leapp preupgrade` eller `leapp upgrade` kan inte utföras.

**Fel: inga matchningar hittades för följande inaktiverade plugin-mönster**
```plaintext
STDERR:
No matches found for the following disabled plugin patterns: subscription-manager
Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
```
**Lösning**\
Inaktivera plugin-programmet för prenumerations hanteraren genom att redigera filen `/etc/yum/pluginconf.d/subscription-manager.conf` och ändra den till `enabled=0` .

Detta beror på att yum-plugin-programmet för prenumerations hanteraren har Aktiver ATS, vilket inte används för virtuella PAYG-datorer.

**Fel: möjliga problem med fjärrinloggning med roten** Det `leapp preupgrade` gick inte att utföra med följande fel:
```structured-text
============================================================
                     UPGRADE INHIBITED
============================================================

Upgrade has been inhibited due to the following problems:
    1. Inhibitor: Possible problems with remote login using root account
Consult the pre-upgrade report for details and possible remediation.

============================================================
                     UPGRADE INHIBITED
============================================================
```
**Lösning**\
Aktivera rot åtkomst i `/etc/sshd_conf` .
Detta beror på att inte aktivera åtkomst till rot-ssh i `/etc/sshd_conf` som i avsnittet "[förberedelser för uppgradering](#preparations-for-the-upgrade)". 

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Red Hat-avbildningarna i Azure](./redhat-images.md).
* Läs mer om den [Red Hat-uppdateringen av infrastrukturen](./redhat-rhui.md).
* Läs mer om [RHEL BYOS-erbjudandet](./byos.md).
* Information om uppgraderings processer för Red Hat på plats finns i Red Hat-dokumentationen, [som uppgraderas från RHEL 7 till RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)
* Information om Red Hat support-principer för alla versioner av RHEL finns på sidan [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata) .