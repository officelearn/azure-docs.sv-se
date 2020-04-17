---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81539387"
---
Du kan nu använda [privata slutpunkter](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) för att säkerhetskopiera dina data på ett säkert sätt från servrar i ett virtuellt nätverk till ditt Recovery Services-valv. Den privata slutpunkten använder en IP från VNET-adressutrymmet för ditt valv. Nätverkstrafiken mellan dina resurser i det virtuella nätverket och valvet färdas över ditt virtuella nätverk och en privat länk i Microsofts stamnätsnätverk. Detta eliminerar exponering från det offentliga internet. Privata slutpunkter kan användas för säkerhetskopiering och återställning av SQL- och SAP HANA-databaser som körs i dina virtuella Azure-datorer. Den kan också användas för dina lokala servrar med MARS-agenten.

Azure VM-säkerhetskopiering kräver inte internetanslutning och kräver därför inte privata slutpunkter för att tillåta nätverksisolering.

>[!NOTE]
> Den här funktionen är för närvarande i begränsad tillgänglighet och är tillgänglig i Västra centrala USA, Södra centrala USA, VÄSTRA USA 2 och ÖSTRA USA (tillgänglighet i andra Azure-regioner att följa). Fyll i [den här enkäten](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) och maila oss på azbackupnetsec@microsoft.com om du är intresserad av att använda privata slutpunkter för Azure Backup. Möjligheten att använda den här funktionen måste godkännas från Azure Backup-tjänsten.
