---
title: Översikt över säkerheten i Azure Data Lake Storage Gen1 | Microsoft Docs
description: Förstå hur Azure Data Lake Storage Gen1 är säkrare lagringsplatser för stordata
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ebd5b2ac-c5cc-46d4-9cfd-1a1ee70024c2
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: a310851819f70d138a4980b1ab61891fb0b2c311
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959620"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Säkerhet i Azure Data Lake Storage Gen1
Många företag utnyttjar stordata för affärsinsikter som hjälper dem att fatta smarta beslut. En organisation kan ha en komplex och reglerade miljö med ett ökande antal olika användare. Det är viktigt för företaget att se till att affärskritiska data lagras mer säkert med rätt nivå av åtkomst till enskilda användare. Azure Data Lake Storage Gen1 är utformad för att uppfylla dessa krav på säkerhet. I den här artikeln lär du dig om de säkerhetsfunktionerna i Data Lake Storage Gen1, inklusive:

* Authentication
* Auktorisering
* Isolering av nätverk
* Dataskydd
* Granskning

## <a name="authentication-and-identity-management"></a>Identitetshantering och autentisering
Autentisering är den process som en användares identitet har verifierats när användaren interagerar med Data Lake Storage Gen1 eller med valfri tjänst som ansluter till Data Lake Storage Gen1. För Identitetshantering och autentisering, använder Data Lake Storage Gen1 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), en omfattande identitets- och åtkomsthanteringsmolnlösning som förenklar hanteringen av användare och grupper.

Varje Azure-prenumeration kan associeras med en instans av Azure Active Directory. Enbart användare och tjänstidentiteter som definieras i Azure Active Directory-tjänsten som har åtkomst till ditt Data Lake Storage Gen1-konto med hjälp av Azure portal, kommandoradsverktyg, eller via klientprogram som din organisation skapar med hjälp av Data Lake Storage Gen1 SDK. Viktiga fördelar med att använda Azure Active Directory som en centraliserad åtkomstkontroll är:

* Förenklad identitetslivcykelhantering. Identiteten för en användare eller en tjänst (en huvudnamn tjänstidentitet) kan skapas snabbt och snabbt återkallas av helt enkelt ta bort eller inaktivera kontot i katalogen.
* Multifaktorautentisering. [Multifaktorautentisering](../active-directory/authentication/multi-factor-authentication.md) ger ett extra lager av säkerhet för användarinloggningar och transaktioner.
* Autentisering från klienter via ett öppet standardprotokoll, till exempel OAuth eller OpenID.
* Federation med enterprise-katalogtjänster och identitet molnleverantörer.

## <a name="authorization-and-access-control"></a>Auktorisering och åtkomstkontroll
När en användare autentiseras i Azure Active Directory så att användaren kan komma åt Data Lake Storage Gen1, åtkomstbehörighet auktoriseringskontroller för Data Lake Storage Gen1. Data Lake Storage Gen1 skiljer auktorisering för konto-relaterade och data som är relaterade aktiviteter på följande sätt:

* [Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) (RBAC) som tillhandahålls av Azure för kontohantering
* POSIX ACL för åtkomst till data i arkivet

### <a name="rbac-for-account-management"></a>RBAC för kontohantering
Fyra grundläggande roller definieras för Data Lake Storage Gen1 som standard. Rollerna tillåter olika åtgärder på ett Data Lake Storage Gen1 konto via Azure portal, PowerShell-cmdletar och REST API: er. Rollerna ägare och deltagare kan utföra en mängd funktioner för administration på kontot. Du kan tilldela rollen Läsare för användare som bara visar hanteringsdata för kontot.

![RBAC-roller](./media/data-lake-store-security-overview/rbac-roles.png "RBAC-roller")

Observera att även om rollerna har tilldelats för hantering av vissa roller påverkar åtkomst till data. Du måste använda ACL: er för att styra åtkomst till åtgärder som en användare kan utföra i filsystemet. I följande tabell visas en sammanfattning av rights management och data åtkomstbehörigheter för standardroller som.

| Roller | Rights Management | Åtkomstbehörighet för data | Förklaring |
| --- | --- | --- | --- |
| Ingen roll som tilldelats |Ingen |Styrs av ACL |Användaren kan inte använda Azure portal eller Azure PowerShell-cmdletar för att bläddra i Data Lake Storage Gen1. Användaren kan använda endast kommandoradsverktyg. |
| Ägare |Alla |Alla |Rollen ägare är en superanvändare. Den här rollen kan hantera allt och har fullständig åtkomst till data. |
| Läsare |Skrivskyddad |Styrs av ACL |Rollen Läsare kan visa allt om kontohantering, till exempel vilka användare som är tilldelad till vilken roll. Med läsarrollen kan inte göra några ändringar. |
| Deltagare |Alla utom Lägg till och ta bort roller |Styrs av ACL |Rollen som deltagare kan hantera vissa aspekter av ett konto, till exempel distributioner eller skapa och hantera aviseringar. Med deltagarrollen kan inte lägga till eller ta bort roller. |
| Administratör för användaråtkomst |Lägga till och ta bort roller |Styrs av ACL |Rollen Administratör för användaråtkomst kan hantera användarnas åtkomst till konton. |

Anvisningar finns i [tilldela användare eller säkerhetsgrupper till Data Lake Storage Gen1 konton](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Med hjälp av ACL: er för åtgärder för filsystem
Data Lake Storage Gen1 finns faktiskt hierarkiskt filsystem som Hadoop Distributed File System (HDFS) och stöder [POSIX-ACL: er](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Den kontrollerar läsa (r), skriva (w) och köra (x) behörighet till resurser för rollen ägare, för gruppen ägare och andra användare och grupper. I Data Lake Storage Gen1 aktiveras ACL: er i rotmappen, undermappar och enskilda filer. Läs mer om hur åtkomstkontrollposter fungerar i kontexten för Data Lake Storage Gen1 [åtkomstkontroll i Data Lake Storage Gen1](data-lake-store-access-control.md).

Vi rekommenderar att du definierar ACL: er för flera användare med hjälp av [säkerhetsgrupper](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Lägga till användare i en säkerhetsgrupp och sedan tilldela ACL: er för en fil eller mapp till säkerhetsgruppen. Detta är användbart när du vill ge tilldelade behörigheter eftersom du är begränsad till högst 28 poster för tilldelade behörigheter. Mer information om hur du bättre skydda data som lagras i Data Lake Storage Gen1 med hjälp av Azure Active Directory-säkerhetsgrupper finns i [tilldela användare eller säkerhetsgrupp som ACL: er till filsystemet Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

![Visa åtkomstbehörigheter](./media/data-lake-store-security-overview/adl.acl.2.png "lista behörigheter")

## <a name="network-isolation"></a>Isolering av nätverk
Använd Data Lake Storage Gen1 för att styra åtkomsten till ditt datalager på nätverksnivå. Du kan fastställa brandväggar och definiera ett IP-adressintervall för dina betrodda klienter. Endast klienter som har en IP-adress inom det angivna intervallet kan ansluta till Data Lake Storage Gen1 med ett IP-adressintervall.

![Brandväggsinställningar och IP-åtkomst till](./media/data-lake-store-security-overview/firewall-ip-access.png "brandväggen inställningar och IP-adress")

## <a name="data-protection"></a>Dataskydd
Data Lake Storage Gen1 skyddar dina data under hela dess livscykel. För information som överförs använder Data Lake Storage Gen1 branschstandard Transport Layer Security (TLS 1.2) protokollet för att skydda data över nätverket.

![Kryptering i Data Lake Storage Gen1](./media/data-lake-store-security-overview/adls-encryption.png "kryptering i Data Lake Storage Gen1")

Data Lake Storage Gen1 innehåller också kryptering för data som lagras i kontot. Du kan välja att ha krypterade data eller välja ingen kryptering. Om du väljer i för kryptering krypteras data som lagras i Data Lake Storage Gen1 före lagring på permanenta media. I detta fall är Data Lake Storage Gen1 automatiskt krypterar data före beständig lagring och dekrypterar data för hämtning, så det är helt transparent för klienten åtkomst till data. Det finns några ändringar i koden som krävs på klientsidan för att kryptera/dekryptera data.

För nyckelhantering har Data Lake Storage Gen1 två lägen för att hantera din huvudkrypteringsnycklar (Huvudkrypteringsnycklar), som krävs för att dekryptera data som lagras i Data Lake Storage Gen1. Du kan antingen låta Data Lake Storage Gen1 hantera Huvudkrypteringsnycklar åt dig, eller väljer att behålla ägarskapet för Huvudkrypteringsnycklar med hjälp av Azure Key Vault-kontot. Du kan ange läget för nyckelhantering när du skapar ett Data Lake Storage Gen1-konto. Mer information om hur du skapar en krypteringsrelaterad konfiguration finns i [Kom igång med Azure Data Lake Storage Gen1 med hjälp av Azure-portalen](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Aktivitets- och diagnostikloggar
Du kan använda aktivitet eller diagnostikloggar, beroende på om du söker efter loggar för kontot datorhanteringsrelaterade aktiviteter eller data som är relaterade aktiviteter.

* Kontot datorhanteringsrelaterade aktiviteter API: erna för Azure Resource Manager och exponeras i Azure-portalen via aktivitetsloggar.
* Data som är relaterade aktiviteter använder WebHDFS REST API: er och exponeras i Azure-portalen via diagnostikloggar.

### <a name="activity-log"></a>Aktivitetslogg
För att uppfylla krav, kan en organisation kräva tillräcklig granskningshistorik för hanteringsaktiviteter för kontot om det behöver gräver djupare i särskilda incidenter. Data Lake Storage Gen1 har inbyggd övervakning och loggas alla hanteringsaktiviteter för kontot.

Visa och välj de kolumner som du vill logga för granskningshistorik för kontot management. Du kan också exportera aktivitetsloggar till Azure Storage.

![Aktivitetsloggen](./media/data-lake-store-security-overview/activity-logs.png "aktivitetsloggen")

Mer information om hur du arbetar med aktivitetsloggar finns [visa aktivitetsloggar till granska åtgärder på resurser](../azure-resource-manager/resource-group-audit.md).

### <a name="diagnostics-logs"></a>Diagnostikloggar
Du kan aktivera granskning av data och Diagnostisk loggning i Azure portal och skicka dem till en Azure Blob storage-konto, en event hub eller Azure Monitor-loggar.

![Diagnostikloggar](./media/data-lake-store-security-overview/diagnostic-logs.png "diagnostikloggar")

Läs mer om hur du arbetar med diagnostikloggar med Data Lake Storage Gen1 [åtkomst till diagnostikloggar för Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Sammanfattning
Enterprise-kunder kräver en plattform för dataanalys molnet som är säkert och enkelt att använda. Data Lake Storage Gen1 är utformad för att uppfylla dessa krav via Identitetshantering och autentisering via Azure Active Directory-integrering, ACL-baserad auktorisering, isolering av nätverk, kryptering under överföring och i vila och granskning.

Om du vill se nya funktioner i Data Lake Storage Gen1 Skicka oss feedback den [Data Lake Storage Gen1 UserVoice-forum](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Se också
* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Kom igång med Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)

