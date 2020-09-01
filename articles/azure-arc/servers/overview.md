---
title: Översikt över Azure Arc-aktiverade servrar (för hands version)
description: Lär dig hur du använder Azure Arc-aktiverade servrar (för hands version) för att hantera servrar som ligger utanför Azure som en Azure-resurs.
keywords: Azure Automation, DSC, PowerShell, önskad tillstånds konfiguration, uppdaterings hantering, ändrings spårning, inventering, Runbooks, python, grafisk, hybrid
ms.custom: references_regions
ms.date: 08/06/2020
ms.topic: overview
ms.openlocfilehash: c368307df911f5143541bb5337eb76a208416909
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228095"
---
# <a name="what-is-azure-arc-enabled-servers-preview"></a>Vad är Azure Arc-aktiverade servrar (för hands version)?

Med Azure Arc-aktiverade servrar (för hands version) kan du hantera dina Windows-och Linux-datorer utanför Azure, i företags nätverket eller någon annan moln leverantör, på samma sätt som du hanterar interna virtuella Azure-datorer. När en hybrid dator är ansluten till Azure blir den en ansluten dator och behandlas som en resurs i Azure. Varje ansluten dator har ett resurs-ID, hanteras som en del av en resurs grupp i en prenumeration och fördelar med Azures standard konstruktioner, till exempel Azure Policy och att använda taggar.

För att kunna leverera den här upplevelsen med dina hybrid datorer utanför Azure måste den Azure-anslutna dator agenten installeras på varje dator som du planerar att ansluta till Azure. Den här agenten levererar inga andra funktioner och ersätter inte Azure [Log Analytics-agenten](../../azure-monitor/platform/log-analytics-agent.md). Log Analytics agent för Windows och Linux krävs om du vill övervaka operativ system och arbets belastningar som körs på datorn proaktivt, hantera den med hjälp av Automation-runbooks eller lösningar som Uppdateringshantering eller använda andra Azure-tjänster som [Azure Security Center](../../security-center/security-center-intro.md).

>[!NOTE]
>Den här för hands versionen är avsedd i utvärderings syfte och vi rekommenderar att du inte hanterar kritiska produktions datorer.
>

## <a name="supported-scenarios"></a>Scenarier som stöds

När du ansluter datorn till Azure Arc-aktiverade servrar (för hands version) gör det möjligt att utföra följande konfigurations hanterings aktiviteter:

- Tilldela [Azure policy gäst konfigurationer](../../governance/policy/concepts/guest-configuration.md) med samma erfarenhet som princip tilldelning för virtuella Azure-datorer.

- Övervaka din anslutna dators prestanda för gäst operativ system och identifiera program komponenter för att övervaka deras processer och beroenden med andra resurser som programmet kommunicerar med hjälp av [Azure Monitor for VMS](../../azure-monitor/insights/vminsights-overview.md).

- Förenkla distributionen med andra Azure-tjänster som Azure Automation tillstånds konfiguration och Azure Monitor Log Analytics arbets yta med [Azure VM-tillägg](manage-vm-extensions.md) som stöds för Windows-eller Linux-datorer som inte kommer från Azure. Detta inkluderar konfiguration av konfiguration eller program vara efter distribution med hjälp av tillägget för anpassat skript.

Loggdata som samlas in och lagras i en Log Analytics-arbetsyta från hybrid datorn innehåller nu egenskaper som är speciella för datorn, till exempel ett resurs-ID. Detta kan användas för att ge stöd åt [resurs Sammanhangs](../../azure-monitor/platform/design-logs-deployment.md#access-mode) logg åtkomst.

## <a name="supported-regions"></a>Regioner som stöds

Med Azure Arc-aktiverade servrar (för hands version) stöds endast vissa regioner:

- Platsen eastus
- WestUS2
- Västeuropa
- Sydostasien

I de flesta fall ska den plats som du väljer när du skapar installations skriptet vara den Azure-region som är geografiskt närmast din dators plats. Data i vila lagras i den region i Azure som innehåller den region som du anger, vilket även kan påverka ditt val av region om du har data placering krav. Om den Azure-region som datorn är ansluten till påverkas av ett avbrott påverkas inte den anslutna datorn, men hanterings åtgärder som använder Azure kan inte slutföras. I händelse av ett regionalt avbrott, om du har flera platser som stöder en geografiskt redundant tjänst, är det bäst att ansluta datorerna på varje plats till en annan Azure-region.

### <a name="agent-status"></a>Agent status

Den anslutna dator agenten skickar ett vanligt pulsslags meddelande till tjänsten var 5: e minut. Om tjänsten slutar att ta emot dessa pulsslags meddelanden från en dator anses datorn vara offline och statusen ändras automatiskt till **frånkopplad** i portalen inom 15 till 30 minuter. När du tar emot ett efterföljande pulsslags meddelande från den anslutna dator agenten kommer dess status automatiskt att ändras till **ansluten**.

## <a name="next-steps"></a>Nästa steg

Innan du utvärderar eller aktiverar Arc-aktiverade servrar (förhands granskning) över flera hybrid datorer kan du läsa [Översikt över anslutna dator agenter](agent-overview.md) för att förstå krav, teknisk information om agenten och distributions metoder.
