---
title: Arbeta med flera klienter i Azure Sentinel för MSSP-tjänst leverantörer | Microsoft Docs
description: Så här arbetar du med flera klienter i Azure Sentinel för MSSP-tjänstleverantörer.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: fdb58686fcdd18a8e2861aab533717dbc91e8893
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "79476023"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Arbeta med flera klienter i Azure Sentinel 

Om du är en hanterad säkerhets tjänst leverantör (MSSP) och använder [Azure Lighthouse](../lighthouse/overview.md) för att hantera dina kunders säkerhets åtgärds Center (SOC), kan du hantera dina kunders Azure Sentinel-resurser utan att ansluta direkt till kundens klient organisation, från din egen Azure-klient. 

## <a name="prerequisites"></a>Krav
- [Publicera Azure-Lighthouse](../lighthouse/how-to/onboard-customer.md)
- För att detta ska fungera korrekt måste klienten vara registrerad på Azure Sentinel Resource Provider på minst en prenumeration. Om du har en registrerad Azure-Sentinel i din klient är du redo att komma igång. Om inte väljer du **prenumerationer** från Azure Portal, följt av **resurs leverantörer**.  Sedan går du till skärmen **SOC-Resource providers** och söker efter och väljer `Microsoft.OperationalInsights` och `Microsoft.SecurityInsights` väljer **Registrera**.
   ![Kontrol lera resurs leverantörer](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Få åtkomst till Azure Sentinel från andra klienter
1. Under **katalog + prenumeration**väljer du de delegerade katalogerna och de prenumerationer där kundens Azure Sentinel-arbetsytor finns.

   ![Generera säkerhets incidenter](media/multiple-tenants-service-providers/directory-subscription.png)

1. Öppna Azure Sentinel. Du ser alla arbets ytor i de valda prenumerationerna och du kommer att kunna arbeta med dem sömlöst, som alla arbets ytor i din egen klient organisation.

> [!NOTE]
> Du kommer inte att kunna distribuera kopplingar i Azure Sentinel inifrån en hanterad arbets yta. Om du vill distribuera en anslutning måste du direkt logga in på den klient som du vill distribuera en anslutning till och autentisera där med de behörigheter som krävs.





## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du hanterar flera Azure Sentinel-klienter sömlöst. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

