---
title: Programmönster för flera innehavare Web | Microsoft Docs
description: Hitta arkitektoniska översikter och designmönster som beskriver hur du implementerar ett webbprogram med flera innehavare i Azure.
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
ms.openlocfilehash: 342c7903e58a5c3bc41278152630187fa0c63b7b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119155"
---
# <a name="multitenant-applications-in-azure"></a>Program med flera klienter i Azure
Ett program för flera är en delad resurs där olika användare eller ”innehavare” visa programmet som om den har sina egna. Ett typiskt scenario som lämpar sig för ett program för flera är en där alla användare av programmet kanske vill anpassa användarupplevelsen men annars har samma grundläggande affärskraven. Exempel på stora program för flera innehavare är Office 365 och Outlook.com visualstudio.com.

Ur en programprovider, fördelarna med flera innehavare främst är relaterade till drift- och effektivitet. En version av programmet uppfyller behoven för många klienter/kunder, så att konsolidera system administrationsuppgifter som övervakning, Prestandajustering, programvaruunderhåll och säkerhetskopiering av data.

Följande innehåller en lista över de viktigaste mål och krav från en leverantörs perspektiv.

* **Etablering**: Du måste kunna etablera nya klienter för programmet.  För program med flera klienter med ett stort antal klienter är det vanligtvis krävs för att automatisera processen genom att aktivera självbetjäning etablering.
* **Underhållsfunktioner**: Du måste kunna uppgradera programmet och utföra andra underhållsaktiviteter när flera klienter använder den.
* **Övervakning av**: Du måste kunna övervaka programmet vid alla tidpunkter för att identifiera eventuella problem och för att felsöka dem. Detta omfattar övervakning hur varje klient använder programmet.

En välimplementerad program med flera klienter ger följande fördelar för användarna.

* **Isolering**: Aktiviteter för enskilda klienter påverkar inte användningen av andra klienter. Klienter inte åtkomst till varandras data. Det kommer till klienten som om de har exklusiv användning av programmet.
* **Tillgänglighet**: Enskilda klienterna vill att programmet ska vara ständigt tillgänglig, kanske med garantier som definierats i ett serviceavtal. Igen, bör aktiviteter med andra klienter inte påverka tillgängligheten för programmet.
* **Skalbarhet**: Programmet kan skalas till att uppfylla behovet av att enskilda klienter. Förekomst och åtgärder för andra hyresgäster bör inte påverka prestanda för programmet.
* **Kostnaderna**: Kostnaderna är lägre än att köra ett program med dedikerad, en enda klient eftersom flera innehavare som möjliggör delning av resurser.
* **Anpassningsbarhet**. Möjlighet att anpassa programmet för en enskild klientorganisation på olika sätt, till exempel att lägga till eller ta bort funktioner, ändra färger och logotyper eller även att lägga till sin egen kod eller skript.

Kort sagt så det finns många saker som du måste tänka på att tillhandahålla en mycket skalbar tjänst, men det finns också ett antal mål och krav som är gemensamma för många program för flera innehavare. Vissa kanske inte är relevanta i specifika scenarier och vikten av enskilda mål och krav varierar i varje scenario. Som leverantör av program för flera klientorganisationer har du också mål och krav som t.ex uppfyller klienternas mål och krav, lönsamhet, fakturering, flera servicenivåer, etablering, underhållsfunktioner övervakning och automatisering.

Mer information om ytterligare beaktanden vid utformning av ett program för flera finns [som är värd för ett program med flera innehavare i Azure][Hosting a Multi-Tenant Application on Azure]. Information om vanliga mönster för dataarkitekturen i SaaS-databasprogram (Software-as-a-Service) med flera klienter finns i [Designmönster för SaaS-program med flera klienter i Azure SQL Database](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Azure tillhandahåller många funktioner som gör det möjligt att åtgärda de viktiga problem som kan uppstå när du utformar ett flerklientsystem.

**Isolering**

* Segmentet webbplats klienter genom att värdhuvuden med eller utan SSL-kommunikation
* Segmentera webbplats klienter genom att frågeparametrar
* Webbtjänster i Worker-roller
  * Worker-roller. som vanligtvis bearbeta data på serverdelen för ett program.
  * Web-roller som vanligtvis fungerar som klientdel för program.

**Storage**

Datahantering, till exempel Azure SQL Database eller Azure Storage-tjänster, till exempel tabelltjänsten som tillhandahåller tjänster för lagring av stora mängder Ostrukturerade data och Blob-tjänsten som tillhandahåller tjänster för att lagra stora mängder ostrukturerad text eller binära data som video, ljud och bilder.

* Att säkra Multitenant-Data i SQL-databas som är lämpliga per-klient SQL Server-inloggningar.
* Azure-tabeller kan för programmet resurser genom att ange en princip för åtkomst av behållaren du möjligheten att justera behörigheterna utan att behöva utfärda nya URL: er för de resurser som skyddas med signaturer för delad åtkomst.
* Azure-köer för programmet resurser Azure-köer används ofta för bearbetning av enheten för klienter, men kan även användas för att fördela arbete som krävs för etablering och hantering.
* Service Bus-köer för programresurser som skickar arbetar för att en delad en tjänst kan du använda en enskild kö där varje innehavare avsändare endast har behörighet (som härletts från anspråk som utfärdats från ACS) att skicka till kön även om endast mottagarna från tjänsten har behörighet för att hämta data från flera klienter från kön.

**Anslutning och säkerhetstjänster**

* Azure Service Bus, en meddelandeinfrastruktur placerad mellan program som kan utbyta meddelanden i ett löst sammansatt skalbarheten och återhämtningskapaciteten.

**Nätverkstjänster**

Azure tillhandahåller flera nätverkstjänster som stöd för autentisering och förbättra hanteringen av dina program med värdar. Dessa tjänster inkluderar följande:

* Azure Virtual Network kan du etablera och hantera virtuella privata nätverk (VPN) i Azure samt på ett säkert sätt länka dem med en lokal IT-infrastruktur.
* Virtuella nätverk Traffic Manager kan du belastningsutjämna inkommande trafik över flera värdbaserade Azure-tjänster om de körs i samma datacenter eller i olika Datacenter runtom i världen.
* Azure Active Directory (Azure AD) är en modern, REST-baserad tjänst som tillhandahåller funktioner för Identitetshantering och åtkomstkontroll för dina molnprogram. Med hjälp av Azure AD för programresurser Azure AD för att gör det enkelt för autentisering och auktorisering av användare att få åtkomst till dina webbprogram och tjänster samtidigt som funktionerna i autentisering och auktorisering för räknas in från din kod.
* Azure Service Bus erbjuder en säker meddelandehantering och kapaciteten för flödet av data för distribuerade och hybridprogram, till exempel kommunikation mellan Azure värdbaserade program och lokala program och tjänster, utan komplexa konfigurationer med brandväggar och säkerhet infrastrukturer. Med Service Bus Relay för programresurser till de tjänster som exponeras som slutpunkter kan höra till klienten (t.ex, utanför systemet, till exempel lokalt) eller också kan vara tjänster som är etablerade specifikt för klienten eftersom ( känsliga, klientspecifik data skickas via dem).

**Etablera resurser**

Azure tillhandahåller ett antal sätt etablera nya klienter för programmet. För program med flera klienter med ett stort antal klienter är det vanligtvis krävs för att automatisera processen genom att aktivera självbetjäning etablering.

* Worker-roller gör att du kan etablera och användares per klient resurser (till exempel när en ny klient registrerar sig eller avbryter), samla in mått för Avläsning av programvara, använda och hantera skala enligt ett visst schema eller som svar på passera tröskelvärden för KPI indikatorer. Den här samma rollen kan även användas för att skicka ut uppdateringar och uppgraderingar till lösningen.
* Azure-Blobar kan användas för att etablera beräkning eller redan initierats lagringsresurser för nya klienter samtidigt på åtkomstprinciper för behållaren att skydda beräkningen som tjänsten paket, VHD-avbildningar och andra resurser.
* Alternativ för att etablera SQL Database-resurser för en klient är:
  
  * DDL i skript eller inbäddade som resurser i sammansättningar
  * SQL Server 2008 R2 DAC-paket distribueras via programmering.
  * Kopiera från en för master-referensdatabasen
  * Med hjälp av databasen för att importera och exportera att etablera nya databaser från en fil.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: https://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716
