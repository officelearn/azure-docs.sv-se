---
title: Erbjudandet om Azure-program | Azure Marketplace
description: Översikt över processen för att publicera ett Azure Application-erbjudande på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: dsindona
ms.openlocfilehash: ed086ffdc49e21b819c0ee05b38ad882b4e269d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285324"
---
# <a name="azure-application-offer"></a>Erbjudande för Azure-program

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> I det här avsnittet beskrivs hur du publicerar ett nytt Azure-programerbjudande till [Azure Marketplace](https://azuremarketplace.microsoft.com).  Varje Azure-program innehåller en Azure Resource Manager-mall som definierar alla tekniska resurser som används av programmet, som vanligtvis innehåller en eller flera virtuella datorer och andra stödda Azure- eller webbaserade tjänster. Alla Azure-apperbjudanden måste aktivera åtkomstsäkerhet via [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).  </div> | ![Ikon för Azure-appar](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Översikt över publicering

Följande video, [Building Solution Malls och Managed Applications for the Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603)är en introduktion: vilka erbjudandetyper som är tillgängliga, vilka tekniska resurser som krävs, hur du skapar en Azure Resource Manager-mall, utvecklar och testar appgränssnittet, hur du publicerar apperbjudandet och appgranskningsprocessen.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Typer av Azure-program

Det finns två typer av Azure-program: hanterade program och lösningsmallar. 

- Lösningsmallar är ett av de viktigaste sätten att publicera en lösning på Marketplace. Den här erbjudandetypen används när din lösning kräver ytterligare distribution och konfigurationsautomatisering utöver en enda virtuell dator (VM). Du kan automatisera tillhandahållandet av mer än en virtuell dator med hjälp av en lösningsmall. Denna automatisering omfattar etablering av nätverks- och lagringsresurser för att tillhandahålla komplexa IaaS-lösningar. En översikt över krav på lösningsmallar och faktureringsmodellen finns i [Azure Applications: lösningsmallar](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

- Hanterade program liknar lösningsmallar, med en viktig skillnad. I ett hanterat program distribueras resurserna till en resursgrupp som hanteras av appens utgivare. Resursgruppen finns i kundens prenumeration, men en identitet i utgivarens klient har åtkomst till resursgruppen. Som utgivare anger du kostnaden för fortlöpande support för lösningen. Använd Azure Managed-program för att enkelt skapa och leverera fullständigt hanterade, nyckelfärdiga program till dina kunder.

Förutom Azure Marketplace kan du även erbjuda hanterade program i en tjänstkatalog. Tjänstkatalogen är en intern katalog med godkända lösningar för användare i en organisation. Du använder katalogen för att uppfylla organisationens standarder och samtidigt erbjuda lösningar för grupper i en organisation. De anställda kan använder katalogen till att enkelt hitta program som rekommenderas och godkänts av IT-avdelningen.

>[!Note]
>Opt-in (Cloud Solution Providers) partnerkanal är nu tillgänglig.  Se [Cloud Solution Providers](../../cloud-solution-providers.md) för mer information om marknadsföring ditt erbjudande via Microsoft CSP partnerkanaler.

Mer information om fördelar och typer av hanterade program finns i [översikten över hanterade azure-program](https://docs.microsoft.com/azure/managed-applications/overview).


## <a name="publishing-process-workflow"></a>Arbetsflöde för publiceringsprocess

Följande diagram visar högnivåprocessen för publicering av ett Azure-programerbjudande.

![Arbetsflöde för publiceringserbjudande](./media/new-offer-process.png)

Stegen på hög nivå för att publicera ett Azure-programerbjudande är:

1. Uppfylla [förutsättningarna](./cpp-prerequisites.md) – (visas inte) Kontrollera att du har uppfyllt affärskraven och de tekniska kraven för att publicera en Azure-app till Azure Marketplace. 

1. [Skapa erbjudandet](./cpp-create-offer.md) - Ge detaljerad information om erbjudandet. Den här informationen omfattar: erbjudandebeskrivning, marknadsföringsmaterial, supportinformation och tillgångsspecifikationer.

1. [Skapa eller samla in befintliga affärs- och tekniska tillgångar](./cpp-create-technical-assets.md) - Skapa affärstillgångar (juridiska dokument och marknadsföringsmaterial) och tekniska tillgångar för den associerade lösningen.

1. [Skapa SKU](./cpp-skus-tab.md) - Skapa de SKU:er som är associerade med erbjudandet. En unik SKU krävs för varje bild som du planerar att publicera.

1. Certifiera och [publicera erbjudandet](./cpp-publish-offer.md) - När erbjudandet och de tekniska tillgångarna har slutförts kan du skicka erbjudandet. Den här inlämningen startar publiceringsprocessen. Under den här processen testas, valideras, certifieras lösningen och "publiceras" på Azure Marketplace.

## <a name="next-steps"></a>Nästa steg

Innan du överväger de här stegen måste du uppfylla de [tekniska kraven och affärskraven](./cpp-prerequisites.md) för att publicera ett hanterat program till Microsoft Azure Marketplace.
