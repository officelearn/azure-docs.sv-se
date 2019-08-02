---
title: Azure Key Vault i Azure Australien
description: Vägledning om hur du konfigurerar och använder Azure Key Vault för nyckel hantering inom de australiska regionerna för att uppfylla de särskilda kraven i den australiska regeringens politik, förordningar och lagstiftning.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 263765c777f994134befc52f0c63c7f18e590b39
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602125"
---
# <a name="azure-key-vault-in-azure-australia"></a>Azure Key Vault i Azure Australien

Säker lagring av kryptografiska nycklar och hantering av livs cykeln för kryptografiska nycklar är kritiska element i kryptografiska system.  Tjänsten som tillhandahåller den här funktionen i Azure är den Azure Key Vault. Key Vault har IRAP säkerhets åtkomst och ACSC certifierats för skyddad.  Den här artikeln beskriver viktiga överväganden när du använder Key Vault för att följa de "ASD-" (ASD) [Information Security](https://acsc.gov.au/infosec/ism/) Restore-kontrollerna (ISM).

Azure Key Vault är en moln tjänst som skyddar krypterings nycklar och hemligheter. Eftersom dessa data är känsliga och affärs kritiska ger Key Vault säker åtkomst till nyckel valv, så att endast auktoriserade användare och program tillåts. Det finns tre huvudsakliga artefakter som hanteras och kontrol leras av Key Vault:

- nycklar
- hemligheter
- certifikat

Den här artikeln fokuserar på hantering av nycklar med hjälp av Key Vault.

![Azure Key Vault](media/azure-key-vault-overview.png)

*Diagram 1 – Azure Key Vault*

## <a name="key-design-considerations"></a>Viktiga design överväganden

### <a name="deployment-options"></a>Distributionsalternativ

Det finns två alternativ för att skapa Azure Key Vault. Båda alternativen använder Thales nshield maskinvarusäkerhetsmodul-familjen för maskin varu säkerhet (HSM), är FIPS (Federal Information Processing Standards) och godkänns för att lagra nycklar i skyddade miljöer. Alternativen är:

- **Program skyddade valv:** FIPS 140-2-nivå 1 har verifierats. Nycklar som lagras på en HSM. Kryptering och dekryptering utförs i beräknings resurser i Azure.
- **HSM-skyddade valv:** FIPS 140-2-nivå 2 har verifierats. Nycklar som lagras på en HSM. Krypterings-och dekrypterings åtgärder utförs på HSM.

Key Vault stöder Rivest-Shamir-Adleman (RSA) och Elliptic Curve Cryptography (ECC)-nycklar. Standardvärdet är RSA 2048-bitars nycklar, men det finns ett avancerat alternativ för RSA 3072-bitars, RSA 4096-bitars och ECC-nycklar.  Alla nycklar uppfyller ISM-kontrollerna, men Elliptic kurv nycklar föredras.

### <a name="resource-operations"></a>Resurs åtgärder

Det finns flera personer som ingår i Azure Key Vault:

- **Key Vault administratör:** Hanterar livs cykeln för valvet
- **Nyckel administratör:** Hanterar livs cykeln för nycklar i valvet
- **Utvecklare/operatör:** Integrera nycklar från valvet i program och tjänster
- **Kontrollant** Övervaka nyckel användning och åtkomst
- **Ansökan** Använd nycklar för att skydda information

Azure Key Vault skyddas med två separata gränssnitt:

- **Hanterings plan:** Det här planet hanterar hantering av valvet och det skyddas av RBAC.
- **Data plan:** Det här planet hanterar hantering och åtkomst till artefakter i valvet.  Skyddas med Key Vault åtkomst princip.

Efter vad som krävs av ISM krävs korrekt autentisering och auktorisering före en anropare (en användare eller ett program) innan de kan få åtkomst till nyckel valvet av något av planerna.

Azure RBAC har en inbyggd roll för Key Vault, [Key Vault deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-contributor), för att styra hanteringen av nyckel valven. Vi rekommenderar att du skapar anpassade roller justerade till mer detaljerade roller för hantering av nyckel valv.

>[!WARNING]
>När åtkomst till nycklar har Aktiver ATS via Key Vault åtkomst princip, har användaren eller programmet åtkomst till alla nycklar i nyckel valvet (till exempel om en användare har behörigheten Ta bort alla nycklar).  Därför bör flera nyckel valv distribueras för att anpassas till säkerhets domäner/-gränser.

### <a name="networking"></a>Nätverk

Du kan konfigurera Key Vault brand väggar och virtuella nätverk för att kontrol lera åtkomsten till data planet.  Du kan tillåta åtkomst till användare eller program i angivna nätverk samtidigt som du nekar åtkomst till användare eller program i alla andra nätverk. [Betrodda tjänster](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services) är ett undantag till den här kontrollen om "Tillåt betrodda tjänster" är aktiverat.  Den virtuella nätverks kontrollen gäller inte för hanterings planet.

Åtkomst till nyckel valv bör uttryckligen begränsas till den minsta uppsättning nätverk som har användare eller program som kräver åtkomst till nycklar.

### <a name="bring-your-own-key-byok"></a>Bring Your Own Key (BYOK)

Key Vault stöder BYOK.  BYOK gör det möjligt för användare att importera nycklar från befintliga nyckel infrastrukturer.  Thales tillhandahåller en [australisk verktygs](https://www.microsoft.com/download/details.aspx?id=45345) uppsättning som stöder säker överföring och import av nycklar från en extern HSM (till exempel nycklar som genereras med en offline-arbetsstation) till Key Vault.

### <a name="key-vault-auditing-and-logging"></a>Key Vault granskning och loggning

ACSC kräver att samväldet entiteter använder lämpliga Azure-tjänster för att utföra övervakning och rapportering i real tid i sina Azure-arbetsbelastningar.

Loggning har Aktiver ATS genom att aktivera diagnostikinställningar **_"AuditEvent"_** för nyckel värden.  Gransknings händelser kommer att loggas till det angivna lagrings kontot.  Perioden **_"RetentionInDays"_** ska ställas in enligt data lagrings principen.  [Åtgärder](https://docs.microsoft.com/azure/key-vault/key-vault-logging#interpret) för både hanterings planet och data planet granskas och loggas. [Azure Key Vault-lösningen i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault) kan användas för att granska Key Vault AuditEvent-loggar.  Ett antal andra Azure-tjänster kan användas för att bearbeta och distribuera Key Vault AuditEvents.

### <a name="key-rotation"></a>Nyckelrotation

Att lagra nycklar i Key Vault tillhandahöll en enda punkt för att underhålla nycklar utanför program som gör att nycklarna kan uppdateras utan att påverka programmets beteende. Att lagra nycklar i Azure Key Vault möjliggör olika strategier för att stödja nyckel rotation:

- Manuellt
- Via programmering via API: er
- Automation-skript (till exempel med PowerShell och Azure Automation)

Med de här alternativen kan nycklar roteras regelbundet för att uppfylla kraven på efterlevnad eller en ad hoc-basis om det finns problem som kan ha komprometterats.

#### <a name="key-rotation-strategies"></a>Strategier för nyckel rotation

Det är viktigt att utveckla en lämplig nyckel rotations strategi för nycklar som lagras i nyckel valvet.  Om du använder fel nyckel leder det till att informationen är felaktigt dekrypterad och om du förlorar nycklar kan det leda till att du förlorar åtkomsten till information.  Exempel på nyckel rotations strategier för olika scenarier är:

- **Synlighetssekvensnummer-data:** flyktig information överförs mellan två parter.  När en nyckel roteras måste båda parter ha en mekanism för att synkront hämta uppdaterade nycklar från nyckel valvet.
- **Data som rest:** En part lagrar krypterade data och dekrypterar den i framtiden för att använda.  När en nyckel ska roteras måste data dekrypteras med den gamla nyckeln och sedan krypteras med den nya, roterade nyckeln.  Det finns metoder för att minimera effekten av dekryptering/kryptera-processen med nyckel krypterings nycklar (se exemplet).  Microsoft hanterar majoriteten av processen som är relaterad till nyckel rotation för Azure Storage (se...)
- **Åtkomst nycklar:** ett antal Azure-tjänster har åtkomst nycklar som kan lagras i Key Vault (till exempel CosmosDB).  Azure-tjänsterna har primära och sekundära åtkomst nycklar.  Det är viktigt att båda nycklarna inte roteras samtidigt.  En nyckel bör därför roteras efter en punkt och nyckel åtgärden har verifierats, men den andra nyckeln kan roteras.

### <a name="high-availability"></a>Hög tillgänglighet

ISM har flera kontroller som relaterar till affärs kontinuitet.
Azure Key Vault har flera lager av redundans med innehåll som replikeras inom regionen och till den sekundära, [kopplade regionen](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

När nyckel valvet är i ett läge där redundans är i skrivskyddat läge och återgår till Läs-och skriv läge, kommer den primära tjänsten att återställas.

ISM har flera kontroller som rör säkerhets kopiering.  Det är viktigt att utveckla och köra lämpliga säkerhets kopierings-och återställnings planer för valv och deras nycklar.

## <a name="key-lifecycle"></a>Livs cykel för nyckel

### <a name="key-operations"></a>Nyckel åtgärder

Key Vault stöd för följande åtgärder på en nyckel:

- **fram** Tillåter en klient att skapa en nyckel i Key Vault. Nyckelns värde genereras av Key Vault och lagras, och frigörs inte till klienten. Asymmetriska nycklar kan skapas i Key Vault.
- **export** Gör att en klient kan importera en befintlig nyckel till Key Vault. Asymmetriska nycklar kan importeras till Key Vault att använda ett antal olika förpacknings metoder inom en JWK-konstruktion.
- **uppdatera** Tillåter en klient med tillräcklig behörighet att ändra de metadata (nyckelattribut) som är associerade med en nyckel som tidigare lagrats i Key Vault.
- **ta bort** Tillåter en klient med tillräcklig behörighet att ta bort en nyckel från Key Vault.
- **lista** Tillåter en klient att visa en lista över alla nycklar i en specifik Key Vault.
- **List versioner:** Tillåter att en klient visar en lista över alla versioner av en specifik nyckel i en specifik Key Vault.
- **ta** Tillåter att en klient hämtar offentliga delar av en viss nyckel i en Key Vault.
- **band** Exporterar en nyckel i ett skyddat formulär.
- **återskapa** Importerar en tidigare säkerhetskopierad nyckel.

Det finns en motsvarande uppsättning behörigheter som kan beviljas användare, tjänstens huvud namn eller program som använder Key Vault åtkomst kontroll poster för att göra det möjligt för dem att köra nyckel åtgärder.

Key Vault har en funktion för mjuk borttagning som tillåter återställning av borttagna valv och nycklar. Som standard är **_"mjuk borttagning"_** inte aktiverat, men när den är aktive rad hålls objekten i 90 dagar (kvarhållningsperioden) och visas som borttagna.  En ytterligare behörighet att **_Rensa_** , tillåter permanent borttagning av nycklar om alternativet **_"Rensa skydd"_** är inaktiverat.

Om du skapar eller importerar en befintlig nyckel skapas en ny version av nyckeln.

### <a name="cryptographic-operations"></a>Kryptografiska åtgärder

Key Vault stöder också kryptografiska åtgärder med hjälp av nycklar:

- **Logga och verifiera:** den här åtgärden är en "Sign hash" eller "verifiera hash". Key Vault stöder inte hashing av innehåll som en del av skapandet av signaturen.
- **nyckel kryptering/rad brytning:** den här åtgärden används för att skydda en annan nyckel.
- **kryptera och dekryptera:** den lagrade nyckeln används för att kryptera eller dekryptera ett enda data block

Det finns en motsvarande uppsättning behörigheter som kan beviljas användare, tjänstens huvud namn eller program som använder Key Vault åtkomst kontroll poster för att göra det möjligt för dem att utföra kryptografiska åtgärder.

Det finns tre nyckelattribut som kan ställas in för att kontrol lera om en nyckel är aktive rad och användning av kryptografiska åtgärder:

- **aktiva**
- **NBF:** ej aktiverat före det angivna datumet
- **exp:** förfallo datum

## <a name="storage-and-keys"></a>Lagring och nycklar

Kundhanterade nycklar är mer flexibla och möjliggör utvärdering och hantering av de nycklar som ska kontrol leras. De aktiverar också granskning av de krypterings nycklar som används för att skydda data.
Det finns tre aspekter av lagring och nycklar som lagras i Key Vault:

- Key Vault hanterade lagrings konto nycklar
- Azure Storage tjänst kryptering (SSE) för vilande data
- Hanterade diskar och Azure Disk Encryption

Key Vault Azure Storage konto nyckel hantering är ett tillägg till Key Vault nyckel tjänst som stöder synkronisering och regenering (rotation) av lagrings konto nycklar.  [Azure Storage integration med Azure Active Directory](https://docs.microsoft.com/azure/storage/common/storage-auth-aad) (för hands version) rekommenderas när den släpps eftersom den ger överlägsen säkerhet och enkel användning.
SSE använder två nycklar för att hantera kryptering av data i vila:

- Nyckel krypterings nycklar (KEK)
- Data krypterings nycklar (DEK)

Medan Microsoft hanterar DEKs, har SSE ett alternativ för att använda Kundhanterade KeyExchange som kan lagras i Key Vault. Detta möjliggör rotation av nycklar i Azure Key Vault enligt tillämpliga efterlevnadsprinciper. När nycklar roteras, Azure Storage krypterar om konto krypterings nyckeln för det lagrings kontot. Detta resulterar inte i en ny kryptering av alla data och det finns ingen annan åtgärd som krävs.

SSE används för hanterade diskar men Kundhanterade nycklar stöds inte.  Kryptering av hanterade diskar kan göras med Azure Disk Encryption med Kundhanterade KEK-nycklar i Key Vault.

## <a name="next-steps"></a>Nästa steg

Läs artikeln om [identitets Federation](identity-federation.md)

Granska ytterligare Azure Key Vault dokumentation och självstudier i [referens biblioteket](reference-library.md)
