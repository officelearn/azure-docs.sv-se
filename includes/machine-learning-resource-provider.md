---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75893978"
---
När du skapar en Azure Machine Learning-arbetsyta eller en resurs som används av arbetsytan kan du få ett fel som liknar följande meddelanden:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

De flesta resursleverantörer registreras automatiskt, men inte alla. Om du får det här meddelandet måste du registrera den nämnda leverantören.

Information om hur du registrerar resursleverantörer finns i [Lösa fel för registrering av resursprovider](../articles/azure-resource-manager/templates/error-register-resource-provider.md).