---
title: Översikt över Azure Blueprint
description: Azure Blueprint är en tjänst i Azure som används för att skapa, definiera och distribuera artefakter i Azure-miljön.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: overview
ms.service: blueprints
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: c7ffbe86407bc776870890e5b4151556f572832e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957524"
---
# <a name="what-is-azure-blueprints"></a>Vad är Azure Blueprint?

Precis som en skiss tillåter en tekniker eller arkitekt att skissa designparametrarna för ett projekt kan Azure Blueprint göra det möjligt för molnarkitekter och centralt IT-ansvariga att definiera en upprepningsbar uppsättning med Azure-resurser som implementerar och tillämpar en organisations standarder, mönster och krav. Med Azure Blueprint kan utvecklingsteam snabbt etablera och skapa nya miljöer med vetskapen om att de är skapade med organisatorisk efterlevnad och innehåller en uppsättning inbyggda komponenter – som nätverk – för att påskynda utveckling och leverans.

Skisser är en deklarativ metod för att dirigera distribution av flera resursmallar och andra artefakter som:

- Rolltilldelningar
- Principtilldelningar
- Azure Resource Manager-mallar
- Resursgrupper

## <a name="how-it-is-different-from-resource-manager-templates"></a>Så här skiljer de sig från Resource Manager-mallar

Skisser har utformats för att hjälpa till med _miljökonfiguration_, som ofta består av en uppsättning resursgrupper, principer och rolltilldelningar utöver Resource Manager-malldistributioner. En skiss är ett paket som ska sammanföra var och en av dessa _artefakttyper_ och gör att du kan skriva och skapa en version för paketet – inklusive via en CI/CD-pipeline. Slutligen tilldelas varje skiss till en prenumeration i en enda åtgärd som kan granskas och spåras.

Nästan allt du vill inkludera för distribution i skisser kan åstadkommas med en Resource Manager-mall. Men en Resource Manager-mall är ett dokument som inte finns inbyggt i Azure – var och en är lagrad lokalt eller i källkontrollen. Mallen används för distribution av en eller flera Azure-resurser, men när resurserna har distribuerats förloras anslutningen och relationen till mallen som användes.

Med skisser finns relationen mellan skissdefinitionen (_som ska_ distribueras) och skisstilldelningen (_vad har_ distribuerats) kvar. Den här anslutningen möjliggör förbättrad spårning och granskning av distributioner, möjlighet att uppgradera flera prenumerationer samtidigt som regleras av samma skiss med mera.

Du behöver inte välja mellan en Resource Manager-mall och en skiss. Varje skiss kan bestå av noll eller flera Resource Manager-mall-_artefakter_. Det innebär att tidigare arbete med att utveckla och underhålla ett bibliotek med Resource Manager-mallar kan användas i skisser.

## <a name="how-it-is-different-from-azure-policy"></a>Hur det skiljer sig från Azure Policy

En skiss är ett paket eller en container för att skapa fokus-specifika uppsättningar standarder, mönster och krav för implementering av Azure-molntjänster, säkerhet och design som kan återanvändas för att säkerställa konsekvens och efterlevnad.

En [princip](../policy/overview.md) är en standard för att tillåta och explicit neka system som fokuserar på resursegenskaper under distributionen och för redan befintliga resurser. Det stöder IT-styrning genom att se till att resurser inom en prenumeration följer krav och standarder.

När en princip inkluderas i en skiss innebär det inte enbart att rätt mönster eller rätt design skapas under tilldelningen av skissen. Det garanterar även att endast godkända eller förväntade ändringar kan göras i miljön för att säkerställa pågående efterlevnad för skissens syfte.

En princip kan ingå som en av flera _artefakter_ i en definition av skisser. Med skisser kan du även använda parametrar med principer och initiativ.

## <a name="blueprint-definition"></a>Definition av skisser

En skiss består av _artefakter_. Skisser stöder för närvarande följande resurser som artefakter:

|Resurs  | Hierarkialternativ| Beskrivning  |
|---------|---------|---------|
|Resursgrupper     | Prenumeration | Skapa en ny resursgrupp för användning av andra artefakter i skissen.  Med de här platshållarresursgrupperna kan du organisera resurser precis som du vill att de struktureras, och de ger en omfångsbegränsning för inkluderade princip- och rolltilldelningsartefakter samt Azure Resource Manager-mallar.         |
|Azure Resource Manager-mall      | Resursgrupp | Mallarna kan användas till att skapa komplexa miljöer som en SharePoint-servergrupp, Azure Automation State Configuration eller en Log Analytics-arbetsyta. |
|Principtilldelning     | Prenumeration, resursgrupp | Tillåter tilldelning av en princip eller ett initiativ till hanteringsgruppen eller prenumerationen som skissen tilldelas. Principen eller initiativet måste vara inom skissens omfång (i skissens hanteringsgrupp eller tidigare). Om principen eller initiativet har parametrar kan de parametrarna tilldelas vid skapandet av skissen eller under skisstilldelningen.       |
|Rolltilldelning   | Prenumeration, resursgrupp | Lägg till en befintlig användare eller grupp till en inbyggd roll för att se till att rätt personer alltid har rätt åtkomst till dina resurser. Rolltilldelningar kan definieras för hela prenumerationen eller kapslade till en specifik resursgrupp som ingår i skissen. |

### <a name="blueprints-and-management-groups"></a>Skisser och hanteringsgrupper

När du skapar en skissdefinition ska du definiera var skissen har sparats. Skisser kan för närvarande endast sparas till en [hanteringsgrupp](../management-groups/overview.md) som du har **deltagaråtkomst**. Skissen kommer sedan att kunna tilldela till alla underordnade (hanteringsgrupp eller prenumeration) för den hanteringsgruppen.

> [!IMPORTANT]
> Om du inte har åtkomst till några hanteringsgrupper eller om inga hanteringsgrupper har konfigurerats visar en inläsning av listan över skissdefinitioner att ingen är tillgänglig. Om du klickar på **Omfång** öppnas ett fönster med en varning om att hämta hanteringsgrupper. Lös detta genom att se till att en prenumeration som du har lämplig åtkomst till är en del av en [hanteringsgrupp](../management-groups/overview.md).

### <a name="blueprint-parameters"></a>Skissparametrar

Skisser kan skicka parametrar till antingen en princip/ett initiativ eller en Azure Resource Manager-mall.
När en _artefakt_ läggs till i en skiss kan författaren besluta sig för att tillhandahålla ett angivet värde för varje skisstilldelning eller tillåta att varje skisstilldelning anger ett värde för tilldelningstiden. Tack vare den här flexibiliteten får du möjlighet att definiera ett förinställt värde för alla användare av skissen eller göra det möjligt för beslutet att göras vid tidpunkten för tilldelningen.

> [!NOTE]
> En skiss kan ha sina egna parametrar, men de kan för närvarande bara skapas om en skiss genereras från REST API:et i stället för via portalen.

Mer information finns [Skissparametrar](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Publicering av skiss

När en skiss först skapas betraktas den som att den är i **utkastläge**. När den är redo att tilldelas måste den vara **publicerad**. Publicering kräver att du definierar en **versionssträng** (bokstäver, siffror och bindestreck med en maximal längd på 20 tecken) tillsammans med valfria **Ändra anteckningar**.
**Versionen** särskiljer den från framtida ändringar i samma skiss och gör att varje version ska tilldelas. Det innebär också att olika **versioner** i samma skiss kan tilldelas till samma prenumeration. När ytterligare ändringar görs i skissen finns fortfarande den **publicerade** **versionen**, utöver **Opublicerade ändringar**. När ändringarna har slutförts är den uppdaterade skissen **publicerad** med en ny och unik **version** och kan nu också tilldelas.

## <a name="blueprint-assignment"></a>Skisstilldelning

Varje **publicerad** **version** av en skiss kan tilldelas till en befintlig prenumeration. I portalen får skissen som standard den **version** som **publicerades** senast. Om det finns artefaktparametrar (eller skissparametrar) definieras sedan parametrarna under tilldelningen.

## <a name="permissions-in-azure-blueprints"></a>Behörigheter i Azure Blueprint

Om du vill använda skisser måste du ha behörighet via [rollbaserad åtkomstkontroll](../../role-based-access-control/overview.md) (RBAC). Om du vill skapa skisser måste ditt konto ha följande behörigheter:

- `Microsoft.Blueprint/blueprints/write` – Skapa en skissdefinition
- `Microsoft.Blueprint/blueprints/artifacts/write` – Skapa artefakter på en skissdefinition
- `Microsoft.Blueprint/blueprints/versions/write` – Publicera en skiss

Om du vill ta bort skisser måste ditt konto ha följande behörigheter:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> Eftersom skissdefinitioner skapas i en hanteringsgrupp måste, behörigheterna för skissens definition beviljas i ett hanteringsgruppomfång eller ärvas till ett hanteringsgruppomfång.

Om du vill tilldela eller ta bort tilldelningar av skisser måste ditt konto ha följande behörigheter:

- `Microsoft.Blueprint/blueprintAssignments/write` – Tilldela en skiss
- `Microsoft.Blueprint/blueprintAssignments/delete` – Ta bort tilldelning av en skiss

> [!NOTE]
> Tilldelningar för skissen skapas på en prenumeration. Behörigheterna för tilldelning och för att ta bort tilldelning av behörigheter måste beviljas för eller ärvas till en prenumerationsomfattning.

Dessa behörigheter har inkluderats i rollen **Ägare** och, med undantag för behörigheterna för skisstilldelningen, ingår även i rollen **Deltagare**. Om dessa inbyggda roller inte motsvarar dina behov kan du skapa en [anpassad roll](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Tjänstens huvudnamn för Azure Blueprint kräver rollen **Ägare** på den tilldelade prenumerationen för att aktivera distribution. Om du använder portalen är den här rollen automatiskt beviljad och återkallad för distributionen. Om du använder REST API måste den här rollen måste beviljas manuellt, men återkallas fortfarande automatiskt när distributionen är klar.

## <a name="next-steps"></a>Nästa steg

- [Skapa en skiss – Portal](create-blueprint-portal.md)
- [Skapa en skiss – REST API](create-blueprint-rest-api.md)