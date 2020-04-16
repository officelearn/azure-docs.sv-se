---
title: Kontrollera åtkomst till lagringskonto för SQL på begäran (förhandsgranskning)
description: Beskriver hur SQL on-demand (preview) får åtkomst till Azure Storage och hur du kan styra lagringsåtkomst för SQL on-demand i Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0d2683091898e9c84457b3b538776f0e6b0469d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424036"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Kontrollera åtkomst till lagringskonto för SQL on-demand (förhandsversion) i Azure Synapse Analytics

En SQL on-demand (preview) fråga läser filer direkt från Azure Storage. Eftersom lagringskontot är ett objekt som är externt till SQL on-demand-resursen krävs lämpliga autentiseringsuppgifter. En användare behöver de behörigheter som beviljas för att använda den nödvändiga autentiseringsidentialen. I den här artikeln beskrivs vilka typer av autentiseringsuppgifter du kan använda och hur autentiseringsuppgifter för SQL- och Azure AD-användare.

## <a name="supported-storage-authorization-types"></a>Lagringsauktoriseringstyper som stöds

En användare som har loggat in på en SQL on-demand-resurs måste ha behörighet att komma åt och fråga filerna i Azure Storage. Tre auktoriseringstyper stöds:

- [Signatur för delad åtkomst](#shared-access-signature)
- [Hanterad identitet](#managed-identity)
- [Användarens identitet](#user-identity)

> [!NOTE]
> [Azure AD-vidaregång](#force-azure-ad-pass-through) är standardbeteendet när du skapar en arbetsyta. Om du använder det behöver du inte skapa autentiseringsuppgifter för varje lagringskonto som används med AD-inloggningar. Du kan [inaktivera det här beteendet](#disable-forcing-azure-ad-pass-through).

I tabellen nedan hittar du de olika auktoriseringstyper som antingen stöds eller kommer att stödjas snart.

| Auktoriseringstyp                    | *SQL-användare*    | *Azure AD-användare*     |
| ------------------------------------- | ------------- | -----------    |
| [Sas](#shared-access-signature)       | Stöds     | Stöds      |
| [Hanterad identitet](#managed-identity) | Stöds inte | Stöds inte  |
| [Användarens identitet](#user-identity)       | Stöds inte | Stöds      |

### <a name="shared-access-signature"></a>Signatur för delad åtkomst

**Signature för delad åtkomst (SAS)** ger delegerad åtkomst till resurser i ett lagringskonto. Med SAS kan en kund ge klienter åtkomst till resurser i ett lagringskonto utan att dela kontonycklar. SAS ger dig detaljerad kontroll över vilken typ av åtkomst du beviljar klienter som har ett SAS-intervall, inklusive giltighetsintervall, beviljade behörigheter, godtagbart IP-adressintervall och det godtagbara protokollet (https/http).

Du kan hämta en SAS-token genom att navigera till **Azure-portalen -> Storage Account -> Signature för delad åtkomst -> Konfigurera behörigheter -> Generera SAS och anslutningssträng.**

> [!IMPORTANT]
> När en SAS-token genereras innehåller den ett frågetecken ('?') i början av token. Om du vill använda token i SQL on-demand måste du ta bort frågetecknet ('?') när du skapar en autentiseringsfråga. Ett exempel:
>
> SAS token: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

### <a name="user-identity"></a>Användarens identitet

**Användaridentitet**, även känd som "genomströmning", är en auktoriseringstyp där identiteten för Azure AD-användare som loggat in på SQL på begäran används för att auktorisera dataåtkomst. Innan du öppnar data måste Azure Storage-administratören bevilja behörigheter till Azure AD-användaren. Som anges i tabellen ovan stöds den inte för SQL-användartypen.

> [!NOTE]
> Om du använder [Azure AD-vidare](#force-azure-ad-pass-through) behöver du inte skapa autentiseringsuppgifter för varje lagringskonto som används med AD-inloggningar.

> [!IMPORTANT]
> Du måste ha en roll för lagringsblobbdata ägare/deltagare/läsare för att använda din identitet för att komma åt data.
> Även om du är ägare till ett lagringskonto måste du fortfarande lägga till dig själv i en av rollerna Lagringsblobbdata.
>
> Om du vill veta mer om åtkomstkontroll i Azure Data Lake Store Gen2 läser du [åtkomstkontrollen i Azure Data Lake Storage Gen2-artikeln.](../../storage/blobs/data-lake-storage-access-control.md)
>

### <a name="managed-identity"></a>Hanterad identitet

**Hanterad identitet** kallas även MSI. Det är en funktion i Azure Active Directory (Azure AD) som tillhandahåller Azure-tjänster för SQL på begäran. Dessutom distribueras en automatiskt hanterad identitet i Azure AD. Den här identiteten kan användas för att auktorisera begäran om dataåtkomst i Azure Storage.

Innan du öppnar data måste Azure Storage-administratören bevilja behörigheter till hanterad identitet för åtkomst till data. Att bevilja behörigheter till hanterad identitet görs på samma sätt som att bevilja behörighet till alla andra Azure AD-användare.

## <a name="create-credentials"></a>Skapa autentiseringsuppgifter

Om du vill fråga en fil som finns i Azure Storage behöver din SQL-slutpunkt på begäran en AUTENTISERINGSENHET på servernivå som innehåller autentiseringsinformationen. En autentiseringstillstånd läggs till genom att köra [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Du måste ange ett AUTENTISERINGSNAMNsargument. Den måste matcha antingen en del av sökvägen eller hela sökvägen till data i Lagring (se nedan).

> [!NOTE]
> Argumentet FÖR KRYPTOGRAFISK LEVERANTÖR stöds inte.

För alla auktoriseringstyper som stöds kan autentiseringsuppgifterna peka på ett konto, en behållare, valfri katalog (icke-rot) eller en enda fil.

AUTENTISERINGSNAMN måste matcha den fullständiga sökvägen till behållaren, mappen eller filen i följande format:`<prefix>://<storage_account_path>/<storage_path>`

| Extern datakälla       | Prefix | Sökväg till lagringskonto                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>.dfs.core.windows.net              |

 "<storage_path>" är en sökväg i ditt lagringsutrymme som pekar på mappen eller filen som du vill läsa.

> [!NOTE]
> Det finns ett särskilt `UserIdentity` AUTENTISERINGSNAMN som [tvingar Azure AD att skicka igenom](#force-azure-ad-pass-through). Läs om vilken effekt den har på sökningen av [autentiseringsuppgifter](#credential-lookup) när du kör frågor.

Om du vill tillåta en användare att skapa eller släppa en autentiseringsuppgifter kan administratören bevilja/neka ändring av behörigheten AUTENTISERINGsuppgifter till en användare:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>Lagringar och auktoriseringstyper som stöds

Du kan använda följande kombinationer av auktoriserings- och Azure Storage-typer:

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *Sas*               | Stöds      | Stöds inte   | Stöds     |
| *Hanterad identitet* | Stöds inte  | Stöds inte    | Stöds inte |
| *Användarens identitet*    | Stöds      | Stöds        | Stöds     |

### <a name="examples"></a>Exempel

Beroende på [auktoriseringstypen](#supported-storage-authorization-types)kan du skapa autentiseringsuppgifter med T-SQL-syntaxen nedan.

**Signature för delad åtkomst och Blob-lagring**

Exchange <*mystorageaccountname*> med ditt faktiska lagringskontonamn och <*>*>> med det faktiska behållarnamnet:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Användaridentitet och Azure Data Lake Storage Gen1**

Exchange <*mystorageaccountname*> med ditt faktiska lagringskontonamn och <*>*>> med det faktiska behållarnamnet:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

**Användaridentitet och Azure Data Lake Storage Gen2**

Exchange <*mystorageaccountname*> med ditt faktiska lagringskontonamn och <*>*>> med det faktiska behållarnamnet:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

## <a name="force-azure-ad-pass-through"></a>Tvinga azure AD-genomströmning

Att tvinga fram en Azure AD-genomströmning är ett standardbeteende som uppnås av ett särskilt AUTENTISERINGSNAMN, `UserIdentity`som skapas automatiskt under Azure Synapse-arbetsytans etablering. Det tvingar användningen av en Azure AD-genomströmning för varje fråga i varje Azure AD-inloggning, som kommer att ske trots att det finns andra autentiseringsuppgifter.

> [!NOTE]
> Azure AD-vidaregång är ett standardbeteende. Du behöver inte skapa autentiseringsuppgifter för varje lagringskonto som används av AD-inloggningar.

Om du [inaktiverar att tvinga Azure AD-genomströmning för varje fråga](#disable-forcing-azure-ad-pass-through)och vill aktivera den igen, kör:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Om du vill aktivera tvinga fram en Azure AD-genomströmning för `UserIdentity` en viss användare kan du bevilja referensbehörighet för autentiseringsuppgifter till den aktuella användaren. I följande exempel kan du tvinga fram en Azure AD-genomströmning för en user_name:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Mer information om hur SQL on-demand hittar autentiseringsuppgifter att använda finns i [autentiseringsuppgifter sökning](#credential-lookup).

## <a name="disable-forcing-azure-ad-pass-through"></a>Inaktivera tvingande Azure AD-genomströmning

Du kan inaktivera [tvingande Azure AD-genomströmning för varje fråga](#force-azure-ad-pass-through). Om du vill `Userdentity` inaktivera den släpper du autentiseringsuppgifterna med:

```sql
DROP CREDENTIAL [UserIdentity];
```

Om du vill återaktivera den igen läser du [avsnittet tvinga Azure AD-vidare.](#force-azure-ad-pass-through)

Om du vill inaktivera tvingande Azure AD-genomströmning för en viss `UserIdentity` användare kan du neka en REFERENSbehörighet för autentiseringsuppgifter för en viss användare. I följande exempel inaktiveras en tvingande Azure AD-genomströmning för en user_name:

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Mer information om hur SQL on-demand hittar autentiseringsuppgifter att använda finns i [autentiseringsuppgifter sökning](#credential-lookup).

## <a name="grant-permissions-to-use-credential"></a>Bevilja behörigheter för att använda autentiseringsuppgifter

Om du vill använda autentiseringsuppgifterna måste en användare ha behörigheten REFERENSER för en viss autentiseringsidenti. Om du vill bevilja en referensbehörighet för en storage_credential för en specific_user kör du:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

För att säkerställa en smidig Azure AD-vidaregenomgångsupplevelse har alla `UserIdentity` användare som standard rätt att använda autentiseringsuppgifterna. Detta uppnås genom en automatisk körning av följande uttryck på Azure Synapse workspace-etablering:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="credential-lookup"></a>Uppslag på autentiseringsuppgifter

Vid auktorisera frågor används sökning efter autentiseringsuppgifter för att komma åt ett lagringskonto och baseras på följande regler:

- Användaren är inloggad som en Azure AD-inloggning

  - Om det finns en UserIdentity-autentiseringsdokumentiv och användaren har referensbehörigheter för den, används Azure AD-vidare genomslagsning, annars [sökuppautentiseringsuppgifter efter sökväg](#lookup-credential-by-path)

- Användaren är inloggad som en SQL-inloggning

  - Använda [sökautentiseringsautentiseringsuppgifter efter bana](#lookup-credential-by-path)

### <a name="lookup-credential-by-path"></a>Uppslagsautentisering efter sökväg

Om det är inaktiverat att tvinga Azure AD-genomströmning baseras autentiseringsuppgifterna på lagringssökvägen (djup först) och förekomsten av en REFERENSER-behörighet för just den autentiseringsplatsen. När det finns flera autentiseringsuppgifter som kan användas för att komma åt samma fil, kommer SQL on-demand att använda den mest specifika.  

Nedan visas en fråga över följande filsökväg: *account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX.ext*

Sökningen av autentiseringsuppgifter slutförs i den här ordningen:

```
account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX
account.dfs.core.windows.net/filesystem/folder1/.../folderN
account.dfs.core.windows.net/filesystem/folder1
account.dfs.core.windows.net/filesystem
account.dfs.core.windows.net
```

Om en användare inte har någon behörighet referenser på autentiseringsuppgifter nummer 5, sql on-demand kommer att kontrollera att användaren har referenser behörighet på en autentiseringsinformation som är en nivå högre tills den hittar autentiseringsuppgifter som användaren har referenser behörighet på. Om ingen sådan behörighet hittas returneras ett felmeddelande.

### <a name="credential-and-path-level"></a>Autentiseringsuppgifter och sökvägsnivå

Beroende på vilken banaform du vill använda gäller följande krav för att köra frågor:

- Om frågan riktar sig till flera filer (mappar, med eller utan jokertecken) måste en användare ha åtkomst till en autentiseringsuppgifter på minst rotkatalognivå (behållarnivå). Den här åtkomstnivån behövs eftersom listningsfilerna är relativa från rotkatalogen (Azure Storage-begränsningar)
- Om frågan riktar sig till en enskild fil måste en användare ha åtkomst till en autentiseringsuppgift på alla nivåer när SQL on-demand kommer åt filen direkt, det vill än att lista mappar.

|                  | *Konto* | *Rotkatalogen* | *Alla andra kataloger* | *Fil*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| *Enkel fil*    | Stöds | Stöds        | Stöds             | Stöds     |
| *Flera filer* | Stöds | Stöds        | Stöds inte         | Stöds inte |

## <a name="next-steps"></a>Nästa steg

Artiklarna nedan hjälper dig att lära dig hur du frågar olika mapptyper, filtyper och skapar och använder vyer:

- [Fråga en enda CSV-fil](query-single-csv-file.md)
- [Fråga mappar och flera CSV-filer](query-folders-multiple-csv-files.md)
- [Fråga specifika filer](query-specific-files.md)
- [Fråga parquet-filer](query-parquet-files.md)
- [Skapa och använda vyer](create-use-views.md)
- [Fråga JSON-filer](query-json-files.md)
- [Frågor Parkett kapslade typer](query-parquet-nested-types.md)
