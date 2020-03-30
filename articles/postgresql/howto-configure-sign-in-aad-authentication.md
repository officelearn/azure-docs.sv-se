---
title: Använda Azure Active Directory - Azure Database för PostgreSQL - Single Server
description: Lär dig mer om hur du konfigurerar Azure Active Directory (AAD) för autentisering med Azure Database for PostgreSQL - Single Server
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a9f12849525daeea69ece6e81077446f062e8889
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384406"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Använda Azure Active Directory för autentisering med PostgreSQL

Den här artikeln kommer att gå igenom stegen hur du konfigurerar Azure Active Directory-åtkomst med Azure Database för PostgreSQL och hur du ansluter med en Azure AD-token.

> [!IMPORTANT]
> Azure AD-autentisering för Azure Database för PostgreSQL är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="setting-the-azure-ad-admin-user"></a>Ställa in Azure AD Admin-användare

Endast en Azure AD-administratörsanvändare kan skapa/aktivera användare för Azure AD-baserad autentisering. Så här skapar och Azure AD-administratörsanvändare följer du följande steg

1. I Azure-portalen väljer du den instans av Azure Database för PostgreSQL som du vill aktivera för Azure AD.
2. Under Inställningar väljer du Active Directory Admin:

![ange azure-annonsadministratör][2]

3. Välj en giltig Azure AD-användare i kundklienten som azure AD-administratör.

> [!IMPORTANT]
> När du anger administratören läggs en ny användare till i Azure Database for PostgreSQL-servern med fullständiga administratörsbehörighet. Azure AD-administratörsanvändaren i Azure Database för PostgreSQL har rollen `azure_ad_admin`.

Endast en Azure AD-administratör kan skapas per PostgreSQL-server och val av en annan kommer att skriva över den befintliga Azure AD-administratören som konfigurerats för servern. Du kan ange en Azure AD-grupp i stället för en enskild användare som har flera administratörer. Observera att du sedan loggar in med gruppnamnet för administrationsändamål.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Skapa Azure AD-användare i Azure Database för PostgreSQL

Om du vill lägga till en Azure AD-användare i din Azure-databas för PostgreSQL-databas utför du följande steg efter anslutningen (se senare avsnitt om hur du ansluter):

1. Kontrollera först att Azure `<user>@yourtenant.onmicrosoft.com` AD-användaren är en giltig användare i Azure AD-klienten.
2. Logga in på din Azure-databas för PostgreSQL-instans som Azure AD Admin-användare.
3. Skapa `<user>@yourtenant.onmicrosoft.com` roll i Azure Database för PostgreSQL.
4. Gör `<user>@yourtenant.onmicrosoft.com` en medlem av roll azure_ad_user. Detta får endast ges till Azure AD-användare.

**Exempel:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Att autentisera en användare via Azure AD ger inte användaren några behörigheter att komma åt objekt i Azure-databasen för PostgreSQL-databasen. Du måste ge användaren de behörigheter som krävs manuellt.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Skapa Azure AD-grupper i Azure Database för PostgreSQL

Om du vill aktivera en Azure AD-grupp för åtkomst till databasen använder du samma mekanism som för användare, men anger i stället gruppnamnet:

**Exempel:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

När du loggar in använder medlemmarna i gruppen sina personliga åtkomsttoken, men signerar med det gruppnamn som anges som användarnamn.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Ansluta till Azure Database för PostgreSQL med Azure AD

Följande högnivådiagram sammanfattar arbetsflödet för att använda Azure AD-autentisering med Azure Database för PostgreSQL:

![autentiseringsflöde][1]

Vi har utformat Azure AD-integreringen så att den fungerar med vanliga PostgreSQL-verktyg som psql, som inte är Azure AD-medvetna och endast stöder att ange användarnamn och lösenord när du ansluter till PostgreSQL. Vi skickar Azure AD-token som lösenord som visas på bilden ovan.

Vi har för närvarande testat följande kunder:

- psql kommandorad (använd PGPASSWORD variabeln för att passera token, se nedan)
- Azure Data Studio (med postgreSQL-tillägget)
- Andra libpq-baserade klienter (t.ex. vanliga programramverk och ORM)Other libpq based clients (t.ex. common application frameworks and ORMs)

> [!NOTE]
> Tänk på att det för närvarande inte stöds att använda Azure AD-token med pgAdmin, eftersom den har en hårdkodad begränsning på 256 tecken för lösenord (som token överskrider).

Det här är de steg som en användare/ett program måste autentisera med Azure AD som beskrivs nedan:

### <a name="step-1-authenticate-with-azure-ad"></a>Steg 1: Autentisera med Azure AD

Kontrollera att du har [Azure CLI installerat](/cli/azure/install-azure-cli).

Anropa Azure CLI-verktyget för att autentisera med Azure AD. Det kräver att du ger ditt Azure AD-användar-ID och lösenordet.

```azurecli-interactive
az login
```

Det här kommandot startar ett webbläsarfönster till sidan Azure AD-autentisering.

> [!NOTE]
> Du kan också använda Azure Cloud Shell för att utföra dessa steg.
> Tänk på att när du hämtar Azure AD-åtkomsttoken i Azure `az login` Cloud Shell måste du uttryckligen anropa och logga in igen (i det separata fönstret med en kod). Efter att logga `get-access-token` in kommandot kommer att fungera som förväntat.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Steg 2: Hämta Azure AD-åtkomsttoken

Anropa Azure CLI-verktyget för att hämta en åtkomsttoken för Azure AD-autentiserade användare från steg 1 för att komma åt Azure Database för PostgreSQL.

Exempel (för offentligt moln):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

Ovanstående resursvärde måste anges exakt som visas. För andra moln kan resursvärdet sökas upp med hjälp av:

```azurecli-interactive
az cloud show
```

För Azure CLI version 2.0.71 och senare kan kommandot anges i följande bekvämare version för alla moln:

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

När autentiseringen har slutförts returnerar Azure AD en åtkomsttoken:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

Token är en Base 64-sträng som kodar all information om den autentiserade användaren och som är riktad till Azure Database for PostgreSQL-tjänsten.

> [!NOTE]
> Åtkomsttokens giltighet är någonstans mellan 5 minuter och 60 minuter. Vi rekommenderar att du hämtar åtkomsttoken precis innan du initierar inloggningen till Azure Database för PostgreSQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>Steg 3: Använd token som lösenord för att logga in med PostgreSQL

När du ansluter måste du använda åtkomsttoken som PostgreSQL-användarlösenordet.

När du `psql` använder kommandoradsklienten måste åtkomsttoken skickas genom `PGPASSWORD` miljövariabeln, `psql` eftersom åtkomsttoken överskrider lösenordslängden som kan acceptera direkt:

Exempel på Windows:

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Linux/macOS Exempel:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Nu kan du initiera en anslutning med Azure Database för PostgreSQL som du normalt skulle:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

Du autentiseras nu till din PostgreSQL-server med Azure AD-autentisering.

## <a name="token-validation"></a>Tokenvalidering

Azure AD-autentisering i Azure Database för PostgreSQL säkerställer att användaren finns i PostgreSQL-servern och kontrollerar tokens giltighet genom att verifiera innehållet i token. Följande tokenverifieringssteg utförs:

- Token signeras av Azure AD och har inte manipulerats
- Token har utfärdats av Azure AD för klienten som är associerad med servern
- Token har inte upphört att gälla
- Token är för Azure Database för PostgreSQL-resurs (och inte en annan Azure-resurs)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Migrera befintliga PostgreSQL-användare till Azure AD-baserad autentisering

Du kan aktivera Azure AD-autentisering för befintliga användare. Det finns två fall att överväga:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Fall 1: Användarnamnet för PostgreSQL matchar Azure AD-användarens huvudnamn

I det osannolika fallet att dina befintliga användare redan matchar `azure_ad_user` Azure AD-användarnamnen kan du ge rollen till dem för att aktivera dem för Azure AD-autentisering:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

De kommer nu att kunna logga in med Azure AD-autentiseringsuppgifter i stället för att använda sitt tidigare konfigurerade PostgreSQL-användarlösenord.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Fall 2: PostgreSQL användarnamn skiljer sig från Azure AD User Principal Name

Om en PostgreSQL-användare antingen inte finns i Azure AD eller har ett annat användarnamn kan du använda Azure AD-grupper för att autentisera som den här PostgreSQL-användaren. Du kan migrera befintlig Azure-databas för PostgreSQL-användare till Azure AD genom att skapa en Azure AD-grupp med ett namn som matchar PostgreSQL-användaren och sedan bevilja roll azure_ad_user till den befintliga PostgreSQL-användaren:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Detta förutsätter att du har skapat en grupp "DBReadUser" i din Azure AD. Användare som tillhör den gruppen kan nu logga in på databasen som den här användaren.

## <a name="next-steps"></a>Nästa steg

* Granska de övergripande begreppen för [Azure Active Directory-autentisering med Azure Database för PostgreSQL - Single Server](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
