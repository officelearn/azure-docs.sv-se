---
title: Secure development bästa metoder för Microsoft Azure
description: Metodtips för att utveckla säkrare kod och distribuera en säkrare program i molnet.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: ee5c043038fbb747e90bca9530cbe2e94c8a95c2
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144469"
---
# <a name="secure-development-best-practices-on-azure"></a>Secure development bästa metoder för Azure
Den här artikelserien anger säkerhetsaktiviteter och kontroller för att tänka på när du utvecklar program för molnet. Faserna i frågor som rör Microsoft Security Development Lifecycle (SDL) och säkerhet och begrepp att överväga under varje fas av livscykeln omfattas. Målet är att hjälpa dig att definiera aktiviteter och Azure-tjänster som du kan använda i varje fas av livscykeln för att utforma, utveckla och distribuera en säkrare program.

Rekommendationerna i artiklarna kommer från vår erfarenhet av Azure-säkerhet och erfarenheter från våra kunder. Du kan använda de här artiklarna som referens för vad du bör tänka på vid en specifik fas i utvecklingsprojektet, men vi rekommenderar att du också läser igenom alla artiklar från början till slut minst en gång. Läsa alla artiklar ger en introduktion till begrepp som du kan ha missat i tidigare faser i ditt projekt. Implementera de här koncepten innan du publicerar din produkt kan hjälpa dig bygga säkra programvara, åtgärda säkerhetskrav och minska utvecklingskostnaderna för.

De här artiklarna är avsedda att vara en resurs för programvaruutvecklare, utvecklare och testare på alla nivåer som bygga och distribuera säkra Azure-program.

## <a name="overview"></a>Översikt

Säkerhet är en av de viktigaste aspekterna av program och det är inte en enkel sak att få rätt. Som tur är kan tillhandahåller Azure många tjänster som kan hjälpa dig att skydda ditt program i molnet. De här artiklarna-aktiviteter och Azure-tjänster som du kan implementera i varje skede av din livscykel för att utveckla säkrare kod och distribuera en säkrare program i molnet.

## <a name="security-development-lifecycle"></a>Livscykler för säkerhetsutveckling

Följa metodtipsen för utveckling av säker programvara kräver integrera säkerhet i varje fas av livscykel, från kravet analys för underhåll, oavsett metodiken som projektet ([vattenfallet](https://en.wikipedia.org/wiki/Waterfall_model), [agil](https://en.wikipedia.org/wiki/Agile_software_development), eller [DevOps](https://en.wikipedia.org/wiki/DevOps)). Aktivering av viktiga dataintrång och utnyttjande av operativa säkerhetsbrister fler utvecklare är för att förstå att säkerhet måste åtgärdas i hela utvecklingsprocessen.

Den senare du åtgärda ett problem i utvecklingslivscykeln, desto mer som åtgärda kommer kostar. Säkerhetsproblem är inget undantag. Om du bortse från säkerhetsproblem i tidiga faser av din programutveckling, kan varje fas som följer ärver sårbarheter den föregående fasen har genomförts. Din slutliga produkt kommer skett flera säkerhetsproblem och risken för ett intrång. Bygga in säkerhet i varje fas i utvecklingsprocessen hjälper dig identifiera problem tidigt och det hjälper dig att minska dina utvecklingskostnader för.

Vi följer faserna i Microsofts [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) presentera aktiviteter och Azure-tjänster som du kan använda för att uppfylla säker programutvecklingsrutiner i varje fas av livscykeln.

Faserna SDL är:

  - Utbildning
  - Krav
  - Design
  - Implementering
  - Verifiering
  - Frisläpp
  - Svar

![Livscykler för säkerhetsutveckling](./media/secure-dev-overview/01-sdl-phase.png)

I de här artiklarna vi gruppera SDL-faser i design, utveckla och distribuera.

## <a name="engage-your-organizations-security-team"></a>Engagera din organisations security-teamet

Din organisation kan ha ett formellt programmet säkerhetsprogram som underlättar säkerhetsaktiviteter från början till slut under utvecklingsprocessen. Om din organisation har säkerhets- och grupper, se till att engagera dem innan du börjar utveckla ditt program. Be dem vid varje fas av SDL-processen om det finns några uppgifter som du har missat.

Vi förstår att många läsare inte kanske har ett säkerhets- eller team att engagera. De här artiklarna kan hjälpa dig i säkerhetsfrågor och beslut som du behöver tänka på vid varje fas av SDL-processen.

## <a name="resources"></a>Resurser

Använd följande resurser om du vill veta mer om att utveckla säkra program och för att skydda dina program på Azure:

[Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – The SDL är en process för utveckling av programvara från Microsoft som hjälper utvecklare att skapa säkrare programvara. Det hjälper dig att lösa säkerhetskrav samtidigt som utvecklingskostnaderna.

[Öppna OWASP Web Application Security Project ()](https://www.owasp.org/index.php/Main_Page) – OWASP är en online-community som producerar gratis artiklar, metodik, dokumentation, verktyg och tekniker i fältet för säkerhet för webbprogram.

[Skicka till vänster, T.ex. en chef](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) – en serie online artiklar som beskriver olika typer av program säkerhetsaktiviteter som utvecklare bör slutföras om du vill skapa säkrare kod.

[Microsoft identity-plattformen](https://docs.microsoft.com/azure/active-directory/develop/) – Microsofts identitetsplattform är en utveckling av Azure AD identity-tjänsten och developer-plattformen. Det är en fullständig plattform som består av en autentiseringstjänst, bibliotek med öppen källkod, programregistrering och konfiguration, fullständig utvecklardokumentation, kodexempel och annat innehåll för utvecklare. Microsoft identity-plattformen stöder branschstandardprotokollen som OAuth 2.0 och OpenID Connect.

[Rekommenderade säkerhetsmetoder för Azure-lösningar](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) – en uppsättning beprövade metoder för att använda när du utformar, distribuerar och hanterar molnlösningar med hjälp av Azure. Det här dokumentet är avsett att vara en resurs för IT-proffs. Detta kan inkludera designers, arkitekter, utvecklare och testare som bygger och distribuerar säkra Azure-lösningar.

[Säkerhet och efterlevnad skisser på Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) – Azure-säkerhet och efterlevnad skisser är resurser som kan hjälpa dig att skapa och lansera molndrivna tillämpningar som uppfyller strikta regler och standarder.

## <a name="next-steps"></a>Nästa steg
I följande artiklar, rekommenderar vi säkerhetskontroller och aktiviteter som kan hjälpa dig att utforma, utveckla och distribuera säkra program.

- [Utforma säkra program](secure-design.md)
- [Utveckla säkra program](secure-develop.md)
- [Distribuera säkra program](secure-deploy.md)
