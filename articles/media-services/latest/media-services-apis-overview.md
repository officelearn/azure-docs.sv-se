---
title: 'Utveckla med v3 API: er – Azure | Microsoft Docs'
description: 'Den här artikeln beskrivs regler som gäller för entiteter och API: er när du utvecklar med Media Services v3.'
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 5ae7563892cb4792f5c329b2850d7b88d37c0e7d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322373"
---
# <a name="developing-with-media-services-v3-apis"></a>Utveckla med Media Services v3-API: er

Den här artikeln beskrivs regler som gäller för entiteter och API: er när du utvecklar med Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Åtkomst till Azure Media Services API

Du kan använda tjänstobjektautentisering för Azure Active Directory (AD) för att få åtkomst till Azure Media Services-resurserna.
Media Services-API kräver att användaren eller programmet att göra REST API-begäranden har åtkomst till resursen i Media Services-konto och använder en **deltagare** eller **ägare** roll. API: et kan användas med den **läsare** roll men endast **hämta** eller **lista**   operations blir tillgängliga. Mer information finns i [rollbaserad åtkomstkontroll för Media Services-konton](rbac-overview.md).

Överväg att använda hanterade identiteter för Azure-resurser för att få åtkomst till Media Services-API via Azure Resource Manager istället för att skapa ett huvudnamn för tjänsten. Läs mer om hanterade identiteter för Azure-resurser i [vad är hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Azure AD-tjänstens huvudnamn 

Om du skapar en Azure AD-program och tjänstens huvudnamn kan måste programmet vara i sin egen klient. När du har skapat programmet kan ge appen **deltagare** eller **ägare** platssystemrollens åtkomst till Media Services-kontot. 

Om du inte är säker på om du har behörighet att skapa ett Azure AD-program, se [behörigheter som krävs för](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

I följande bild representerar talen flödet av begäranden i kronologisk ordning:

![Mellannivå appar](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. En mellannivå appen begär en åtkomsttoken för Azure AD som har följande parametrar:  

   * Azure AD tenant-slutpunkten.
   * Media Services resurs-URI.
   * Resurs-URI för REST Media Services.
   * Azure AD application värden: klient-ID och klienthemlighet.
   
   Om du vill hämta värdena som behövs, se [åtkomst till Azure Media Services-API med Azure CLI](access-api-cli-how-to.md)

2. Azure AD-åtkomsttoken skickas till mellannivån.
4. På mellannivå skickar begäran till Azure Media REST-API med Azure AD-token.
5. Mellannivån får tillbaka data från Media Services.

## <a name="naming-conventions"></a>Namngivningskonventioner

Azure Media Services v3-resursnamn (till exempel tillgångar, jobb, transformeringar) är föremål Azure Resource Manager-namnbegränsningar. I enlighet med Azure Resource Manager är resursnamnen alltid unika. Därför kan du använda alla strängar som unika identifierare (till exempel GUID) för ditt resursnamn. 

Media Services resursnamn får inte innehålla: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', apostrof eller några andra kontrolltecken. Alla andra tecken tillåts. Maxlängden för ett resursnamn är 260 tecken. 

Mer information om namngivning av Azure Resource Manager finns i: [Namngivningskrav](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) och [Namngivningskonventioner](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="long-running-operations"></a>Långvariga åtgärder

Åtgärderna som markerats med `x-ms-long-running-operation` i Azure Media Services [swagger filer](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) är långa åtgärder. 

Mer information om hur du spårar asynkrona åtgärder i Azure finns [asynkrona åtgärder](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Media Services har följande långvariga åtgärder:

* Skapa LiveEvent
* Uppdatera LiveEvent
* Delete LiveEvent
* Starta LiveEvent
* Stoppa LiveEvent
* Återställ LiveEvent
* Skapa LiveOutput
* Delete LiveOutput
* Skapa StreamingEndpoint
* Uppdatera StreamingEndpoint
* Ta bort StreamingEndpoint
* Starta StreamingEndpoint
* Stoppa StreamingEndpoint
* Skala StreamingEndpoint

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrering, skrivordning, växling av Media Services-entiteter

Se [filtrering, sortering, växling av Azure Media Services-entiteter](entities-overview.md)

## <a name="next-steps"></a>Nästa steg

[Börja utveckla med Media Services v3-API med hjälp av SDK: er/verktyg](developers-guide.md)
