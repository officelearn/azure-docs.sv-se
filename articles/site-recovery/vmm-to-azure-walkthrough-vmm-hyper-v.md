---
title: "Förbereda VMM för System Center för Hyper-V-replikering till Azure | Microsoft Docs"
description: "Beskriver hur du förbereder System Center VMM-servern för Hyper-V-replikering till Azure med Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: afcd81ae-d192-4013-a0af-3dac45b3c7e9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: ec118ed837dbf140083b3ae1e4ecd41c81562018
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2017
---
# <a name="step-6-prepare-vmm-servers-and-hyper-v-hosts-for-hyper-v-replication-to-azure"></a>Steg 6: Förbereda VMM-servrar och Hyper-V-värdar för Hyper-V-replikering till Azure

När du har installerat [Azure komponenter](vmm-to-azure-walkthrough-prepare-azure.md) för distributionen, följ instruktionerna i den här artikeln förbereda lokal VMM-servrar och Hyper-V-värdar att interagera med Azure Site Recovery.

När du har läst den här artikeln efter eventuella kommentarer längst ned eller tekniska frågor om den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-vmm-servers"></a>Förbereda VMM-servrar

- Du behöver minst en VMM-server som uppfyller kraven för stöd för Site Recovery replikering (site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
- Kontrollera att du har förberett VMM-server för [nätverksmappning](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure).
- Kontrollera att VMM-servern kan komma åt dessa URL: er

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- Om du har IP-adressbaserade brandväggsregler ontrollerar du att de tillåter kommunikation till Azure.
- Tillåt [Azure Datacenter IP Ranges](https://www.microsoft.com/download/confirmation.aspx?id=41653) (IP-intervall för Azures datacenter) och HTTPS-port 443.
- Tillåt IP-adressintervall för Azure-regionen för din prenumeration och för USA, västra (används för åtkomstkontroll och identitetshantering).

Under distributionen av Site Recovery hämta Site Recovery-providern och installera den på varje VMM-server. VMM-servern registreras i Recovery Services-valvet.




## <a name="next-steps"></a>Nästa steg

Gå till [steg 7: skapa ett valv](vmm-to-azure-walkthrough-create-vault.md)

