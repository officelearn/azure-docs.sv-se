---
title: Felsök problem med kapacitets pooler för Azure NetApp Files | Microsoft Docs
description: Beskriver eventuella problem som kan uppstå när du hanterar kapacitets grupper och ger lösningar på problemen.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 0b5558501042dd7816202ea05b3a332b23400ff4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91345502"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Felsök problem med kapacitets pooler

I den här artikeln beskrivs lösningar på problem som du kan ha när du hanterar kapacitets grupper. 

## <a name="issues-creating-a-capacity-pool"></a>Problem med att skapa en pool för kapacitet

Kontrol lera att antalet kapacitets pooler inte överskrider gränsen. Se [resurs gränser för Azure NetApp Files](azure-netapp-files-resource-limits.md).  Om antalet är mindre än gränsen och du fortfarande stöter på problem, så kan du ange ett support ärende och ange poolens namn.

## <a name="issues-deleting-a-capacity-pool"></a>Problem med att ta bort en pool med kapacitet

Kontrol lera att du har tagit bort alla Azure NetApp Files volymer och ögonblicks bilder i prenumerationen där du försöker ta bort kapacitets gruppen.   

Om du redan har tagit bort alla volymer och ögonblicks bilder och du fortfarande inte kan ta bort kapacitets gruppen kan det fortfarande finnas referenser till resurser utan att visas i portalen. I det här fallet, File a Support Ticket och ange att du har utfört ovanstående rekommenderade steg. 

## <a name="volume-creation-or-modification-fails-with-requested-throughput-not-available-error"></a>Det gick inte att skapa eller ändra volymen eftersom det begärda data flödet inte är tillgängligt

Tillgängligt data flöde för en volym bestäms av poolens storlek och tjänst nivå. Om du inte har tillräckligt med data flöde bör du öka Poolens storlek eller justera det befintliga volym data flödet.


## <a name="next-steps"></a>Nästa steg  

* [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
* [Hantera en manuell pool för QoS-kapacitet](manage-manual-qos-capacity-pool.md)
