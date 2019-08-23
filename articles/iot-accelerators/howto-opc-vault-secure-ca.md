---
title: Så här kör du OPC Vault Certificate Management-tjänsten på ett säkert sätt – Azure | Microsoft Docs
description: Beskriver hur du kör OPC Vault Certificate Management-tjänsten på ett säkert sätt i Azure och andra säkerhets rikt linjer.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b08358680793ccdadca27c5f2aa57fbffe89b53a
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69973797"
---
# <a name="how-to-run-the-opc-ua-certificate-management-service-securely"></a>Så här kör du OPC UA Certificate Management-tjänsten på ett säkert sätt

Den här artikeln förklarar hur du kör OPC-tjänsten för hantering av certifikat på ett säkert sätt i Azure och andra säkerhets rikt linjer.

## <a name="roles"></a>Roller

### <a name="trusted-and-authorized-roles"></a>Betrodda och auktoriserade roller

Mikrotjänsten i OPC Vault har kon figurer ATS för att tillåta särskilda roller att komma åt olika delar av tjänsten.

> [!IMPORTANT]
> Under distributionen lägger skriptet bara till den användare som kör distributions skriptet som en användare för alla roller.
> Den här roll tilldelningen bör granskas för en produktions distribution och konfigureras om korrekt enligt rikt linjerna nedan.
> Den här uppgiften kräver manuell tilldelning av roller och tjänster i Azure AD Enterprise Applications-portalen.

### <a name="certificate-management-service-roles"></a>Tjänst roller för certifikat hantering

Mikrotjänsten definierar följande roller:

- **Läsare**: Som standard har alla autentiserade användare i klienten Läs behörighet. 
  - Läs åtkomst till program och certifikat begär Anden. Kan lista och fråga efter program-och certifikat begär Anden. Dessutom är enhets identifierings information och offentliga certifikat tillgängliga med Läs behörighet.
- **Skribent**: Skrivar rollen tilldelas till en användare för att kunna lägga till Skriv behörighet för vissa uppgifter. 
  - Läs-/Skriv behörighet till program och certifikat begär Anden. Kan registrera, uppdatera och avregistrera program. Kan skapa certifikat begär Anden och hämta godkända privata nycklar och certifikat. Det går också att ta bort privata nycklar.
- **God kännare**: God kännare-rollen tilldelas en användare för att godkänna eller avvisa certifikat begär Anden. Rollen innehåller ingen annan roll.
  - Förutom rollen god kännare för att få åtkomst till API: et för OPC Vault mikrotjänst måste användaren också ha behörigheten nyckel signering i Key Vault för att kunna signera certifikaten.
  - Rollen skrivare och god kännare bör tilldelas till olika användare.
  - Den huvudsakliga rollen hos god kännaren är godkännande av generering och avvisande av certifikat begär Anden.
- **Administratör**: Rollen administratör tilldelas till en användare för att hantera certifikat grupperna. Rollen har inte stöd för rollen god kännare, men innehåller rollen skrivare.
  - Administratören kan hantera certifikat grupper, ändra konfigurationen och återkalla program certifikat genom att utfärda en ny lista över återkallade certifikat.
  - Rollerna idealisk, författare, god kännare och administratör tilldelas olika användare. För ytterligare säkerhet behöver en användare med rollen god kännare eller administratör också nyckel signerings behörighet i ett nyckel valv för att utfärda certifikat eller förnya ett CA-certifikat för certifikat utfärdare.
  - Förutom rollen som mikrotjänst administration inkluderar rollen även, men är inte begränsad till:
    - Ansvarar för att administrera implementeringen av CA: ns säkerhets rutiner.
    - Hantering av generering, åter kallelse och upphävande av certifikat. 
    - Livs cykel hantering av kryptografisk nyckel (till exempel förnyelse av utfärdares CA-nycklar).
    - Installation, konfiguration och underhåll av tjänster som använder certifikat utfärdaren.
    - Daglig drift av tjänsterna. 
    - Säkerhets kopiering och återställning av CA och databasen.

### <a name="other-role-assignments"></a>Andra roll tilldelningar

Följande roller bör också beaktas och tilldelas när tjänsten körs:

- Företags ägare till avtal för certifikat inköp med den externa rot certifikat utfärdaren (om ägaren köper certifikat från en extern certifikat utfärdare eller använder en certifikat utfärdare som är underordnad en extern certifikat utfärdare).
- Utveckling och validering av certifikat utfärdaren.
- Granskning av gransknings poster.
- Personal som bidrar till att stödja CA: n eller för att hantera fysiska och moln tjänster, men som inte är direkt betrodda att utföra CA-åtgärder, definieras som en behörig roll. Uppsättningen med uppgifter som personer i den auktoriserade rollen tillåts utföra måste också dokumenteras.

### <a name="memberships-of-trusted-and-authorized-roles-must-be-reviewed-annually"></a>Medlemskap i betrodda och auktoriserade roller måste granskas varje år

Medlemskap i betrodda och auktoriserade roller måste granskas minst kvartals vis för att säkerställa att en uppsättning personer (för manuella processer) eller tjänst identiteter (för automatiserade processer) i varje roll hålls till ett minimum.

### <a name="certificate-issuance-process-must-enforce-role-separation-between-certificate-requester-and-approver"></a>Processen för utfärdande av certifikat måste framtvinga separering av roller mellan certifikat begär ande och god kännare

Processen för utfärdande av certifikat måste tillämpa rollseparering mellan certifikat begär Anden och certifikat god kännare roller (personer eller automatiska system). Utfärdande av certifikat måste godkännas av en certifikats god kännare roll som verifierar att den begär ande certifikat utfärdaren har behörighet att skaffa certifikat. De personer som innehar rollen som god kännare måste vara en formellt auktoriserad person.

### <a name="privileged-role-management-must-restrict-access-and-be-reviewed-quarterly"></a>Privilegie rad roll hantering måste begränsa åtkomsten och granskas varje kvartal

Tilldelning av privilegierade roller, till exempel auktorisering av medlemskap i gruppen Administratörer och god kännare, måste begränsas till en begränsad uppsättning behörig personal. Ändringar av privilegierade roller måste ha åtkomst återkallade inom 24 timmar. Slutligen måste du granska privilegierade roll tilldelningar varje kvartal och alla onödiga eller utgångna tilldelningar måste tas bort.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>Privilegierade roller bör använda tvåfaktorautentisering

Multi-Factor Authentication (tvåfaktorautentisering, MFA eller TFA) måste användas för interaktiva inloggningar av god kännare och administratörer till tjänsten.

## <a name="certificate-service-operation-guidelines"></a>Rikt linjer för certifikat tjänst åtgärder

### <a name="operational-contacts"></a>Drift kontakter

Certifikat tjänsten måste ha en uppdaterad säkerhets svars plan för en fil som innehåller detaljerade rapporter om drift incident svar.

### <a name="security-updates"></a>Säkerhetsuppdateringar

Alla system måste övervakas kontinuerligt och uppdateras med de senaste säkerhets uppdateringarna/korrigerings kraven.

> [!IMPORTANT]
> GitHub-lagringsplatsen i OPC Vault-tjänsten uppdateras kontinuerligt med säkerhets korrigeringar. Uppdateringarna på GitHub bör övervakas och uppdateringarna tillämpas på tjänsten med jämna mellanrum.

### <a name="security-monitoring"></a>Säkerhetsövervakning

Prenumerera på eller implementera lämplig säkerhets övervakning, t. ex. genom att prenumerera på en central övervaknings lösning (till exempel Azure Security Center, O365-övervaknings lösning) och konfigurera den på lämpligt sätt för att säkerställa att säkerhets händelser överförs till övervaknings lösning.

> [!IMPORTANT]
> OPC Vault-tjänsten distribueras som standard med [Azure Application](https://docs.microsoft.com/azure/azure-monitor/app/devops) insikter som en övervaknings lösning. Det rekommenderas starkt att du lägger till en säkerhetslösning som [Azure Security Center](https://azure.microsoft.com/services/security-center/) .

### <a name="assess-security-of-open-source-software-components"></a>Utvärdera säkerheten för program varu komponenter med öppen källkod

Alla komponenter med öppen källkod som används i en produkt eller tjänst måste vara fria från medelhög eller större säkerhets sårbarheter.

> [!IMPORTANT]
> GitHub-lagringsplatsen i OPC Vault-tjänsten genomsöker alla komponenter under kontinuerlig integrerings versioner för sårbarheter. Uppdateringarna på GitHub bör övervakas och uppdateringarna tillämpas på tjänsten med jämna mellanrum.

### <a name="maintain-an-inventory"></a>Underhålla en inventering

En till gångs inventering måste upprätthållas för alla produktions värdar (inklusive beständiga virtuella datorer), enheter, alla interna IP-adressintervall, VIP och offentliga DNS-domännamn. Denna inventering måste uppdateras med tillägg eller borttagning av ett system, en IP-adress, en VIP-eller offentlig DNS-domän inom 30 dagar.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Inventering av standard produktions distributionen för Azure OPC Vault-mikrotjänster: 

I **Azure**:
- **App Service plan**: App Service-plan för tjänst värdar. Standard S1.
- **App Service** för mikrotjänster: OPC Vault-tjänstens värd.
- **App Service** för exempel program: OPC-valvets exempel program värd.
- Nyckel **valv standard**: För att lagra hemligheter och Cosmos DB nycklar för webb tjänsterna.
- Nyckel **valv Premium**: För att vara värd för utfärdares CA-nycklar, för signerings tjänst, för valv konfiguration och lagring av program privata nycklar.
- **Cosmos DB**: Databas för program-och certifikat begär Anden. 
- **Application Insights**: (valfritt) övervaknings lösning för webb tjänst och program.
- **AzureAD program registrering**: En registrering för exempel programmet, tjänsten och Edge-modulen.

För moln tjänsterna ska alla värdnamn, resurs grupper, resurs namn, prenumerations-ID, TenantId som används för att distribuera tjänsten dokumenteras. 

I **IoT Edge** eller en lokal **IoT Edge Server**:
- **OPC-valv IoT Edge modul**: Stöd för en global identifierings Server för en fabriks nätverk OPC UA. 

För IoT Edge enheter ska värd namnen och IP-adresserna dokumenteras. 

### <a name="document-the-certification-authorities-cas"></a>Dokumentera certifikat utfärdare (ca)

Dokumentationen för CA-hierarkin måste innehålla alla drivna ca: er, inklusive alla relaterade underordnade certifikat utfärdare, överordnade certifikat utfärdare och rot certifikat utfärdare, även när de inte hanteras av tjänsten. En fullständig uppsättning CA-certifikat som inte har upphört att gälla kan anges i stället för formell dokumentation.

> [!IMPORTANT]
> Exempel programmet OPC Vault stöder hämtning av alla certifikat som används och produceras i tjänsten för dokumentation.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Dokumentera de utfärdade certifikaten från alla certifikat utfärdare

En fullständig uppsättning av alla certifikat som utfärdats under de senaste 12 månaderna bör tillhandahållas för dokumentation.

> [!IMPORTANT]
> Exempel programmet OPC Vault stöder hämtning av alla certifikat som används och produceras i tjänsten för dokumentation.

### <a name="document-the-sop-for-securely-deleting-cryptographic-keys"></a>Dokumentera SOP för säker borttagning av kryptografiska nycklar

Det går bara sällan att ta bort nyckeln under en CERTIFIKATs livs längd, detta är anledningen till att ingen användare har ett certifikat för nyckel valv borttagnings rättigheten ta bort och varför det inte finns några API: er tillgängliga för att ta bort ett CA-certifikat Den manuella standard drift proceduren för att säkert ta bort certifikat utfärdarens kryptografiska nycklar är bara tillgänglig genom direkt åtkomst till nyckel valvet i Azure Portal och genom att ta bort certifikat gruppen i nyckel valvet. För att säkerställa att omedelbar borttagning av nyckel [valv är mjuk](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) borttagning ska inaktive ras.

## <a name="certificates"></a>Certifikat

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Certifikat måste följa minsta certifikat profil

OPC Vault-tjänsten är en online-certifikatutfärdare (ca) som utfärdar certifikat från slutentiteten till prenumeranter.
Mikrotjänsten OPC Vault följer dessa rikt linjer i standard implementeringen.

- Alla certifikat måste innehålla följande X. 509-fält som anges nedan:
  - Innehållet i versions fältet måste vara v3. 
  - Innehållet i fältet serialNumber måste innehålla minst 8 byte av entropi som hämtats från en FIPS-baserad (Federal Information Processing standard) 140-godkänd slump nummer generator.<br>
    > [!IMPORTANT]
    > Serie numret för OPC-valvet är som standard 20 byte och hämtas från den slumpmässiga nummer generatorn för OS-kryptografi. Slump tals generatorn är FIPS 140 som godkänts på Windows-enheter, men inte i Linux-varianter. Detta faktum måste beaktas när du väljer en tjänst distribution, som använder virtuella Linux-datorer eller Linux Docker-behållare där den underliggande teknikens OpenSSL inte är FIPS 140-godkänd.
  - Fälten issuerUniqueID och subjectUniqueID får inte förekomma.
  - Certifikat för slutanvändare måste identifieras med tillägget grundläggande begränsningar i enlighet med IETF RFC 5280.
  - PathLenConstraint-fältet måste anges till 0 för det utfärdande CA-certifikatet. 
  - Tillägget för utökad nyckel användning måste finnas och innehålla en minimal uppsättning av objekt identifierare för utökad nyckel användning (OID). AnyExtendedKeyUsage OID (2.5.29.37.0) får inte anges. 
  - CDP-tillägget (CRL Distribution Point) måste finnas i utfärdarens CA-certifikat.<br>
    > [!IMPORTANT]
    > CDP-tillägget (CRL Distribution Point) finns i OPC Vault CA-certifikat, men OPC UA-enheter använder anpassade metoder för att distribuera listor över återkallade certifikat.
  - Åtkomst tillägget för auktoritets information måste finnas i prenumerantens certifikat.<br>
    > [!IMPORTANT]
    > Åtkomst tillägget för auktoritets information finns i OPC Vault-prenumeranter, men OPC UA-enheter använder anpassade metoder för att distribuera information om utfärdare av certifikat utfärdare.
- Godkända asymmetriska algoritmer, nyckel längder, hash-funktioner och utfyllnads lägen måste användas.
  - **RSA** och **SHA-2** är de enda algoritmer som stöds (*).
  - RSA kan användas för kryptering, nyckel utbyte och signatur.
  - RSA-kryptering får endast använda OAEP-, RSA-KEM-eller RSA-PSS-utfyllnad.
  - Nyckel längder > = 2048 bitar krävs.
  - Använd SHA-2-familjen för hash-algoritmer (SHA256, SHA384 och SHA512).
  - RSA-rotens CA-nycklar med en typisk livstid > = 20 år måste vara 4096 bitar eller mer.
  - RSA Issuer-CA-nycklar måste vara minst 2048 bitar; om certifikat UTFÄRDARens förfallo datum är efter 2030 måste CA-nyckeln vara 4096 bitar eller mer.
- Certifikatets livs längd
  - Certifikat för rot certifikat utfärdare: Den maximala giltighets perioden för certifikat för rot certifikat utfärdare får inte överstiga 25 år.
  - Certifikat utfärdare eller certifikat från certifikat utfärdare för online utfärdare: Den maximala giltighets perioden för certifikatet för certifikat utfärdare som är online och endast utfärdar prenumerations certifikat får inte överstiga sex år. För dessa certifikat utfärdare får inte den relaterade privata signatur nyckeln användas längre än tre år för att utfärda nya certifikat.<br>
    > [!IMPORTANT]
    > Utfärdarcertifikatet som det genereras i standard-OPC Vault-mikrotjänsten utan extern rot certifikat utfärdare behandlas som en online-certifikatutfärdare med respektive krav och livstid. Standard livs längden är inställd på fem år med en nyckel längd > = 2048.
  - Alla asymmetriska nycklar måste ha en högsta fem års livs längd, vilket rekommenderas för en års livs längd.<br>
    > [!IMPORTANT]
    > Som standard har livs längden för program certifikat som utfärdas med OPC-valvet en livs längd på två år och bör ersättas varje år. 
  - När ett certifikat förnyas förnyas det med en ny nyckel.
- OPC UA-specifika tillägg i program instans certifikat
  - SubjectAltName-tillägget innehåller programmets URI och värdnamn, som även kan innehålla FQDN-, IPv4-och IPv6-adresser.
  - Användningen innehåller digitalSignature, oavvislig het, keyEncipherment och dataEncipherment.
  - ExtendedKeyUsage innehåller serverAuth och/eller clientAuth.
  - AuthorityKeyIdentifier anges i signerade certifikat.

### <a name="certificate-authority-ca-keys-and-certificates-must-meet-minimum-requirements"></a>Certifikat utfärdare (CA) nycklar och certifikat måste uppfylla minimi kraven

- **Privata nycklar**: **RSA** -nycklar måste vara minst 2048 bitar; om certifikat UTFÄRDARens förfallo datum är efter 2030 måste CA-nyckeln vara 4096 bitar eller mer.
- **Livs längd**: Den maximala giltighets perioden för certifikatet för certifikat utfärdare som är online och endast utfärdar prenumerations certifikat får inte överstiga sex år. För dessa certifikat utfärdare får inte den relaterade privata signatur nyckeln användas längre än tre år för att utfärda nya certifikat.

### <a name="ca-keys-are-protected-using-hardware-security-modules-hsm"></a>CA-nycklar skyddas med hjälp av maskinvarukonfigurationer (Hardware Security modules)

- OpcVault använder Azure nyckel valv Premium och nycklar skyddas av FIPS 140-2 nivå 2. 

De kryptografiska moduler som Key Vault använder, om HSM eller program vara, är FIPS (Federal Information Processing Standards) som verifieras.<br>
Nycklar som skapas eller importeras som HSM-skyddade bearbetas i en HSM, verifieras till FIPS 140-2 nivå 2.<br>
Nycklar som skapas eller importeras som program varu skyddade bearbetas i kryptografiska moduler validerade till FIPS 140-2 nivå 1.

## <a name="operational-practices"></a>Operativa metoder

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Dokumentera och underhålla vanliga PKI-rutiner för certifikat registrering

Dokumentera och underhålla standard operativa procedurer (SOP) för hur CAs utfärdar certifikat, inklusive: 
- Hur prenumeranten identifieras och autentiseras 
- Hur certifikatbegäran bearbetas och verifieras (om det är tillämpligt inkluderar även hur certifikat förnyelse och nyckel förnyelse begär Anden bearbetas) 
- Hur utfärdade certifikat distribueras till prenumeranterna 

SOP för OPC Vault-mikrotjänsten beskrivs i [översikten](overview-opc-vault-architecture.md) och [hur du hanterar](howto-opc-vault-manage.md) dokument. Praxis följer OPC Unified Architecture Specification del 12: Identifiering och globala tjänster.


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Dokumentera och underhålla standardiserade PKI-metoder för certifikat åter kallelse

Återkallnings processen för certifikat beskrivs i [översikten](overview-opc-vault-architecture.md) och [hur du hanterar](howto-opc-vault-manage.md) dokument.
    
### <a name="document-certification-authority-key-generation-ceremony"></a>Dokumentera certifikat utfärdarens nyckel generations ceremoni 

Genereringen av utfärdarens CA-nyckel i OPC Vault-mikrotjänsten förenklas på grund av den säkra lagringen i Azure-valv och beskrivs i dokumentationen [för att hantera](howto-opc-vault-manage.md) dokumentationen.

När en extern rot certifikat utfärdare används måste dock en certifikat utfärdare (CA) för att skapa en certifierings ceremoni uppfylla följande krav:

CA-nyckeln för att skapa ceremonin måste utföras mot ett dokumenterat skript som innehåller minst följande objekt: 
1. Definition av roller och deltagares ansvar
2. Godkännande för att genomföra CA-nyckelns generations ceremoni
3. Kryptografisk maskin vara och aktiverings material som krävs för ceremonin
4. Maskin varu förberedelse (inklusive uppdatering av till gång/konfigurations information och signering)
5. Installation av operativ system
6. Vissa steg som utförs under CA-nyckelns generations ceremoni, till exempel: 
7. Installation och konfiguration av CA-program
8. Generering av CA-nyckel
9. Säkerhets kopiering av CA-nyckel
10. Certifikat signering
9. Import av signerade nycklar i den skyddade HSM för tjänsten.
11. System avstängning för CA
12. Beredning av material för lagring


## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar OPC-valvet på ett säkert sätt är det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Skydda OPC UA-enheter med OPC-valvet](howto-opc-vault-secure.md)