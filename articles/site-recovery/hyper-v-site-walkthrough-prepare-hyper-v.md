---
title: "Förbereda Hyper-V-värdar (utan VMM för System Center) för replikering till Azure | Microsoft Docs"
description: "Beskriver hur du förbereder Hyper-V-värdar för replikering till Azure med Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0f204e24-8d78-4076-95c5-8137d1be9c01
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: f9bcaa8e55be6e8fddaf88ebc3f18f5dbb2811e4
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="step-6-prepare-hyper-v-hosts-for-replication-to-azure"></a>Steg 6: Förbereda Hyper-V-värdar för replikering till Azure

Använd instruktionerna i den här artikeln för att förbereda lokala Hyper-V-värdar att interagera med Azure Site Recovery.

När du har läst den här artikeln efter eventuella kommentarer längst ned eller tekniska frågor om den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-hosts"></a>Förbered värdar

- Kontrollera att Hyper-V-värdar uppfyller de [krav](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-to-azure-no-vmm).
- Kontrollera att värdarna kan komma åt de nödvändiga URL: er:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- Om du har IP-adressbaserade brandväggsregler ontrollerar du att de tillåter kommunikation till Azure.
- Tillåt [Azure Datacenter IP Ranges](https://www.microsoft.com/download/confirmation.aspx?id=41653) (IP-intervall för Azures datacenter) och HTTPS-port 443.
- Tillåt IP-adressintervall för Azure-regionen för din prenumeration och för USA, västra (används för åtkomstkontroll och identitetshantering).

Under distributionen av Site Recovery kan du lägga till Hyper-V-värdar som innehåller virtuella datorer som du vill replikera till en Hyper-V-plats. Site Recovery-Provider och Recovery Services-agenten installeras på varje värd. Hyper-V-platsen har registrerats i Recovery Services-valvet.

## <a name="next-steps"></a>Nästa steg

Gå till [steg 7: skapa ett valv](hyper-v-site-walkthrough-create-vault.md)

