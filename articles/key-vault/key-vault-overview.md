---
title: Översikt över Azure Key Vault – Azure Key Vault | Microsoft Docs
description: Azure Key Vault är en molntjänst som fungerar som säkert lager för hemligheter.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.assetid: 34af20ee-3fa7-4f28-9d98-6168b1759764
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: overview
ms.custom: mvc
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 474148289bb03088bbf92eeadc4049ce19625e3e
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56115954"
---
# <a name="what-is-azure-key-vault"></a>Vad är Azure Key Vault?

Azure Key Vault kan hjälpa dig att lösa följande problem:

- **Hantering av hemligheter** – Azure Key Vault kan användas för säker lagring av och kontrollerad åtkomst till token, lösenord, certifikat, API-nycklar och andra hemligheter
- **Nyckelhantering** – Azure Key Vault kan även användas som nyckelhanteringslösning. Med Azure Key Vault är det enkelt att skapa och kontrollera de krypteringsnycklar som används för att kryptera dina data. 
- **Hantering av certifikat** – Azure Key Vault är också en tjänst där du enkelt kan etablera, hantera och distribuera offentliga och privata SSL/TLS-certifikat (Secure Sockets Layer/Transport Layer Security) för användning med Azure och dina interna anslutna resurser. 
- **Lagra hemligheter som backas upp av Hardware Security-moduler** – Hemligheterna och nycklarna kan skyddas av programvara eller FIPS 140-2 nivå 2-validerade HS-moduler

## <a name="why-use-azure-key-vault"></a>Därför ska du använda Azure Key Vault

### <a name="centralize-application-secrets"></a>Central lagring av programhemligheter

När dina programhemligheter lagras lokalt i Azure Key Vault kan du styra spridningen av dem. Key Vault minskar kraftigt risken för att hemligheter sprids av misstag. När du använder Key Vault behöver programutvecklare inte längre lagra säkerhetsinformation i sina program. När du inte behöver lagra säkerhetsinformation i program eliminerar du behovet av att göra den här informationen till en del av koden. Ett program kan till exempel behöva ansluta till en databas. Istället för att lagra anslutningssträngen i appkoden så kan du lagra den säkert i Key Vault.

Dina program får säker åtkomst till den information de behöver med hjälp av URI:er. Dessa URI:er gör det möjligt för programmen att hämta specifika versioner av en hemlighet. Du behöver inte skriva någon egen kod för att skydda den hemliga information som lagras i Key Vault.

### <a name="securely-store-secrets-and-keys"></a>Lagra hemligheter och nycklar säkert

Hemligheter och nycklar skyddas av Azure med branschstandardalgoritmer, nyckellängder och maskinvarusäkerhetsmoduler (HSM). De HSM:er som används är FIPS-validerade (Federal Information Processing Standards) enligt 140-2 Level 2.

För åtkomst till ett nyckelvalv krävs en korrekt autentisering och auktorisering (av en användare eller ett program). Autentiseringen etablerar anroparens identitet medan auktoriseringen avgör vilka åtgärder som anroparen får utföra.

Autentiseringen görs via Azure Active Directory. Auktoriseringen kan göras via rollbaserad åtkomstkontroll (RBAC) eller med åtkomstprinciper för Key Vault. RBAC används vid hanteringen av valv och åtkomstprinciper för nyckelvalv används vid försök att komma åt data som lagras i ett valv.

Skyddet för Azure Key Vaults kan vara antingen program - eller maskinvarubaserat (HSM). I situationer där du vill öka säkerheten ytterligare kan du importera och generera nycklar i maskinvarubaserade säkerhetsmoduler (HSM:er) som aldrig lämnar HSM-gränsen. Microsoft använder Thales HSM:er. Du kan använda Thales verktyg till att flytta en nyckel från din HSM till Azure Key Vault.

Azure Key Vault är dessutom utformat så att Microsoft inte kan se eller extrahera dina data.

### <a name="monitor-access-and-use"></a>Övervaka åtkomst och användning

När du väl har skapat några nyckelvalv vill du förmodligen övervaka hur och när dina nycklar och hemligheter används. Du kan övervaka aktiviteten genom att aktivera loggning för dina valv. Du kan konfigurera Azure Key Vault att göra följande:

- arkivera till ett lagringskonto
- strömma till en händelsehubb
- skicka loggarna till Log Analytics.

Du har kontroll över dina loggar, du kan skydda dem genom att begränsa åtkomsten och du kan ta bort loggar du inte längre behöver.

### <a name="simplified-administration-of-application-secrets"></a>Enklare administration av programhemligheter

Det ingår ett flertal steg när du lagrar värdefulla data. Säkerhetsinformationen måste skyddas, den måste följa en livscykel och ha hög tillgänglighet. Azure Key Vault gör det enklare att uppfylla dessa krav genom att:

- Eliminera behovet av intern expertis inom maskinvarusäkerhetsmoduler
- Du kan skala upp med kort varsel vid tillfälliga toppar i organisationens användning.
- Innehållet i dina nyckelvarv kan replikeras inom en region och till en sekundär region. Datareplikering säkerställer tillgängligheten och gör att administratören inte behöver utlösa redundansväxlingen manuellt.
- Du har tillgång till vanliga administrationsalternativ för Azure via portalen, Azure CLI:t och PowerShell.
- Vissa uppgifter kring certifikat som du köper från offentliga certifikatutfärdare automatiseras, som registrering och förnyelse.

Dessutom kan du särskilja programhemligheter i Azure Key Vaults. Program kan då endast komma åt valv som de har behörighet till, och de kan begränsas till att endast utföra specifika åtgärder. Du kan skapa ett nyckelvalv per program och begränsa hemligheterna som lagras i ett nyckelvalv till ett visst program och utvecklarteam.

### <a name="integrate-with-other-azure-services"></a>Integrera med andra Azure-tjänster

Som säker lagring i Azure har Key Vault använts för att förenkla scenarier som:
-  [Azure Disk Encryption](../security/azure-security-disk-encryption.md)
-  De [alltid krypterade]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funktionerna i SQL Server och Azure SQL Database
- [Azure App Service]( https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site). 

Själva Key Vault kan integreras med lagringskonton, händelsehubbar och Log Analytics.

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Skapa ett nyckelvalv med hjälp av CLI:et](quick-create-cli.md)
- [Konfigurera ett Azure-webbprogram för att läsa en hemlighet från Key Vault](tutorial-web-application-keyvault.md)
