---
title: Publicerings guide för erbjudanden om virtuella datorer på Azure Marketplace
description: I den här artikeln beskrivs kraven för att publicera en virtuell dator och en program varu kostnads fri utvärderings version som ska distribueras från Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 09/04/2020
ms.openlocfilehash: cc6b040731cbeb7271d7a7c0de1c32fa2d007013
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89484196"
---
# <a name="publishing-guide-for-virtual-machine-offers"></a>Publicerings guide för virtuella dator erbjudanden

Att publicera virtuella dator avbildningar är ett av de största sätten att publicera en lösning på Azure Marketplace. Använd den här guiden för att förstå kraven för den här typen av erbjudande. 

Erbjudanden för virtuella datorer är transaktions erbjudanden som distribueras och debiteras via Azure Marketplace. Det List alternativ som en användare ser är att *Hämta det nu*.

## <a name="free-trial"></a>Kostnadsfri utvärderingsversion 

För att användarna ska kunna testa ditt erbjudande kan du få åtkomst till begränsade program varu licenser när du använder BYOL-fakturerings modellen. 

## <a name="test-drive"></a>Test enhet

Du kan distribuera en eller flera virtuella datorer via IaaS (Infrastructure as a Service) eller SaaS-appar (program vara som en tjänst). En fördel med publicerings alternativet *test enhet* är den automatiserade konfigurationen av en virtuell dator eller hela lösningen som leds av en guidad guidad rundtur i en partner. En testenhet gör det möjligt för kunderna att utvärdera virtuella datorer utan extra kostnad. En kund behöver inte vara en befintlig Azure-kund för att kunna kommunicera med utvärderings versionen. 

Mer information om test enheter finns i [Vad är en test enhet?](what-is-test-drive.md)

|Krav  |Information |
|---------|---------|
| Du har en Azure Marketplace-app   |  En eller flera virtuella datorer via IaaS eller SaaS.      |

## <a name="interactive-demo"></a>Interaktiv demo

Med det här erbjudandet ger du kunderna en guidad upplevelse av din lösning med hjälp av en interaktiv demonstration. Fördelen med ett interaktivt alternativ för demo publicering är att du kan erbjuda en utvärderings upplevelse utan att behöva tillhandahålla en komplicerad konfiguration av din komplexa lösning. 

## <a name="virtual-machine-offer"></a>Erbjudande på virtuella datorer

Använd den *virtuella datorns* erbjudande typ när du distribuerar en virtuell installation till den prenumeration som är associerad med kunden. Virtuella datorer är helt handelsaktiverade med hjälp av "betala per användning"-eller "BYOL-licensierings modeller". Microsoft är värd för handels transaktionen och fakturerar kunden för din räkning. Du får fördelen med att använda den prioriterade betalnings relationen mellan kunden och Microsoft, inklusive eventuella företags avtal.

> [!NOTE]
> Vid detta tillfälle kan de monetära åtaganden som är kopplade till en Enterprise-avtal användas mot Azures användning av din virtuella dator, men inte mot licens avgifterna för program varan.  
> 
> [!NOTE]
> Du kan begränsa identifieringen och distributionen av den virtuella datorn till en speciell uppsättning kunder genom att publicera avbildningen och prissättningen som ett privat erbjudande. Privata erbjudanden gör att du kan skapa exklusiva erbjudanden för dina närmaste kunder och erbjuda anpassade program och villkor. Med de anpassade villkoren kan du markera olika scenarier, inklusive fält LED ande avtal med särskilda priser och villkor samt tidig åtkomst till begränsad version av program vara. Med privata erbjudanden kan du ge särskilda priser eller produkter till en begränsad uppsättning kunder genom att skapa en ny plan med dessa uppgifter.  
>
> Mer information finns i [privata erbjudanden på Azure Marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Krav | Information |  
|:--- |:--- | 
| Fakturering och mätning | Din virtuella dator måste ha stöd för antingen BYOL eller betala per användning. |  
| Azure-kompatibel virtuell hård disk (VHD) | Virtuella datorer måste byggas på Windows eller Linux. Mer information om hur du skapar en virtuell hård disk finns i: <ul> <li>[Linux-distributioner](../virtual-machines/linux/endorsed-distros.md) som har godkänts på Azure (för Linux-VHD: er).</li> <li>[Skapa en Azure-kompatibel virtuell hård disk](./partner-center-portal/azure-vm-create-offer.md) (för Windows-VHD: er).</li> </ul> |  

>[!Note]
>Du kan välja att välja partner kanal för CSP (Cloud Solution Provider) nu. Mer information om marknadsföring av ditt erbjudande via Microsoft CSP partner Channels finns i [moln lösnings leverantörer](./cloud-solution-providers.md).

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, lär du dig hur du kan [Utöka din moln verksamhet med Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Registrera dig och börja arbeta i Partner Center:

- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra ditt erbjudande.
- Mer information finns i [skapa ett erbjudande för virtuella datorer](./partner-center-portal/azure-vm-create-offer.md) .
