---
title: SQL on-demand (förhandsgranskning)
description: Lär dig mer om Synapse SQL on-demand i Azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8d4de424d5d4d6da1ee80e04b35e63ae29df57c8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424911"
---
# <a name="sql-on-demand-preview-in-azure-synapse-analytics"></a>SQL on-demand (förhandsversion) i Azure Synapse Analytics 

Varje Azure Synapse Analytics arbetsyta (förhandsversion) levereras med SQL on-demand (preview) slutpunkter som du kan använda för att fråga data i sjön.

SQL on-demand är en frågetjänst över data i datasjön. Det gör att du kan komma åt dina data via följande funktioner:
 
- En välbekant T-SQL-syntax för att fråga data på plats utan att behöva kopiera eller läsa in data i ett specialiserat arkiv. 
- Integrerad anslutning via T-SQL-gränssnittet som erbjuder ett brett utbud av business intelligence och ad hoc-frågeverktyg, inklusive de mest populära drivrutinerna. 

SQL on-demand är ett distribuerat databehandlingssystem, byggt för stor skala av data och beräkning. SQL on-demand kan du analysera dina Big Data på några sekunder till minuter, beroende på arbetsbelastningen. Tack vare inbyggd feltolerans för frågekörning ger systemet hög tillförlitlighet och framgång även för tidskrävande frågor som involverar stora datauppsättningar.

SQL on-demand är serverlös, därför finns det ingen infrastruktur för installation eller kluster att underhålla. En standardslutpunkt för den här tjänsten tillhandahålls inom varje Azure Synapse-arbetsyta, så att du kan börja fråga data så snart arbetsytan skapas. Det finns ingen avgift för reserverade resurser, du debiteras bara för data som skannas av frågor som du kör, varför den här modellen är en sann pay-per-use-modell.  

Om du använder Spark i datapipelinen, för dataförberedelse, rensning eller anrikning, kan du [fråga alla Spark-tabeller](develop-storage-files-spark-tables.md) som du har skapat i processen, direkt från SQL on-demand. Använd [Privat länk](../security/how-to-connect-to-workspace-with-private-links.md) för att föra in din SQL on-demand-slutpunkt i ditt hanterade virtuella nätverk för [arbetsytan](../security/synapse-workspace-managed-vnet.md).  

## <a name="who-is-sql-on-demand-for"></a>Vem är SQL on-demand för

Om du behöver utforska data i datasjön, få insikter från den eller optimera din befintliga dataomvandlingspipeline kan du dra nytta av att använda SQL på begäran. Den är lämplig för följande scenarier:

- Grundläggande upptäckt och utforskning - Snabbt resonera om data i olika format (Parkett, CSV, JSON) i din datasjö, så att du kan planera hur du extraherar insikter från den.
- Logiskt informationslager – Ange en relationsabstraktion ovanpå råa eller olika data utan att flytta och omvandla data, så att alltid aktuell vy av dina data.
- Dataomvandling - Enkelt, skalbart och högpresterande sätt att omvandla data i sjön med T-SQL, så att den kan matas till BI och andra verktyg, eller läsas in i ett relationsdatalager (Synapse SQL-databaser, Azure SQL Database, etc.).

Olika yrkesroller kan dra nytta av SQL on-demand:

- Datatekniker kan utforska sjön, omvandla och förbereda data med den här tjänsten och förenkla sina dataomvandlingspipelpipels. Mer information finns i den här [självstudien](tutorial-data-analyst.md).
- Data Forskare kan snabbt resonera om innehållet och strukturen på data i sjön, tack vare funktioner som OPENROWSET och automatisk schemainferens.
- Dataanalytiker kan [utforska data och Spark-tabeller](develop-storage-files-spark-tables.md) som skapats av dataforskare eller datatekniker med välbekanta T-SQL-språk eller deras favoritverktyg, som kan ansluta till SQL on-demand.
- BI Professionals kan snabbt [skapa Power BI-rapporter ovanpå data i sjö-](tutorial-connect-power-bi-desktop.md) och Spark-tabellerna.

## <a name="what-do-i-need-to-do-to-start-using-it"></a>Vad behöver jag göra för att börja använda den?

SQL on-demand-slutpunkt tillhandahålls i alla Azure Synapse-arbetsyta. Du kan skapa en arbetsyta och börja fråga data direkt med hjälp av verktyg som du är bekant med.

## <a name="client-tools"></a>Klientverktyg

SQL on-demand gör det möjligt för befintliga SQL-ad hoc-frågor och business intelligence-verktyg att utnyttja datasjön. Eftersom det ger bekant T-SQL-syntax kan alla verktyg som kan upprätta SQL-erbjudanden för TDS [ansluta till och fråga Synapse SQL](connect-overview.md) på begäran. Du kan ansluta till Azure Data Studio och köra ad hoc-frågor eller ansluta till Power BI för att få insikter på några minuter.

## <a name="is-full-t-sql-supported"></a>Stöds fullständig T-SQL?

SQL on-demand erbjuder T-SQL frågayta, som är något förbättrad / utökad i vissa aspekter för att rymma för upplevelser kring att fråga halvstrukturerade och ostrukturerade data. Dessutom stöds inte vissa aspekter av T-SQL-språket på grund av utformningen av SQL on-demand, som ett exempel, DML-funktioner stöds för närvarande inte.

- Arbetsbelastningen kan ordnas med välbekanta begrepp:
- Databaser - SQL-slutpunkt på begäran kan ha flera databaser.
- Scheman - I en databas kan det finnas en eller flera objektägarskapsgrupper som kallas scheman.
- Vyer
- Externa resurser – datakällor, filformat och tabeller

Säkerhet kan upprätthållas med hjälp av:

- Inloggningar och användare
- Autentiseringsuppgifter för att kontrollera åtkomsten till lagringskonton
- Bevilja, neka och återkalla behörigheter per objektnivå
- Azure Active Directory-integrering

T-SQL som stöds:

- Full [SELECT-yta](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) stöds, inklusive en majoritet av SQL-funktioner
- CETAS - SKAPA EXTERN TABELL SOM SELECT
- DDL-satser som endast är relaterade till vyer och säkerhet

SQL on-demand har ingen lokal lagring, endast metadataobjekt lagras i databaser. Därför stöds inte T-SQL-relaterade till följande begrepp:

- Tabeller
- Utlösare
- Materialiserade vyer
- Andra DDL-satser än de som rör vyer och säkerhet
- DML-uttryck

### <a name="extensions"></a>Tillägg

För att möjliggöra smidig upplevelse för på plats fråga av data som finns i filer i datasjön, SQL on-demand utökar den befintliga [OPENROWSET-funktionen](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) genom att lägga till följande funktioner:

[Fråga flera filer eller mappar](develop-storage-files-overview.md#query-multiple-files-or-folders)

[PARETT filformat](develop-storage-files-overview.md#parquet-file-format)

[Ytterligare alternativ för att arbeta med avgränsad text (fältterminering, radsluter, escape-tecken)](develop-storage-files-overview.md#additional-options-for-working-with-delimited-text)

[Läsa en vald delmängd av kolumner](develop-storage-files-overview.md#read-a-chosen-subset-of-columns)

[Inferens för schema](develop-storage-files-overview.md#schema-inference)

[funktionen filnamn](develop-storage-files-overview.md#filename-function)

[funktionen filsöka](develop-storage-files-overview.md#filepath-function)

[Arbeta med komplexa typer och kapslade eller upprepade datastrukturer](develop-storage-files-overview.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Säkerhet

SQL on-demand erbjuder mekanismer för att skydda åtkomsten till dina data.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integrering och multifaktorautentisering

Sql on-demand kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster med [Azure Active Directory-integration](../../sql-database/sql-database-Azure AD-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Den här funktionen förenklar hanteringen av behörighet och ger ökad säkerhet. Azure Active Directory (Azure AD) stöder [MFA (Multi Factor Authentication)](../../sql-database/sql-database-ssms-mfa-authentication-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) för att öka data- och programsäkerheten samtidigt som den stöder en enda inloggningsprocess.

#### <a name="authentication"></a>Autentisering

SQL on-demand-autentisering refererar till hur användare bevisar sin identitet när de ansluter till slutpunkten. Två typer av autentisering stöds:

- **SQL-autentisering**

  Den här autentiseringsmetoden använder ett användarnamn och lösenord.

- **Azure Active Directory-autentisering:**

  Den här autentiseringsmetoden använder identiteter som hanteras av Azure Active Directory. För Azure AD-användare kan multifaktorautentisering aktiveras. Använd Active Directory-autentisering (integrerad säkerhet) [närhelst det går](/sql/relational-databases/security/choose-an-authentication-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

#### <a name="authorization"></a>Auktorisering

Auktorisering refererar till vad en användare kan göra i en SQL-databas på begäran och styrs av användarkontots databasrollmedlemskap och behörigheter på objektnivå.

Om SQL-autentisering används finns SQL-användaren endast i SQL on-demand och behörigheter begränsas till objekten i SQL on-demand. Åtkomst till objekt som kan begränsas i andra tjänster (till exempel Azure Storage) kan inte beviljas SQL-användare direkt eftersom den bara finns i sql on-demand. SQL-användaren måste använda en av de [auktoriseringstyper som stöds](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) för att komma åt filerna.

Om Azure AD-autentisering används kan en användare logga in på SQL on-demand och andra tjänster, till exempel Azure Storage, och kan bevilja behörigheter till Azure AD-användaren.

### <a name="access-to-storage-accounts"></a>Tillgång till lagringskonton

En användare som är inloggad på SQL on-demand-tjänsten måste ha behörighet att komma åt och fråga filerna i Azure Storage. SQL on-demand stöder följande auktoriseringstyper:

- **Signature för delad åtkomst (SAS)** ger delegerad åtkomst till resurser i lagringskontot. Med en SAS kan du ge klienter åtkomst till resurser i lagringskontot utan att dela kontonycklar. En SAS ger dig detaljerad kontroll över vilken typ av åtkomst du beviljar till klienter som har SAS: giltighetsintervall, beviljade behörigheter, godtagbart IP-adressintervall, godtagbart protokoll (https/http).

- **Användaridentitet** (kallas även "genomströmning") är en auktoriseringstyp där identiteten för Azure AD-användaren som loggat in på SQL på begäran används för att auktorisera åtkomst till data. Innan azure Storage-administratören får åtkomst till data måste den bevilja azure AD-användare behörigheter för åtkomst till data. Den här auktoriseringstypen använder Azure AD-användaren som loggat in på SQL på begäran, därför stöds den inte för SQL-användartyper.

## <a name="next-steps"></a>Nästa steg
Ytterligare information om slutpunktsanslutning och frågefiler finns i följande artiklar: 
- [Anslut till slutpunkten](connect-overview.md)
- [Fråga dina filer](develop-storage-files-overview.md)
