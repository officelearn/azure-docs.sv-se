---
title: Översikt över säkerheten i Azure Data Lake Storage Gen1 | Microsoft-dokument
description: Förstå hur Azure Data Lake Storage Gen1 är ett säkrare stordatalager
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ebd5b2ac-c5cc-46d4-9cfd-1a1ee70024c2
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 4e640aa1cb02174c935c0f7c1d61ab2fca5ea046
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974583"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Säkerhet i Azure Data Lake Storage Gen1
Många företag utnyttjar stordataanalys för affärsinsikter för att hjälpa dem att fatta smarta beslut. En organisation kan ha en komplex och reglerad miljö, med ett ökande antal olika användare. Det är viktigt för ett företag att se till att viktiga affärsdata lagras säkrare, med rätt åtkomstnivå för enskilda användare. Azure Data Lake Storage Gen1 är utformad för att uppfylla dessa säkerhetskrav. I den här artikeln kan du läsa mer om säkerhetsfunktionerna i Data Lake Storage Gen1, inklusive:

* Autentisering
* Auktorisering
* Isolering av nätverk
* Dataskydd
* Granskning

## <a name="authentication-and-identity-management"></a>Autentisering och identitetshantering
Autentisering är den process genom vilken en användares identitet verifieras när användaren interagerar med Data Lake Storage Gen1 eller med någon tjänst som ansluter till Data Lake Storage Gen1. För identitetshantering och autentisering använder Data Lake Storage Gen1 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), en omfattande molnlösning för identitets- och åtkomsthantering som förenklar hanteringen av användare och grupper.

Varje Azure-prenumeration kan associeras med en instans av Azure Active Directory. Endast användare och tjänstidentiteter som definieras i din Azure Active Directory-tjänst kan komma åt ditt Data Lake Storage Gen1-konto, med hjälp av Azure-portalen, kommandoradsverktyg eller via klientprogram som organisationen bygger med hjälp av Datasjö Lagring Gen1 SDK. De viktigaste fördelarna med att använda Azure Active Directory som en centraliserad åtkomstkontrollmekanism är:

* Förenklad livscykelhantering för identitet. Identiteten på en användare eller en tjänst (en tjänsthuvudidentitet) kan snabbt skapas och snabbt återkallas genom att helt enkelt ta bort eller inaktivera kontot i katalogen.
* Multifaktorautentisering. [Multifaktorautentisering](../active-directory/authentication/multi-factor-authentication.md) ger ytterligare ett säkerhetslager för användarloggningar och transaktioner.
* Autentisering från valfri klient via ett öppet standardprotokoll, till exempel OAuth eller OpenID.
* Federation med företagskatalogtjänster och molnidentitetsleverantörer.

## <a name="authorization-and-access-control"></a>Auktoriserings- och åtkomstkontroll
När Azure Active Directory har autentiserat en användare så att användaren kan komma åt Data Lake Storage Gen1, kontrollerar auktorisering åtkomstbehörigheter för Data Lake Storage Gen1. Data Lake Storage Gen1 separerar auktorisering för kontorelaterade och datarelaterade aktiviteter på följande sätt:

* [Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) (RBAC) som tillhandahålls av Azure för kontohantering
* POSIX ACL för åtkomst till data i butiken

### <a name="rbac-for-account-management"></a>RBAC för kontohantering
Fyra grundläggande roller definieras som standard för DataSjölagring gen1. Rollerna tillåter olika åtgärder på ett Data Lake Storage Gen1-konto via Azure-portalen, PowerShell-cmdlets och REST-API:er. Rollerna Ägare och Deltagare kan utföra en mängd olika administrationsfunktioner på kontot. Du kan tilldela rollen Läsare till användare som bara visar kontohanteringsdata.

![RBAC-roller](./media/data-lake-store-security-overview/rbac-roles.png "RBAC-roller")

Observera att även om roller har tilldelats för kontohantering påverkar vissa roller åtkomsten till data. Du måste använda åtkomstkontrollistor för att styra åtkomsten till åtgärder som en användare kan utföra i filsystemet. I följande tabell visas en sammanfattning av hanteringsrättigheter och dataåtkomsträttigheter för standardrollerna.

| Roller | Förvaltningsrättigheter | Rättigheter för dataåtkomst | Förklaring |
| --- | --- | --- | --- |
| Ingen roll tilldelad |Inget |Styrs av ACL |Användaren kan inte använda Azure-portalen eller Azure PowerShell-cmdletar för att bläddra i Data Lake Storage Gen1. Användaren kan endast använda kommandoradsverktyg. |
| Ägare |Alla |Alla |Ägarrollen är en superanvändare. Den här rollen kan hantera allt och har full åtkomst till data. |
| Läsare |Skrivskyddad |Styrs av ACL |Rollen Läsare kan visa allt om kontohantering, till exempel vilken användare som tilldelas vilken roll. Rollen Läsare kan inte göra några ändringar. |
| Deltagare |Alla utom lägga till och ta bort roller |Styrs av ACL |Rollen Deltagare kan hantera vissa aspekter av ett konto, till exempel distributioner och skapa och hantera aviseringar. Rollen Deltagare kan inte lägga till eller ta bort roller. |
| Administratör för användaråtkomst |Lägga till och ta bort roller |Styrs av ACL |Rollen Administratör för användaråtkomst kan hantera användaråtkomst till konton. |

Instruktioner finns i [Tilldela användare eller säkerhetsgrupper till DataSjölagringsgend1-konton](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Använda åtkomstkontrollistor för åtgärder i filsystem
Data Lake Storage Gen1 är ett hierarkiskt filsystem som Hadoop Distributed File System (HDFS), och stöder [POSIX-ACL.](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists) Den styr läs-, skriv-,skriv- och körningsbehörigheter (x) till resurser för rollen Ägare, för gruppen Ägare och för andra användare och grupper. I Data Lake Storage Gen1 kan ACL:er aktiveras i rotmappen, på undermappar och på enskilda filer. Mer information om hur ACL fungerar i samband med Gen1 för lagring av datasjö finns [i Åtkomstkontroll i Gen1 för lagring av datasjö.](data-lake-store-access-control.md)

Vi rekommenderar att du definierar ACL:er för flera användare med hjälp av [säkerhetsgrupper](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Lägg till användare i en säkerhetsgrupp och tilldela sedan ACL:erna för en fil eller mapp till den säkerhetsgruppen. Detta är användbart när du vill ange tilldelade behörigheter, eftersom du är begränsad till högst 28 poster för tilldelade behörigheter. Mer information om hur du skyddar data som lagras i Data Lake Storage Gen1 med hjälp av Azure Active Directory-säkerhetsgrupper finns i [Tilldela användare eller säkerhetsgrupp som ACL:er till filsystemet Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

![Behörigheter för lista åtkomst](./media/data-lake-store-security-overview/adl.acl.2.png "Behörigheter för lista åtkomst")

## <a name="network-isolation"></a>Isolering av nätverk
Använd DataSjölagring Gen1 för att kontrollera åtkomsten till ditt datalager på nätverksnivå. Du kan upprätta brandväggar och definiera ett IP-adressintervall för dina betrodda klienter. Med ett IP-adressintervall kan endast klienter som har en IP-adress inom det definierade intervallet ansluta till Data Lake Storage Gen1.

![Brandväggsinställningar och IP-åtkomst](./media/data-lake-store-security-overview/firewall-ip-access.png "Brandväggsinställningar och IP-adress")

## <a name="data-protection"></a>Dataskydd
Data Lake Storage Gen1 skyddar dina data under hela livscykeln. För data under överföring använder Data Lake Storage Gen1 TLS 1.2-protokollet (Transport Layer Security) för att skydda data över nätverket.

![Kryptering i Gen1 för lagring av datasjö](./media/data-lake-store-security-overview/adls-encryption.png "Kryptering i Gen1 för lagring av datasjö")

Data Lake Storage Gen1 tillhandahåller också kryptering för data som lagras i kontot. Du kan välja att ha krypterade data eller välja ingen kryptering. Om du väljer kryptering krypteras data som lagras i Data Lake Storage Gen1 innan du lagrar på beständiga medier. I så fall krypterar Data Lake Storage Gen1 automatiskt data innan data kvarstår och dekrypterar data före hämtning, så det är helt öppet för klienten att komma åt data. Det krävs ingen kodändring på klientsidan för att kryptera/dekryptera data.

För nyckelhantering innehåller Data Lake Storage Gen1 två lägen för hantering av dina huvudkrypteringsnycklar (MEK), som krävs för att dekryptera data som lagras i Data Lake Storage Gen1. Du kan antingen låta Data Lake Storage Gen1 hantera MEK:erna åt dig eller välja att behålla äganderätten till MEK:erna med ditt Azure Key Vault-konto. Du anger läget för nyckelhantering när du skapar ett Data Lake Storage Gen1-konto. Mer information om hur du tillhandahåller krypteringsrelaterad konfiguration finns i [Komma igång med Azure Data Lake Storage Gen1 med Azure Portal](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Aktivitets- och diagnostikloggar
Du kan använda aktivitets- eller diagnostikloggar, beroende på om du letar efter loggar för kontohanteringsrelaterade aktiviteter eller datarelaterade aktiviteter.

* Kontohanteringsrelaterade aktiviteter använder Azure Resource Manager API:er och visas i Azure-portalen via aktivitetsloggar.
* Datarelaterade aktiviteter använder WebHDFS REST API:er och visas i Azure-portalen via diagnostikloggar.

### <a name="activity-log"></a>Aktivitetslogg
För att följa reglerna kan en organisation kräva lämpliga granskningshistorik för kontohanteringsaktiviteter om den behöver gräva i specifika incidenter. Data Lake Storage Gen1 har inbyggd övervakning och loggar alla kontohanteringsaktiviteter.

För granskningshistorik för kontohantering lyser och väljer du de kolumner som du vill logga. Du kan också exportera aktivitetsloggar till Azure Storage.

![Aktivitetslogg](./media/data-lake-store-security-overview/activity-logs.png "Aktivitetslogg")

Mer information om hur du arbetar med aktivitetsloggar finns i [Visa aktivitetsloggar för granskningsåtgärder på resurser](../azure-resource-manager/management/view-activity-logs.md).

### <a name="diagnostics-logs"></a>Diagnostikloggar
Du kan aktivera granskning och diagnostikloggning av dataåtkomst i Azure-portalen och skicka loggarna till ett Azure Blob-lagringskonto, en händelsehubb eller Azure Monitor-loggar.

![Diagnostikloggar](./media/data-lake-store-security-overview/diagnostic-logs.png "Diagnostikloggar")

Mer information om hur du arbetar med diagnostikloggar med Data Lake Storage Gen1 finns i [Komma åt diagnostikloggar för Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Sammanfattning
Företagskunder kräver en molnplattform för dataanalys som är säker och enkel att använda. Data Lake Storage Gen1 är utformad för att hjälpa till att lösa dessa krav genom identitetshantering och autentisering via Azure Active Directory-integrering, ACL-baserad auktorisering, nätverksisolering, datakryptering under överföring och i vila samt granskning.

Om du vill se nya funktioner i Data Lake Storage Gen1, skicka oss din feedback i [Data Lake Storage Gen1 UserVoice forum](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Se även
* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Komma igång med Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
