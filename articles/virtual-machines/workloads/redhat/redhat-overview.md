---
title: Red Hat-arbetsbelastningar på Azure-översikt | Microsoft Docs
description: Lär dig mer om Red Hat-produktsortimentet som är tillgängligt i Azure
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: df787d4102752bdf61e30bc00d0d08307ac12319
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473166"
---
# <a name="red-hat-workloads-on-azure"></a>Red Hat-arbetsbelastningar på Azure
Red Hat-arbetsbelastningar stöds via en mängd olika erbjudanden i Azure. Red Hat Enterprise Linux-avbildningar (RHEL) finns i kärnan av RHEL-arbetsbelastningar, precis som i Red Hat Update-infrastrukturen (RHUI).

## <a name="red-hat-enterprise-linux-rhel-images"></a>Red Hat Enterprise Linux-avbildningar (RHEL)
Azure erbjuder ett brett utbud av RHEL-avbildningar på Azure. De här avbildningarna görs tillgängliga via två olika licensierings modeller: betala per användning (PAYG) och ta-egen prenumeration (BYOS). Nya RHEL-avbildningar på Azure publiceras när nya RHEL-versioner släpps och uppdateras under hela livs cykeln vid behov.

### <a name="pay-as-you-go-payg-images"></a>PAYG-avbildningar (betala per användning)
Azure erbjuder en rad olika RHEL PAYG-bilder. De här avbildningarna kommer att ha rätt till RHEL och är kopplade till en källa med uppdateringar (Red Hat-uppdaterings infrastruktur). De här avbildningarna debiterar en Premium avgift för RHEL-rättigheterna och-uppdateringarna. RHEL PAYG avbildnings varianter är:
* Standard RHEL
* RHEL för SAP
* RHEL för SAP med hög tillgänglighet och uppdaterings tjänster.

Du kanske vill använda PAYG-avbildningarna om du inte vill oroa dig för att betala separat för lämpligt antal prenumerationer.

### <a name="bring-your-own-subscription-byos-images"></a>BYOS-avbildningar (Hämta egna prenumerationer)
Azure erbjuder också RHEL BYOS-avbildningar. De här avbildningarna kan vara användbara för kunder som har befintliga Red Hat-prenumerationer och vill använda dem i Azure. Du måste konvertera dina befintliga prenumerationer till moln åtkomst prenumerationer innan du kan använda dem i Azure. Åtkomst till de här avbildningarna beviljas endast när Red Hat verifierar att du har tillräckliga prenumerationer för moln åtkomst. Med de här avbildningarna kan en kund undvika dubbel fakturering som kan uppstå från att använda PAYG-avbildningar. Du kan begära åtkomst till BYOS-avbildningarna [här](https://aka.ms/rhel-byos).

> [!NOTE]
> Observera dubbel fakturering: dubbel fakturering uppstår när en användare betalar två gånger för RHEL-prenumerationer. Detta inträffar vanligt vis när en kund använder prenumerations hanteraren för att koppla en rättighet till en RHEL PAYG VM. En kund som till exempel använder prenumerations hanteraren för att ansluta en rättighet för SAP-paket på en RHEL PAYG-avbildning kommer indirekt att debiteras eftersom de kommer att betala två gånger för RHEL – en gång via PAYG Premium-avgiften och en gång via SAP-prenumerationen. Detta sker inte för BYOS avbildnings användare.

## <a name="red-hat-update-infrastructure-rhui"></a>Red Hat-uppdaterings infrastruktur (RHUI)
Azure tillhandahåller endast uppdaterings infrastruktur i Red Hat för virtuella PAYG RHEL-datorer (VM). RHUI är en spegling av Red Hat-CDN, men är bara tillgänglig för virtuella Azure PAYG RHEL-datorer. Du kommer att ha åtkomst till lämpliga paket beroende på vilken RHEL-avbildning som du har distribuerat. En RHEL för SAP-avbildning kommer till exempel att ha åtkomst till SAP-paketen förutom Base RHEL-paket.

### <a name="rhui-update-behavior"></a>RHUI uppdaterings beteende
RHEL-avbildningar som är anslutna till RHUI uppdateras som standard till den senaste lägre versionen av RHEL när en `yum update` körs. Det innebär att en virtuell RHEL 7,4-dator kan uppgraderas till RHEL 7,7 om en `yum update`-åtgärd körs på den. Detta är avsiktligt RHUI. Det här uppgraderings beteendet kan dock minskas genom att växla från vanliga RHEL-databaser till [EUs-lagringsplatsen (Extended Update Support)](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [RHEL-avbildningar på Azure](./redhat-images.md)
* Läs mer om [Red Hats uppdaterings infrastruktur](./redhat-rhui.md)
* Läs mer om [erbjudandet om RHEL BYOS](./redhat-byos.md)