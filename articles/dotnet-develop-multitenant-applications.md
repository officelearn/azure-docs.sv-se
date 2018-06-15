---
title: Flera innehavare Web programmönster | Microsoft Docs
description: Hitta arkitektur översikter och designmönster som beskriver hur du implementerar ett webbprogram med flera innehavare i Azure.
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
ms.openlocfilehash: 57ba0e46139bda2d74c9f7db0ffab2f2122b0df2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23850716"
---
# <a name="multitenant-applications-in-azure"></a>Flera program i Azure
Ett multitenant program är en delad resurs som gör att olika användare eller ”innehavare” visa programmet som om den har sina egna. Ett typiskt scenario som lämpar sig för en multitenant program är en där alla användare av programmet kan vilja anpassa användarupplevelsen men annars har samma grundläggande affärsbehov. Exempel på stora multitenant program är Office 365, Outlook.com och visualstudio.com.

En programprovider ur fördelarna med multitenancy främst är relaterade till drift- och effektivitet. En version av programmet uppfyller behoven för många hyresgäster/kunder, så att slå samman system administrationsuppgifter, till exempel övervakning, Prestandajustering, programvaruunderhåll och säkerhetskopiering av data.

Här följer en lista över de viktigaste mål och krav ur en provider.

* **Etablering**: du måste kunna etablera nya klienter för programmet.  Det är vanligtvis för att automatisera processen genom att aktivera etablering av Självbetjäning för flera program med ett stort antal klienter.
* **Underhålla**: du måste kunna uppgradera programmet och utföra andra underhållsaktiviteter när flera klienter använder den.
* **Övervaka**: du måste kunna övervaka program vid alla tidpunkter för att identifiera eventuella problem och för att felsöka dem. Detta omfattar övervakning av hur varje klient använder programmet.

En välimplementerad multitenant program ger följande fördelar för användarna.

* **Isolering**: aktiviteter för enskilda klienter påverkar inte användningen av andra klienter. Klienter kommer inte åt varandras data. Det kommer till klienten som om de har exklusiv användning av programmet.
* **Tillgänglighet**: enskilda klienterna vill att programmet ska vara ständigt tillgänglig kanske garantier som definierats i ett serviceavtal. Igen, bör inte aktiviteter för andra hyresgäster påverka tillgängligheten för programmet.
* **Skalbarhet**: programmet skalas för att uppfylla behovet av enskilda klienter. Förekomst och åtgärder för andra hyresgäster bör inte påverka prestanda för programmet.
* **Kostnader**: kostnader som är lägre än med en dedikerad, stöd för en innehavare programmet eftersom multitenans aktiverar delning av resurser.
* **Anpassningsbarheten**. Möjligheten att anpassa programmet för en enskild klientorganisation på olika sätt, till exempel att lägga till eller ta bort funktioner, ändra färger och logotyper eller även att lägga till egen kod eller skript.

Kort sagt: det finns många saker som du måste ta hänsyn till att tillhandahålla en mycket skalbar tjänst, men det finns också ett antal mål och krav som är gemensamma för många program med flera innehavare. Vissa kanske inte är relevant i specifika scenarier och vikten av enskilda mål och krav varierar i varje scenario. Som en provider för flera program har du också mål och krav som, uppfyller klienternas mål och krav, lönsamhet, fakturering, flera servicenivåer, etablering, underhålla övervakning och automatisering.

Mer information om ytterligare överväganden för flera program, se [värd för flera innehavare-program på Azure][Hosting a Multi-Tenant Application on Azure]. Information om vanliga mönster för dataarkitekturen i SaaS-databasprogram (Software-as-a-Service) med flera klienter finns i [Designmönster för SaaS-program med flera klienter i Azure SQL Database](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Azure tillhandahåller många funktioner som gör att du kan åtgärda viktiga problem uppstod när du utformar en multitenant system.

**Isolering**

* Segment webbplats innehavare av värdhuvuden med eller utan SSL-kommunikation
* Segmentera webbplats innehavare av frågeparametrar
* Web Services arbetsroller
  * Arbetsroller. som vanligtvis bearbeta data på serverdelen av ett program.
  * Web-roller som vanligtvis fungerar som klientdel för program.

**Storage**

Datahantering, till exempel Azure SQL Database eller Azure Storage-tjänster, till exempel tabelltjänsten som tillhandahåller tjänster för lagring av stora mängder Ostrukturerade data och Blob-tjänst som tillhandahåller tjänster för att lagra stora mängder Ostrukturerade text eller binär data, till exempel video, ljud och bilder.

* För att skydda Multitenant Data i SQL-databas som är lämpliga per-innehavaren SQL Server-inloggningar.
* Azure-tabeller kan för programmet resurser genom att ange en princip för åtkomst av behållare du möjligheten att justera behörigheter utan att behöva utfärda nya URL för de resurser som skyddas med signaturer för delad åtkomst.
* Azure köer för programmet resurser Azure köer används ofta för bearbetning av enheten för klienter, men kan även användas för att distribuera arbetet som krävs för etablering och hantering.
* Service Bus-köer för programresurser push-meddelanden fungerar som en delad en tjänst kan du använda en enskild kö där varje innehavare avsändare endast har behörighet (som härletts från anspråk som utfärdats av ACS) att skicka till kön, medan har endast mottagarna från tjänsten behörighet för att hämta data från flera klienter från kön.

**Anslutning och säkerhetstjänster**

* Azure Service Bus i en infrastruktur för meddelanden som placeras mellan program så att de för utbyte av meddelanden på ett löst kopplade sätt för att bättre skala och återhämtning.

**Nätverkstjänster**

Azure tillhandahåller flera nätverkstjänster som stöder autentisering och förbättra hanteringen av dina program med värdar. Tjänsterna omfattar bland annat följande:

* Azure Virtual Network, kan du etablera och hantera virtuella privata nätverk (VPN) i Azure som på ett säkert sätt länka dessa med lokal IT-infrastruktur.
* Hanteraren för virtuella nätverk trafik kan du läsa in saldo inkommande trafik över flera Azure värdtjänster oavsett om de använder i samma datacenter eller i olika Datacenter runtom i världen.
* Azure Active Directory (AD Azure) är en modern REST-baserad tjänst som tillhandahåller identity management och åtkomst kontroll funktioner för dina molnprogram. Använda Azure AD för programresurser Azure AD för att ger ett enkelt sätt att autentisera användare att få tillgång till ditt webbprogram och tjänster medan funktioner för autentisering och auktorisering vägas ut från din kod.
* Azure Service Bus är en säker och kapaciteten för flödet av data för distribuerade och hybridprogram, till exempel kommunikation mellan Azure finns program och lokala program och tjänster, utan komplexa brandvägg och säkerhet infrastruktur. Använda Service Bus Relay för resurser till de tjänster som visas som slutpunkter kan höra till klienten (till exempel finns utanför systemet, till exempel lokalt) eller de kanske tjänster som skapats specifikt för innehavaren eftersom ( känsliga, klient-specifika data överförs mellan dem).

**Etablering av resurser**

Azure tillhandahåller ett antal sätt etablera nya klienter för programmet. Det är vanligtvis för att automatisera processen genom att aktivera etablering av Självbetjäning för flera program med ett stort antal klienter.

* Arbetsroller kan du etablera och avinstallation etablera per klient resurser (t.ex när en ny klient loggar in eller avbryter), samla in statistik för Avläsning av använda och hantera skala enligt ett visst schema eller som svar på korsande av tröskelvärden för prestanda indikatorer. Samma rollen kan även användas för att skicka ut uppdateringar och uppgraderingar i lösningen.
* Azure BLOB kan användas för att etablera beräkning eller förinitierad lagringsresurser för nya klienter samtidigt som man behållaren administratörsnivå principer att skydda beräknade tjänsten paket, VHD-avbildningar och andra resurser.
* Alternativ för att etablera SQL Database-resurser för en klient inkluderar:
  
  * DDL i skript eller inbäddade som resurser i sammansättningar
  * SQL Server 2008 R2 DAC-paket distribueras via programmering.
  * Kopiera från en för master-referensdatabasen
  * Med hjälp av databasen för att importera och exportera att etablera nya databaser från en fil.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: http://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: http://msdn.microsoft.com/library/windowsazure/hh689716
