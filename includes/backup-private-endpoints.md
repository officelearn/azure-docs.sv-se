---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81539387"
---
Du kan nu använda [privata slut punkter](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) för att säkerhetskopiera dina data på ett säkert sätt från servrar i ett virtuellt nätverk till ditt Recovery Services-valv. Den privata slut punkten använder en IP-adress från VNET-adressutrymmet för ditt valv. Nätverks trafiken mellan resurserna i det virtuella nätverket och valvet överförs över ditt virtuella nätverk och en privat länk i Microsoft stamnät nätverket. Detta eliminerar exponeringen från det offentliga Internet. Privata slut punkter kan användas för att säkerhetskopiera och återställa SQL-och SAP HANA-databaser som körs i dina virtuella Azure-datorer. Det kan också användas för lokala servrar som använder MARS-agenten.

Den virtuella Azure-säkerhetskopieringen kräver inte Internet anslutning och kräver därför inte privata slut punkter för att tillåta nätverks isolering.

>[!NOTE]
> Den här funktionen är för närvarande begränsad tillgänglig och är tillgänglig i USA, västra centrala USA, södra centrala USA, västra USA 2 och östra USA (tillgänglighet i andra Azure-regioner att följa). Fyll i [den här undersökningen](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) och kontakta oss på azbackupnetsec@microsoft.com om du är intresse rad av att använda privata slut punkter för Azure Backup. Möjligheten att använda den här funktionen är beroende av godkännande från Azure Backups tjänsten.
