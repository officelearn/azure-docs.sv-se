---
title: Erbjudande för virtuell dator publicerar Guide för Azure Marketplace
description: Den här artikeln beskrivs kraven för att publicera en virtuell dator och en kostnadsfri utvärdering av programvara som ska distribueras från Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: fcfb3943109adbc095b90073f041662fb5c65b56
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54078889"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Erbjudande för virtuell dator publicerar Guide

Avbildningar av virtuella datorer är en av huvudmetoderna för att publicera en lösning på Azure Marketplace. Använd den här guiden för att förstå kraven för det här erbjudandet. 

Det här är transaktionen erbjudanden som distribueras och faktureras via Marketplace. Anropet till åtgärden som en användare ser är ”Hämta nu”.

## <a name="free-trial"></a>Kostnadsfri utvärderingsversion 

Du kan ordna för användare att testa ditt erbjudande genom att öppna programvarulicenser för begränsad tid när du använder faktureringsmodellen Bring Your Own License (BYOL). Nedan visas kraven för att distribuera det här erbjudandet. 

|Krav  |Information  |
|---------|---------|
|Kostnadsfria utvärderingsperioden och utvärderingsmiljö     |   Dina kunder kan testa din app kostnadsfritt under en begränsad tid. Dina kunder är Obs krävs för att betala alla avgifter licens eller prenumeration för ditt erbjudande. Dina kunder behöver inte betala för de underliggande Microsoft från första part produkt eller tjänst. Alla utvärderingsversionsalternativ som distribueras till din Azure-prenumeration. Du har ensam kontroll över kostnadsoptimeringar och hantering. Du kan välja en kostnadsfri utvärderingsversion eller en interaktiv demonstration. Oavsett vad du väljer, ger din kostnadsfria utvärderingsversion kunder en förinställda lång tid att testa ditt erbjudande utan extra kostnad.|
|Enkelt att konfigurera och färdiga att använda lösning    |  Din app måste vara enkelt och snabbt att göra inställningar.       |
|Tillgänglighet / drifttid    |    Din SaaS-app eller en plattform måste ha en drifttid på minst 99,9%.     |
|Azure Active Directory     |    Erbjudandet måste tillåta Azure Active Directory (Azure AD) federerad enkel inloggning (SSO) (Azure AD-federerad enkel inloggning) med medgivande aktiverat.     |

## <a name="test-drive"></a>Test Drive

Du distribuerar en eller flera virtuella datorer via infrastructure-as-a-service (IaaS) eller software-as-a-service (SaaS)-appar. En fördel med test drive publiceringsalternativ är automatiserad etablering av en virtuell dator eller hela lösningen som leds av en värdpartner guidad visning. Testa ger en utvärdering utan extra kostnad till kunden. Kunden behöver inte vara en befintlig Azure-kund för att öka interaktionen med utvärderingen. 

Kontakta oss på [amp testdrive](mailto:amp-testdrive@microsoft.com) att komma igång. 

|Krav  |Information |
|---------|---------|
| Du har en Marketplace-app   |    En eller flera virtuella datorer via IaaS eller SaaS.      |

## <a name="interactive-demo"></a>Interaktiv demo

Du kan ange en guidad upplevelse av din lösning till dina kunder med hjälp av en interaktiv demonstration. Fördelen med Interaktiv demo publicera alternativet är att du anger en utvärderingsmiljö utan komplicerad etablering av din komplexa lösning. 

## <a name="virtual-machine-offer"></a>Erbjudande för virtuell dator

Använd typ av erbjudande för virtuell dator när du distribuerar en virtuell installation till den prenumeration som är associerad med kunden. Virtuella datorer är helt aktiverat med betalning per användning eller bring-your-own-license (BYOL) licensieringsmodellerna handel. Microsoft är värd för handel transaktionen och fakturerar kunden å dina vägnar. Du får fördelen med att använda önskade betalning relationen mellan kunden och Microsoft, inklusive några Enterprise-avtal.

>[!NOTE]
>För tillfället kan monetära åtaganden som är associerade med ett Enterprise-avtal användas mot Azure-användning för den virtuella datorn, men inte mot din avgifter för programvarulicenser.  

>[!NOTE]
>Du kan begränsa identifiering och distribution av den virtuella datorn till en specifik uppsättning kunder genom att publicera avbildningen och priser som ett privat erbjudande. Privata erbjudanden utnyttja möjligheten att skapa exklusiva erbjudanden för kunderna närmaste och erbjuda anpassade programvara och villkor. Anpassade villkor kan du markera en mängd olika scenarier, inklusive ledda av fältet behandlar specialiserade prissättning och villkor samt snabb åtkomst till begränsad version programvara. Privata erbjudanden aktivera att ge specifika pris eller produkter som ska ett begränsat antal kunder genom att skapa en ny SKU med detaljer.  
*   Mer information om privata erbjudanden finns privata erbjudanden på Azure Marketplace-sidan finns på [azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Krav | Information |  
|:--- |:--- | 
| Fakturering och mätning | Den virtuella datorn måste ha stöd för BYOL eller användningsbaserad fakturering. |  
| Azure-kompatibel virtuell hårddisk (VHD) | Virtuella datorer måste vara baserade på Windows eller Linux. <ul> <li>Mer information om hur du skapar en Linux-VHD finns [Linux-distributioner på Azure-godkända](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Läs mer om hur du skapar en virtuell Windows-Hårddisk [skapa en Azure-kompatibel VHD](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

## <a name="next-steps"></a>Nästa steg

Om du inte redan gjort det, 

- [Registrera](https://azuremarketplace.microsoft.com/sell) i marketplace.

Om du är registrerad och skapar ett nytt erbjudande eller arbetar på en befintlig

- [Logga in på partnerportalen i molnet](https://cloudpartner.azure.com) att skapa eller slutföra ditt erbjudande.
- Se [erbjudande för virtuell dator](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-virtual-machine-offer) för mer information.
