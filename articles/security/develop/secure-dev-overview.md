---
title: Metodtips för säker utveckling på Microsoft Azure
description: Metodtips som hjälper dig att utveckla säkrare kod och distribuera ett säkrare program i molnet.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c4314a0dcbbcb907ef4d6de0a2788cf04dfe1641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934870"
---
# <a name="secure-development-best-practices-on-azure"></a>Metodtips för säker utveckling på Azure
Den här artikelserien innehåller säkerhetsaktiviteter och kontroller att tänka på när du utvecklar program för molnet. Faserna i Microsoft Security Development Lifecycle (SDL) och säkerhetsfrågor och begrepp som ska beaktas under varje fas av livscykeln behandlas. Målet är att hjälpa dig att definiera aktiviteter och Azure-tjänster som du kan använda i varje fas av livscykeln för att utforma, utveckla och distribuera ett säkrare program.

Rekommendationerna i artiklarna kommer från vår erfarenhet av Azure-säkerhet och från våra kunders erfarenheter. Du kan använda dessa artiklar som referens för vad du bör tänka på under en viss fas av ditt utvecklingsprojekt, men vi föreslår att du också läser igenom alla artiklar från början till minst en gång. Om du läser alla artiklar introduceras du till begrepp som du kanske har missat i tidigare faser av projektet. Genom att implementera dessa begrepp innan du släpper produkten kan du skapa säker programvara, uppfylla kraven på säkerhetsefterlevnad och minska utvecklingskostnaderna.

Dessa artiklar är avsedda att vara en resurs för programvarudesigners, utvecklare och testare på alla nivåer som bygger och distribuerar säkra Azure-program.

## <a name="overview"></a>Översikt

Säkerhet är en av de viktigaste aspekterna av alla program, och det är inte en enkel sak att få rätt. Lyckligtvis tillhandahåller Azure många tjänster som kan hjälpa dig att skydda ditt program i molnet. Dessa artiklar behandlar aktiviteter och Azure-tjänster som du kan implementera i varje skede av livscykeln för programvaruutveckling för att hjälpa dig att utveckla säkrare kod och distribuera ett säkrare program i molnet.

## <a name="security-development-lifecycle"></a>Säkerhetsutvecklingslivscykel (SDL)

För att följa bästa praxis för säker programvaruutveckling krävs att säkerheten integreras i varje fas av livscykeln för programvaruutveckling, från kravanalys till underhåll, oavsett projektmetodik ([vattenfall,](https://en.wikipedia.org/wiki/Waterfall_model) [agila](https://en.wikipedia.org/wiki/Agile_software_development)eller [DevOps](https://en.wikipedia.org/wiki/DevOps)). I kölvattnet av uppmärksammade dataintrång och utnyttjande av brister i driftsäkerheten förstår fler utvecklare att säkerhet måste åtgärdas under hela utvecklingsprocessen.

Ju senare du åtgärdar ett problem i utvecklingslivscykeln, desto mer kostar den korrigeringen dig. Säkerhetsproblem är inget undantag. Om du bortser från säkerhetsproblem i de tidiga faserna av programvaruutvecklingen kan varje fas som följer ärva sårbarheterna i föregående fas. Din slutprodukt kommer att ha samlat flera säkerhetsproblem och möjligheten till ett brott. Genom att skapa säkerhet i varje fas av utvecklingslivscykeln kan du fånga upp problem tidigt och det hjälper dig att minska dina utvecklingskostnader.

Vi följer faserna i [Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) för att introducera aktiviteter och Azure-tjänster som du kan använda för att uppfylla säkra metoder för programvaruutveckling i varje fas av livscykeln.

SDL-faserna är:

  - Utbildning
  - Krav
  - Design
  - Implementering
  - Verifiering
  - Frisläpp
  - Svar

![Säkerhetsutvecklingslivscykel](./media/secure-dev-overview/01-sdl-phase.png)

I dessa artiklar grupperar vi SDL-faserna i design, utveckling och distribution.

## <a name="engage-your-organizations-security-team"></a>Engagera organisationens säkerhetsteam

Din organisation kan ha ett formellt programsäkerhetsprogram som hjälper dig med säkerhetsaktiviteter från början till under utvecklingslivscykeln. Om din organisation har säkerhets- och efterlevnadsteam måste du engagera dem innan du börjar utveckla programmet. Fråga dem i varje fas av SDL om det finns några uppgifter du missat.

Vi förstår att många läsare kanske inte har ett säkerhets- eller efterlevnadsteam att engagera sig i. Dessa artiklar kan hjälpa dig i säkerhetsfrågor och beslut som du behöver tänka på i varje fas av SDL.

## <a name="resources"></a>Resurser

Använd följande resurser för att lära dig mer om hur du utvecklar säkra program och för att skydda dina program på Azure:

[Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – SDL är en process för programvaruutveckling från Microsoft som hjälper utvecklare att skapa säkrare programvara. Det hjälper dig att hantera krav på säkerhetsefterlevnad samtidigt som du minskar utvecklingskostnaderna.

[Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) – OWASP är en onlinecommunity som producerar fritt tillgängliga artiklar, metoder, dokumentation, verktyg och tekniker inom webbapplikationssäkerhet.

[Pushing Vänster, som en chef](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) - En serie online-artiklar som beskriver olika typer av aktiviteter applikationssäkerhet som utvecklare bör slutföra för att skapa säkrare kod.

[Microsofts identitetsplattform](../../active-directory/develop/index.yml) – Microsofts identitetsplattform är en utveckling av Azure AD-identitetstjänsten och utvecklarplattformen. Det är en fullfjädrad plattform som består av en autentiseringstjänst, bibliotek med öppen källkod, programregistrering och konfiguration, fullständig utvecklardokumentation, kodexempel och annat utvecklarinnehåll. Microsofts identitetsplattform stöder protokoll av branschstandard som OAuth 2.0 och OpenID Connect.

[Metodtips för säkerhet för Azure-lösningar](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) – En samling metodtips för säkerhet som ska användas när du utformar, distribuerar och hanterar molnlösningar med hjälp av Azure. Detta dokument är avsett att vara en resurs för IT-proffs. Detta kan omfatta designers, arkitekter, utvecklare och testare som bygger och distribuerar säkra Azure-lösningar.

[Säkerhets- och efterlevnadsritningar på Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) – Azure Security and Compliance Blueprints är resurser som kan hjälpa dig att skapa och starta molnbaserade program som uppfyller stränga regler och standarder.

## <a name="next-steps"></a>Nästa steg
I följande artiklar rekommenderar vi säkerhetskontroller och aktiviteter som kan hjälpa dig att utforma, utveckla och distribuera säkra program.

- [Utforma säkra program](secure-design.md)
- [Utveckla säkra program](secure-develop.md)
- [Distribuera säkra program](secure-deploy.md)
