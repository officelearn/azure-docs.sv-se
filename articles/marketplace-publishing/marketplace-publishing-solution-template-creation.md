---
title: "Guide för att skapa en lösningsmall för Marketplace | Microsoft Docs"
description: "Detaljerade anvisningar för hur du skapar, certifiera och distribuera en lösning för flera Virtuella bilden mall för inköp på Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e14e05f2-2385-4ce0-b351-0747cb74ba19
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: b753bfb4bd69bd9aacf4eebd8999397394c28bc4
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Guide för att skapa en för lösningsmall för Azure Marketplace
När du har slutfört steg 1, [skapande av konton och registrering][link-acct-creation], vi Interaktiv du vid skapandet av en Azure-kompatibel lösningsmall på [tekniska krav för att skapa en lösningsmall](marketplace-publishing-solution-template-creation-prerequisites.md). Nu går du igenom stegen för att skapa en lösningsmall för för flera virtuella datorer på den [Publiceringsportal] [ link-pubportal] för Azure Marketplace.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>Skapa lösningen mallen erbjudandet i Publishing Portal
Gå till [https://publish.windowsazure.com](http://publish.windowsazure.com). När du loggar in för första gången och det [Publiceringsportal](https://publish.windowsazure.com/), Använd samma konto med företagets säljare profil har registrerats. Du kan senare lägga till anställda på företaget som medadministratör i Publishing Portal.

### <a name="1-select-solution-templates"></a>1. Välj ”lösningsmallar”
  ![Rita][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. Skapa en ny lösningsmall
  ![Rita][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. Börja med topologier
En lösningsmall är "överordnad" alla dess topologier. Du kan definiera flera topologier i en erbjudande-/lösningsmall. När ett erbjudande pushas till mellanlagring pushas den med alla sina topologier. Följ stegen nedan för att definiera erbjudandet:     

* Skapa en topologi: ”ID” är vanligtvis namnet på topologin för lösningsmall. Topologi-identifieraren används i URL: en som visas nedan:

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/ {PublisherNamespace} / {OfferIdentifier} {TopologyIdentifier}

  Azure-portalen: https://portal.azure.com/#gallery/ {PublisherNamespace}. {OfferIdentifier} {TopologyIdentifier}
* Lägg till en ny version.

### <a name="4-get-your-topology-versions-certified"></a>4. Hämta din topologi-versioner som är certifierade
Överför en zip-fil som innehåller alla filer som krävs för att etablera den viss versionen av topologin. Den här zipfilen måste innehålla följande:

* *mainTemplate.json* och *createUiDefinition.json* fil på dess rotkatalog.
* Alla länkade mallar och alla nödvändiga skript.

  > [!TIP]
  > När utvecklarna arbetar på att skapa lösningen mallen topologier och få dem certifierad, företag, kan marknadsföring och/eller juridiska avdelningar inom företaget arbeta med marknadsföring och juridiska innehållet.
  >
  >

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat din lösning och upp zip-filen, följ instruktionerna i den [Marketplace marketing content guiden](marketplace-publishing-push-to-staging.md) innan du skickar erbjudandet till Förproduktion. En fullständig uppsättning marketplace publicera artiklar finns [komma igång: hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md).

Du kan också vara intresserad av dessa relaterade artiklar:

* VM-avbildningar: [om avbildningar av virtuella datorer i Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
* VM-tillägg: [VM-agenten och VM-tillägg översikt](https://msdn.microsoft.com/library/azure/dn832621.aspx) och [Azure VM-tillägg och funktioner](https://msdn.microsoft.com/library/azure/dn606311.aspx)
* Azure Resource Manager: [redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md) och [enkel mall-exempel](https://github.com/rjmax/ArmExamples)
* Storage-konto begränsar: [övervaka för Storage-konto begränsning](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) och [Premium-lagring](../storage/common/storage-premium-storage.md#scalability-and-performance-targets)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
