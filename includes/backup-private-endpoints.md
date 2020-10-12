---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: b8941bb4fa77cb0008cb7271681e972bc21d6588
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "83659536"
---
Du kan nu använda [privata slut punkter](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) för att säkerhetskopiera dina data på ett säkert sätt från servrar i ett virtuellt nätverk till ditt Recovery Services-valv. Den privata slut punkten använder en IP-adress från VNET-adressutrymmet för ditt valv. Nätverks trafiken mellan resurserna i det virtuella nätverket och valvet överförs över ditt virtuella nätverk och en privat länk i Microsoft stamnät nätverket. Detta eliminerar exponeringen från det offentliga Internet. Privata slut punkter kan användas för att säkerhetskopiera och återställa SQL-och SAP HANA-databaser som körs i dina virtuella Azure-datorer. Det kan också användas för lokala servrar som använder MARS-agenten.

Den virtuella Azure-säkerhetskopieringen kräver inte Internet anslutning och kräver därför inte privata slut punkter för att tillåta nätverks isolering.

Läs mer om privata slut punkter för Azure Backup [här](https://docs.microsoft.com/azure/backup/private-endpoints).
