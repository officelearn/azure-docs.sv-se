---
title: IP-brandvägg för Azure Cosmos-konton
description: Lär dig hur du skyddar Azure Cosmos DB-data med hjälp av IP-åtkomstkontrollprinciper för brandväggsstöd.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241083"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>IP-brandvägg i Azure Cosmos DB

För att skydda data som lagras i ditt konto stöder Azure Cosmos DB en hemlig baserad auktoriseringsmodell som använder en stark Hash-baserad meddelandeautentiseringskod (HMAC). Dessutom stöder Azure Cosmos DB IP-baserade åtkomstkontroller för stöd för inkommande brandvägg. Den här modellen liknar brandväggsreglerna i ett traditionellt databassystem och ger en extra säkerhetsnivå för ditt konto. Med brandväggar kan du konfigurera ditt Azure Cosmos-konto så att det endast är tillgängligt från en godkänd uppsättning datorer och/eller molntjänster. Åtkomst till data som lagras i din Azure Cosmos-databas från dessa godkända uppsättningar av datorer och tjänster kräver fortfarande att anroparen visar en giltig auktoriseringstoken.

## <a name="ip-access-control-overview"></a><a id="ip-access-control-overview"></a>Översikt över IP-åtkomstkontroll

Som standard är ditt Azure Cosmos-konto tillgängligt från internet, så länge begäran åtföljs av en giltig auktoriseringstoken. För att konfigurera IP-principbaserad åtkomstkontroll måste användaren ange den uppsättning IP-adresser eller IP-adressintervall i CIDR-formuläret (Classless Inter-Domain Routing) som ska inkluderas som tillåten lista över klient-IP-adresser för att komma åt ett visst Azure Cosmos-konto. När den här konfigurationen har tillämpats får alla begäranden som kommer från datorer utanför den här tillåtna listan 403 (Förbjudet) svar. När du använder IP-brandväggen rekommenderas att Azure-portalen får åtkomst till ditt konto. Åtkomst krävs för att tillåta användning av datautforskare samt för att hämta mått för ditt konto som visas på Azure-portalen. När du använder datautforskaren måste du förutom att låta Azure-portalen komma åt ditt konto, även uppdatera brandväggsinställningarna för att lägga till din aktuella IP-adress i brandväggsreglerna. Observera att det kan ta upp till 15min att sprida brandväggen. 

Du kan kombinera IP-baserad brandvägg med undernät och VNET-åtkomstkontroll. Genom att kombinera dem kan du begränsa åtkomsten till alla källor som har en offentlig IP och/eller från ett visst undernät inom VNET. Mer information om hur du använder undernäts- och VNET-baserad åtkomstkontroll finns [i Access Azure Cosmos DB-resurser från virtuella nätverk](vnet-service-endpoint.md).

Sammanfattningsvis krävs alltid auktoriseringstoken för att komma åt ett Azure Cosmos-konto. Om IP-brandväggen och VNET Access Control List (ACLs) inte har konfigurerats kan Azure Cosmos-kontot nås med auktoriseringstoken. När IP-brandväggen eller VNET-ACL:er eller båda har konfigurerats på Azure Cosmos-kontot får endast begäranden som kommer från de källor som du har angett (och med auktoriseringstoken) giltiga svar. 

## <a name="next-steps"></a>Nästa steg

Därefter kan du konfigurera IP-brandväggen eller VNET-tjänstslutpunkten för ditt konto med hjälp av följande dokument:

* [Konfigurera IP-brandväggen för ditt Azure Cosmos-konto](how-to-configure-firewall.md)
* [Få tillgång till Azure Cosmos DB-resurser från virtuella nätverk](vnet-service-endpoint.md)
* [Konfigurera slutpunkt för virtuella nätverkstjänster för ditt Azure Cosmos-konto](how-to-configure-vnet-service-endpoint.md)




