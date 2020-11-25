---
author: msftradford
ms.author: parkerra
ms.date: 11/20/2020
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 41c0a6292bf7369a30fbb1e4bd474e70ffaedcb6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999776"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Konfigurera sessionen för moln rums ankare

Vi tar hand om konfigureringen av molnets spatiala Anchor-session härnäst. På den första raden ställer vi in sensor leverantören i sessionen. Från och med nu kommer alla ankare som vi skapar under sessionen att associeras med en uppsättning sensor avläsningar. Härnäst instansierar vi en nära enhet som letar efter kriterier och initierar den för att matcha program kraven. Slutligen instruerar vi sessionen att använda sensor data när du söker efter ankare genom att skapa en bevakare från vårt nära enhets kriterium.