---
title: Använda Azure Active Directory - Azure Database för MySQL
description: Lär dig mer om hur du konfigurerar Azure Active Directory (Azure AD) för autentisering med Azure Database för MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 0403edadd491609c2c88d5b5ac6980d97163f8d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299013"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Använda Azure Active Directory för autentisering med MySQL

Den här artikeln kommer att gå igenom stegen hur du konfigurerar Azure Active Directory-åtkomst med Azure Database för MySQL och hur du ansluter med en Azure AD-token.

> [!IMPORTANT]
> Azure AD-autentisering för Azure Database för MySQL är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="setting-the-azure-ad-admin-user"></a>Ställa in Azure AD Admin-användare

Endast en Azure AD-administratörsanvändare kan skapa/aktivera användare för Azure AD-baserad autentisering. Så här skapar och Azure AD-administratörsanvändare följer du följande steg

1. I Azure-portalen väljer du den instans av Azure Database för MySQL som du vill aktivera för Azure AD.
2. Under Inställningar väljer du Active Directory Admin:

![ange azure-annonsadministratör][2]

3. Välj en giltig Azure AD-användare i kundklienten som azure AD-administratör.

> [!IMPORTANT]
> När du anger administratören läggs en ny användare till i Azure Database for MySQL-servern med fullständiga administratörsbehörighet.

Endast en Azure AD-administratör kan skapas per MySQL-server och val av en annan kommer att skriva över den befintliga Azure AD-administratören som konfigurerats för servern.

I en framtida version kommer vi att stödja att ange en Azure AD-grupp i stället för en enskild användare att ha flera administratörer, men detta stöds för närvarande inte ännu.

När du har konfigurerat administratören kan du nu logga in:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Ansluta till Azure Database för MySQL med Azure AD

Följande diagram på hög nivå sammanfattar arbetsflödet för att använda Azure AD-autentisering med Azure Database for MySQL:

![autentiseringsflöde][1]

Vi har utformat Azure AD-integreringen så att den fungerar med vanliga MySQL-verktyg som mysql CLI, som inte är Azure AD-medvetna och bara stöder att ange användarnamn och lösenord när du ansluter till MySQL. Vi skickar Azure AD-token som lösenord som visas på bilden ovan.

Vi har för närvarande testat följande kunder:

- MySQLWorkbench 
- Mysql CLI

Vi har också testat de vanligaste programdrivrutinerna, du kan se detaljer i slutet av den här sidan.

Det här är de steg som en användare/ett program måste autentisera med Azure AD som beskrivs nedan:

### <a name="step-1-authenticate-with-azure-ad"></a>Steg 1: Autentisera med Azure AD

Kontrollera att du har [Azure CLI installerat](/cli/azure/install-azure-cli).

Anropa Azure CLI-verktyget för att autentisera med Azure AD. Det kräver att du ger ditt Azure AD-användar-ID och lösenordet.

```
az login
```

Det här kommandot startar ett webbläsarfönster till sidan Azure AD-autentisering.

> [!NOTE]
> Du kan också använda Azure Cloud Shell för att utföra dessa steg.
> Tänk på att när du hämtar Azure AD-åtkomsttoken i Azure `az login` Cloud Shell måste du uttryckligen anropa och logga in igen (i det separata fönstret med en kod). Efter att logga `get-access-token` in kommandot kommer att fungera som förväntat.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Steg 2: Hämta Azure AD-åtkomsttoken

Anropa Azure CLI-verktyget för att hämta en åtkomsttoken för Azure AD-autentiserade användare från steg 1 för att komma åt Azure Database för MySQL.

Exempel (för offentligt moln):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

Ovanstående resursvärde måste anges exakt som visas. För andra moln kan resursvärdet sökas upp med hjälp av:

```shell
az cloud show
```

För Azure CLI version 2.0.71 och senare kan kommandot anges i följande bekvämare version för alla moln:

```shell
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

Token är en Base 64-sträng som kodar all information om den autentiserade användaren och som är inriktad på Azure Database for MySQL-tjänsten.

> [!NOTE]
> Åtkomsttokens giltighet är någonstans mellan 5 minuter och 60 minuter. Vi rekommenderar att du hämtar åtkomsttoken precis innan du initierar inloggningen till Azure Database for MySQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Steg 3: Använd token som lösenord för att logga in med MySQL

När du ansluter måste du använda åtkomsttoken som MySQL-användarlösenord. När du använder GUI-klienter som MySQLWorkbench kan du använda metoden ovan för att hämta token. 

När du använder CLI kan du använda den här korthanden för att ansluta: 

**Exempel (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

Observera inställningen "enable-cleartext-plugin" – du måste använda en liknande konfiguration med andra klienter för att se till att token skickas till servern utan att hasheras.

Du autentiseras nu till mySQL-servern med Azure AD-autentisering.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Skapa Azure AD-användare i Azure Database för MySQL

Om du vill lägga till en Azure AD-användare i din Azure-databas för MySQL-databas utför du följande steg efter anslutning (se senare avsnitt om hur du ansluter):

1. Kontrollera först att Azure `<user>@yourtenant.onmicrosoft.com` AD-användaren är en giltig användare i Azure AD-klienten.
2. Logga in på din Azure-databas för MySQL-instans som Azure AD-administratörsanvändare.
3. Skapa `<user>@yourtenant.onmicrosoft.com` användare i Azure Database för MySQL.

**Exempel:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

För användarnamn som överstiger 32 tecken rekommenderar vi att du använder ett alias i stället för att användas vid anslutning: 

Exempel:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> Att autentisera en användare via Azure AD ger inte användaren några behörigheter att komma åt objekt i Azure-databasen för MySQL-databasen. Du måste ge användaren de behörigheter som krävs manuellt.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Skapa Azure AD-grupper i Azure Database för MySQL

Om du vill aktivera en Azure AD-grupp för åtkomst till databasen använder du samma mekanism som för användare, men anger i stället gruppnamnet:

**Exempel:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

När du loggar in använder medlemmarna i gruppen sina personliga åtkomsttoken, men signerar med det gruppnamn som anges som användarnamn.

## <a name="token-validation"></a>Tokenvalidering

Azure AD-autentisering i Azure Database for MySQL säkerställer att användaren finns på MySQL-servern och kontrollerar tokens giltighet genom att verifiera innehållet i token. Följande tokenverifieringssteg utförs:

-   Token signeras av Azure AD och har inte manipulerats
-   Token har utfärdats av Azure AD för klienten som är associerad med servern
-   Token har inte upphört att gälla
-   Token är för Azure Database for MySQL-resurs (och inte en annan Azure-resurs)

## <a name="compatibility-with-application-drivers"></a>Kompatibilitet med programdrivrutiner

De flesta drivrutiner stöds, men se till att använda inställningarna för att skicka lösenordet i klartext, så att token skickas utan ändringar.

* C/C++
  * libmysqlclient: Stöds
  * mysql-connector-c++: Stöds
* Java
  * Anslutning/J (mysql-connector-java): Stöds, `useSSL` måste använda inställningen
* Python
  * Anslutning/Python: Stöds
* Ruby
  * mysql2: Stöds
* .NET
  * mysql-connector-net: Stöds, måste lägga till plugin för mysql_clear_password
  * mysql-net/MySqlConnector: Stöds
* Node.js
  * mysqljs: Stöds inte (skickar inte token i klartext utan patch)
  * nod-mysql2: Stöds
* Perl
  * DBD::mysql: Stöds
  * Net::MySQL: Stöds inte
* Go
  * go-sql-driver: Stöds, `?tls=true&allowCleartextPasswords=true` lägg till i anslutningssträngen

## <a name="next-steps"></a>Nästa steg

* Granska de övergripande begreppen för [Azure Active Directory-autentisering med Azure Database för MySQL](concepts-azure-ad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
