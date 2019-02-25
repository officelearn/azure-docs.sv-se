---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 0e4c0b6d622fe0d8d20692ec6c0c889779e4601a
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753234"
---
### <a name="access-tokens"></a>Åtkomsttoken

Åtkomsttoken är en mer stabil metod för att autentisera med Azure Spatial fästpunkter. Särskilt när du förbereder ditt program för en Produktionsdistribution. Sammanfattning av den här metoden är att ställa in en backend-tjänst som ditt klientprogram på ett säkert sätt kan autentisera med. Backend-tjänst gränssnitten med AAD vid körning och med Azure Spatial ankare STS-tjänsten och begär en åtkomsttoken. Denna token är sedan levereras till klientprogrammet och används i SDK för att autentisera med Azure Spatial fästpunkter.
