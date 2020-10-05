---
title: Översikt över Azure Key Vault – Azure Key Vault | Microsoft Docs
description: Azure Key Vault är en säker hemligheter-butik som ger hantering för hemligheter, nycklar och certifikat, som alla backas upp av säkerhetsmoduler för maskin vara.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.custom: mvc
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 500648b3037a81b39f474538ec062ef922b6e2df
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "89421653"
---
# <a name="about-azure-key-vault"></a>Om Azure Key Vault

Azure Key Vault kan hjälpa dig att lösa följande problem:

- **Hantering av hemligheter** – Azure Key Vault kan användas för säker lagring av och kontrollerad åtkomst till token, lösenord, certifikat, API-nycklar och andra hemligheter
- **Nyckelhantering** – Azure Key Vault kan även användas som nyckelhanteringslösning. Med Azure Key Vault är det enkelt att skapa och kontrollera de krypteringsnycklar som används för att kryptera dina data. 
- **Certifikat hantering** – Azure Key Vault är också en tjänst som gör att du enkelt kan etablera, hantera och distribuera offentliga och privata Transport Layer Security/Secure Sockets Layer-certifikat (TLS/SSL) för användning med Azure och dina interna anslutna resurser. 
- **Lagra hemligheter som backas upp av säkerhetsmoduler för maskin vara** – hemligheter, nycklar och certifikat i butiken krypteras antingen med program varu nyckel (standard nivå) eller FIPS 140-2 Level 2-verifierad HSM: er-nyckel (Premium-nivå) 

## <a name="why-use-azure-key-vault"></a>Därför ska du använda Azure Key Vault

### <a name="centralize-application-secrets"></a>Central lagring av programhemligheter

När dina programhemligheter lagras lokalt i Azure Key Vault kan du styra spridningen av dem. Key Vault minskar kraftigt risken för att hemligheter sprids av misstag. När du använder Key Vault behöver programutvecklare inte längre lagra säkerhetsinformation i sina program. När du inte behöver lagra säkerhetsinformation i program eliminerar du behovet av att göra den här informationen till en del av koden. Ett program kan till exempel behöva ansluta till en databas. Istället för att lagra anslutningssträngen i appkoden så kan du lagra den säkert i Key Vault.

Dina program får säker åtkomst till den information de behöver med hjälp av URI:er. Dessa URI:er gör det möjligt för programmen att hämta specifika versioner av en hemlighet. Du behöver inte skriva någon egen kod för att skydda den hemliga information som lagras i Key Vault.

### <a name="securely-store-secrets-and-keys"></a>Lagra hemligheter och nycklar säkert

Hemligheter och nycklar skyddas av Azure med branschstandardalgoritmer, nyckellängder och maskinvarusäkerhetsmoduler (HSM). De HSM:er som används är FIPS-validerade (Federal Information Processing Standards) enligt 140-2 Level 2.

För åtkomst till ett nyckelvalv krävs en korrekt autentisering och auktorisering (av en användare eller ett program). Autentiseringen etablerar anroparens identitet medan auktoriseringen avgör vilka åtgärder som anroparen får utföra.

Autentiseringen görs via Azure Active Directory. Auktoriseringen kan göras via rollbaserad åtkomstkontroll (RBAC) eller med åtkomstprinciper för Key Vault. RBAC används vid hanteringen av valv och åtkomstprinciper för nyckelvalv används vid försök att komma åt data som lagras i ett valv.

Skyddet för Azure Key Vaults kan vara antingen program - eller maskinvarubaserat (HSM). I situationer där du vill öka säkerheten ytterligare kan du importera och generera nycklar i maskinvarubaserade säkerhetsmoduler (HSM:er) som aldrig lämnar HSM-gränsen. Microsoft använder hjälp programmet nCipher Hardware Security-moduler. Du kan använda hjälp programmet nCipher-verktyg för att flytta en nyckel från HSM till Azure Key Vault.

Azure Key Vault är dessutom utformat så att Microsoft inte kan se eller extrahera dina data.

### <a name="monitor-access-and-use"></a>Övervaka åtkomst och användning

När du väl har skapat några nyckelvalv vill du förmodligen övervaka hur och när dina nycklar och hemligheter används. Du kan övervaka aktiviteten genom att aktivera loggning för dina valv. Du kan konfigurera Azure Key Vault att göra följande:

- arkivera till ett lagringskonto
- strömma till en händelsehubb
- Skicka loggarna till Azure Monitor loggar.

Du har kontroll över dina loggar, du kan skydda dem genom att begränsa åtkomsten och du kan ta bort loggar du inte längre behöver.

### <a name="simplified-administration-of-application-secrets"></a>Enklare administration av programhemligheter

Det ingår ett flertal steg när du lagrar värdefulla data. Säkerhets information måste skyddas, den måste följa en livs cykel och måste ha hög tillgänglighet. Azure Key Vault gör det enklare att uppfylla dessa krav genom att:

- Ta bort behovet av interna kunskaper om säkerhetsmoduler för maskin vara.
- Skala upp på kort varsel för att uppfylla din organisations användnings toppar.
- Innehållet i dina nyckelvarv kan replikeras inom en region och till en sekundär region. Datareplikering säkerställer tillgängligheten och gör att administratören inte behöver utlösa redundansväxlingen manuellt.
- Du har tillgång till vanliga administrationsalternativ för Azure via portalen, Azure CLI:t och PowerShell.
- Vissa uppgifter kring certifikat som du köper från offentliga certifikatutfärdare automatiseras, som registrering och förnyelse.

Dessutom kan du särskilja programhemligheter i Azure Key Vaults. Program kan då endast komma åt valv som de har behörighet till, och de kan begränsas till att endast utföra specifika åtgärder. Du kan skapa ett nyckelvalv per program och begränsa hemligheterna som lagras i ett nyckelvalv till ett visst program och utvecklarteam.

### <a name="integrate-with-other-azure-services"></a>Integrera med andra Azure-tjänster

Som säker lagring i Azure har Key Vault använts för att förenkla scenarier som:
-  [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md)
-  Funktionerna [Always Encrypted]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) och [Transparent datakryptering]( https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15) i SQL Server och Azure SQL Database
- [Azure App Service]( https://docs.microsoft.com/azure/app-service/configure-ssl-certificate). 

Själva Key Vault kan integreras med lagringskonton, händelsehubbar och Log Analytics.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [nycklar, hemligheter och certifikat](about-keys-secrets-certificates.md)
- [Snabbstart: Skapa ett nyckelvalv med hjälp av CLI](../secrets/quick-create-cli.md)
- [Autentisering, begär Anden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
