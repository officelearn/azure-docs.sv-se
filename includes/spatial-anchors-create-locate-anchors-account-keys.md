---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 9bd213b63b69a25fb2530cd8f6659abf5357616a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76694636"
---
## <a name="set-up-authentication"></a>Konfigurera autentisering

För att komma åt tjänsten måste du ange en kontonyckel, åtkomsttoken eller Azure Active Directory-auth-token. Du kan också läsa mer om detta på [sidan Autentiseringskoncept](/azure/spatial-anchors/concepts/authentication).

### <a name="account-keys"></a>Kontonycklar

Kontonycklar är en autentiseringsinformation som gör att ditt program kan autentiseras med Azure Spatial Anchors-tjänsten. Det avsedda syftet med Kontonycklar är att hjälpa dig att komma igång snabbt. Speciellt under utvecklingsfasen av programmets integrering med Azure Spatial Anchors. Du kan därför använda kontonycklar genom att bädda in dem i klientprogrammen under utvecklingen. När du går vidare utöver utvecklingen rekommenderar vi starkt att du flyttar till en autentiseringsmekanism som är produktionsnivå, som stöds av åtkomsttoken eller Azure Active Directory-användarautentisering. Om du vill hämta en kontonyckel för utveckling besöker du ditt Azure Spatial Anchors-konto och navigerar till fliken Nycklar.
