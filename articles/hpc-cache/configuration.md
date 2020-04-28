---
title: Konfigurera inställningar för Azure HPC-cache
description: Förklarar hur du konfigurerar ytterligare inställningar för cachen som MTU och No-root-squash, samt hur du får åtkomst till Express-ögonblicksbilder från Azure Blob Storage-mål.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: v-erkel
ms.openlocfilehash: 7938fcc0819fc3e5e0762cc8c3c2931594ed1c68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195068"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Konfigurera ytterligare inställningar för Azure HPC cache

**Konfigurations** sidan i Azure Portal har alternativ för att anpassa flera inställningar. De flesta användare behöver inte ändra dessa inställningar från standardvärdena.

Den här artikeln beskriver också hur du använder ögonblicks bild funktionen för Azure Blob Storage-mål. Ögonblicks bild funktionen har inga konfigurerbara inställningar.

Om du vill se inställningarna öppnar du cachens **konfigurations** sida i Azure Portal.

![skärm bild av konfigurations sidan i Azure Portal](media/configuration.png)

## <a name="adjust-mtu-value"></a>Justera MTU-värde
<!-- linked from troubleshoot-nas article -->

Du kan välja den maximala storleken för överförings enheter för cacheminnet med hjälp av den nedrullningsbara menyn med namnet **MTU-storlek**.

Standardvärdet är 1500 byte, men du kan ändra det till 1400.

> [!NOTE]
> Om du sänker cachens MTU-storlek kontrollerar du att de klienter och lagrings system som kommunicerar med cachen har samma MTU-inställning eller ett lägre värde.

Att sänka cachens MTU-värde kan hjälpa dig att komma runt paket storleks begränsningar i resten av cacheminnets nätverk. Vissa VPN kan till exempel inte överföra paket med full storlek 1500 byte. Att minska storleken på paket som skickas via VPN kan eliminera det problemet. Observera dock att en lägre cache-MTU-inställning innebär att alla andra komponenter som kommunicerar med cachen, inklusive klienter och lagrings system, måste också ha en lägre MTU-inställning för att undvika kommunikations problem.

Om du inte vill ändra MTU-inställningarna på andra system komponenter bör du inte minska cachens MTU-inställning. Det finns andra lösningar för att undvika storleks begränsningar för VPN-paket. Läs [ändra storleks begränsningar för VPN-paket](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) i fel söknings artikeln för NAS för att lära dig mer om att diagnostisera och lösa det här problemet.

Läs mer om MTU-inställningar i virtuella Azure-nätverk genom [att läsa TCP/IP-prestanda för virtuella Azure-datorer](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="configure-root-squash"></a>Konfigurera rot-squash
<!-- linked from troubleshoot -->

Inställningen **Aktivera rot squash** styr hur Azure HPC cache tillåter rot åtkomst. Rot-squash hjälper till att förhindra åtkomst till rot nivå från obehöriga klienter.

Med den här inställningen kan användare kontrol lera rot åtkomst på cachenivå, vilket kan hjälpa dig att ``no_root_squash`` kompensera för den nödvändiga inställningen för de NAS-system som används som lagrings mål. (Läs mer om [NFS-mål krav för lagring](hpc-cache-prereqs.md#nfs-storage-requirements).) Det kan också förbättra säkerheten när den används med Azure Blob Storage-mål.

Standardinställningen är **Ja**. (Cacheminnen som skapats före april 2020 kan ha standardvärdet **Nej**.)

När den här funktionen är aktive rad förhindrar den här funktionen också att en Set-UID-behörighet används i klient begär anden till cacheminnet.

## <a name="view-snapshots-for-blob-storage-targets"></a>Visa ögonblicks bilder för Blob Storage-mål

Azure HPC cache sparar automatiskt lagrings ögonblicks bilder för Azure Blob Storage-mål. Ögonblicks bilder tillhandahåller en snabb referens punkt för innehållet i backend storage-behållaren.

Ögonblicks bilder är inte en ersättning för säkerhets kopiering av data, och de innehåller ingen information om statusen för cachelagrade data.

> [!NOTE]
> Den här ögonblicks bild funktionen skiljer sig från ögonblicks bild funktionen som ingår i NetApp eller Isilon Storage-programvara. De här ögonblicks bilds implementeringarna tömmer ändringar från cachen till Server delens lagrings system innan du tar ögonblicks bilden.
>
> För att vara effektiv tömmer Azure HPC cache-ögonblicksbilden ändringar först och registrerar bara data som har skrivits till BLOB-behållaren. Den här ögonblicks bilden representerar inte statusen för cachelagrade data, så den kanske inte innehåller de senaste ändringarna.

Den här funktionen är endast tillgänglig för Azure Blob Storage-mål och dess konfiguration kan inte ändras.

Ögonblicks bilder tas var åttonde timme, kl. UTC 0:00, 08:00 och 16:00.

Azure HPC-cache lagrar dagliga, veckovis och månatliga ögonblicks bilder tills de ersätts av nya. Gränserna är:

* upp till 20 dagliga ögonblicks bilder
* upp till 8 veckors ögonblicks bilder
* upp till tre månatliga ögonblicks bilder

Få åtkomst till ögonblicks bilderna `.snapshot` från katalogen i Blob Storage-målets namnrymd.
