---
title: Vanliga frågor och svar om Azure konfidentiell data behandling
description: Svar på vanliga frågor om Azures konfidentiella data behandling.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: 2bd9430fc6f48d72faa2c1850af0bb8432a7c5f5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82149507"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Vanliga frågor och svar om Azures konfidentiella data behandling

Den här artikeln innehåller svar på några av de vanligaste frågorna om att köra [konfidentiella data behandlings arbets belastningar på Azure](overview.md).

Om ditt Azure-problem inte åtgärdas i den här artikeln kan du gå till Azure-forumen på [MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera ditt problem i dessa forum eller publicera [ @AzureSupport på Twitter](https://twitter.com/AzureSupport). Du kan också skicka in en support förfrågan för Azure. Om du vill skicka en supportbegäran väljer du hämta support på [Sidan Support för Azure](https://azure.microsoft.com/support/options/).

## <a name="confidential-computing-virtual-machines"></a>Konfidentiell bearbetning Virtual Machines<a id="vm-faq"></a>

1. **Hur kan du börja distribuera virtuella datorer i DCsv2-serien?**

   Här följer några exempel på hur du kan distribuera en virtuell DCsv2-dator:
   - Använda en [Azure Resource Manager mall](../virtual-machines/windows/template-description.md)
   - Från [Azure Portal](https://portal.azure.com/#create/hub)
   - I [Azures lösning för konfidentiell beräkning (virtuell dator)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) Marketplace. Med mallen Marketplace-lösning kan du begränsa en kund till de scenarier som stöds (regioner, avbildningar, tillgänglighet, disk kryptering). 

1. **Kommer alla OS-avbildningar att fungera med Azures konfidentiella data behandling?**

   Nej. De virtuella datorerna kan bara distribueras på virtuella datorer i generation 2. Vi erbjuder generation 2-stöd för Ubuntu Server 18,04, Ubuntu Server 16,04 och Windows Server 2016 Data Center. Läs mer om generation 2 virtuella datorer i [Linux](../virtual-machines/linux/generation-2.md) och [Windows](../virtual-machines/windows/generation-2.md)

1. **DCsv2 virtuella datorer är nedtonade i portalen och jag kan inte välja en**

   Utifrån informations bubblan bredvid den virtuella datorn finns det olika åtgärder att vidta:
    -   **UnsupportedGeneration**: ändra avbildningen av den virtuella dator avbildningen till "Gen2".
    -   **NotAvailableForSubscription** : regionen är inte tillgänglig ännu för din prenumeration. Välj en tillgänglig region.
    -   **InsufficientQuota**: [skapa en support förfrågan för att öka din kvot](../azure-portal/supportability/per-vm-quota-requests.md). De kostnads fria utvärderings prenumerationerna har ingen kvot för konfidentiella datorer. 

1. **DCsv2 virtuella datorer visas inte när jag försöker söka efter dem i väljaren för Portal storlek**

   Kontrol lera att du har valt en tillgänglig region. Kontrol lera också att du väljer "Rensa alla filter" i storleks väljaren. 

1. **Vad är skillnaden mellan virtuella datorer i DCsv2-serien och DC-serien?**

   Virtuella datorer i DC-serien körs på äldre 6-kärnors Intel-processorer med SGX. Detta har mindre total minne, mindre EPC-minne (enklaven Page bur) och är tillgängliga i mindre regioner. De här virtuella datorerna är endast tillgängliga i östra USA och Västeuropa är tillgängliga i två storlekar: Standard_DC2s och Standard_DC4s. De går inte att använda GA och kan bara distribueras i den [konfidentiella Compute DC-serien VM [Preview] Marketplace-](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) instans

1. **Är virtuella DCsv2-datorer tillgängliga globalt?**

   Nej, de här virtuella datorerna är bara tillgängliga i utvalda regioner. På [sidan produkter per region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) finns de senaste tillgängliga regionerna. 

1. **Hur gör jag för att installerar du Open enklaven SDK?**
   
   Instruktioner om hur du installerar OE SDK på en dator oavsett om det finns i Azure eller lokalt, följer du anvisningarna på [Öppna ENKLAVEN SDK-GitHub](https://github.com/openenclave/openenclave).
     
   Du kan också öppna enklaven SDK-GitHub för att installera OS-information:
     - [Installera OE SDK i Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
     - [Installera OE SDK på Ubuntu 18,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
     - [Installera OE SDK på Ubuntu 16,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)
