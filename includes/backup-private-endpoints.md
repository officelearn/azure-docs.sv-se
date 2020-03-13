---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: a3bae0ce3e6ebcf64936d0ca4af4fb702e5ea404
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137032"
---
Du kan nu använda [privata slut punkter](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) för att säkerhetskopiera dina data på ett säkert sätt från servrar i ett virtuellt nätverk till ditt Recovery Services-valv. Den privata slut punkten använder en IP-adress från VNET-adressutrymmet för ditt valv. Nätverks trafiken mellan resurserna i det virtuella nätverket och valvet överförs över ditt virtuella nätverk och en privat länk i Microsoft stamnät nätverket. Detta eliminerar exponeringen från det offentliga Internet. Privata slut punkter kan användas för att säkerhetskopiera och återställa SQL-och SAP HANA-databaser som körs i dina virtuella Azure-datorer. Det kan också användas för lokala servrar som använder MARS-agenten.

Den virtuella Azure-säkerhetskopieringen kräver inte Internet anslutning och kräver därför inte privata slut punkter för att tillåta nätverks isolering.

>[!NOTE]
> Den här funktionen är för närvarande i tidig användning. Fyll i [den här undersökningen](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) och skicka e-post till oss azbackupnetsec@microsoft.com om du är intresse rad av att använda privata slut punkter för Azure Backup. Möjligheten att använda den här funktionen är beroende av godkännande från Azure Backups tjänsten.
