---
title: Krav för virtuell dator för Microsoft Azure | Azure Marketplace
description: Lista över krav som krävs för att publicera ett virtuellt dator erbjudande på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: a657ceaacf3680de54dc0d639a3f1a0aff6a6a03
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824424"
---
# <a name="virtual-machine-prerequisites"></a>Krav för virtuell dator

Den här artikeln innehåller både de tekniska och affärs krav som du måste uppfylla innan du kan publicera ett virtuellt dator erbjudande på [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).  Om du inte redan har gjort det går du igenom [publicerings guiden för virtuella datorer](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Tekniska krav

De tekniska kraven för att publicera en lösning för virtuella datorer (VM) är enkla:

- Du måste ha ett aktivt Azure-konto. Om du inte har någon kan du registrera dig på den [Microsoft Azure webbplatsen](https://azure.microsoft.com).  
- Du måste ha en miljö konfigurerad för att stödja Windows eller Linux VM-utveckling.  Mer information finns i den tillhör ande dokumentations webbplatsen för virtuella datorer:
    - [Dokumentation om virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Dokumentation om virtuella Windows-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Affärskrav

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
