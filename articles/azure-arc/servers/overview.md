---
title: Översikt över Azure Arc for Servers (för hands version)
description: Lär dig hur du använder Azure Arc för servrar för att hantera datorer som ligger utanför Azure, som om det är en Azure-resurs.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: Azure Automation, DSC, PowerShell, önskad tillstånds konfiguration, uppdaterings hantering, ändrings spårning, inventering, Runbooks, python, grafisk, hybrid
ms.date: 03/24/2020
ms.topic: overview
ms.openlocfilehash: e775945526a5453085946ed4eea2a2e19761ba78
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2020
ms.locfileid: "85482198"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Vad är Azure Arc för servrar (för hands version)?

Med Azure Arc för servrar (för hands version) kan du hantera dina Windows-och Linux-datorer utanför Azure på företagets nätverk eller annan moln leverantör, på samma sätt som du hanterar virtuella datorer i Azure. När en hybrid dator är ansluten till Azure blir den en ansluten dator och behandlas som en resurs i Azure. Varje ansluten dator har ett resurs-ID, hanteras som en del av en resurs grupp i en prenumeration och fördelar med Azures standard konstruktioner, till exempel Azure Policy och att använda taggar.

För att kunna leverera den här upplevelsen med dina hybrid datorer utanför Azure måste den Azure-anslutna dator agenten installeras på varje dator som du planerar att ansluta till Azure. Den här agenten levererar inga andra funktioner och ersätter inte Azure [Log Analytics-agenten](../../azure-monitor/platform/log-analytics-agent.md). Log Analytics agent för Windows och Linux krävs om du vill övervaka operativ system och arbets belastningar som körs på datorn proaktivt, hantera den med hjälp av Automation-runbooks eller lösningar som Uppdateringshantering eller använda andra Azure-tjänster som [Azure Security Center](../../security-center/security-center-intro.md).

>[!NOTE]
>Den här för hands versionen är avsedd i utvärderings syfte och vi rekommenderar att du inte hanterar kritiska produktions datorer.
>

## <a name="supported-scenarios"></a>Scenarier som stöds

Azure-båge för servrar (för hands version) stöder följande scenarier med anslutna datorer:

- Tilldela [Azure policy gäst konfigurationer](../../governance/policy/concepts/guest-configuration.md) med samma erfarenhet som princip tilldelning för virtuella Azure-datorer.
- Loggdata som samlas in av Log Analytics-agenten som lagras i arbets ytan Log Analytics som datorn har registrerats. Loggdata från hybrid datorn innehåller nu egenskaper som är speciella för datorn, till exempel ett resurs-ID som kan användas för att ge stöd åt [resurs kontexts](../../azure-monitor/platform/design-logs-deployment.md#access-mode) loggar.

## <a name="supported-regions"></a>Regioner som stöds

Med Azure Arc for Servers (för hands version) stöds endast vissa regioner:

- Platsen eastus
- WestUS2
- Västeuropa
- Sydostasien

I de flesta fall ska den plats som du väljer när du skapar installations skriptet vara den Azure-region som är geografiskt närmast din dators plats. Data i vila lagras i den region i Azure som innehåller den region som du anger, vilket även kan påverka ditt val av region om du har data placering krav. Om den Azure-region som datorn är ansluten till påverkas av ett avbrott påverkas inte den anslutna datorn, men hanterings åtgärder som använder Azure kan inte slutföras. För återhämtning i händelse av ett regionalt avbrott, om du har flera platser som tillhandahåller en geografiskt redundant tjänst, är det bäst att ansluta datorerna på varje plats till en annan Azure-region.

### <a name="agent-status"></a>Agent status

Den anslutna dator agenten skickar ett vanligt pulsslags meddelande till tjänsten var 5: e minut. Om tjänsten slutar att ta emot dessa pulsslags meddelanden från en dator anses datorn vara offline och statusen ändras automatiskt till **frånkopplad** i portalen inom 15 till 30 minuter. När du tar emot ett efterföljande pulsslags meddelande från den anslutna dator agenten kommer dess status automatiskt att ändras till **ansluten**.

## <a name="next-steps"></a>Nästa steg

Innan du utvärderar eller aktiverar båge för servrar (förhands granskning) över flera hybrid datorer kan du läsa artikeln [Översikt över anslutna dator agenter](agent-overview.md) för att förstå vad som krävs, teknisk information om agenten och distributions metoder.
