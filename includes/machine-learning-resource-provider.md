---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "75893978"
---
När du skapar en Azure Machine Learning arbets yta eller en resurs som används av arbets ytan kan du få ett fel meddelande som liknar följande meddelanden:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

De flesta resurs leverantörer registreras automatiskt, men inte alla. Om du får det här meddelandet måste du registrera den angivna providern.

Information om hur du registrerar resurs leverantörer finns i [lösa fel för registrering av resurs leverantör](../articles/azure-resource-manager/templates/error-register-resource-provider.md).