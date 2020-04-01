---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 574003a150ef294aa6a2ebc035fe48cf877dec66
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76545232"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Konfigurera molnbaserade rumsliga ankarsession

Vi tar hand om att konfigurera molnet rumsliga ankare session nästa. På första raden ställer vi in sensorleverantören på sessionen. Från och med nu kommer alla ankare vi skapar under sessionen att associeras med en uppsättning sensoravläsningar. Därefter instansierar vi en nära enhet hitta kriterier och initiera den för att matcha ansökan krav. Slutligen instruerar vi sessionen att använda sensordata när du hittar ankare genom att skapa en watcher från våra kriterier för nära enheter.