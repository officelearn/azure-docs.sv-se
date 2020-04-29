---
title: Krav för virtuell dator för Microsoft Azure | Azure Marketplace
description: Lista över krav som krävs för att publicera ett virtuellt dator erbjudande på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: 24c560aff9e8ba7ca0858fdb7fa11a59301c2360
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80277624"
---
# <a name="virtual-machine-prerequisites"></a>Krav för virtuell dator

Den här artikeln innehåller både de tekniska och affärs krav som du måste uppfylla innan du kan publicera ett virtuellt dator erbjudande på [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).  Om du inte redan har gjort det går du igenom [publicerings guiden för virtuella datorer](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Tekniska krav

De tekniska kraven för att publicera en lösning för virtuella datorer (VM) är enkla:

- Du måste ha ett aktivt Azure-konto. Om du inte har någon kan du registrera dig på den [Microsoft Azure webbplatsen](https://azure.microsoft.com).  
- Du måste ha en miljö konfigurerad för att stödja Windows eller Linux VM-utveckling.  Mer information finns i den tillhör ande dokumentations webbplatsen för virtuella datorer:
    - [Dokumentation för virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Dokumentation för virtuella Windows-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Affärs krav

Affärs kraven omfattar procedur mässiga, avtals liga och juridiska skyldigheter: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Du måste vara registrerad Cloud Marketplace-utgivare.  Om du inte har registrerat dig ännu följer du stegen i artikeln [bli en Cloud Marketplace-utgivare](https://docs.microsoft.com/azure/marketplace/become-publisher).

    > [!NOTE]
    > Du bör använda samma registrerings konto för Microsoft Developer Center för att logga in på [Cloud Partner Portal](https://cloudpartner.azure.com).
    > Du bör bara ha ett Microsoft-konto för dina Azure Marketplace-erbjudanden. Det bör inte vara specifikt för enskilda tjänster eller erbjudanden.
    
- Ditt företag (eller dess dotter bolag) måste finnas i en försäljnings-från-land/-region som stöds av Azure Marketplace.  En aktuell lista över dessa länder/regioner finns i [Microsoft Azure Marketplace deltagar principer](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- Produkten måste vara licensierad på ett sätt som är kompatibelt med fakturerings modeller som stöds av Azure Marketplace.  Mer information finns i [fakturerings alternativ i Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Du ansvarar för att göra teknisk support tillgänglig för kunderna på ett kommersiellt rimligt sätt. Detta stöd kan vara kostnads fritt, betalas eller via community-metoder.
- Du ansvarar för licensiering av program vara och program vara från tredje part.
- Du måste ange innehåll som uppfyller kriterierna för att ditt erbjudande ska visas på Azure Marketplace och i Azure Portal. <!-- TD: Meaning/links? -->
- Du måste samtycka till villkoren i [principerna för Microsoft Azure Marketplace deltagande](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) och utgivarens avtal.
- Du måste följa [användnings villkoren för Microsoft Azure webbplatsen](https://azure.microsoft.com/support/legal/website-terms-of-use/), [Microsofts sekretess policy](https://privacy.microsoft.com/privacystatement)och [Microsoft Azure certifierade program avtal](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>Nästa steg

När du har uppfyllt dessa krav kan du [skapa ett virtuellt dator erbjudande](./cpp-create-offer.md).
