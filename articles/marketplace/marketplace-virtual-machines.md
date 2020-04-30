---
title: Publicerings guide för virtuella datorer tillhandahåller publicerings guide för Azure Marketplace
description: I den här artikeln beskrivs kraven för att publicera en virtuell dator och en kostnads fri utvärderings version av program vara som ska distribueras från Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 2fa67d81546db86535c179a9c59d0602c1175cba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687500"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Publicerings guide för virtuell dator erbjudande

Avbildningar av virtuella datorer är ett av de största sätten att publicera en lösning på Azure Marketplace. Använd den här guiden för att förstå kraven för det här erbjudandet. 

Detta är transaktions erbjudanden som distribueras och faktureras via Marketplace. Anropet till åtgärden som en användare ser är "Hämta nu".

## <a name="free-trial"></a>Kostnadsfri utvärderingsversion 

Du kan arrangera för användare att testa ditt erbjudande genom att få åtkomst till begränsad term licens för program vara när du använder BYOL-fakturerings modell. 

## <a name="test-drive"></a>Test Drive

Du distribuerar en eller flera virtuella datorer via IaaS (Infrastructure-as-a-Service) eller SaaS-appar (program vara som en tjänst). En fördel med publicerings alternativet test enhet är den automatiserade etableringen av en virtuell dator eller en hel lösning som leds av en guidad guidad rundtur i en partner. En testen het ger en utvärdering utan extra kostnad för kunden. Kunden behöver inte vara en befintlig Azure-kund för att kunna kommunicera med utvärderings versionen. 

Kontakta oss på [amp-testdrive](mailto:amp-testdrive@microsoft.com) för att komma igång. 

|Krav  |Information |
|---------|---------|
| Du har en Marketplace-app   |    En eller flera virtuella datorer via IaaS eller SaaS.      |

## <a name="interactive-demo"></a>Interaktiv demonstration

Du får en guidad upplevelse av din lösning för dina kunder med hjälp av en interaktiv demonstration. Fördelen med interaktiv demo publicering är att du anger en utvärderings version utan en komplicerad etablering av din komplexa lösning. 

## <a name="virtual-machine-offer"></a>Erbjudande för virtuell dator

Använd den virtuella datorns erbjudande typ när du distribuerar en virtuell installation till prenumerationen som är kopplad till din kund. Virtuella datorer är fullständigt aktiverade med hjälp av BYOL-licensierings modeller enligt principen betala per användning eller hämta licens. Microsoft är värd för handels transaktionen och fakturerar kunden för din räkning. Du får fördelen med att använda den prioriterade betalnings relationen mellan kunden och Microsoft, inklusive eventuella företags avtal.

> [!NOTE]
> Vid detta tillfälle kan de monetära åtaganden som är kopplade till en Enterprise-avtal användas mot Azures användning av din virtuella dator, men inte mot licens avgifterna för program varan.  
> 
> [!NOTE]
> Du kan begränsa identifieringen och distributionen av den virtuella datorn till en speciell uppsättning kunder genom att publicera avbildningen och prissättningen som ett privat erbjudande. Privata erbjudanden gör att du kan skapa exklusiva erbjudanden för dina närmaste kunder och erbjuda anpassade program och villkor. Med de anpassade villkoren kan du markera olika scenarier, inklusive fält LED ande avtal med särskilda priser och villkor samt tidig åtkomst till begränsad version av program vara. Med privata erbjudanden kan du ge särskilda priser eller produkter till en begränsad uppsättning kunder genom att skapa en ny SKU med dessa uppgifter.  
> *   Mer information om privata erbjudanden finns på sidan med privata erbjudanden på Azure Marketplace-sidan på [Azure.Microsoft.com/blog/Private-offers-on-Azure-Marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Krav | Information |  
|:--- |:--- | 
| Fakturering och mätning | Din virtuella dator måste ha stöd för antingen BYOL eller betala per användning. |  
| Azure-kompatibel virtuell hård disk (VHD) | Virtuella datorer måste byggas på Windows eller Linux. <ul> <li>Mer information om hur du skapar en Linux-VHD finns i [Linux-distributioner](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)som har godkänts på Azure.</li> <li>Mer information om hur du skapar en virtuell Windows-hårddisk finns i [skapa en Azure-kompatibel virtuell hård disk](./partner-center-portal/azure-vm-create-offer.md).</li> </ul> |  

>[!Note]
>Partner kanal för Cloud solution providers (CSP) är nu tillgängligt.  Se [leverantörer av moln lösningar](./cloud-solution-providers.md) för mer information om marknadsföring av ditt erbjudande via Microsoft CSP partner-kanaler.

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, 

- [Lär dig mer](https://azuremarketplace.microsoft.com/sell) om Marketplace.

Om du har registrerat och skapar ett nytt erbjudande eller arbetar på ett befintligt,

- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra ditt erbjudande.
- Mer information finns i [skapa ett erbjudande för virtuella datorer](./partner-center-portal/azure-vm-create-offer.md) .
