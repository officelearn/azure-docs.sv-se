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
ms.openlocfilehash: 0d2683091898e9c84457b3b538776f0e6b0469d4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424036"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Kontrol lera åtkomsten till lagrings kontot för SQL på begäran (för hands version) i Azure Synapse Analytics

En fråga om SQL på begäran (för hands version) läser filer direkt från Azure Storage. Eftersom lagrings kontot är ett objekt som är externt för SQL-resursen på begäran krävs lämpliga autentiseringsuppgifter. En användare behöver de behörigheter som krävs för att kunna använda den nödvändiga autentiseringsuppgiften. Den här artikeln beskriver de typer av autentiseringsuppgifter som du kan använda och hur sökning efter autentiseringsuppgifter registreras för SQL-och Azure AD-användare.

## <a name="supported-storage-authorization-types"></a>Typer av lagringspooler som stöds

En användare som har loggat in på en SQL-resurs på begäran måste ha behörighet att komma åt och fråga filerna i Azure Storage. Tre typer av verifierings typer stöds:

- [Signatur för delad åtkomst](#shared-access-signature)
- [Hanterad identitet](#managed-identity)
- [Användar identitet](#user-identity)

> [!NOTE]
> [Azure AD-vidarekoppling](#force-azure-ad-pass-through) är standard beteendet när du skapar en arbets yta. Om du använder det behöver du inte skapa autentiseringsuppgifter för varje lagrings konto som nås med AD-inloggningar. Du kan [inaktivera det här beteendet](#disable-forcing-azure-ad-pass-through).

I tabellen nedan hittar du de olika typer av autentisering som antingen stöds eller som kommer att stödjas snart.

| Auktoriseringstyp                    | *SQL-användare*    | *Azure AD-användare*     |
| ------------------------------------- | ------------- | -----------    |
| [SÄKERHETS](#shared-access-signature)       | Stöds     | Stöds      |
| [Hanterad identitet](#managed-identity) | Stöds inte | Stöds inte  |
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

> [!NOTE]
> Om du använder [Azure AD-vidarekoppling](#force-azure-ad-pass-through) behöver du inte skapa autentiseringsuppgifter för varje lagrings konto som nås med AD-inloggningar.

> [!IMPORTANT]
> Du måste ha rollen som ägare/deltagare/läsare för Storage BLOB-rollen för att kunna använda din identitet för att komma åt data.
> Även om du är ägare till ett lagrings konto behöver du fortfarande lägga till dig själv i en av lagrings BLOB-datarollerna.
>
> Läs mer om åtkomst kontroll i Azure Data Lake Store Gen2 genom att granska [åtkomst kontrollen i Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md) artikeln.
>

### <a name="managed-identity"></a>Hanterad identitet

**Hanterad identitet** kallas även MSI. Det är en funktion i Azure Active Directory (Azure AD) som tillhandahåller Azure-tjänster för SQL på begäran. Dessutom distribueras en automatiskt hanterad identitet i Azure AD. Den här identiteten kan användas för att auktorisera begäran om data åtkomst i Azure Storage.

Innan du får åtkomst till data måste Azure Storages administratören bevilja behörighet till hanterad identitet för att komma åt data. Att bevilja behörigheter till hanterad identitet görs på samma sätt som när du beviljar behörighet till andra Azure AD-användare.

## <a name="create-credentials"></a>Skapa autentiseringsuppgifter

Om du vill fråga en fil som finns i Azure Storage måste din SQL-slutpunkt på begäran ha AUTENTISERINGSUPPGIFTER på server nivå som innehåller autentiseringsinformationen. Du lägger till en autentiseringsuppgift genom att köra [skapa autentiseringsuppgifter](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Du måste ange ett namn argument för AUTENTISERINGSUPPGIFTER. Den måste matcha antingen en del av sökvägen eller hela sökvägen till data i lagringen (se nedan).

> [!NOTE]
> Argumentet FOR CRYPTOGRAPHIC PROVIDER stöds inte.

För alla typer som stöds kan autentiseringsuppgifter peka på ett konto, en behållare, alla kataloger (icke-rot) eller en enskild fil.

NAMNET på AUTENTISERINGSUPPGIFTEN måste matcha den fullständiga sökvägen till behållaren, mappen eller filen i följande format:`<prefix>://<storage_account_path>/<storage_path>`

| Extern data Källa       | Prefix | Sökväg till lagrings konto                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>. blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>. dfs.core.windows.net              |

 <storage_path> är en sökväg i lagrings platsen som pekar på den mapp eller fil som du vill läsa.

> [!NOTE]
> Det finns ett särskilt namn `UserIdentity` på autentiseringsuppgifter som [TVINGAR fram Azure AD-vidarekoppling](#force-azure-ad-pass-through). Läs om den påverkan den har vid [sökning efter autentiseringsuppgifter](#credential-lookup) vid körning av frågor.

Om du vill tillåta att en användare skapar eller släpper en autentiseringsuppgift, kan administratören bevilja/neka behörighet att ändra AUTENTISERINGSUPPGIFTER till en användare:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>Lagrings enheter och verifierings typer som stöds

Du kan använda följande kombinationer av auktoriserings-och Azure Storage typer:

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SÄKERHETS*               | Stöds      | Stöds inte   | Stöds     |
| *Hanterad identitet* | Stöds inte  | Stöds inte    | Stöds inte |
| *Användar identitet*    | Stöds      | Stöds        | Stöds     |

### <a name="examples"></a>Exempel

Beroende på typ av [auktorisering](#supported-storage-authorization-types)kan du skapa autentiseringsuppgifter med hjälp av T-SQL-syntaxen nedan.

**Signatur för delad åtkomst och Blob Storage**

Exchange <*mystorageaccountname*> med ditt faktiska lagrings konto namn och <*mystorageaccountcontainername*> med det faktiska behållar namnet:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Användar identitet och Azure Data Lake Storage Gen1**

Exchange <*mystorageaccountname*> med ditt faktiska lagrings konto namn och <*mystorageaccountcontainername*> med det faktiska behållar namnet:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

**Användar identitet och Azure Data Lake Storage Gen2**

Exchange <*mystorageaccountname*> med ditt faktiska lagrings konto namn och <*mystorageaccountcontainername*> med det faktiska behållar namnet:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

## <a name="force-azure-ad-pass-through"></a>Tvinga Azure AD-vidarekoppling

Att tvinga fram en Azure AD-vidarekoppling är ett standard beteende som uppnås av ett särskilt `UserIdentity`inloggnings namn, som skapas automatiskt under etablering av Azure Synapse-arbetsytan. Den tvingar användningen av ett Azure AD-vidarekoppling för varje fråga om varje Azure AD-inloggning, vilket sker trots att andra autentiseringsuppgifter finns.

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

Mer information om hur SQL på begäran hittar autentiseringsuppgifter som ska användas finns i [Sök efter autentiseringsuppgifter](#credential-lookup).

## <a name="disable-forcing-azure-ad-pass-through"></a>Inaktivera tvingande Azure AD-vidarekoppling

Du kan inaktivera [Tvingad Azure AD-vidarekoppling för varje fråga](#force-azure-ad-pass-through). Du inaktiverar den genom att släppa `Userdentity` autentiseringsuppgiften med:

```sql
DROP CREDENTIAL [UserIdentity];
```

Om du vill återaktivera det igen läser du avsnittet om [Azure AD-vidarekoppling](#force-azure-ad-pass-through) .

Om du vill inaktivera tvingande Azure AD-vidarekoppling för en viss användare kan du neka en referens behörighet för `UserIdentity` autentiseringsuppgifter för en viss användare. I följande exempel inaktive ras en tvingande Azure AD-vidarekoppling för en user_name:

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Mer information om hur SQL på begäran hittar autentiseringsuppgifter som ska användas finns i [Sök efter autentiseringsuppgifter](#credential-lookup).

## <a name="grant-permissions-to-use-credential"></a>Bevilja behörighet att använda autentiseringsuppgifter

Om du vill använda autentiseringsuppgifterna måste en användare ha behörighet för referenser till en angiven autentiseringsuppgift. Om du vill ge en referens behörighet för en storage_credential för en specific_user kör du:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

För att säkerställa en smidig Azure AD-direktautentisering har alla användare som standard rätt att använda `UserIdentity` autentiseringsuppgiften. Detta uppnås genom en automatisk körning av följande uttryck vid Azure Synapse-arbetsytans etablering:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="credential-lookup"></a>Sökning efter autentiseringsuppgifter

Vid auktorisering av frågor används autentisering av autentiseringsuppgifter för att få åtkomst till ett lagrings konto och baseras på följande regler:

- Användaren är inloggad som en Azure AD-inloggning

  - Om det finns en UserIdentity-autentiseringsuppgift och användaren har referens behörighet för den, kommer Azure AD-vidarekoppling att användas, annars [sökning efter autentiseringsuppgifter efter sökväg](#lookup-credential-by-path)

- Användaren är inloggad som SQL-inloggning

  - Använd Sök [autentiseringsuppgifter efter sökväg](#lookup-credential-by-path)

### <a name="lookup-credential-by-path"></a>Sök efter autentiseringsuppgifter efter sökväg

Om tvingande Azure AD-vidarekoppling är inaktiverat baseras autentisering av autentiseringsuppgifter baserat på lagrings Sök vägen (djup) och förekomsten av en referens behörighet för den aktuella autentiseringsuppgiften. Om det finns flera autentiseringsuppgifter som kan användas för åtkomst till samma fil, kommer SQL på begäran att använda den mest aktuella.  

Nedan visas ett exempel på en fråga över följande fil Sök väg: *Account.DFS.Core.Windows.net/filesystem/folder1/.../folderN/fileX.ext*

Sökning efter autentiseringsuppgifter slutförs i den här ordningen:

```
account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX
account.dfs.core.windows.net/filesystem/folder1/.../folderN
account.dfs.core.windows.net/filesystem/folder1
account.dfs.core.windows.net/filesystem
account.dfs.core.windows.net
```

Om en användare inte har behörighet att använda Credential Number 5, kontrollerar SQL på begäran att användaren har behörighet till en autentiseringsuppgift som är en högre nivå än den som har behörighet som användaren har behörighet för. Om det inte går att hitta någon sådan behörighet returneras ett fel meddelande.

### <a name="credential-and-path-level"></a>Autentiseringsuppgift-och Sök vägs nivå

Beroende på vilken Sök vägs form du vill använda är följande krav på plats för att köra frågor:

- Om frågan är riktad mot flera filer (mappar, med eller utan jokertecken) måste användaren ha åtkomst till en autentiseringsuppgift på minst rot Katalog nivå (container nivå). Den här åtkomst nivån krävs eftersom en lista över filer är relativa från rot katalogen (Azure Storage begränsningar)
- Om frågan är riktad mot en enskild fil måste användaren ha åtkomst till en autentiseringsuppgift på valfri nivå när SQL på begäran kommer åt filen direkt, det vill säga utan att visa mappar.

|                  | *Konto* | *Rot Katalog* | *Alla andra kataloger* | *Fil*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| *Enskild fil*    | Stöds | Stöds        | Stöds             | Stöds     |
| *Flera filer* | Stöds | Stöds        | Stöds inte         | Stöds inte |

## <a name="next-steps"></a>Nästa steg

Artiklarna i listan nedan hjälper dig att lära dig hur frågar olika typer av mappar, filtyper och hur du skapar och använder vyer:

- [Fråga en enkel CSV-fil](query-single-csv-file.md)
- [Fråga mappar och flera CSV-filer](query-folders-multiple-csv-files.md)
- [Fråga efter vissa filer](query-specific-files.md)
- [Efterfråga Parquet-filer](query-parquet-files.md)
- [Skapa och använda vyer](create-use-views.md)
- [Efterfråga JSON-filer](query-json-files.md)
- [Efterfråga kapslade Parquet-typer](query-parquet-nested-types.md)
