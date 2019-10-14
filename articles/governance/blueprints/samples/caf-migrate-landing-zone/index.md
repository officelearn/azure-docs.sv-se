---
title: Exempel på skiss för CAF Migration-landningszon – översikt
description: Översikt och arkitektur för skissexemplet för CAF Migration-landningszonen.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/20/2019
ms.topic: sample
ms.service: blueprints
ms.custom: fasttrack-new
ms.openlocfilehash: c74ec9f425b99bb11011fce7b0b7bdca98005ad8
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72297872"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>Översikt över skissexemplet för Microsoft Cloud Adoption Framework for Azure Migration-landningszonen

Skissen för Microsoft Cloud Adoption Framework for Azure (CAF) Migration-landningszonen är en infrastrukturuppsättning som hjälper dig att förbereda migreringen av din första arbetsbelastning och att hantera din molnegendom i enlighet med CAF.

[CAF Foundation](../caf-foundation/index.md)-skissexemplet bygger vidare på det här exemplet.

## <a name="architecture"></a>Arkitektur

Skissexemplet för CAF Migration-landningszonen distribuerar grundläggande infrastrukturresurser i Azure som kan användas av organisationer till att förbereda sin prenumeration för migrering av virtuella datorer. Det hjälper även till att etablera de styrningskontroller som krävs för att hantera molnegendomen. Det här exemplet distribuerar och framtvingar resurser, principer och mallar som gör det möjligt för en organisation att på ett säkert sätt komma igång med Azure.

![CAF Migration-landningszon, bild som beskriver vad som installeras som en del av CAF-vägledningen för den inledande landningszonen ](../../media/caf-blueprints/caf-migration-landing-zone-architecture.png)

Den här miljön består av flera Azure-tjänster som används för att tillhandahålla säker, fullständigt övervakad och företagsfärdig styrning. Den här miljön består av:

- En [Azure Key Vault](../../../../key-vault/key-vault-overview.md)-instans som värdhanterar de hemligheter som används för de certifikat, nycklar och hemligheter som distribueras i miljön för delade tjänster
- [Log Analytics](../../../../azure-monitor/overview.md) distribueras för att se till att alla åtgärder och tjänster loggas till en central plats direkt när du påbörjar din migrering
- Distribution av [Azure Security Center](../../../../security-center/security-center-intro.md) (standardversionen) ger skydd mot hot för dina migrerade arbetsbelastningar.
- Distribution av [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md) tillhandahåller ett isolerat nätverk och undernät för din virtuella dator.
- Distribution av [Azure Migrate-projekt](../../../..//migrate/migrate-overview.md) för identifiering och utvärdering. Vi lägger till verktygen för serverutvärdering, servermigrering, databasutvärdering samt databasmigrering.  


Alla dessa element följer beprövade metoder som finns publicerade i [Referensarkitekturer i Azure Architecture Center](/azure/architecture/reference-architectures/).

> [!NOTE]
> CAF Migration-skissen utgör en landningszon för dina arbetsbelastningar. Du behöver fortfarande utföra utvärderingen och migreringen av dina virtuella datorer/databaser ovanpå den här grundläggande arkitekturen.

Mer information finns i [Microsoft Cloud Adoption Framework for Azure – migrera](/azure/architecture/cloud-adoption/migrate/).

## <a name="next-steps"></a>Nästa steg

Du har läst översikten och arkitekturen för skissexemplet för CAF Migrate-landningszonen.

> [!div class="nextstepaction"]
>  [Skiss för CAF Migration-landningszon – distributionssteg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).