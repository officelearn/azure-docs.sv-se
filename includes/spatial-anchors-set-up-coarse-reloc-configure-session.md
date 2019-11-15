---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 7ac98f608cb1ca5bab590714623bc78036d46b81
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093141"
---
## <a name="configure-the-cloud-anchor-session"></a>Konfigurera molnets Anchor-session

Vi tar hand om konfigureringen av molnets Anchor-session härnäst. På den första raden ställer vi in sensor leverantören i sessionen. Från och med nu kommer alla ankare som vi skapar under sessionen att associeras med en uppsättning sensor avläsningar. Härnäst instansierar vi en nära enhet som letar efter kriterier och initierar den för att matcha program kraven. Slutligen instruerar vi sessionen att använda sensor data när du söker efter ankare genom att skapa en bevakare från vårt nära enhets kriterium.