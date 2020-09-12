---
title: Azure privat länk för Azure Data Factory
description: Lär dig om den privata Azure-länken fungerar i Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 164a1005d9382711874b644e14b23d2154d613a0
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596030"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure privat länk för Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Med privat länk kan du ansluta till olika PaaS-tjänster i Azure via en privat slut punkt. En lista över PaaS-tjänster som stöder funktionen för privat länk finns på [dokumentations sidan för privat länk](https://docs.microsoft.com/azure/private-link/). En privat slut punkt är en privat IP-adress inom ett särskilt virtuellt nätverk och undernät.

## <a name="secure-communication-between-customer-network-and-azure-data-factory-service"></a>Säker kommunikation mellan kundens nätverk och Azure Data Factory tjänsten
För att skydda dina Azure-resurser mot attacker i offentliga nätverk eller för att kommunicera säkert med varandra kan du konfigurera ett Azure-Virtual Network som en logisk representation av ditt nätverk i molnet. Du kan också ansluta ett lokalt nätverk till ditt virtuella nätverk genom att konfigurera IPSec VPN (plats-till-plats) eller ExpressRoute (privat peering). Den lokala Integration Runtime kan installeras på en lokal dator eller virtuell dator i Virtual Network för att köra kopierings aktiviteter mellan ett moln data lager och ett data lager i ett privat nätverk eller skicka omvandlings aktiviteter mot beräknings resurser i ett lokalt nätverk eller i ett virtuellt Azure-nätverk. 

Det finns flera kommunikations kanaler som krävs mellan Data Factory och kundens virtuella nätverk.


| **Domän** | **Port** | **Beskrivning** |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Kontroll plan. Krävs för Data Factory redigering och övervakning. |
| `*.{region}.datafactory.azure.net` | 443 | Krävs av den Integration Runtime med egen värd för att ansluta till Data Factorys tjänsten. |
| `*.servicebus.windows.net` | 443 | Krävs av den egna värdbaserade Integration Runtime för interaktiv redigering. |
| `download.microsoft.com` | 443 | Krävs av den egna värdbaserade Integration Runtime för att ladda ned uppdateringarna. |


Med stöd för en privat Azure-länk för Azure Data Factory kan du skapa en privat slut punkt (PE) i det virtuella nätverket och aktivera den privata anslutningen till vissa Azure Data Factory. Kommunikationen med att Azure Data Factory tjänsten går via Azures privata länk som ger säker privat anslutning. Och du behöver inte konfigurera över domän och port i virtuellt nätverk eller i företags brand väggen som ger ett säkrare sätt att skydda dina resurser.  

![Azure Data Factory-arkitektur för privata länkar](./media/data-factory-private-link/private-link-architecture.png)

Här är fördelarna med att aktivera tjänsten för privat länk för var och en av de kommunikations kanaler som beskrivs ovan:
- Tillåtna Du kan redigera och övervaka Azure Data Factory i ditt virtuella nätverk, även om du blockerar all utgående kommunikation.
- Tillåtna Kommando kommunikationen mellan egen värd Integration Runtime och Azure Data Factory-tjänsten kan utföras på ett säkert sätt i en privat nätverks miljö. Trafiken mellan egen värd Integration Runtime och Azure Data Factory tjänsten går via en privat länk. 
- (Stöds inte för närvarande) Interaktiv redigering med egen värd Integration Runtime gå via privat länk, till exempel testa anslutning, bläddra i Mapplista och tabell lista, Hämta schema och förhandsgranska data.
- (Stöds inte för närvarande) Den nya versionen av egen värd Integration Runtime kan hämtas automatiskt från Download Center om du aktiverar automatisk uppdatering.

> [!NOTE]
> För funktioner som inte stöds för närvarande måste du fortfarande konfigurera över domän och port i det virtuella nätverket eller företagets brand vägg. 

> [!WARNING]
> När du skapar en länkad tjänst kontrollerar du att autentiseringsuppgiften lagras i Azure Key Vault. Annars fungerar det inte när du aktiverar tjänsten för privat länk i Azure Data Factory.

## <a name="how-to-set-up-private-link-for-azure-data-factory"></a>Så här konfigurerar du en privat länk för Azure Data Factory
Privata slut punkter kan skapas med hjälp av Azure Portal, PowerShell eller Azure CLI:

[Portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)


Du kan också navigera till din Azure Data Factory i Azure Portal och skapa en privat slut punkt (PE):

![Skapa privat slutpunkt](./media/data-factory-private-link/create-private-endpoint.png)


Om du vill blockera offentlig åtkomst till den här Azure Data Factory och endast tillåta åtkomst via privat länk kan du inaktivera nätverks åtkomst till Azure Data Factory i Azure Portal:

![Skapa privat slutpunkt](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> Inaktive ring av offentligt nätverks åtkomst kan bara användas på egen värd Integration Runtime, inte Azure Integration Runtime och SSIS Integration Runtime.

> [!NOTE]
> Användare kan fortfarande komma åt Azure Data Factory Portal via offentliga nätverk när de har inaktiverat offentlig nätverks åtkomst.

## <a name="next-steps"></a>Nästa steg

- [Skapa en data fabrik med hjälp av Azure Data Factory gränssnittet](quickstart-create-data-factory-portal.md)

- [Introduktion till Azure Data Factory](introduction.md)

- [Visuell redigering i Azure Data Factory](author-visually.md)

