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
ms.date: 02/06/2019
ms.author: pbutlerm
ms.openlocfilehash: 9faa38a23b2039902366e5b885ab73c68a2a3d80
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076028"
---
# <a name="azure-application-offer"></a>Erbjudandet för Azure-program

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> Det här avsnittet beskrivs hur du publicerar ett nytt erbjudande för Azure-program till den [Azure Marketplace](https://azuremarketplace.microsoft.com).  Varje Azure-program innehåller en Azure Resource Manager-mallen som definierar de tekniska resurser som används av programmet, vilket omfattar vanligtvis en eller flera virtuella datorer och andra Azure - eller webbaserade stödtjänster. Alla Azure-app-erbjudanden måste aktivera åtkomstsäkerhet via [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).  </div> | ![Med Azure apps ikon](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Översikt över publicering

På nedanstående video, [mallar för att skapa lösningar och hanterade program för Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), är en introduktion: vad erbjuder typer är tillgängliga, vilka tekniska resurser är krävs, hur du skapar en Azure Resource Manager mall, utveckla och testa appens användargränssnitt, hur du publicerar apperbjudandet och granskningsprocessen app.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Typer av Azure-program

Det finns två typer av Azure-program: hanterade program och lösningsmallar. 

- Lösningsmallar är en av huvudmetoderna för att publicera en lösning i Marketplace. Den här erbjudandetypen används när din lösning kräver ytterligare distribution och konfiguration av automation utöver en enskild virtuell dator (VM). Du kan automatisera att tillhandahålla av mer än en virtuell dator med en lösningsmall. Det här automation innefattar etablering av nätverk och lagringsresurser för att tillhandahålla avancerade IaaS-lösningar. En översikt över mallkraven för lösningen och faktureringsmodellen finns i [program i Azure: lösningsmallar](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

- Hanterade program liknar lösningsmallar, med en viktig skillnad. I ett hanterat program distribueras resurserna till en resursgrupp som hanteras av appens utgivare. Resursgruppen finns i kundens prenumeration, men en identitet i utgivarens klient har åtkomst till resursgruppen. Som utgivare anger du kostnaden för fortlöpande support för lösningen. Använda Azure-hanterade program för att enkelt skapa och leverera helt hanterade och nyckelfärdiga program till dina kunder.

Du kan också erbjuda hanterade program i en tjänstkatalog förutom på Azure Marketplace. Tjänstkatalogen är en intern katalog med godkända lösningar för användare i en organisation. Du kan använda katalogen för att uppfylla organisationens standarder och erbjuder lösningar för grupper i en organisation. De anställda kan använder katalogen till att enkelt hitta program som rekommenderas och godkänts av IT-avdelningen.

>[!Note]
>Cloud Solution Providers (CSP) partner channel anmälan är nu tillgänglig.  Se [Cloud Solution Providers](../../cloud-solution-providers.md) mer information om marknadsföring av ditt erbjudande via Microsoft CSP partner kanaler.

Mer information om fördelar och typer av hanterade program finns i den [Azure hanterade program, översikt](https://docs.microsoft.com/azure/managed-applications/overview).


## <a name="publishing-process-workflow"></a>Publicering av processen

Följande diagram visar den övergripande processen för att publicera ett erbjudande för Azure-program.

![Arbetsflöde för att publicera erbjudandet](./media/new-offer-process.png)

Anvisningar för att publicera ett erbjudande för Azure-program är:

1. Uppfyller den [krav](./cpp-prerequisites.md) – (visas inte) kontrollera att du har uppfyllt affärsmässiga och tekniska krav för att publicera en Azure-app på Azure Marketplace. 

1. [Skapa erbjudandet](./cpp-create-offer.md) -ger detaljerad information om erbjudandet. Informationen omfattar: erbjudandet beskrivning, marknadsföringsmaterial, supportinformation och tillgångsspecifikationer.

1. [Skapa eller samla in befintliga företags- och tekniska resurser](./cpp-create-technical-assets.md) -skapa företagstillgångar (juridiska dokument och marknadsföringsmaterial) och tekniska resurser för den associerade lösningen.

1. [Skapa SKU: N](./cpp-skus-tab.md) -skapa den SKU: er som är kopplad till erbjudandet. En unik SKU måste anges för varje bild som du planerar att publicera.

1. Certifiera och [publicera erbjudandet](./cpp-publish-offer.md) -när erbjudandet och tekniska resurser har slutförts, kan du skicka erbjudandet. Den här publiceringen börjar publiceringsprocessen. Under den här processen testas lösningen verifierats, certifierade, sedan ”lanseras” på Azure Marketplace.

## <a name="next-steps"></a>Nästa steg

Innan du betraktar dessa steg måste du uppfylla de [tekniska krav och affärskrav](./cpp-prerequisites.md) för att publicera ett hanterat program i Microsoft Azure Marketplace.
