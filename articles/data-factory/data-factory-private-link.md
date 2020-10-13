---
title: Azure privat länk för Azure Data Factory
description: Lär dig mer om hur Azures privata länkar fungerar i Azure Data Factory.
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
ms.openlocfilehash: 2cd9f01404a4e33303356dd3f452cd7dbc47a747
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328572"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure privat länk för Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Med hjälp av en privat Azure-länk kan du ansluta till olika PaaS-distributioner (Platform as a Service) i Azure via en privat slut punkt. En privat slut punkt är en privat IP-adress inom ett särskilt virtuellt nätverk och undernät. En lista över PaaS-distributioner som stöder funktionen för privat länk finns i [dokumentationen för privat länk](https://docs.microsoft.com/azure/private-link/). 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Säker kommunikation mellan kund nätverk och Azure Data Factory 
Du kan konfigurera ett virtuellt Azure-nätverk som en logisk representation av ditt nätverk i molnet. Detta ger följande fördelar:
* Du hjälper till att skydda dina Azure-resurser mot attacker i offentliga nätverk.
* Du låter nätverken och Data Factory kommunicera på ett säkert sätt med varandra. 

Du kan också ansluta ett lokalt nätverk till ditt virtuella nätverk genom att konfigurera en Internet Protocol security (IPsec) VPN-anslutning (plats-till-plats) eller en Azure-ExpressRoute (privat peering). 

Du kan också installera en integration runtime med egen värd på en lokal dator eller en virtuell dator i det virtuella nätverket. På så sätt kan du:
* Kör kopierings aktiviteter mellan ett moln data lager och ett data lager i ett privat nätverk.
* Skicka transformerings aktiviteter mot beräknings resurser i ett lokalt nätverk eller i ett virtuellt Azure-nätverk. 

Flera kommunikations kanaler krävs mellan Azure Data Factory och det virtuella kund nätverket, som du ser i följande tabell:

| Domän | Port | Beskrivning |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Ett kontroll plan, som krävs för Data Factory redigering och övervakning. |
| `*.{region}.datafactory.azure.net` | 443 | Krävs av integration runtime med egen värd för att ansluta till tjänsten Data Factory. |
| `*.servicebus.windows.net` | 443 | Krävs av integration runtime med egen värd för interaktiv redigering. |
| `download.microsoft.com` | 443 | Krävs av den egna värdbaserade integrerings körningen för att ladda ned uppdateringarna. |

Med stöd för privat länk för Azure Data Factory kan du:
* Skapa en privat slut punkt i det virtuella nätverket.
* Aktivera den privata anslutningen till en speciell Data Factory-instans. 

Kommunikationen med att Azure Data Factory tjänsten går via en privat länk och ger säker privat anslutning. Du behöver inte konfigurera föregående domän och port i ett virtuellt nätverk eller i företags brand väggen för att kunna skydda dina resurser på ett säkrare sätt.  

![Diagram över privat länk för Azure Data Factory arkitektur.](./media/data-factory-private-link/private-link-architecture.png)

Att aktivera tjänsten för privata Länkar för var och en av de föregående kommunikations kanalerna har följande funktioner:
- **Stöds**:
   - Du kan skapa och övervaka data fabriken i det virtuella nätverket, även om du blockerar all utgående kommunikation.
   - Kommando kommunikationen mellan den lokala integrerings körningen och tjänsten Azure Data Factory kan utföras på ett säkert sätt i en privat nätverks miljö. Trafiken mellan den lokala integrerings körningen och Azure Data Factory tjänsten går via en privat länk. 
- **Stöds inte för närvarande**:
   - Interaktiv redigering som använder en lokal integrerings körning, till exempel testa anslutning, bläddra i Mapplista och tabell lista, Hämta schema och förhandsgranska data, går via en privat länk.
   - Den nya versionen av den egna värdbaserade integrerings körningen kan hämtas automatiskt från Microsoft Download Center om du aktiverar AutoUpdate.

   > [!NOTE]
   > För funktioner som inte stöds för närvarande måste du fortfarande konfigurera den tidigare nämnda domänen och porten i det virtuella nätverket eller företagets brand vägg. 

> [!WARNING]
> När du skapar en länkad tjänst ser du till att dina autentiseringsuppgifter lagras i ett Azure Key Vault. Annars fungerar inte autentiseringsuppgifterna när du aktiverar privat länk i Azure Data Factory.

## <a name="set-up-private-link-for-azure-data-factory"></a>Konfigurera en privat länk för Azure Data Factory
Du kan skapa privata slut punkter med hjälp av [Azure Portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal), PowerShell eller Azure CLI.

Du kan också gå till Azure Data Factory i Azure Portal och skapa en privat slut punkt, som du ser här:

![Skärm bild av fönstret "privat slut punkts anslutningar" för att skapa en privat slut punkt.](./media/data-factory-private-link/create-private-endpoint.png)


Om du vill blockera offentlig åtkomst till Azure Data Factory och bara tillåta åtkomst via privat länk inaktiverar du nätverks åtkomst till Azure Data Factory i Azure Portal, som du ser här:

![Skärm bild av fönstret "nätverks åtkomst" för att skapa en privat slut punkt.](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> Inaktive ring av offentlig nätverks åtkomst gäller endast för integration runtime med egen värd, inte för att Azure Integration Runtime och SQL Server Integration Services (SSIS) Integration Runtime.

> [!NOTE]
> Du kan fortfarande komma åt Azure Data Factory Portal via ett offentligt nätverk när du har inaktiverat offentlig nätverks åtkomst.

## <a name="next-steps"></a>Nästa steg

- [Skapa en data fabrik med hjälp av Azure Data Factory gränssnittet](quickstart-create-data-factory-portal.md)
- [Introduktion till Azure Data Factory](introduction.md)
- [Visuell redigering i Azure Data Factory](author-visually.md)

