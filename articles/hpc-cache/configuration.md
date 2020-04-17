---
title: Konfigurera Azure HPC-cacheinställningar
description: Förklarar hur du konfigurerar ytterligare inställningar för cacheminnet som MTU och no-root-squash och hur du kommer åt expressögonblicksbilderna från Azure Blob-lagringsmål.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: 137e41a3f7d346bb612605660d7798ac2fc297d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538822"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Konfigurera ytterligare Azure HPC-cacheinställningar

**Konfigurationssidan** i Azure-portalen har alternativ för att anpassa flera inställningar. De flesta användare behöver inte ändra dessa från standardvärdena.

I den här artikeln beskrivs också hur du använder ögonblicksbildfunktionen för Azure Blob-lagringsmål. Ögonblicksbildfunktionen har inga konfigurerbara inställningar.

Öppna cachens **konfigurationssida** i Azure-portalen om du vill se inställningarna.

![skärmbild av konfigurationssidan i Azure Portal](media/configuration.png)

## <a name="adjust-mtu-value"></a>Justera MTU-värde
<!-- linked from troubleshoot-nas article -->

Du kan välja den maximala överföringsenhetsstorleken för cacheminnet med hjälp av den nedrullningsbara menyn **MTU-storlek**.

Standardvärdet är 1500 byte, men du kan ändra det till 1400.

> [!NOTE]
> Om du sänker cachens MTU-storlek kontrollerar du att klienter och lagringssystem som kommunicerar med cachen har samma MTU-inställning eller ett lägre värde.

Genom att sänka MTU-värdet för cacheminnet kan du komma runt begränsningar för paketstorlek i resten av cachens nätverk. Vissa VPN-nätverk kan till exempel inte överföra paket med full storlek på 1500 byte. Om du minskar storleken på paket som skickas via VPN kan problemet elimineras. Observera dock att en lägre MTU-cacheinställning innebär att alla andra komponenter som kommunicerar med cacheminnet - inklusive klienter och lagringssystem - också måste ha en lägre inställning för att undvika kommunikationsproblem med cachen.

Om du inte vill ändra MTU-inställningarna för andra systemkomponenter bör du inte sänka cachens MTU-inställning. Det finns andra lösningar för att komma runt VPN-paketstorleksbegränsningar. Läs [Justera begränsningar för VPN-paketstorlek](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) i nas-felsökningsartikeln om du vill veta mer om hur du diagnostiserar och åtgärdar det här problemet.

Läs mer om MTU-inställningar i virtuella Azure-nätverk genom att läsa [TCP/IP-prestandajustering för virtuella Azure-datorer](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="configure-root-squash"></a>Konfigurera rot squash
<!-- linked from troubleshoot -->

Inställningen **Aktivera rot squash** styr hur Azure HPC-cachen tillåter root-åtkomst. Root squash hjälper till att förhindra åtkomst på rotnivå från obehöriga klienter.

Med den här inställningen kan användare styra root-åtkomst på ``no_root_squash`` cachenivå, vilket kan kompensera för den inställning som krävs för NAS-system som används som lagringsmål. (Läs mer om [NFS-lagringsmålsförutsättning .)](hpc-cache-prereqs.md#nfs-storage-requirements) Det kan också förbättra säkerheten när den används med Azure Blob lagringsmål.

Standardinställningen är **Ja**. (Cacheminnen som skapats före april 2020 kan ha standardinställningen **Nej**.) När den här funktionen är aktiverad förhindrar den här funktionen också användning av UID-behörighetsbitar i klientbegäranden till cachen.

## <a name="view-snapshots-for-blob-storage-targets"></a>Visa ögonblicksbilder för blob-lagringsmål

Azure HPC Cache sparar automatiskt ögonblicksbilder av lagring för Azure Blob lagringsmål. Ögonblicksbilder ger en snabbreferenspunkt för innehållet i slutlagringsbehållaren. Ögonblicksbilder ersätter inte säkerhetskopiering av data och de innehåller ingen information om tillståndet för cachelagrade data.

> [!NOTE]
> Den här ögonblicksbildfunktionen skiljer sig från ögonblicksbildfunktionen som ingår i NetApp-, Isilon- eller ZFS-lagringsprogramvaran. Dessa ögonblicksbildimplementeringar töms ändringar från cacheminnet till backend-lagringssystemet innan ögonblicksbilden tas.
>
> För effektivitet rensar ögonblicksbilden av Azure HPC-cachen inte först och registrerar endast data som har skrivits till Blob-behållaren. Den här ögonblicksbilden representerar inte tillståndet för cachelagrade data, så de senaste ändringarna kan uteslutas.

Den här funktionen är endast tillgänglig för Azure Blob-lagringsmål och dess konfiguration kan inte ändras.

Ögonblicksbilder tas var åttonde timme, vid UTC 0:00, 08:00 och 16:00.

Azure HPC Cache lagrar dagliga, veckovisa och månatliga ögonblicksbilder tills de ersätts av nya. Gränserna är:

* upp till 20 dagliga ögonblicksbilder
* upp till 8 ögonblicksbilder per vecka
* upp till 3 månatliga ögonblicksbilder

Få tillgång till `.snapshot` ögonblicksbilder från katalogen i blob storage-målets namnområde.
