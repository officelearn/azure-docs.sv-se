---
title: Uppgradering på plats av Red Hat Enterprise Linux avbildningar på Azure
description: Hitta steg för att utföra en uppgradering på plats från Red Hat Enterprise 7. x-bilder till senaste 8. x-version
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 48884e6faa5f26f027c772b44d5f960979a40d1d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447903"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux uppgraderingar på plats

Den här artikeln innehåller stegvisa instruktioner om hur du utför en uppgradering på plats från Red Hat Enterprise Linux 7 till Red Hat Enterprise Linux 8 med hjälp av verktyget "Leapp" i Azure. Under uppgraderingen på plats ersätts det befintliga RHEL 7-operativsystemet av RHEL 8-versionen.

>[!Note] 
> SQL Server på Red Hat Enterprise Linux-erbjudanden stöder inte uppgradering på plats på Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Vad som ska förväntas under uppgraderingen
Systemet startas om några gånger under uppgraderingen och är normalt. Den senaste omstarten kommer att uppgradera den virtuella datorn till den senaste mindre versionen av RHEL 8.

## <a name="preparations-for-the-upgrade"></a>Förberedelser för uppgradering
Uppgraderingar på plats är det som rekommenderas av Red Hat och Azure, vilket gör det möjligt för kunder att uppgradera systemet till nästa högre version. Innan du utför uppgraderingen här är några saker som du bör känna till och ta hänsyn till. 

>[!Important] 
> Ta en ögonblicks bild av avbildningen innan du utför uppgraderingen.

>[!NOTE]
> Kommandona i den här artikeln måste köras med rot kontot

* Se till att du använder den senaste versionen av RHEL 7, som för närvarande är RHEL 7,9. Om du använder en låst version och inte kan uppgradera till RHEL 7,9 kan du använda [stegen här för att växla till en icke-EUs-lagringsplats](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Kör kommandot nedan för att se hur din uppgradering går och om den ska slutföras. Kommandot ska generera en fil under "/var/log/leapp/leapp-report.txt" som förklarar processen och vad som görs och om uppgraderingen är möjlig eller inte
    ```bash
    leapp preupgrade --no-rhsm
    ```

## <a name="steps-for-performing-the-upgrade"></a>Steg för att utföra uppgraderingen

Utför de här stegen noggrant. Vi rekommenderar att du testar uppgraderingen på en test dator innan du kör produktions instanser.

1. Utför en yum-uppdatering för att hämta de senaste klient paketen.
    ```bash
    yum update
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
    
1. Aktivera PermitRootLogin i/etc/ssh/-sshd_config
    1. Öppna filen/etc/ssh/sshd_config
    1. Sök efter "#PermitRootLogin ja"
    1. Ta bort ' # ' för att ta bort kommentar



1. Genomför Leapp-uppgraderingen.
    ```bash
    leapp upgrade --no-rhsm
    ```
1. Starta om sshd-tjänsten för att ändringarna ska börja gälla
    ```bash
    systemctl restart sshd
    ```
1. Kommentera ut PermitRootLogin i/etc/ssh/sshd_config igen
    1. Öppna filen/etc/ssh/sshd_config
    1. Sök efter "#PermitRootLogin ja"
    1. Lägg till kommentaren ' # '

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Red Hat-avbildningarna i Azure](./redhat-images.md).
* Läs mer om den [Red Hat-uppdateringen av infrastrukturen](./redhat-rhui.md).
* Läs mer om [RHEL BYOS-erbjudandet](./byos.md).
* Information om uppgraderings processer för Red Hat på plats finns i Red Hat-dokumentationen, [som uppgraderas från RHEL 7 till RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)
* Information om Red Hat support-principer för alla versioner av RHEL finns på sidan [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata) .