---
ms.openlocfilehash: b4fbeb6baa4516ca4cf3ca6194195fae2c688b07
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165674"
---
Standard tids zonen som används med CRON-uttryck är Coordinated Universal Time (UTC). Om du vill att ditt CRON-uttryck ska baseras på en annan tidszon skapar du en app-inställning för din Function-app med namnet `WEBSITE_TIME_ZONE` . 

Värdet för den här inställningen beror på vilket operativ system och vilken funktion appen körs på.

|Operativsystem |Planera |Värde |
|-|-|-|
| **Windows** |Alla | Ange värdet till namnet på önskad tidszon enligt beskrivningen i [Microsoft Time Zone index] ( https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10) . |
| **Linux** |Premium<br/>Dedikerad |Ange värdet till namnet på den önskade tids zonen som visas i [TZ-databasen](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE`stöds för närvarande inte i Linux-förbruknings planen.

Till exempel är *Eastern Standard Time* (Windows) eller *America/New_York* (Linux) UTC-05:00. Använd följande NCRONTAB-uttryck som kontona för UTC-tidszonen för att utlösa timer-utlösare vid 10:00 AM EST per dag:

```
"0 0 15 * * *"
``` 

Eller skapa en app-inställning för din Function-app med namnet `WEBSITE_TIME_ZONE` , ange värdet till `Eastern Standard Time` (Windows) eller `America/New_York` (Linux) och Använd sedan följande NCRONTAB-uttryck: 

```
"0 0 10 * * *"
``` 

När du använder `WEBSITE_TIME_ZONE` , justeras tiden för tid ändringar i den angivna tids zonen, t. ex. sommar tid. 
