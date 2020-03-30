---
title: Azure ExpressRoute CrossConnnections API-utveckling och -integration
description: Den här artikeln innehåller en detaljerad översikt för ExpressRoute-partner om resurstypen expressRouteCrossConnections.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: mialdrid
ms.openlocfilehash: b4a83013d2cbeb2871a3963ec0c95144c02f4d66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187017"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>ExpressRoute CrossConnnections API utveckling och integration

ExpressRoute Partner Resource Manager API gör det möjligt för ExpressRoute-partner att hantera layer-2- och layer-3-konfigurationen av kundExpressRoute-kretsar. Api:et för ExpressRoute Partner Resource Manager introducerar en ny resurstyp, **expressRouteCrossConnections**. Partner använder den här resursen för att hantera expressroutekretsar för kunder.

## <a name="workflow"></a>Arbetsflöde

ExpressRouteCrossConnections-resursen är en skuggresurs till ExpressRoute-kretsen. När en Azure-kund skapar en ExpressRoute-krets och väljer en specifik ExpressRoute-partner skapar Microsoft en expressRouteCrossConnections-resurs i partnerns Azure ExpressRoute-hanteringsprenumeration. På så sätt definierar Microsoft en resursgrupp för att skapa expressRouteCrossConnections-resursen i. Namngivningsstandarden för resursgruppen är **CrossConnection-* PeeringLocation***; där PeeringLocation = ExpressRoute-platsen. Om en kund till exempel skapar en ExpressRoute-krets i Denver skapas CrossConnection i partnerns Azure-prenumeration i följande resursgrupp: **CrossConnnection-Denver**.

ExpressRoute-partner hanterar layer-2- och layer-3-konfiguration genom att utfärda REST-åtgärder mot expressRouteCrossConnections-resursen.

## <a name="benefits"></a>Fördelar

Fördelar med att flytta till expressRouteCrossConnections-resursen:

* Eventuella framtida förbättringar för ExpressRoute-partner kommer att göras tillgängliga på ExpressRouteCrossConnection-resursen.

* Partner kan använda [rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/overview) på expressRouteCrossConnection-resursen. Dessa kontroller kan definiera behörigheter för vilka användare konton kan ändra expressRouteCrossConnection-resursen och lägga till/uppdatera/ta bort peering-konfigurationer.

* ExpressRouteCrossConnection-resursen exponerar API:er som kan vara till hjälp vid felsökning av ExpressRoute-anslutningar. Detta inkluderar ARP-tabell, BGP-rutttabellsammanfattning och bgp-rutttabellinformation. Den här funktionen stöds inte av klassiska distributions-API:er.

* Partner kan också slå upp de annonserade grupperna på Microsoft-peering med hjälp av *RouteFilter-resursen.*

## <a name="api-development-and-integration-steps"></a>API-utvecklings- och integrationssteg

För att utveckla mot partner-API:et utnyttjar ExpressRoute-partner en testkund- och testpartnerinställning. Testkundsinställningarna används för att skapa ExpressRoute-kretsar på test peering-platser som mappas till dummy-enheter och portar. Testpartnerns inställningar används för att hantera ExpressRoute-kretsarna som skapats på test peering-platsen.

### <a name="1-enlist-subscriptions"></a>1. Värva prenumerationer

Om du vill begära testpartnern och testa kundinställningarna registrerar du två Azure-prenumerationer för användningsbaserad betalning till din ExpressRoute-teknikkontakt:
* **ExpressRoute_API_Dev_Provider_Sub:** Den här prenumerationen används för att hantera ExpressRoute-kretsar som skapats på test peering-platser på dummy-enheter och portar.

* **ExpressRoute_API_Dev_Customer_Sub:** Den här prenumerationen används för att skapa ExpressRoute-kretsar på test peering-platser som mappas till dummy-enheter och portar.

Test peering-platserna: dummy-enheter och portar är inte exponerade för produktionskunder som standard. För att kunna skapa ExpressRoute-kretsar som mappas till testkonfigurationen måste en flagga för prenumerationsfunktion aktiveras.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. Registrera Dev_Provider prenumeration för att få tillgång till api:et för expressRouteCrossConnections

För att kunna komma åt API:et för expressRouteCrossConnections måste partnerprenumerationen registreras i **Microsoft.Network Resource Provider**. Följ stegen i artikeln [för Azure-resursleverantörer och typer](/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal) för att slutföra registreringsprocessen.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. Konfigurera autentisering för REST API-anrop för Azure Resource Manager

De flesta Azure-tjänster kräver klientkod för att autentisera med Resource Manager, med hjälp av giltiga autentiseringsuppgifter, innan du anropar tjänst-API:er. Autentisering samordnas mellan de olika aktörerna av Azure AD och ger klienten en åtkomsttoken som bevis på autentisering.

Autentiseringsprocessen omfattar två huvudsteg:

1. [Registrera klienten](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad).
2. [Skapa åtkomstbegäran](https://docs.microsoft.com/rest/api/azure/#create-the-request).

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. Ge behörighet till klientprogrammet för nätverksdeltagare

När autentiseringen har konfigurerats måste du ge nätverksdeltagare åtkomst till klientprogrammet under Dev_Provider_Sub. Om du vill bevilja behörighet loggar du in på [Azure-portalen](https://ms.portal.azure.com/#home) och utför följande steg:

1. Navigera till Prenumerationer och välj Dev_Provider_Sub
2. Navigera till åtkomstkontroll (IAM)
3. Lägg till rolltilldelning
4. Välj rollen Nätverksdeltagare
5. Tilldela åtkomst till Azure AD-användare, grupp eller tjänsthuvudnamn
6. Välj klientprogram
7. Spara ändringar

### <a name="5-develop"></a>5. Utveckla

Utveckla mot api:et [för expressRouteCrossConnections](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections).

## <a name="rest-api"></a>REST API

See [ExpressRoute CrossConnections REST API](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections) for REST API documentation.

## <a name="next-steps"></a>Nästa steg

Mer information om alla ExpressRoute REST API:er finns i [ExpressRoute REST API:er](https://docs.microsoft.com/rest/api/expressroute/).