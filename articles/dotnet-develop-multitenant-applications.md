---
title: Mönster för webbprogram för flera innehavare | Microsoft-dokument
description: Hitta arkitektoniska översikter och designmönster som beskriver hur du implementerar ett webbprogram för flera innehavare på Azure.
services: ''
documentationcenter: .net
author: wadepickett
manager: wpickett
editor: ''
ms.assetid: 4f0281d2-1555-42b0-a99d-1222fade0b0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/05/2015
ms.author: wpickett
ms.openlocfilehash: d3e267eab056589ed38c436620dd0db185291da1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425909"
---
# <a name="multitenant-applications-in-azure"></a>Program med flera klienter i Azure
Ett program med flera innehavare är en delad resurs som gör att "användare i separata klienter" kan visa programmet som om det vore deras eget. Ett typiskt scenario som lämpar sig för ett program med flera innehavare är ett scenario där alla användare av programmet från olika klienter kanske vill anpassa användarupplevelsen men i övrigt har samma grundläggande affärskrav. Exempel på stora program med flera trogna är Office 365, Outlook.com och visualstudio.com.

Ur en applikationsleverantörs perspektiv handlar fördelarna med multitenancy främst om drifts- och kostnadseffektivitet. En version av ditt program kan uppfylla behoven hos många klienter/kunder, vilket möjliggör konsolidering av systemadministrationsuppgifter som övervakning, prestandajustering, programvaruunderhåll och säkerhetskopiering av data.

Följande innehåller en lista över de viktigaste målen och kraven ur en leverantörs perspektiv.

* **Etablering**: Du måste kunna etablera nya klienter för programmet.  För program med flera innehavare med ett stort antal klienter är det vanligtvis nödvändigt att automatisera den här processen genom att aktivera självbetjäningsetablering.
* **Underhåll:** Du måste kunna uppgradera programmet och utföra andra underhållsuppgifter medan flera klienter använder det.
* **Övervakning**: Du måste kunna övervaka programmet hela tiden för att identifiera eventuella problem och felsöka dem. Detta inkluderar övervakning av hur varje klient använder programmet.

Ett korrekt implementerat program för flera följare ger följande fördelar för användarna.

* **Isolering**: Enskilda klienters aktiviteter påverkar inte andra klienters användning av programmet. Klienter kan inte komma åt varandras data. Det verkar för hyresgästen som om de har exklusiv användning av programmet.
* **Tillgänglighet**: Enskilda klienter vill att programmet ska vara ständigt tillgängligt, kanske med garantier definierade i ett serviceavtal. Återigen bör andra klienters aktiviteter inte påverka programmets tillgänglighet.
* **Skalbarhet**: Programmet skalas för att möta efterfrågan från enskilda klienter. Närvaro och åtgärder för andra klienter bör inte påverka programmets prestanda.
* **Kostnader**: Kostnaderna är lägre än att köra ett dedikerat program med en klienteftergripande eftersom flera innehavare möjliggör resursdelning.
* **Anpassningsbarhet**. Möjligheten att anpassa programmet för en enskild klient på olika sätt, till exempel lägga till eller ta bort funktioner, ändra färger och logotyper, eller till och med lägga till sin egen kod eller skript.

Kort sagt, även om det finns många överväganden som du måste ta hänsyn till för att ge en mycket skalbar tjänst, det finns också ett antal mål och krav som är gemensamma för många multitenant program. Vissa kanske inte är relevanta i specifika scenarier, och vikten av individuella mål och krav skiljer sig åt i varje scenario. Som leverantör av multitenantprogrammet har du också mål och krav som att uppfylla klientens mål och krav, lönsamhet, fakturering, flera servicenivåer, etablering, underhållsövervakning och automatisering.

Mer information om ytterligare designöverväganden för ett program med flera innehavare finns i [Värd för ett program för flera innehavare på Azure][Hosting a Multi-Tenant Application on Azure]. Information om vanliga mönster för dataarkitekturen i SaaS-databasprogram (Software-as-a-Service) med flera klienter finns i [Designmönster för SaaS-program med flera klienter i Azure SQL Database](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Azure innehåller många funktioner som gör att du kan åtgärda de viktigaste problemen när du utformar ett system med flera liggande företag.

**Isolering**

* Segment webbplats klienter efter värdrubriker med eller utan SSL-kommunikation
* Segment webbplats klienter efter frågeparametrar
* Webbtjänster i arbetarroller
  * Arbetarroller som vanligtvis bearbetar data på backend för ett program.
  * Webbroller som vanligtvis fungerar som klientdel för program.

**Lagring**

Datahantering som Azure SQL Database eller Azure Storage-tjänster som tabelltjänsten, som tillhandahåller tjänster för lagring av stora mängder ostrukturerade data och Blob-tjänsten, som tillhandahåller tjänster för att lagra stora mängder ostrukturerad text eller binära data som video, ljud och bilder.

* Skydda multitenantdata i SQL Database-SQL Server-inloggningar per klient.
* Med hjälp av Azure-tabeller för programresurser genom att ange en åtkomstprincip för behållarnivå kan du ha möjlighet att justera behörigheter utan att behöva utfärda nya URL:er för de resurser som skyddas med signaturer för delad åtkomst.
* Azure-köer för Azure-köer för Azure-köer används ofta för att köra bearbetning på uppdrag av klienter, men kan också användas för att distribuera arbete som krävs för etablering eller hantering.
* Service bus-köer för programresurser som skickar arbete till en delad tjänst, kan du använda en enda kö där varje klientavsändare bara har behörigheter (som härleds från anspråk som utfärdats från ACS) för att skicka till den kön, medan endast mottagarna från tjänsten har behörighet att hämta data från kön som kommer från flera klienter.

**Anslutnings- och säkerhetstjänster**

* Azure Service Bus, en meddelandeinfrastruktur som sitter mellan program som gör det möjligt för dem att utbyta meddelanden på ett löst kopplat sätt för förbättrad skala och återhämtning.

**Nätverkstjänster**

Azure tillhandahåller flera nätverkstjänster som stöder autentisering och förbättrar hanterbarheten för dina värdbaserade program. Dessa tjänster omfattar följande:

* Med Azure Virtual Network kan du etablera och hantera virtuella privata nätverk (VPN) i Azure samt på ett säkert sätt länka dessa till lokal IT-infrastruktur.
* Med Virtual Network Traffic Manager kan du läsa in aldot på inkommande trafik över flera värdbaserade Azure-tjänster oavsett om de körs i samma datacenter eller i olika datacenter runt om i världen.
* Azure Active Directory (Azure AD) är en modern, REST-baserad tjänst som tillhandahåller funktioner för identitetshantering och åtkomstkontroll för dina molnprogram. Att använda Azure AD för programresurser är ett enkelt sätt att autentisera och auktorisera användare att få åtkomst till dina webbprogram och tjänster samtidigt som funktionerna för autentisering och auktorisering kan vägas ut ur din kod.
* Azure Service Bus tillhandahåller en säker meddelande- och dataflödesfunktion för distribuerade och hybridprogram, till exempel kommunikation mellan Azure-värdbaserade program och lokala program och tjänster, utan att kräva komplex brandvägg och säkerhet Infrastrukturer. Använda Service Bus Relay för programresurser för att komma åt de tjänster som exponeras som slutpunkter kan tillhöra klienten (till exempel värd utanför systemet, till exempel lokalt), eller så kan de vara tjänster som är specifika för klienten (eftersom känsliga, klientspecifika data färdas över dem).

**Etableringsresurser**

Azure innehåller ett antal sätt att etablera nya klienter för programmet. För program med flera innehavare med ett stort antal klienter är det vanligtvis nödvändigt att automatisera den här processen genom att aktivera självbetjäningsetablering.

* Med arbetarroller kan du etablera och avetableringa per klientresurser (till exempel när en ny klient registrerar sig eller avbryter), samla in mått för mätning av användning och hantera skala enligt ett visst schema eller som svar på passage av tröskelvärden för nyckelprestanda Indikatorer. Samma roll kan också användas för att skicka ut uppdateringar och uppgraderingar till lösningen.
* Azure Blobbar kan användas för att etablera beräknings- eller förinitierade lagringsresurser för nya klienter samtidigt som principer för åtkomst på behållarnivå tillhandahåller principer för beräkningstjänstpaket, VHD-avbildningar och andra resurser.
* Alternativ för etablering av SQL Database-resurser för en klient är:
  
  * DDL i skript eller inbäddade som resurser i sammansättningar.
  * SQL Server 2008 R2 DAC-paket distribueras programmässigt.
  * Kopiera från en huvudreferensdatabas.
  * Använda databasimport och export för att etablera nya databaser från en fil.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: https://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716
