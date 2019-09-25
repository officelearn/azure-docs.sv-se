---
title: Arbeta med flera klienter i Azure Sentinel för MSSP-tjänst leverantörer | Microsoft Docs
description: Så här arbetar du med flera klienter i Azure Sentinel för MSSP-tjänstleverantörer.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 34997fb3cd91e4540c76ecdd781d21e2ed1bdb07
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240495"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Arbeta med flera klienter i Azure Sentinel 

Om du är en hanterad säkerhets tjänst leverantör (MSSP) och använder [Azure Lighthouse](../lighthouse/overview.md) för att hantera dina kunders säkerhets åtgärds Center (SOC), kan du hantera dina kunders Azure Sentinel-resurser utan att ansluta direkt till kundens klient organisation, från din egen Azure-klient. 

## <a name="prerequisites"></a>Förutsättningar
- [Publicera Azure-Lighthouse](../lighthouse/how-to/onboard-customer.md)
- För att detta ska fungera korrekt måste klienten vara registrerad på Azure Sentinel Resource Provider på minst en prenumeration. Om du har en registrerad Azure-Sentinel i din klient är du redo att komma igång. Om inte, i Azure Portal väljer du **prenumerationer** följt av **resurs leverantörer** och söker sedan efter `Microsoft.Security.Insights` och väljer **Registrera**.
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

