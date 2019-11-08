---
title: Azure Application erbjudande | Azure Marketplace
description: Översikt över processen för att publicera ett Azure Application erbjudande på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: pabutler
ms.openlocfilehash: 9125b5c71b63b27c58ea72b7bfd49f730854b33d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818790"
---
# <a name="azure-application-offer"></a>Erbjudande för Azure-program

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> Det här avsnittet beskriver hur du publicerar ett nytt Azure-programerbjudande på [Azure Marketplace](https://azuremarketplace.microsoft.com).  Varje Azure-program innehåller en Azure Resource Manager mall som definierar alla tekniska till gångar som används av programmet, som vanligt vis innehåller en eller flera virtuella datorer och andra stöd för Azure-eller webbaserade tjänster. Alla Azure App-erbjudanden måste aktivera åtkomst säkerhet via [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).  </div> | ![Ikon för Azure-appar](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Publicerings översikt

Följande video, [skapa Solution-mallar och hanterade program för Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603)är en introduktion: vilka erbjudande typer som är tillgängliga, vilka tekniska till gångar som krävs, hur du redigerar en Azure Resource Manager mall, utvecklar och testa användar gränssnittet för appen, publicera appens erbjudande och gransknings processen för appen.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Typer av Azure-program

Det finns två typer av Azure-program: hanterade program och Solution-mallar. 

- Solution templates är ett av de viktigaste sätten att publicera en lösning på Marketplace. Den här erbjudande typen används när lösningen kräver ytterligare distributions-och konfigurations automatisering utöver en enskild virtuell dator (VM). Du kan automatisera att tillhandahålla mer än en virtuell dator med hjälp av en lösnings mall. Denna automatisering omfattar etablering av nätverks-och lagrings resurser för att tillhandahålla komplexa IaaS-lösningar. En översikt över krav på lösnings mal len och fakturerings modellen finns i [Azure-program: Solution templates](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

- Hanterade program liknar Solution-mallar med en viktig skillnad. I ett hanterat program distribueras resurserna till en resursgrupp som hanteras av appens utgivare. Resursgruppen finns i kundens prenumeration, men en identitet i utgivarens klient har åtkomst till resursgruppen. Som utgivare anger du kostnaden för fortlöpande support för lösningen. Använd Azure-hanterade program för att enkelt skapa och leverera fullständigt hanterade, nyckel färdiga program till dina kunder.

Förutom Azure Marketplace kan du även erbjuda hanterade program i en tjänst katalog. Tjänstkatalogen är en intern katalog med godkända lösningar för användare i en organisation. Du använder katalogen för att uppfylla organisationens standarder och erbjuder lösningar för grupper i en organisation. De anställda kan använder katalogen till att enkelt hitta program som rekommenderas och godkänts av IT-avdelningen.

>[!Note]
>Partner kanal för Cloud solution providers (CSP) är nu tillgängligt.  Se [leverantörer av moln lösningar](../../cloud-solution-providers.md) för mer information om marknadsföring av ditt erbjudande via Microsoft CSP partner-kanaler.

Mer information om fördelarna och typerna av hanterade program finns i [Översikt över Azure Managed Applications](https://docs.microsoft.com/azure/managed-applications/overview).


## <a name="publishing-process-workflow"></a>Arbets flöde för publicerings process

Följande diagram visar den övergripande processen för att publicera ett erbjudande för Azure-program.

![Arbets flöde för publicerings erbjudande](./media/new-offer-process.png)

De övergripande stegen för att publicera ett Azure-program erbjuder:

1. Uppfylla kraven [– (](./cpp-prerequisites.md) visas inte) kontrol lera att du har uppfyllt affärs kraven och de tekniska kraven för att publicera en Azure-app på Azure Marketplace. 

1. [Skapa erbjudandet](./cpp-create-offer.md) – ange detaljerad information om erbjudandet. Den här informationen omfattar: Beskrivning av erbjudandet, marknadsförings material, supportinformation och till gångs uppgifter.

1. [Skapa eller samla in befintliga affärs-och teknik resurser](./cpp-create-technical-assets.md) – skapa företags till gångar (juridiska dokument och marknadsförings material) och tekniska till gångar för den associerade lösningen.

1. [Skapa SKU](./cpp-skus-tab.md) – skapa de SKU: er som är associerade med erbjudandet. En unik SKU krävs för varje avbildning som du planerar att publicera.

1. Certifiera och [publicera erbjudandet](./cpp-publish-offer.md) – när erbjudandet och de tekniska till gångarna har slutförts kan du skicka in erbjudandet. Den här sändningen startar publicerings processen. Under den här processen testas, verifieras, godkänns, certifieras och aktive ras på Azure Marketplace.

## <a name="next-steps"></a>Nästa steg

Innan du tar hänsyn till de här stegen måste du uppfylla de [tekniska och affärsmässiga kraven](./cpp-prerequisites.md) för att publicera ett hanterat program till Microsoft Azure Marketplace.
