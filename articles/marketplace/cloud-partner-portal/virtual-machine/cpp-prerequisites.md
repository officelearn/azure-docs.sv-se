---
title: Förutsättningar för virtuell dator för Microsoft Azure | Microsoft Docs
description: Lista över kraven för att publicera ett erbjudande för virtuell dator på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: 5c26fd3d6c2593217aef8588e2a58377a86412ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844048"
---
# <a name="virtual-machine-prerequisites"></a>Förutsättningar för virtuell dator

Den här artikeln innehåller både tekniska och affärskrav som du måste uppfylla innan du kan publicera ett erbjudande för virtuell dator till den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).  Om du inte redan har gjort det, kan du granska den [VM erbjuder publiceringsguide](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Tekniska krav

Tekniska krav för att publicera en virtuell dator (VM)-lösning är enkla:

- Du måste ha ett aktivt Azure-konto. Om du inte har någon kan du registrera dig på den [Microsoft Azure site](https://azure.microsoft.com).  
- Du måste ha en miljö som konfigurerats för att stödja utveckling för Windows eller Linux VM.  Mer information finns i den associerade VM dokumentationswebbplatsen:
    - [Dokumentation för Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Dokumentation för Windows-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Affärskrav

Affärskraven är procedurmässig avtalsenliga och juridiska skyldigheter: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Du måste vara ett registrerat moln Marketplace Publisher.  Om du inte är registrerad ännu, följer du stegen i artikeln [blir molnet Marketplace utgivare](../../become-publisher.md).

    > [!NOTE]
    > Du bör använda samma konto för registrering av Microsoft Developer Center för att logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com).
    > Du bör ha endast en Microsoft-konto för alla dina Azure Marketplace-erbjudanden. Det får inte vara specifika för enskilda tjänster eller erbjudanden.
    
- Ditt företag (eller dess filial) måste finnas i en sälj-från-stöds av Azure Marketplace.  En aktuell lista över dessa länder/regioner, se [Deltagandepolicyer för Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- Produkten måste licensieras på ett sätt som är kompatibel med faktureringsmodellerna som stöds av Azure Marketplace.  Mer information finns i [betalningsalternativ på Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Du ansvarar för att göra teknisk support tillgänglig för kunder på ett kommersiellt rimligt sätt. Detta stöd kan vara kostnadsfria, betald, eller via community-metoder.
- Du är ansvarig för licensiering för ditt program och eventuella beroenden för programvara från tredje part.
- Du måste ange innehåll som uppfyller villkoren för ditt erbjudande ska visas på Azure Marketplace och i Azure-portalen. <!-- TD: Meaning/links? -->
- Du måste godkänna villkoren i den [Deltagandepolicyer för Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) och avtalet för utgivare.
- Du måste följa den [Microsoft Azure av användningsvillkor för webbplatsen](https://azure.microsoft.com/support/legal/website-terms-of-use/), [Microsoft Privacy Statement](https://privacy.microsoft.com/privacystatement), och [Microsoft Azure Certified-programavtalet](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>Nästa steg

När du har uppfyllt dessa krav, kan du [skapa VM-erbjudande](./cpp-create-offer.md).
