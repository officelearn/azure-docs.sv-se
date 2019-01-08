---
title: Guide för att skapa en lösningsmall för Marketplace | Microsoft Docs
description: Detaljerade anvisningar för hur du skapar, certifiera och distribuera en Lösningsmall för Multi-VM-avbildning för köp på Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: e14e05f2-2385-4ce0-b351-0747cb74ba19
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: hascipio; v-divte
ROBOTS: NOINDEX
ms.openlocfilehash: 914dece4ba064af00c5b2c34d43dedbb1d62e2e2
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073749"
---
# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Guiden för att skapa en lösningsmall för Azure Marketplace
När du har slutfört steg 1, [skapande och registrering][link-acct-creation], vi vägleds du vid skapandet av en mall för Azure-kompatibel lösning på [tekniska krav för att skapa en lösningsmallen](marketplace-publishing-solution-template-creation-prerequisites.md). Nu vi vägleder dig genom stegen för att skapa en lösningsmall för flera virtuella datorer på den [Publiceringsportalen] [ link-pubportal] för Azure Marketplace.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>Skapa din lösning mall erbjudande i Publiceringsportalen
Gå till [ https://publish.windowsazure.com ](http://publish.windowsazure.com). När du loggar in för första gången för att den [Publiceringsportalen](https://publish.windowsazure.com/), Använd samma konto med ditt företags försäljning profil har registrerats. Senare, kan du lägga till någon enskild anställd hos ditt företag som en coadmin i Publiceringsportalen.

### <a name="1-select-solution-templates"></a>1. Välj ”lösningsmallar”
  ![Rita][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. Skapa en ny lösningsmall
  ![Rita][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. Börja med topologier
En lösningsmall är "överordnad" alla dess topologier. Du kan definiera flera topologier i en erbjudande-/lösningsmall. När ett erbjudande pushas till mellanlagring pushas den med alla sina topologier. Följ stegen nedan för att definiera ditt erbjudande:     

* Skapa en topologi: ”ID” är vanligtvis namnet på topologin för lösningsmallen. Topologi-identifieraren används i URL: en som visas nedan:

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Azure-portalen: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}
* Lägg till en ny version.

### <a name="4-get-your-topology-versions-certified"></a>4. Hämta din topologi-versioner som är certifierade
Ladda upp en zip-fil som innehåller alla filer som krävs för att etablera den särskilda utgåvan av topologin. Den här zipfilen måste innehålla följande filer:

* *mainTemplate.json* och *createUiDefinition.json* fil i rotkatalogen.
* Alla länkade mallar och alla nödvändiga skript.

  > [!TIP]
  > Medan utvecklarna som arbetar med att skapa lösningen mall-topologier och få dem certifieras, företag, kan marknadsföring och/eller juridiska avdelningar i ditt företag arbeta med marknadsföring och juridiska innehållet.
  >
  >

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat din mall och överfört zip-filen, följer du anvisningarna i den [marketing content för för Marketplace](marketplace-publishing-push-to-staging.md) innan du skickar erbjudandet till mellanlagring. Den fullständiga uppsättningen marketplace publicera artiklar finns [komma igång: Hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md).

Du kanske också är intresserad av dessa relaterade artiklar:

* Avbildningar av Virtuella datorer: [Om avbildningar av virtuella datorer i Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
* VM-tillägg: [Azure VM-tillägg och funktioner](../virtual-machines/extensions/features-windows.md)
* Med Azure Resource Manager: [Skapa Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md) och [enkel mall-exempel](https://github.com/rjmax/ArmExamples)
* Begränsningar för Storage-konto: [Övervaka för begränsning av Storage-konto](https://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) och [Premium storage](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
