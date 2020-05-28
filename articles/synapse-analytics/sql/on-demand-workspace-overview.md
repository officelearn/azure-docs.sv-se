---
title: SQL på begäran (för hands version)
description: Lär dig mer om Synapse SQL på begäran i Azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 55ef72b23f804ce7fcdb339ba4c66bcefe39db46
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020820"
---
# <a name="sql-on-demand-preview-in-azure-synapse-analytics"></a>SQL på begäran (för hands version) i Azure Synapse Analytics 

Varje Azure Synapse Analytics-arbetsyta (för hands version) levereras med slut punkter för SQL on-demand (för hands version) som du kan använda för att fråga efter data i sjön.

SQL på begäran är en fråge tjänst över data i din data Lake. Du kan komma åt dina data med hjälp av följande funktioner:
 
- En välbekant T-SQL-syntax för att skicka frågor till data på plats utan att behöva kopiera eller läsa in data i ett specialiserat lager. 
- Integrerad anslutning via T-SQL-gränssnittet som erbjuder en mängd olika Business Intelligence-och ad hoc-frågemeddelanden, inklusive de mest populära driv rutinerna. 

SQL på begäran är ett distribuerat data bearbetnings system som skapats för stor data skalning och data bearbetning. Med SQL på begäran kan du analysera dina Big data på några sekunder till minuter, beroende på arbets belastningen. Tack vare den inbyggda fel toleransen för körning av frågor ger systemet hög tillförlitlighet och lyckade priser även för långvariga frågor som omfattar stora data mängder.

SQL på begäran är Server lös, därför finns det ingen infrastruktur för att konfigurera eller kluster att underhålla. En standard slut punkt för den här tjänsten finns i varje Azure Synapse-arbetsyta, så att du kan börja fråga efter data så fort arbets ytan skapas. Det kostar inget att betala för resurser. du debiteras bara för de data som genomsöks av frågor som du kör, och därför är den här modellen en sann kostnad per användnings modell.  

Om du använder Apache Spark för Azure-Synapse i din datapipeline för förberedelse av data, rengöring eller anrikning, kan du [fråga externa Spark-tabeller](develop-storage-files-spark-tables.md) som du har skapat i processen direkt från SQL på begäran. Använd [privat länk](../security/how-to-connect-to-workspace-with-private-links.md) för att flytta din SQL-slutpunkt på begäran till din [hanterade VNet-VNet](../security/synapse-workspace-managed-vnet.md).  

## <a name="who-is-sql-on-demand-for"></a>Vem är SQL på begäran för

Om du behöver utforska data i data Lake, få insikter från den eller optimera din befintliga pipeline för datatransformering kan du dra nytta av SQL på begäran. Det är lämpligt för följande scenarier:

- Grundläggande identifiering och utforskning – snabbt orsaken till data i olika format (Parquet, CSV, JSON) i data Lake, så att du kan planera hur du ska extrahera insikter från den.
- Logiskt informations lager – ge en relationell abstraktion ovanpå rå data eller olika data utan att flytta och transformera data, vilket ger alltid aktuell vy över dina data.
- Datatransformering – enkelt, skalbart och effektivt sätt att omvandla data i sjön med T-SQL, så att det kan matas till BI och andra verktyg eller läsas in i ett Relations data lager (Synapse SQL-databaser, Azure SQL Database osv.).

Olika professionella roller kan dra nytta av SQL på begäran:

- Data tekniker kan utforska sjö, transformera och förbereda data med hjälp av den här tjänsten och förenkla sina pipeliner för datatransformering. Mer information hittar du i den här [självstudien](tutorial-data-analyst.md).
- Data forskare kan snabbt och enkelt tänka på innehållet och strukturen i data i sjön, tack vare funktioner som OpenRowSet och automatisk schema härledning.
- Data analytiker kan [utforska data och Spark-externa tabeller](develop-storage-files-spark-tables.md) som skapats av data experter eller data tekniker som använder välbekanta T-SQL-språk eller deras favorit verktyg, som kan ansluta till SQL på begäran.
- BI-proffs kan snabbt [skapa Power BI rapporter ovanpå data i sjö](tutorial-connect-power-bi-desktop.md) -och Spark-tabellerna.

## <a name="what-do-i-need-to-do-to-start-using-it"></a>Vad behöver jag för att börja använda det?

En slut punkt för SQL på begäran finns i varje Azure Synapse-arbetsyta. Du kan skapa en arbets yta och börja fråga efter data direkt med hjälp av verktyg som du är van vid.

## <a name="client-tools"></a>Klient verktyg

SQL på begäran aktiverar befintliga SQL ad-hoc-frågor och Business Intelligence verktyg för att trycka på data Lake. Eftersom det ger en välbekant T-SQL-syntax kan alla verktyg som kan upprätta SQL-erbjudanden för TDS-anslutning [ansluta till och fråga SYNAPSE SQL](connect-overview.md) på begäran. Du kan ansluta till Azure Data Studio och köra Ad hoc-frågor eller ansluta till Power BI för att få insikter på några minuter.

## <a name="is-full-t-sql-supported"></a>Stöds full T-SQL?

SQL på begäran erbjuder T-SQL frågor på Surface-området, som är något förbättrat/utökat i vissa aspekter för att få plats med frågor om att fråga om delvis strukturerade och ostrukturerade data. Dessutom stöds inte vissa aspekter av T-SQL-språket på grund av utformningen av SQL på begäran, som ett exempel på att DML-funktionen inte stöds för närvarande.

- Arbets belastningen kan organiseras med hjälp av välbekanta koncept:
- Databaser-SQL-slutpunkt på begäran kan ha flera databaser.
- Scheman – inom en databas kan det finnas en eller flera objekt ägarskaps grupper som kallas scheman.
- Vyer
- Externa resurser – data källor, fil format och tabeller

Säkerhet kan tillämpas med:

- Inloggningar och användare
- Autentiseringsuppgifter för att kontrol lera åtkomsten till lagrings konton
- Bevilja, neka och återkalla behörigheter per objekt nivå
- Azure Active Directory-integrering

T-SQL som stöds:

- Det finns stöd för fullständig [Select](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) -arean, inklusive en majoritet av SQL-funktioner
- CETAS – SKAPA EXTERN TABELL SOM VÄLJ
- DDL-instruktioner relaterade till vyer och säkerhet

SQL på begäran har ingen lokal lagring, endast metadata-objekt lagras i-databaser. Det går därför inte att använda T-SQL som är relaterade till följande begrepp:

- Tabeller
- Utlösare
- Materialiserade vyer
- Andra DDL-instruktioner än de som är relaterade till vyer och säkerhet
- DML-uttryck

### <a name="extensions"></a>Tillägg

För att möjliggöra smidig användning av frågor om data som finns i filer i data Lake, utökar SQL på begäran den befintliga [OpenRowSet](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) -funktionen genom att lägga till följande funktioner:

[Fråga efter flera filer eller mappar](develop-storage-files-overview.md#query-multiple-files-or-folders)

[PARQUET-filformat](develop-storage-files-overview.md#parquet-file-format)

[Ytterligare alternativ för att arbeta med avgränsad text (fält avslutning, rad avslutning, escape-tecken)](develop-storage-files-overview.md#additional-options-for-working-with-delimited-text)

[Läs en vald delmängd av kolumner](develop-storage-files-overview.md#read-a-chosen-subset-of-columns)

[Schema härledning](develop-storage-files-overview.md#schema-inference)

[funktionen filename](develop-storage-files-overview.md#filename-function)

[funktionen filsökväg](develop-storage-files-overview.md#filepath-function)

[Arbeta med komplexa typer och kapslade eller upprepade data strukturer](develop-storage-files-overview.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Säkerhet

SQL på begäran erbjuder mekanismer för att skydda åtkomsten till dina data.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integrering och multifaktorautentisering

Med SQL på begäran kan du centralt hantera identiteter för databas användare och andra Microsoft-tjänster med [Azure Active Directory-integrering](../../azure-sql/database/authentication-aad-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Den här funktionen förenklar hanteringen av behörighet och ger ökad säkerhet. Azure Active Directory (Azure AD) stöder [Multi-Factor Authentication](../../azure-sql/database/authentication-mfa-ssms-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (MFA) för att öka säkerheten för data och program med stöd för en enkel inloggnings process.

#### <a name="authentication"></a>Autentisering

SQL-autentisering på begäran avser hur användare bekräftar sin identitet när de ansluter till slut punkten. Två typer av autentisering stöds:

- **SQL-autentisering**

  Den här autentiseringsmetoden använder ett användar namn och lösen ord.

- **Azure Active Directory autentisering**:

  Den här autentiseringsmetoden använder identiteter som hanteras av Azure Active Directory. För Azure AD-användare kan Multi-Factor Authentication aktive ras. Använd Active Directory-autentisering (integrerad säkerhet) [närhelst det går](/sql/relational-databases/security/choose-an-authentication-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

#### <a name="authorization"></a>Auktorisering

Auktorisering syftar på vad en användare kan göra inom en SQL-databas på begäran och styrs av ditt användar kontos databas roll medlemskap och behörigheter på objekt nivå.

Om SQL-autentisering används, finns SQL-användaren bara i SQL på begäran och behörigheter begränsas till objekten i SQL på begäran. Åtkomst till skydds bara objekt i andra tjänster (till exempel Azure Storage) kan inte beviljas till SQL-användare direkt eftersom det bara finns i omfattningen av SQL på begäran. SQL-användaren måste använda en av de [typer av auktorisering som stöds](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) för att komma åt filerna.

Om Azure AD-autentisering används kan en användare logga in på SQL på begäran och andra tjänster, t. ex. Azure Storage, och kan bevilja Azure AD-användare behörighet.

### <a name="access-to-storage-accounts"></a>Åtkomst till lagrings konton

En användare som är inloggad på SQL-tjänsten på begäran måste ha behörighet att komma åt och fråga filerna i Azure Storage. SQL på begäran stöder följande typer av auktorisering:

- **Signaturen för delad åtkomst (SAS)** ger delegerad åtkomst till resurser i lagrings kontot. Med en SAS kan du bevilja klienter åtkomst till resurser i lagrings kontot, utan att dela konto nycklar. SAS ger dig detaljerad kontroll över vilken typ av åtkomst du beviljar till klienter med SAS: giltighets intervall, beviljade behörigheter, acceptabelt IP-adressintervall, acceptabelt protokoll (https/http).

- **Användar identitet** (kallas även "vidarekoppling") är en typ av auktorisering där identiteten för den Azure AD-användare som loggade in i SQL på begäran används för att ge åtkomst till data. Azure Storage-administratören måste bevilja behörigheter till Azure AD-användare för att komma åt data innan de kan komma åt data. Den här typen av auktorisering använder den Azure AD-användare som loggade in på SQL på begäran, vilket innebär att det inte finns stöd för SQL-användargrupper.

## <a name="next-steps"></a>Nästa steg
Ytterligare information om slut punkts anslutning och fråga om filer finns i följande artiklar: 
- [Anslut till din slut punkt](connect-overview.md)
- [Fråga dina filer](develop-storage-files-overview.md)
