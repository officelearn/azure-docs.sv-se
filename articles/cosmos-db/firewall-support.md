---
title: IP-brandvägg för Azure Cosmos-konton
description: Lär dig hur du skyddar Azure Cosmos DB data med hjälp av principer för IP-åtkomstkontroll för brand Väggs stöd.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "66241083"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>IP-brandvägg i Azure Cosmos DB

För att skydda data som lagras i ditt konto har Azure Cosmos DB stöd för en hemlig, baserad auktoriserings modell som använder sig av en stark hash-baserad Message Authentication Code (HMAC). Dessutom stöder Azure Cosmos DB IP-baserade åtkomst kontroller för inkommande brand Väggs stöd. Den här modellen liknar brand Väggs reglerna i ett traditionellt databas system och ger ytterligare en säkerhets nivå för ditt konto. Med brand väggar kan du konfigurera ditt Azure Cosmos-konto så att det bara kan nås från en godkänd uppsättning datorer och/eller moln tjänster. Åtkomst till data som lagras i din Azure Cosmos-databas från dessa godkända uppsättningar av datorer och tjänster kräver fortfarande att anroparen presenterar en giltig autentiseringstoken.

## <a name="ip-access-control-overview"></a><a id="ip-access-control-overview"></a>Översikt över IP-åtkomst kontroll

Som standard är ditt Azure Cosmos-konto tillgängligt från Internet, förutsatt att begäran åtföljs av en giltig autentiseringstoken. Om du vill konfigurera IP-principbaserad åtkomst kontroll måste användaren ange en uppsättning IP-adresser eller IP-adressintervall i CIDR-form (Classless Inter-Domain routing) som ska ingå i listan över tillåtna klient-IP-adresser för att få åtkomst till ett angivet Azure Cosmos-konto. När den här konfigurationen används får alla begär Anden som kommer från datorer utanför den här tillåtna listan mottagning 403 (förbjuden) svar. När du använder IP-brandvägg rekommenderar vi att du tillåter Azure Portal att komma åt ditt konto. Åtkomst krävs för att tillåta användning av data Utforskaren samt för att hämta mått för ditt konto som visas på Azure Portal. När du använder data Utforskaren måste du, förutom att tillåta Azure Portal komma åt ditt konto, uppdatera brand Väggs inställningarna för att lägga till din aktuella IP-adress i brand Väggs reglerna. Observera att det kan ta upp till 15min att sprida brand Väggs ändringar. 

Du kan kombinera IP-baserad brand vägg med åtkomst kontroll för undernät och VNET. Genom att kombinera dem kan du begränsa åtkomsten till alla källor som har en offentlig IP-adress och/eller från ett särskilt undernät i VNET. Om du vill veta mer om hur du använder undernät och VNET-baserad åtkomst kontroll, se [åtkomst Azure Cosmos DB resurser från virtuella nätverk](vnet-service-endpoint.md).

För att sammanfatta krävs alltid autentiseringstoken för att få åtkomst till ett Azure Cosmos-konto. Om IP-brandväggen och VNET Access Control List (ACL) inte har kon figurer ATS kan Azure Cosmos-kontot nås med autentiseringstoken. När IP-brandväggen eller VNET-ACL: er eller båda har kon figurer ATS på Azure Cosmos-kontot får du bara begär Anden som härstammar från de källor du har angett (och med autentiseringstoken) get giltiga svar. 

## <a name="next-steps"></a>Nästa steg

Härnäst kan du konfigurera IP-brandväggen eller VNET-tjänstens slut punkt för ditt konto genom att använda följande dokument:

* [Så här konfigurerar du IP-brandvägg för ditt Azure Cosmos-konto](how-to-configure-firewall.md)
* [Åtkomst Azure Cosmos DB resurser från virtuella nätverk](vnet-service-endpoint.md)
* [Konfigurera tjänst slut punkten för virtuellt nätverk för ditt Azure Cosmos-konto](how-to-configure-vnet-service-endpoint.md)




