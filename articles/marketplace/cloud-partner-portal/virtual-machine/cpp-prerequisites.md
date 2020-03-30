---
title: Förutsättningar för virtuella datorer för Microsoft Azure | Azure Marketplace
description: Lista över förutsättningar som krävs för att publicera ett VM-erbjudande till Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: 24c560aff9e8ba7ca0858fdb7fa11a59301c2360
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277624"
---
# <a name="virtual-machine-prerequisites"></a>Förutsättningar för virtuella datorer

I den här artikeln visas både de tekniska och affärsmässiga krav som du måste uppfylla innan du kan publicera ett VM-erbjudande till [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).  Om du inte redan har gjort det läser du [publiceringsguiden för virtuell dator.](../../marketplace-virtual-machines.md)


## <a name="technical-requirements"></a>Tekniska krav

De tekniska förutsättningarna för att publicera en virtuell datorlösning (VM) är enkla:

- Du måste ha ett aktivt Azure-konto. Om du inte har någon kan du registrera dig på [Microsoft Azure-webbplatsen](https://azure.microsoft.com).  
- Du måste ha en miljö konfigurerad för att stödja utveckling av antingen Windows eller Linux VM.  Mer information finns på den associerade vm-dokumentationsplatsen:
    - [Dokumentation för virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Dokumentation för virtuella Windows-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Affärskrav

Affärskraven omfattar processuella, avtalsenliga och rättsliga skyldigheter: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Du måste vara registrerad Cloud Marketplace Publisher.  Om du inte är registrerad ännu följer du stegen i artikeln [Bli en Cloud Marketplace Publisher](https://docs.microsoft.com/azure/marketplace/become-publisher).

    > [!NOTE]
    > Du bör använda samma registreringskonto för Microsoft Developer Center för att logga in på [Cloud Partner Portal](https://cloudpartner.azure.com).
    > Du bör bara ha ett Microsoft-konto för dina Azure Marketplace-erbjudanden. Det bör inte vara specifikt för enskilda tjänster eller erbjudanden.
    
- Ditt företag (eller dess dotterbolag) måste finnas i en sälj-från-land/region som stöds av Azure Marketplace.  En aktuell lista över dessa länder/regioner finns i [Microsoft Azure Marketplace Participation Policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- Din produkt måste vara licensierad på ett sätt som är kompatibelt med faktureringsmodeller som stöds av Azure Marketplace.  Mer information finns [i Faktureringsalternativ på Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Du är ansvarig för att göra teknisk support tillgänglig för kunder på ett kommersiellt rimligt sätt. Detta stöd kan vara gratis, betalas, eller genom gemenskap metoder.
- Du är ansvarig för licensiering av din programvara och eventuella tredjepartsprogramberoenden.
- Du måste ange innehåll som uppfyller kriterierna för att ditt erbjudande ska visas på Azure Marketplace och i Azure-portalen. <!-- TD: Meaning/links? -->
- Du måste godkänna villkoren i [Microsoft Azure Marketplace Participation Policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) och Publisher Agreement.
- Du måste följa [användarvillkoren för Microsoft Azure,](https://azure.microsoft.com/support/legal/website-terms-of-use/) [Microsofts sekretesspolicy](https://privacy.microsoft.com/privacystatement)och [Microsoft Azure Certified Program Agreement](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>Nästa steg

När du har uppfyllt dessa förutsättningar kan du [skapa ditt VM-erbjudande](./cpp-create-offer.md).
