---
title: "Översikt över Azure hanterade program | Microsoft Docs"
description: "Beskriver konceptet för Azure hanterade program"
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 682b7577135e327457976dc77ce4b4364bd12e48
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="azure-managed-applications-overview"></a>Översikt över Azure hanterade program

Azure hanterade program kan du erbjuda molnlösningar som är enkelt för kunder att distribuera och driva. Du har implementerat infrastrukturen och pågående stödja. Om du vill göra ett hanterat program tillgängliga för alla kunder att publicera den på Azure marketplace. Om du vill göra den tillgänglig för endast användare i din organisation, att publicera det till en intern katalog. 

Ett hanterat program liknar en lösningsmall i Marketplace med en nyckel skillnad. I ett hanterat program etableras resurser till en resursgrupp som hanteras av utgivaren av appen. Resursgruppen finns i kundens prenumeration, men en identitet i utgivarens klienten har åtkomst till resursgruppen. Som utgivare anger du kostnaden för pågående stöd för lösningen.

## <a name="advantages-of-managed-applications"></a>Fördelarna med hanterade program

Hanterade program minska barriärer till konsumenter med hjälp av dina lösningar. De behöver inte kunskaper i cloud-infrastruktur för att använda din lösning. Användare har begränsad åtkomst till viktiga resurser. De behöver inte bekymra dig om att göra fel vid hantering av den. 

Hanterade program kan du upprätta en pågående relation med dina användare. Du definierar villkoren för att hantera programmet och alla avgifter hanteras via Azure fakturering.

Kunder distribuera dessa hanterade program i sina prenumerationer, men de inte behöver underhålla, uppdatera eller tjänsten dem. Du kan se till att alla kunder använder godkända versioner. Kunder behöver utveckla programspecifika kunskap för att hantera dessa program. Kunder hämta automatiskt programuppdateringar utan att behöva bekymra dig om felsökning och diagnos av problem med program. 

För IT-team kan hanterade program du erbjuda förhandsgodkända lösningar till användare i organisationen. Du kan kontrollera dessa lösningar är kompatibla med organisationens normer.

## <a name="types-of-managed-applications"></a>Typer av hanterade program

Du kan publicera det hanterade programmet internt eller externt.

![Publicera internt eller externt](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>tjänstkatalog

Tjänstkatalogen är en intern katalog godkända lösningar för användare i en organisation. Du kan använda katalogen för att säkerställa kompatibilitet med vissa organisationens normer när de tillhandahålla lösningar för organisationer. Anställda använder katalogen för att enkelt identifiera omfattande uppsättning program som rekommenderas och godkänts av sina IT-avdelningar. De hanterade program som andra personer i organisationen som delar med dem. visas.

Information om hur du publicerar ett program för Tjänstkatalog hanteras finns [skapa katalogen tjänstprogram](publish-service-catalog-app.md).

### <a name="marketplace"></a>Marketplace

Leverantörer som önskar debiterar för sina tjänster kan göra ett hanterat program tillgängliga via Azure marketplace. När leverantören publicerar ett program, är den tillgänglig för användare utanför organisationen. Med den här metoden, hanterade-leverantörer (MSPs), oberoende programvaruleverantörer (ISV) och system kan dietetisk (SIs) erbjuda lösningar till alla Azure-kunder.

Information om hur du publicerar ett hanterat program till Marketplace finns [skapa marketplace program](publish-marketplace-app.md).

## <a name="resource-groups-for-managed-applications"></a>Resursgrupper för hanterade program

Resurser för ett hanterat program finns vanligtvis i två resursgrupper. Konsumenten hanterar en resursgrupp och utgivaren hanterar andra resursgruppen. När du definierar det hanterade programmet anger utgivaren av åtkomstnivåer. Följande bild illustrerar ett scenario där utgivaren begär rollen ägare för den hantera resursgruppen. Utgivaren placera ett skrivskyddat lås på den här resursgruppen konsumenten.

![Åtkomst till företagsresurser grupp](./media/overview/access.png)

### <a name="application-resource-group"></a>Programmet resursgruppen.

Den här resursgruppen innehåller hanterade programinstansen. Den här resursgruppen får bara innehålla en resurs. Resurstypen för det hanterade programmet är **Microsoft.Solutions/applications**.

Konsumenten har fullständig åtkomst till resursgruppen och används för att hantera livscykeln för det hanterade programmet.

### <a name="managed-resource-group"></a>Hanterade resursgruppen.

Den här resursgruppen innehåller alla resurser som krävs av det hanterade programmet. Den här resursgruppen innehåller till exempel virtuella datorer, lagringskonton och virtuella nätverk för lösningen. Konsumenten har begränsad åtkomst till den här resursgruppen eftersom klienten inte hantera enskilda resurser för det hanterade programmet. Utgivarens åtkomst till den här resursgruppen motsvarar rollen som angavs i programdefinitionen för hanterade. Utgivaren kan till exempel begära rollen ägare eller deltagare för den här resursgruppen.

När användaren tar bort det hanterade programmet, hanterade resursgruppen också att tas bort.

## <a name="next-steps"></a>Nästa steg

* En introduktion till definiera och distribuera hanterade program, finns [skapa och distribuera en Azure hanterade program med Azure CLI](managed-apps-quickstart-cli.md)
* Information om hur du publicerar en intern App finns i [skapa katalogen tjänstprogram](publish-service-catalog-app.md).
* Information om hur du publicerar hanterade program till marketplace finns [skapa marketplace program](publish-marketplace-app.md).

