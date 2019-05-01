---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767126"
---
[Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview) är ett företag [integrering](https://azure.microsoft.com/product-categories/integration/) meddelandekö. Service bus stöder två typer av kommunikation: köer och ämnen. 

Köerna stöder asynkron kommunikation mellan program. En app skickar meddelanden till en kö, som lagrar meddelanden. Det mottagande programmet sedan ansluter till och läser in meddelanden från kön.

Ämnena stöder Publicera-prenumerera mönster som gör det möjligt för en en-till-många-relation mellan meddelande avsändaren och meddelande receiver(s).