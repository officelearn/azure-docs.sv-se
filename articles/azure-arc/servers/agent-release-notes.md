---
title: Vad är nytt med Azure Arc-aktiverade servrar (för hands version)
description: Den här artikeln innehåller viktig information om Azure Arc-aktiverade servrar (för hands version). För många av de sammanfattade problemen finns det länkar till ytterligare information.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 0f5060189d6f9cac620b49f414e0e27bed3e356e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236772"
---
# <a name="whats-new-with-azure-arc-enabled-servers-preview-agent"></a>Vad är nytt med Azure Arc-aktiverade servrar (för hands version)

Azure Arc-aktiverade servrar (förhands granskning) anslutna dator agenter får fort löp ande förbättringar. För att hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln information om:

- De senaste versionerna
- Kända problem
- Felkorrigeringar

## <a name="august-2020"></a>Augusti 2020

Version: 0,11

- Stöd för Ubuntu 20,04.

- Tillförlitlighets förbättringar för tilläggs distributioner.

### <a name="known-issues"></a>Kända problem

Om du använder en äldre version av Linux-agenten och konfigurerat den för att använda en proxyserver måste du konfigurera om inställningen för proxyservern efter uppgraderingen. Det gör du genom att köra `sudo azcmagent_proxy add http://proxyserver.local:83` .

## <a name="next-steps"></a>Nästa steg

Innan du utvärderar eller aktiverar Arc-aktiverade servrar (förhands granskning) över flera hybrid datorer kan du läsa [Översikt över anslutna dator agenter](agent-overview.md) för att förstå krav, teknisk information om agenten och distributions metoder.