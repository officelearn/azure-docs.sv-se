---
title: "Tekniska krav för att skapa en datatjänst för Marketplace | Microsoft Docs"
description: "Förstå kraven för att skapa en datatjänst för att distribuera och sälja på Azure Marketplace"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: aaff609a-1cd1-4146-98f4-d04166b0fce0
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 52827723477677bc292c645e2390c435fbad3ee4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="technical-pre-requisites-for-creating-a-data-service-offer-for-the-azure-marketplace"></a>Tekniska krav för att skapa en datatjänst erbjudande för Azure Marketplace
> [!IMPORTANT]
> **Just nu vi inte längre onboarding några nya Data Service-utgivare. Nya dataservices kommer inte godkännas för lista.** Om du har ett SaaS-affärsprogram som du vill publicera på AppSource hittar du mer information [här](https://appsource.microsoft.com/partners). Om du har en IaaS-program eller developer-tjänsten som du vill publicera på Azure Marketplace du hittar mer information [här](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

Läs noggrant innan du börjar processen och förstå varför och där varje steg utförs. Så mycket som möjligt du ska förbereda företagets information och annan information, hämta nödvändiga verktyg och skapa tekniska komponenter innan du börjar skapa erbjudande.

Du bör ha följande till hands innan du börjar:

## <a name="make-a-decision-on-what-technology-will-be-used-to-publish-your-data-service-offer"></a>Fatta ett beslut om vilken teknik som används för att publicera Data Service-erbjudande
En utgivare kan du välja mellan flera tekniker när du publicerar Data Service i Azure Marketplace. De huvudsakliga tekniker som stöds beskrivs nedan. Oavsett vilken teknik för att publicera Data Service kan slutanvändaren förbrukar data via den **OData-feeden** visas av Azure Marketplace-tjänsten. Fullständig information om OData-tjänst som du hittar på [http://www.odata.org/](http://www.odata.org/)

## <a name="sql-azure-database"></a>SQL Azure-databas
Med dataset redo i SQL Azure är Publisher ansvar. Du behöver att prenumerera på Azure, etablera lämpliga storleken på databasen och ladda upp Data till SQL Azure DB. Utgivaren är också ansvarig för att hålla sina data alltid uppdaterad. Mer information om att prenumerera på Azure-tjänster hittar du på [https://azure.microsoft.com/services/sql-database/](https://azure.microsoft.com/services/sql-database/)

När du flyttar data till SQL Azure kan Azure Marketplace exponera tabeller och vyer. Utgivaren kan ange vilka tabeller/vyer och kolumner som exponeras av slutanvändaren. Ytterligare kan innehållsleverantören också ange vilka kolumner som kan efterfrågas av slutanvändaren och vilka som returneras endast i nyttolasten. Detta ger en hög nivå av flexibilitet om vilka data i databasen bör exponeras. Kolumner som kan efterfrågas måste vara backas upp av en eller flera index i databasen.

## <a name="rest-based-web-service"></a>REST-baserad webbtjänst
Protokoll som stöds: **endast HTTPS**

Befintliga REST-baserade tjänster kan exponeras via Azure Marketplace. Eftersom datamängden är alltid exponerade för slutanvändaren som en OData-feed, baserat på Azure Marketplace behöver för att kunna mappa tjänsten till en OData service. Så RESTEN baserat slutpunkter måste du exponera alla parametrar som HTTP-parametrar.

Nyttolasten måste vara i ett formulär kan mappas till ett ATOM-svar. Därför svaret från services måste vara i XML-format och kan bara innehålla ett upprepande element som innehåller värdena nyttolasten (till exempel postuppsättning). Tjänsten Azure Marketplace mappa upprepade noden till noden posten i ATOM och nyttolast-värden i egenskapen noder i post-nod.

Auktoriseringsinformation (till exempel API nyckeln, autentisering token, osv.) måste anges som en HTTP-parameter eller i HTTP-huvudet (nyckel/värde-par) – grundläggande autentisering stöds också. En giltig nyckel måste anges och alla förfrågningar via Azure Marketplace görs via nyckeln. Övervakning och fakturering sker på Azure Marketplace-nivån.

Fel som returnerats av tjänsten måste mappas till HTTP-statuskoder. Om tjänsten returnerar en XML-kod som innehåller fel kommer dessa att mappas av tjänsten Azure Marketplace till HTTP-statuskoder.

## <a name="soap-based-web-services"></a>SOAP-baserat webbtjänsterna
Protokoll: **endast HTTPS**

Kraven är desamma som för övriga baserat avsnittet service. Den enda skillnaden är parametrar kan också anges i en XML-meddelandetext som skickas till utgivarens tjänsten med alla begäranden som görs via Azure Marketplace. Detta innebär att HTTP-parametrar som användaren anger på frontend som översätts till XML-element i ett XML-dokument som skickas med en begäran om att innehåll provider-webbtjänsten.

## <a name="odata-based-web-services"></a>OData-baserat webbtjänsterna
Protokoll: **endast HTTPS**

Data kan visas som en OData-tjänst för Azure Marketplace. Systemet ska skicka tjänsten via och ersätter roten för tjänsten Azure Marketplace service roten – så alla efterföljande anrop gå igenom Azure Marketplace.

OData-tjänster behöver bara gå mot en databas i serverdelen. OData stöder alla typer av lagring eller affärslogiken för att köra tjänsten.

## <a name="next-steps"></a>Nästa steg
Nu när du har granskat kraven och slutföra de nödvändiga uppgifterna du kan gå vidare med att skapa ditt Data tjänsterbjudande enligt anvisningarna i den [Data Service publiceringsguide](marketplace-publishing-data-service-creation.md).

Eller, om du vill granska den övergripande processen och respektive artiklar för var och en av de publishing faserna finns i artikeln [komma igång: hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md).

[link-acct]:marketplace-publishing-accounts-creation-registration.md
