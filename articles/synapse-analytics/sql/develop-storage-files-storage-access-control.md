---
title: Kontrol lera åtkomsten till lagrings kontot för SQL på begäran (för hands version)
description: Beskriver hur SQL på begäran (för hands version) får åtkomst Azure Storage och hur du kan kontrol lera lagrings åtkomst för SQL på begäran i Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 6ebf23720d1d323b66671c6770ab2121c9091920
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197693"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview"></a>Kontrol lera åtkomsten till lagrings kontot för SQL på begäran (för hands version)

En SQL-fråga på begäran läser filer direkt från Azure Storage. Behörigheter för att komma åt filerna i Azure Storage styrs på två nivåer:
- **Lagrings nivå** – användaren bör ha behörighet att komma åt underliggande lagringsfiler. Lagrings administratören ska tillåta att Azure AD-huvudobjektet läser/skriver filer, eller genererar SAS-nyckel som ska användas för att komma åt lagringen.
- **SQL Service Level** -användaren ska ha `ADMINISTER BULK ADMIN` behörighet att köra `OPENROWSET` och även behörighet att använda autentiseringsuppgifter som används för att komma åt lagringen.

Den här artikeln beskriver de typer av autentiseringsuppgifter som du kan använda och hur sökning efter autentiseringsuppgifter registreras för SQL-och Azure AD-användare.

## <a name="supported-storage-authorization-types"></a>Typer av lagringspooler som stöds

En användare som har loggat in på en SQL-resurs på begäran måste ha behörighet att komma åt och fråga filerna i Azure Storage. Tre typer av verifierings typer stöds:

- [Signatur för delad åtkomst](#shared-access-signature)
- [Hanterad identitet](#managed-identity)
- [Användar identitet](#user-identity)

> [!NOTE]
> [Azure AD-vidarekoppling](#force-azure-ad-pass-through) är standard beteendet när du skapar en arbets yta. Om du använder det behöver du inte skapa autentiseringsuppgifter för varje lagrings konto som nås med hjälp av Azure AD-inloggningar. Du kan [inaktivera det här beteendet](#disable-forcing-azure-ad-pass-through).

I tabellen nedan hittar du tillgängliga typer av autentisering:

| Auktoriseringstyp                    | *SQL-användare*    | *Azure AD-användare*     |
| ------------------------------------- | ------------- | -----------    |
| [SÄKERHETS](#shared-access-signature)       | Stöds     | Stöds      |
| [Hanterad identitet](#managed-identity) | Stöds inte | Stöds      |
| [Användar identitet](#user-identity)       | Stöds inte | Stöds      |

### <a name="shared-access-signature"></a>Signatur för delad åtkomst

**Signaturen för delad åtkomst (SAS)** ger delegerad åtkomst till resurser i ett lagrings konto. Med SAS kan en kund bevilja klienter åtkomst till resurser i ett lagrings konto utan att dela konto nycklar. SAS ger detaljerad kontroll över vilken typ av åtkomst du beviljar till klienter som har en SAS, inklusive giltighets intervall, beviljade behörigheter, acceptabelt IP-adressintervall och acceptabelt protokoll (https/http).

Du kan få en SAS-token genom att gå till **Azure Portal-> lagrings konto-> signatur för delad åtkomst-> konfigurera behörigheter – > generera SAS och anslutnings sträng.**

> [!IMPORTANT]
> När en SAS-token skapas, innehåller den ett frågetecken ("?") i början av token. Om du vill använda token i SQL på begäran måste du ta bort frågetecknet (?) när du skapar en autentiseringsuppgift. Ett exempel:
>
> SAS-token:? sa = 2018-03-28&SS = bfqt&SRT = SCO&SP = rwdlacup&se = 2019-04-18T20:42:12Z&St = 2019-04-18T12:42:12Z&spr = https&sig = lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78% 3D

### <a name="user-identity"></a>Användar identitet

**Användar identitet**, som även kallas "vidarekoppling", är en typ av auktorisering där identiteten för den Azure AD-användare som loggade in på begäran används för att ge åtkomst till data. Innan du får åtkomst till data måste Azure Storages administratören bevilja behörighet till Azure AD-användaren. Som anges i tabellen ovan stöds det inte för SQL-användargruppen.

> [!IMPORTANT]
> Du måste ha rollen som ägare/deltagare/läsare för Storage BLOB-rollen för att kunna använda din identitet för att komma åt data.
> Även om du är ägare till ett lagrings konto behöver du fortfarande lägga till dig själv i en av lagrings BLOB-datarollerna.
>
> Läs mer om åtkomst kontroll i Azure Data Lake Store Gen2 genom att granska [åtkomst kontrollen i Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md) artikeln.
>

Du måste uttryckligen aktivera Azure AD-direktautentisering för att ge Azure AD-användare åtkomst till lagrings utrymme med hjälp av sina identiteter.

#### <a name="force-azure-ad-pass-through"></a>Tvinga Azure AD-vidarekoppling

Att tvinga fram en Azure AD-vidarekoppling är ett standard beteende som uppnås av ett särskilt INLOGGNINGs namn, `UserIdentity` som skapas automatiskt under etablering av Azure Synapse-arbetsytan. Den tvingar användningen av ett Azure AD-vidarekoppling för varje fråga om varje Azure AD-inloggning, vilket sker trots att andra autentiseringsuppgifter finns.

> [!NOTE]
> Azure AD-vidarekoppling är ett standard beteende. Du behöver inte skapa autentiseringsuppgifter för varje lagrings konto som används av AD-inloggningar.

Om du har [inaktiverat tvinga fram Azure AD-vidarekoppling för varje fråga](#disable-forcing-azure-ad-pass-through)och vill aktivera det igen kör du:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Om du vill aktivera en Azure AD-vidarekoppling för en viss användare kan du bevilja referens behörighet för autentiseringsuppgifter `UserIdentity` till den specifika användaren. I följande exempel kan du tvinga fram en Azure AD-vidarekoppling för en user_name:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

#### <a name="disable-forcing-azure-ad-pass-through"></a>Inaktivera tvingande Azure AD-vidarekoppling

Du kan inaktivera [Tvingad Azure AD-vidarekoppling för varje fråga](#force-azure-ad-pass-through). Du inaktiverar den genom att släppa `Userdentity` autentiseringsuppgiften med:

```sql
DROP CREDENTIAL [UserIdentity];
```

Om du vill återaktivera det igen läser du avsnittet om [Azure AD-vidarekoppling](#force-azure-ad-pass-through) .

### <a name="managed-identity"></a>Hanterad identitet

**Hanterad identitet** kallas även MSI. Det är en funktion i Azure Active Directory (Azure AD) som tillhandahåller Azure-tjänster för SQL på begäran. Dessutom distribueras en automatiskt hanterad identitet i Azure AD. Den här identiteten kan användas för att auktorisera begäran om data åtkomst i Azure Storage.

Innan du får åtkomst till data måste Azure Storages administratören bevilja behörighet till hanterad identitet för att komma åt data. Att bevilja behörigheter till hanterad identitet görs på samma sätt som när du beviljar behörighet till andra Azure AD-användare.

## <a name="credentials"></a>Autentiseringsuppgifter

Om du vill fråga en fil som finns i Azure Storage måste din SQL-slutpunkt på begäran ha en autentiseringsuppgift som innehåller autentiseringsinformationen. Två typer av autentiseringsuppgifter används:
- AUTENTISERINGSUPPGIFTER på server nivå används för ad hoc-frågor som körs med hjälp av `OPENROWSET` funktion. Namnet på autentiseringsuppgiften måste matcha lagrings-URL: en.
- DATABASens begränsade AUTENTISERINGSUPPGIFTER används för externa tabeller. En extern tabell refererar till `DATA SOURCE` den autentiseringsuppgift som ska användas för åtkomst till lagring.

Du lägger till en autentiseringsuppgift genom att köra [skapa autentiseringsuppgifter](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Du måste ange ett namn argument för AUTENTISERINGSUPPGIFTER. Den måste matcha antingen en del av sökvägen eller hela sökvägen till data i lagringen (se nedan).

> [!NOTE]
> Argumentet FOR CRYPTOGRAPHIC PROVIDER stöds inte.

För alla typer som stöds kan autentiseringsuppgifter peka på ett konto eller en behållare.

Namnet på Server nivåns AUTENTISERINGSUPPGIFTER måste matcha den fullständiga sökvägen till lagrings kontot (och eventuellt container) i följande format:`<prefix>://<storage_account_path>/<storage_path>`

| Extern data Källa       | Prefix | Sökväg till lagrings konto                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>. blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>. dfs.core.windows.net              |


> [!NOTE]
> Det finns särskilda AUTENTISERINGSUPPGIFTER på server nivå `UserIdentity` som [tvingar fram Azure AD-vidarekoppling](#force-azure-ad-pass-through).

Om du vill tillåta att en användare skapar eller släpper en autentiseringsuppgift, kan administratören bevilja/neka behörighet att ändra AUTENTISERINGSUPPGIFTER till en användare:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>Lagrings enheter och verifierings typer som stöds

Du kan använda följande kombinationer av auktoriserings-och Azure Storage typer:

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SÄKERHETS*               | Stöds      | Stöds inte   | Stöds     |
| *Hanterad identitet* | Stöds      | Stöds        | Stöds     |
| *Användar identitet*    | Stöds      | Stöds        | Stöds     |


### <a name="grant-permissions-to-use-credential"></a>Bevilja behörighet att använda autentiseringsuppgifter

Om du vill använda autentiseringsuppgifterna måste en användare ha behörighet för referenser till en angiven autentiseringsuppgift. Om du vill ge en referens behörighet för en storage_credential för en specific_user kör du:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

För att säkerställa en smidig Azure AD-direktautentisering har alla användare som standard rätt att använda `UserIdentity` autentiseringsuppgiften. Detta uppnås genom en automatisk körning av följande uttryck vid Azure Synapse-arbetsytans etablering:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

### <a name="examples"></a>Exempel

Beroende på typ av [auktorisering](#supported-storage-authorization-types)kan du skapa autentiseringsuppgifter med hjälp av T-SQL-syntaxen nedan.
- Autentiseringsuppgifter för Server omfång används när SQL login-anrop `OPENROWSET` fungerar utan `DATA_SOURCE` att läsa filer på vissa lagrings konton. Namnet på serverns begränsade autentiseringsuppgifter **måste** matcha URL: en för Azure Storage.
- Autentiseringsuppgifter för databasens omfattning används när en huvud anrops `OPENROWSET` funktion med `DATA_SOURCE` eller väljer data från extern tabell som inte kommer åt offentliga filer. Databasens begränsade autentiseringsuppgifter behöver inte matcha namnet på lagrings kontot eftersom det används explicit i DATA källan som definierar lagrings platsen.

**Server-begränsade autentiseringsuppgifter med signaturen för delad åtkomst för Blob Storage**

Följande skript skapar en autentiseringsuppgift på server nivå som kan användas för att få åtkomst till alla filer i Azure Storage med SAS-token. Skapa den här autentiseringsuppgiften för att aktivera SQL-huvudobjektet som kör `OPENROWSET` funktionen för att läsa filer som skyddas med SAS-nyckel på Azure Storage som matchar URL: en i autentiseringsuppgiften.

Exchange <*mystorageaccountname*> med ditt faktiska lagrings konto namn och <*mystorageaccountcontainername*> med det faktiska behållar namnet:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Server-begränsade autentiseringsuppgifter som ger åtkomst till offentlig lagring**

Följande skript skapar en autentiseringsuppgift på server nivå som kan användas för att få åtkomst till alla filer på offentligt tillgängliga Azure-lagring. Skapa den här autentiseringsuppgiften för att aktivera SQL-huvudobjektet som kör `OPENROWSET` funktionen för att läsa offentligt tillgängliga filer på Azure Storage som matchar URL: en med namnet på autentiseringsuppgiften.

Du måste ha Exchange <*mystorageaccountname* -> med det faktiska lagrings konto namnet och <*mystorageaccountcontainername*> med det faktiska behållar namnet:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = '';
GO
```

**Autentiseringsuppgifter för databas omfattning med SAS-token**

Följande skript skapar en autentiseringsuppgift som används för att komma åt filer på lagrings platsen med SAS-token som anges i autentiseringsuppgiften.

```sql
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Autentiseringsuppgifter för databas omfattning med Azure AD-identitet**

Följande skript skapar en autentiseringsuppgift som används av externa tabeller och `OPENROWSET` funktioner som använder data källa med autentiseringsuppgifter för att komma åt lagringsfiler med sin egen Azure AD-identitet.

```sql
CREATE DATABASE SCOPED CREDENTIAL [AzureAD]
WITH IDENTITY = 'User Identity';
GO
```

**Autentiseringsuppgifter för databas omfattning med hanterad identitet**

Följande skript skapar en autentiseringsuppgift som kan användas för att personifiera den aktuella Azure AD-användaren som hanterad identitet för tjänsten. 

```sql
CREATE DATABASE SCOPED CREDENTIAL [SynapseIdentity]
WITH IDENTITY = 'Managed Identity';
GO
```

Databasens begränsade autentiseringsuppgifter behöver inte matcha namnet på lagrings kontot eftersom det används explicit i DATA källan som definierar lagrings platsen.

## <a name="next-steps"></a>Nästa steg

Artiklarna i listan nedan hjälper dig att lära dig hur frågar olika typer av mappar, filtyper och hur du skapar och använder vyer:

- [Fråga en enkel CSV-fil](query-single-csv-file.md)
- [Fråga mappar och flera CSV-filer](query-folders-multiple-csv-files.md)
- [Fråga efter vissa filer](query-specific-files.md)
- [Efterfråga Parquet-filer](query-parquet-files.md)
- [Skapa och använda vyer](create-use-views.md)
- [Efterfråga JSON-filer](query-json-files.md)
- [Efterfråga kapslade Parquet-typer](query-parquet-nested-types.md)
