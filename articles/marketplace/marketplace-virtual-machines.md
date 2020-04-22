---
title: Erbjudandeguide för virtuell dator för Azure Marketplace
description: I den här artikeln beskrivs kraven för att publicera en virtuell dator och en kostnadsfri utvärderingsversion som ska distribueras från Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 2fa67d81546db86535c179a9c59d0602c1175cba
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687500"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Publiceringsguide för virtuell dator

Avbildningar för virtuella datorer är ett av de viktigaste sätten att publicera en lösning på Azure Marketplace. Använd den här guiden för att förstå kraven för det här erbjudandet. 

Det här är transaktionserbjudanden som distribueras och faktureras via Marketplace. Uppmaningen som en användare ser är "Hämta den nu".

## <a name="free-trial"></a>Kostnadsfri utvärderingsversion 

Du kan ordna så att användarna kan testa ditt erbjudande genom att komma åt tidsbegränsade programvarulicenser när de använder faktureringsmodellen Bring Your Own License (BYOL). 

## <a name="test-drive"></a>Test Drive

Du distribuerar en eller flera virtuella datorer via IaaS-appar (Infrastructure-as-a-service) eller SaaS-appar (Software-as-a-service). En fördel med testkörningspubliceringsalternativet är automatisk etablering av en virtuell dator eller hel lösning som leds av en partnervärd guidad tur. En provkörning ger en utvärdering utan extra kostnad för din kund. Din kund behöver inte vara en befintlig Azure-kund för att kunna interagera med utvärderingsversionen. 

Kontakta oss på [amp-testdrive](mailto:amp-testdrive@microsoft.com) för att komma igång. 

|Krav  |Information |
|---------|---------|
| Du har en Marketplace-app   |    En eller flera virtuella datorer via IaaS eller SaaS.      |

## <a name="interactive-demo"></a>Interaktiv demo

Du ger en guidad upplevelse av din lösning till dina kunder genom att använda en interaktiv demonstration. Fördelen med interaktiv demo publicering alternativ är att du ger en testupplevelse utan komplicerad etablering av din komplexa lösning. 

## <a name="virtual-machine-offer"></a>Erbjudande om virtuell dator

Använd erbjudandetypen för den virtuella datorn när du distribuerar en virtuell installation till prenumerationen som är kopplad till kunden. Virtuella datorer är fullt aktiverade med hjälp av betal-as-you-go eller bring-your-own-license (BYOL) licensieringsmodeller. Microsoft är värd för handelstransaktionen och fakturerar din kund för din räkning. Du får förmånen att använda den önskade betalningsrelationen mellan kunden och Microsoft, inklusive eventuella Enterprise-avtal.

> [!NOTE]
> För närvarande kan de monetära åtaganden som är associerade med ett Enterprise-avtal användas mot Azure-användningen av din virtuella dator, men inte mot dina licensavgifter för programvara.  
> 
> [!NOTE]
> Du kan begränsa identifieringen och distributionen av den virtuella datorn till en viss uppsättning kunder genom att publicera avbildningen och prissättningen som ett privat erbjudande. Privata erbjudanden låser upp möjligheten för dig att skapa exklusiva erbjudanden för dina närmaste kunder och erbjuda anpassad programvara och villkor. De anpassade termerna gör att du kan lyfta fram en mängd olika scenarier, inklusive fältledda erbjudanden med specialiserad prissättning och villkor samt tidig tillgång till programvara med begränsad utgåva. Privata erbjudanden gör att du kan ge specifika priser eller produkter till en begränsad uppsättning kunder genom att skapa en ny SKU med dessa detaljer.  
> *   Mer information om privata erbjudanden finns på sidan Privata erbjudanden på Azure Marketplace som finns på [azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Krav | Information |  
|:--- |:--- | 
| Fakturering och mätning | Din virtuella dator måste ha stöd för antingen BYOL eller Pay-As-You-Go månadsvis fakturering. |  
| Azure-kompatibel virtuell hårddisk (VHD) | Virtuella datorer måste byggas på Windows eller Linux. <ul> <li>Mer information om hur du skapar en Virtuell Linux-hårddisk finns i [Linux-distributioner som är godkända på Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Mer information om hur du skapar en Virtuell Windows-hårddisk finns i [Skapa en Azure-kompatibel virtuell hårddisk](./partner-center-portal/azure-vm-create-offer.md).</li> </ul> |  

>[!Note]
>Opt-in (Cloud Solution Providers) partnerkanal är nu tillgänglig.  Se [Cloud Solution Providers](./cloud-solution-providers.md) för mer information om marknadsföring ditt erbjudande via Microsoft CSP partnerkanaler.

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, 

- [Läs mer](https://azuremarketplace.microsoft.com/sell) om marknadsplatsen.

Om du är registrerad och skapar ett nytt erbjudande eller arbetar med ett befintligt,

- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra erbjudandet.
- Mer information finns i Skapa ett erbjudande om [en virtuell dator.](./partner-center-portal/azure-vm-create-offer.md)
