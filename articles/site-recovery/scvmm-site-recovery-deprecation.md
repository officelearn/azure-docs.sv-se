---
title: Kommande utfasning av DR mellan kundägda webbplatser med Hyper-V och mellan platser som hanteras av SCVMM till Azure | Microsoft Docs
description: Information om kommande utfasning av DR mellan kundägda webbplatser med Hyper-V och mellan platser som hanteras av SCVMM till Azure och alternativa alternativ
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/13/2019
ms.author: rajanaki
ms.openlocfilehash: 972223815810917684f35c4e99f04e1ab5b882c8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952126"
---
# <a name="upcoming-deprecation-of-dr-between-customer-owned-sites-using-hyper-v-and-between-sites-managed-by-scvmm-to-azure"></a>Kommande utfasning av DR mellan kundägda webbplatser med Hyper-V och mellan platser som hanteras av SCVMM till Azure

Den här artikeln beskriver den kommande avskrivnings planen, motsvarande konsekvenser och de alternativa alternativ som är tillgängliga för kunderna i följande scenarier som stöds av Azure Site Recovery. 

- DR mellan virtuella Hyper-V-datorer som hanteras av SCVMM mellan kundägda webbplatser 
- DR för virtuella Hyper-V-datorer som hanteras av SCVMM till Azure

> [!IMPORTANT]
> De här scenarierna är markerade för utfasning och kunder förväntas vidta åtgärder för att åtgärda det tidigaste för att undvika störningar i deras miljö. 
 

## <a name="what-changes-should-you-expect"></a>Vilka ändringar bör du förvänta dig?

- Med början från november 2019 kommer inga nya användare att vara på plan att tillåtas i dessa scenarier. **Befintliga replikeringar och hanterings åtgärder** , inklusive redundans, testning av redundans, övervakning osv. påverkas **inte**.

- När scenarierna är inaktuella kan du få följande konsekvenser om inte kunden följer de rekommenderade stegen.

    - DR mellan virtuella Hyper-V-datorer som hanteras av SCVMM mellan kundägda webbplatser: replikeringarna fortsätter att fungera eftersom den underliggande funktionen i Hyper-V-replikering fortsätter att fungera, men kunderna kan inte Visa, hantera eller utföra ny DR-relaterade åtgärder via Azures SIRET-återställnings upplevelse i Azure Portal. 
    - DR för virtuella Hyper-V-datorer som hanteras av SCVMM till Azure: befintliga replikeringar avbryts och kunder kan inte Visa, hantera eller utföra ny DR-relaterade åtgärder via Azure Site Recovery


## <a name="recommended-actions-to-be-taken"></a>Rekommenderade åtgärder som ska vidtas

Nedan visas alternativa alternativ som kunden måste se till att deras DR-strategi inte påverkas när scenariot är föråldrat. 

- Välj att [börja använda Azure som Dr-mål för virtuella datorer på Hyper-V-värdar](hyper-v-azure-tutorial.md).

> [!IMPORTANT]
> Observera att din lokala miljö fortfarande kan ha SCVMMM, men du konfigurerar ASR med referenser till Hyper-V-värdarna.

- Välj att fortsätta med plats-till-plats-replikering, men med den underliggande [lösningen för Hyper-V-replikering](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica), men kan inte hantera Dr-konfigurationer med Azure Site Recovery i Azure Portal. 


## <a name="next-steps"></a>Nästa steg
Planera för utfasningen och välj ett alternativt alternativ som passar din infrastruktur och ditt företag bäst. Om du har frågor om detta kan du kontakta Microsoft Support

