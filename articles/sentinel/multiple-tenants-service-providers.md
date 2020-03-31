---
title: Arbeta med flera klienter till Azure Sentinel för MSSP-tjänsteleverantörer| Microsoft-dokument
description: Så här arbetar du med flera klienter till Azure Sentinel för MSSP-tjänstleverantörer.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476023"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Arbeta med flera klienter i Azure Sentinel 

Om du är en hanterad leverantör av säkerhetstjänster (MSSP) och du använder [Azure Lighthouse](../lighthouse/overview.md) för att hantera dina kunders säkerhetsoperationscenter (SOC) kan du hantera dina kunders Azure Sentinel-resurser utan att ansluta direkt till kundens klientorganisation, från din egen Azure-klientorganisation. 

## <a name="prerequisites"></a>Krav
- [Inbyggd Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)
- För att det här ska fungera korrekt måste din klient vara registrerad på Azure Sentinel Resource Provider på minst en prenumeration. Om du har en registrerad Azure Sentinel i din klient är du redo att komma igång. Om inte, välj **Prenumerationer** från Azure-portalen, följt av **resursleverantörer**.  Sedan, från **soc - resursleverantörer** skärmen, `Microsoft.OperationalInsights` `Microsoft.SecurityInsights`söka efter och välj och , och välj **Registrera**.
   ![Kontrollera resursleverantörer](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Så här kommer du åt Azure Sentinel från andra klienter
1. Under **Katalog + prenumeration**väljer du de delegerade katalogerna och de prenumerationer där kundens Azure Sentinel-arbetsytor finns.

   ![Generera säkerhetsincidenter](media/multiple-tenants-service-providers/directory-subscription.png)

1. Öppna Azure Sentinel. Du kommer att se alla arbetsytor i de valda prenumerationerna och du kommer att kunna arbeta med dem sömlöst, som alla arbetsytor i din egen klientorganisation.

> [!NOTE]
> Du kommer inte att kunna distribuera kopplingar i Azure Sentinel från en hanterad arbetsyta. Om du vill distribuera en koppling måste du logga in direkt på klienten där du vill distribuera en koppling och autentisera där med de behörigheter som krävs.





## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du hanterar flera Azure Sentinel-klienter sömlöst. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

