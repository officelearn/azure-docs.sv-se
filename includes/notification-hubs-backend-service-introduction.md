---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 98c9d83424e7d347bb6e1166a3b00304d39e0cce
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081523"
---
En [ASP.net Core webb-API-](https://dotnet.microsoft.com/apps/aspnet/apis) backend används för att hantera [enhets registrering](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) för klienten med den senaste och bästa [installations](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#installations) metoden. Tjänsten kommer också att skicka push-meddelanden på plattforms oberoende sätt. 

Dessa åtgärder hanteras med hjälp av [Notification HUBS SDK för Server dels åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). Mer information om den övergripande metoden finns i [registreringen från appens Server dels](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#registration-management-from-a-backend) dokumentation.
