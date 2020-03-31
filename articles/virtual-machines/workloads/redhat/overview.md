---
title: Red Hat-arbetsbelastningar på Azure översikt | Microsoft-dokument
description: Läs mer om red hat-produkterbjudanden som finns tillgängliga på Azure.
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 718447e1dbf597af4349eab0be78a2bb544dec90
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78970178"
---
# <a name="red-hat-workloads-on-azure"></a>Red Hat-arbetsbelastningar på Azure

Red Hat-arbetsbelastningar stöds via en mängd olika erbjudanden på Azure. Red Hat Enterprise Linux (RHEL) avbildningar är kärnan i RHEL-arbetsbelastningar, liksom Red Hat Update Infrastructure (RHUI).

## <a name="red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux-bilder

Azure erbjuder ett brett utbud av RHEL-avbildningar på Azure. Dessa bilder görs tillgängliga via två olika licensieringsmodeller: betala per användning och bring-your-own-prenumeration (BYOS). Nya RHEL-avbildningar på Azure publiceras när nya RHEL-versioner släpps och uppdateras under hela livscykeln, efter behov.

### <a name="pay-as-you-go-images"></a>Bilder från du ska betala per du ska

Azure erbjuder en mängd olika RHEL-avbildningar för betalning via ning. Dessa bilder har rätt till RHEL och är kopplade till en uppdateringskälla (Red Hat Update Infrastructure). Dessa bilder tar ut en premieavgift för RHEL-rättigheten och uppdateringarna. RHEL pay-as-you-go-bildvarianter inkluderar:

* Standard RHEL.
* RHEL för SAP.
* RHEL för SAP med hög tillgänglighet och uppdateringstjänster.

Du kanske vill använda pay-as-you-go-bilder om du inte vill oroa dig för att betala separat för lämpligt antal prenumerationer.

### <a name="red-hat-gold-images"></a>Red Hat Guld Bilder

Azure erbjuder också Red`rhel-byos`Hat Gold Images ( ). Dessa avbildningar kan vara användbara för kunder som har befintliga Red Hat-prenumerationer och vill använda dem i Azure. Du måste aktivera dina befintliga Red Hat-prenumerationer för Red Hat Cloud Access innan du kan använda dem i Azure. Åtkomst till dessa avbildningar beviljas automatiskt när dina Red Hat-prenumerationer är aktiverade för Cloud Access och uppfyller behörighetskraven. Med hjälp av dessa bilder kan en kund undvika dubbel fakturering som kan uppstå från att använda pay-as-you-go-bilder.
* Lär dig hur du [aktiverar dina Red Hat-prenumerationer för Cloud Access med Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs).
* Lär dig hur du [hittar Red Hat Gold-avbildningar i Azure-portalen, Azure CLI eller PowerShell cmdlet](./byos.md).

> [!NOTE]
> Dubbel fakturering uppstår när en användare betalar två gånger för RHEL-prenumerationer. Det här scenariot inträffar vanligtvis när en kund använder Red Hat Subscription-Manager för att bifoga ett berättigande för en virtuell dator med abonnemang för RHEL. En kund som använder Subscription-Manager för att bifoga ett berättigande för SAP-paket på en RHEL-avbildning för användningsbaserad betalning debiteras indirekt eftersom de betalar två gånger för RHEL. De betalar en gång genom pay-as-you-go premie avgift och en gång genom sin SAP-prenumeration. Det här scenariot händer inte BYOS-bildanvändare.

### <a name="generation-2-images"></a>Generation 2-bilder

Generation 2 virtuella datorer (VMs) ger några nyare funktioner jämfört med Generation 1 virtuella datorer. Mer information finns i [dokumentationen generation 2](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2). Den viktigaste skillnaden från en RHEL bild perspektiv är att Generation 2 virtuella datorer använder en UEFI i stället för BIOS firmware gränssnitt. De använder också en GPT (GUID Partition Table) i stället för en mbr-startpost (Master Boot Record) vid starttiden. Användning av en GPT möjliggör bland annat OS diskstorlekar större än 2 TB. Dessutom körs [virtuella datorer i Mv2-serien](../../mv2-series.md) endast på Generation 2-avbildningar.

RHEL Generation 2-avbildningar är tillgängliga på Azure Marketplace. Leta efter "gen2" i avbildningen SKU i listan över alla avbildningar som visas när du använder Azure CLI. Gå till fliken **Avancerat** i den virtuella datorns distributionsprocess för att distribuera en virtuell generation 2-dator.

## <a name="red-hat-update-infrastructure"></a>Red Hat Update Infrastructure

Azure tillhandahåller Red Hat Update-infrastruktur endast för virtuella RHEL-datorer som gäller för betalning per dun. RHUI är i själva verket en spegel av Red Hat-CDN:erna men är endast tillgänglig för Azure-virtuella RHEL-datorer. Du har tillgång till lämpliga paket beroende på vilken RHEL-avbildning du har distribuerat. En RHEL för SAP-avbildning har till exempel åtkomst till SAP-paketen utöver bas-RHEL-paket.

### <a name="rhui-update-behavior"></a>RHUI-uppdateringsbeteende

RHEL-bilder som är anslutna till RHUI uppdateras som `yum update` standard till den senaste delversionen av RHEL när en körs. Det här problemet innebär att en virtuell dator med RHEL 7.4 `yum update` kan uppgraderas till RHEL 7.7 om en åtgärd körs på den. Detta är avsiktligt för RHUI. Om du vill minska det här uppgraderingsbeteendet växlar du från vanliga RHEL-databaser till [utökade supportdatabaser](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)för uppdatering .

## <a name="next-steps"></a>Nästa steg

* Läs mer om [RHEL-avbildningar på Azure](./redhat-images.md).
* Läs mer om [Red Hat Update Infrastructure](./redhat-rhui.md).
* Läs mer om [Red Hat`rhel-byos`Gold Image ( ) erbjudande](./byos.md).
