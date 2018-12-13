---
title: Azure Application-erbjudande | Microsoft Docs
description: Översikt över processen för att publicera ett program för Azure erbjuder på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: pbutlerm
ms.openlocfilehash: 63b7ee4e0d9cb9d0d1f26119fe73573b124d04e8
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53197338"
---
# <a name="azure-application-offer"></a>Erbjudandet för Azure-program

Det här avsnittet beskrivs hur du publicerar ett nytt erbjudande för Azure-program på Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>.
|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Varje Azure-program innehåller en Azure Resource Manager-mallen som definierar de tekniska resurser som används av programmet, vilket omfattar vanligtvis en eller flera virtuella datorer och andra Azure - eller webbaserade stödtjänster. | ![Med Azure apps ikon](./media/azureapp-icon1.png)  |

## <a name="benefits"></a>Fördelar

Här är några av fördelarna med att visa en lista över dina program på en Microsoft marketplace:

* Nå 100 miljoner Azure Active Directory-användare i Office 365 och Dynamics 365.

* Utöka försäljningsteamet: nå företag över hela världen och få en försäljningskanal som engagerar användare, genererar leads och initierar samtal med nya kunder branscher.

* Få användbara insikter: Vi kommer att dela insikter om hur appen fungerar på AppSource, vad som fungerar bra och hur att ytterligare förbättra din försäljning procedurer.

## <a name="types-of-azure-applications"></a>Typer av Azure-program

Det finns två typer av Azure-program: ett hanterat program och en lösningsmall. Liknande, men det finns några viktiga skillnader.

### <a name="solution-template"></a>Lösningsmall

Lösningsmallar är en av huvudmetoderna för att publicera en lösning i Marketplace. Den här erbjudandetypen används när din lösning kräver ytterligare distribution och konfiguration av automation utöver en enskild virtuell dator (VM). Du kan automatisera att tillhandahålla av mer än en virtuell dator med en lösningsmall. Detta innefattar etablering av nätverk och lagringsresurser för att tillhandahålla avancerade IaaS-lösningar. En översikt över mallkraven för lösningen och faktureringsmodellen finns i [program i Azure: lösningsmallar](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

### <a name="managed-application"></a>Hanterat program

Ett hanterat program liknar en lösningsmall i Marketplace med en viktig skillnad. I ett hanterat program distribueras resurserna till en resursgrupp som hanteras av appens utgivare. Resursgruppen finns i kundens prenumeration, men en identitet i utgivarens klient har åtkomst till resursgruppen. Som utgivare anger du kostnaden för fortlöpande support för lösningen. Använda Azure-hanterade program för att enkelt skapa och leverera helt hanterade och nyckelfärdiga program till dina kunder.

Du kan också erbjuda hanterade program i en tjänstkatalog förutom på Marketplace. Tjänstkatalogen är en intern katalog med godkända lösningar för användare i en organisation. Du kan använda katalogen för att uppfylla organisationens standarder och erbjuder lösningar för grupper i en organisation. De anställda kan använder katalogen till att enkelt hitta program som rekommenderas och godkänts av IT-avdelningen.

Mer information om fördelar och typer av hanterade program finns i den [Azure hanterade program, översikt](https://docs.microsoft.com/azure/managed-applications/overview).

## <a name="publishing-overview"></a>Översikt över publicering

På nedanstående video, [mallar för att skapa lösningar och hanterade program för Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), är en översikt över hur du skapar en Azure Resource Manager-mall för att definiera en Azure lösning och sedan hur du därefter publicera apperbjudandet på Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]

## <a name="publishing-process-workflow"></a>Publicering av processen

Följande diagram visar den övergripande processen för att publicera ett erbjudande för Azure-program.

![Arbetsflöde för att publicera erbjudandet](./media/new-offer-process.png)

## <a name="offer-components"></a>Erbjudandet komponenter

Det här avsnittet beskriver de element för att publicera ett erbjudande om hanterat program och är avsedd som en vägledning för utgivare på Azure Marketplace. Publicerings uppdelad i följande huvuddelar: 

* [Förutsättningar](./cpp-prerequisites.md) – visar en lista över tekniska och affärsmässiga krav innan du skapar eller publicera ett erbjudande om hanterat program. 
* [Skapa erbjudandet](./cpp-create-offer.md) -anges de steg som krävs för att skapa en post för erbjudandet av hanterade program med hjälp av Cloud Partner Portal. 
* [Publicera erbjudandet](./cpp-publish-offer.md)– beskriver hur du skickar in erbjudande för publicering på Azure Marketplace.

## <a name="steps-in-the-publishing-process"></a>Stegen i publiceringsprocessen

Anvisningar för att publicera ett erbjudande för Azure-program är:

1. Skapa erbjudandet – ger detaljerad information om erbjudandet. Informationen omfattar: erbjudandet beskrivning, marknadsföringsmaterial, supportinformation och tillgångsspecifikationer.

2. Skapa affärs- och tekniska resurser – skapa företagstillgångar (juridiska dokument och marknadsföringsmaterial) och tekniska resurser för den associerade lösningen.

3. Skapa SKU - skapa den SKU: er som är kopplad till erbjudandet. En unik SKU måste anges för varje bild som du planerar att publicera.

4. Certifiera och publicera erbjudande - när erbjudandet och tekniska resurser har slutförts, kan du skicka erbjudandet. Den här publiceringen börjar publiceringsprocessen. Under den här processen testas lösningen verifierats, certifierade, sedan ”lanseras” på Azure Marketplace.

## <a name="next-steps"></a>Nästa steg

Innan du betraktar dessa steg måste du uppfylla de [tekniska krav och affärskrav](./cpp-prerequisites.md) för att publicera ett hanterat program i Microsoft Azure Marketplace.