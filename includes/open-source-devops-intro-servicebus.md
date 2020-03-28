---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67188033"
---
[Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview) är en meddelandemäklare [för företagsintegrering.](https://azure.microsoft.com/product-categories/integration/) Servicebuss stöder två typer av kommunikation: köer och ämnen. 

Köer stöder asynkron kommunikation mellan program. En app skickar meddelanden till en kö som lagrar meddelandena. Det mottagande programmet ansluter sedan till och läser meddelandena från kön.

Avsnitten stöder mönstret publicera och prenumerera, vilket möjliggör en 1:1-relation mellan meddelandeass upphovsmannen och meddelandemottagaren.Topics support the publish-subscribe pattern, which enables a one-to-many relation between the message originator and the messager receiver(s).