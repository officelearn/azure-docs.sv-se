---
title: Krypteringsfunktionen för infasning av Azure Site Recovery data | Microsoft-dokument
description: Information om kryptering av Azure Site Recovery-data
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74135001"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Krypteringsfunktionen för datakryptering av webbplatsåterställning

I det här dokumentet beskrivs information om utfasning och den åtgärd som du behöver vidta om du använder krypteringsfunktionen för kryptering av webbplatsåterställningsdata samtidigt som du konfigurerar haveriberedskap av virtuella Hyper-V-datorer till Azure. 

## <a name="deprecation-information"></a>Information om utfasning


Krypteringsfunktionen för kryptering av platsåterställning var tillgänglig för kunder som skyddar Virtuella Hyper-V-datorer för att säkerställa att replikerade data skyddades mot säkerhetshot. den här funktionen kommer att vara föråldrad senast **den 30 december 2019**. Den ersätts av den mer avancerade [funktionen Kryptering i vila,](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) som använder [SSE (Storage Service Encryption).](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) Med SSE krypteras data innan de sparas till lagring och dekrypteras vid hämtning, och vid redundans till Azure körs dina virtuella datorer från de krypterade lagringskontona, vilket möjliggör ett förbättrat återställningstidsmål (RTO).

Observera att om du är en befintlig kund som använder den här funktionen, skulle du ha fått kommunikation med deprecation detaljer och reparation steg. 


## <a name="what-are-the-implications"></a>Vilka är konsekvenserna?

Efter **den 30 december 2019**tillåts inte alla virtuella datorer som fortfarande använder den pensionerade krypteringsfunktionen att utföra redundans. 

## <a name="required-action"></a>Nödvändig åtgärd
För att fortsätta lyckade redundansåtgärder och replikeringar följer stegen nedan:

Följ dessa steg för varje virtuell dator: 
1.  [Inaktivera replikering](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Skapa en ny replikeringsprincip](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy).
3.  [Aktivera replikering](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication) och välj ett lagringskonto med SSE aktiverat.

När du har slutfört den första replikeringen till lagringskonton med SSE aktiverat, kommer dina virtuella datorer att använda Kryptering i vila med Azure Site Recovery.


## <a name="next-steps"></a>Nästa steg
Planera för att utföra reparationsstegen och kör dem tidigast. Om du har några frågor om den här utfasningen, vänligen kontakta Microsoft Support. Mer information om Hyper-V till Azure-scenario finns [här](hyper-v-vmm-architecture.md).

