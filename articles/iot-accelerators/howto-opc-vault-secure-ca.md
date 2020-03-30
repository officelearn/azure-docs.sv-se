---
title: Så här kör du certifikathanteringstjänsten OPC Vault på ett säkert sätt – Azure | Microsoft-dokument
description: Beskriver hur du kör OPC Vault-certifikathanteringstjänsten på ett säkert sätt i Azure och granskar andra säkerhetsriktlinjer att tänka på.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 88f8188779c5fb6b3cd07c67e9f35a6b8f9ad97d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271712"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>Kör tjänsten för hantering av OPC Vault-certifikat på ett säkert sätt

I den här artikeln beskrivs hur du kör OPC Vault-certifikathanteringstjänsten på ett säkert sätt i Azure och granskar andra säkerhetsriktlinjer att tänka på.

## <a name="roles"></a>Roller

### <a name="trusted-and-authorized-roles"></a>Betrodda och auktoriserade roller

OPC Vault-mikrotjänsten möjliggör olika roller för att komma åt olika delar av tjänsten.

> [!IMPORTANT]
> Under distributionen lägger skriptet bara till användaren som kör distributionsskriptet som användare för alla roller. För en produktionsdistribution bör du granska den här rolltilldelningen och konfigurera om på rätt sätt genom att följa riktlinjerna nedan. Den här uppgiften kräver manuell tilldelning av roller och tjänster i Azure Active Directory (Azure AD) Enterprise Applications-portalen.

### <a name="certificate-management-service-roles"></a>Tjänstroller för certifikathantering

OPC Vault-mikrotjänsten definierar följande roller:

- **Läsare**: Som standard har alla autentiserade användare i klienten läsbehörighet. 
  - Läs åtkomst till program och certifikatbegäranden. Kan lista och fråga efter program och certifikatbegäranden. Även information om enhetsidentifiering och offentliga certifikat är tillgängliga med läsåtkomst.
- **Författare**: Rollen Writer tilldelas en användare för att lägga till skrivbehörigheter för vissa uppgifter. 
  - Läs-/skrivåtkomst till program och certifikatbegäranden. Kan registrera, uppdatera och avregistrera program. Kan skapa certifikatbegäranden och hämta godkända privata nycklar och certifikat. Kan också ta bort privata nycklar.
- **Godkännare**: Rollen Godkännare tilldelas en användare att godkänna eller avvisa certifikatbegäranden. Rollen innehåller inte någon annan roll.
  - Förutom rollen Godkännare för att komma åt OPC Vault microservice API måste användaren också ha behörigheten nyckelsignering i Azure Key Vault för att kunna signera certifikaten.
  - Rollen Författare och godkännare ska tilldelas olika användare.
  - Den viktigaste rollen för godkännaren är godkännandet av generering och avslag av certifikatbegäranden.
- **Administratör**: Administratörsrollen tilldelas en användare för att hantera certifikatgrupperna. Rollen stöder inte rollen Godkännare, men inkluderar rollen Författare.
  - Administratören kan hantera certifikatgrupperna, ändra konfigurationen och återkalla programcertifikat genom att utfärda en ny lista över återkallade certifikat (CRL).
  - Helst tilldelas rollerna Författare, Godkännare och Administratör till olika användare. För ytterligare säkerhet behöver en användare med rollen Godkännare eller administratör också behörigheten nyckelsignering i Key Vault, utfärda certifikat eller förnya ett certifikatutfärdare.
  - Förutom rollen för mikrotjänstadministration ingår rollen, men är inte begränsad till:
    - Ansvar för att administrera genomförandet av certifikatutfärdarens säkerhetspraxis.
    - Hantering av generering, återkallande och tillfälligt upphävande av certifikat. 
    - Kryptografisk nyckel livscykelhantering (till exempel förnyelse av EmittentENS CA-nycklar).
    - Installation, konfiguration och underhåll av tjänster som driver certifikatutfärdaren.
    - Den dagliga driften av tjänsterna. 
    - CA och säkerhetskopiering och återställning av databaser.

### <a name="other-role-assignments"></a>Andra rolltilldelningar

Tänk också på följande roller när du kör tjänsten:

- Företagsägare i certifikatanskaffningskontraktet med den externa rotcertifikatutfärdaren (till exempel när ägaren köper certifikat från en extern certifikatutfärdare eller driver en certifikatutfärdare som är underordnad en extern certifikatutfärdare).
- Utveckling och validering av certifikatutfärdaren.
- Granskning av granskningsposter.
- Personal som hjälper till att stödja certifikatutfärdaren eller hantera de fysiska anläggningarna och molnanläggningarna, men som inte är direkt betrodda att utföra CA-åtgärder, är i den *behöriga* rollen. Den uppsättning uppgifter som personer i den behöriga rollen tillåts utföra måste också dokumenteras.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>Granska medlemskap i betrodda och auktoriserade roller kvartalsvis

Granska medlemskap i betrodda och auktoriserade roller minst en gång i kvartalet. Se till att uppsättningen personer (för manuella processer) eller tjänstidentiteter (för automatiserade processer) i varje roll hålls till ett minimum.

### <a name="role-separation-between-certificate-requester-and-approver"></a>Rollsepare mellan certifikatbeställare och godkännare

Certifikatutfärdningsprocessen måste tvinga fram rollsepare mellan certifikatbegäranden och certifikatgodkännarens roller (personer eller automatiserade system). Certifikatutfärdandet måste godkännas av en certifikatgodkännareroll som verifierar att certifikatutfärdaren har behörighet att erhålla certifikat. De personer som innehar rollen som certifikatgodkännare måste vara en formellt behörig person.

### <a name="restrict-assignment-of-privileged-roles"></a>Begränsa tilldelning av privilegierade roller

Du bör begränsa tilldelningen av privilegierade roller, till exempel att godkänna medlemskap i gruppen Administratörer och godkännare, till en begränsad uppsättning auktoriserad personal. Alla privilegierade rolländringar måste ha åtkomst återkallad inom 24 timmar. Granska slutligen privilegierade rolltilldelningar kvartalsvis och ta bort onödiga eller utgångna tilldelningar.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>Privilegierade roller bör använda tvåfaktorsautentisering

Använd multifaktorautentisering (kallas även tvåfaktorsautentisering) för interaktiva inloggningar av godkännare och administratörer till tjänsten.

## <a name="certificate-service-operation-guidelines"></a>Riktlinjer för certifikattjänståtgärd

### <a name="operational-contacts"></a>Operativa kontakter

Certifikattjänsten måste ha en uppdaterad säkerhetssvarsplan registrerad, som innehåller detaljerade operativa incidenthanteringskontakter.

### <a name="security-updates"></a>Säkerhetsuppdateringar

Alla system måste övervakas kontinuerligt och uppdateras med de senaste säkerhetsuppdateringarna.

> [!IMPORTANT]
> GitHub-databasen för OPC Vault-tjänsten uppdateras kontinuerligt med säkerhetskorrigeringar. Övervaka dessa uppdateringar och tillämpa dem på tjänsten med jämna mellanrum.

### <a name="security-monitoring"></a>Säkerhetsövervakning

Prenumerera på eller genomföra lämplig säkerhetsövervakning. Du kan till exempel prenumerera på en central övervakningslösning (till exempel Azure Security Center eller Office 365-övervakningslösning) och konfigurera den på rätt sätt för att säkerställa att säkerhetshändelser överförs till övervakningslösningen.

> [!IMPORTANT]
> Som standard distribueras OPC Vault-tjänsten med [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops) som en övervakningslösning. Det rekommenderas starkt att lägga till en säkerhetslösning som [Azure Security Center.](https://azure.microsoft.com/services/security-center/)

### <a name="assess-the-security-of-open-source-software-components"></a>Bedöma säkerheten för programvarukomponenter med öppen källkod

Alla komponenter med öppen källkod som används i en produkt eller tjänst måste vara fria från måttliga eller större säkerhetsproblem.

> [!IMPORTANT]
> Under kontinuerlig integreringsversioner söker GitHub-lagringsplatsen för OPC Vault-tjänsten igenom alla komponenter efter sårbarheter. Övervaka dessa uppdateringar på GitHub och tillämpa dem på tjänsten med jämna mellanrum.

### <a name="maintain-an-inventory"></a>Underhåll ett lager

Underhåll ett tillgångslager för alla produktionsvärdar (inklusive beständiga virtuella datorer), enheter, alla interna IP-adressintervall, VIP-domäner och offentliga DNS-domännamn. När du lägger till eller tar bort ett system, enhets-IP-adress, VIP eller offentlig DNS-domän måste du uppdatera lagret inom 30 dagar.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Inventering av standarddistributionen av Azure OPC Vault-mikrotjänst 

I Azure:
- **App Service Plan:** App serviceplan för tjänstvärdar. Standard S1.
- **Apptjänst** för mikrotjänst: OPC Vault-tjänstvärden.
- **Apptjänst** för exempelprogram: Exempelprogramvärden för OPC Vault.
- **Key Vault Standard:** För att lagra hemligheter och Azure Cosmos DB nycklar för webbtjänster.
- **Key Vault Premium:** För att vara värd för Utfärdarens CA-nycklar, för signeringstjänst och för valvkonfiguration och lagring av privata programnycklar.
- **Azure Cosmos DB**: Databas för program- och certifikatbegäranden. 
- **Application Insights**: (valfritt) Övervakningslösning för webbtjänst och program.
- **Azure AD-programregistrering:** En registrering för exempelprogrammet, tjänsten och kantmodulen.

För molntjänsterna ska alla värdnamn, resursgrupper, resursnamn, prenumerations-ID och klient-ID:er som används för att distribuera tjänsten dokumenteras. 

I Azure IoT Edge eller en lokal IoT Edge-server:
- **OPC Vault IoT Edge-modul:** För att stödja ett fabriksnätverk OPC UA Global Discovery Server. 

För IoT Edge-enheterna ska värdnamnen och IP-adresserna dokumenteras. 

### <a name="document-the-certification-authorities-cas"></a>Dokumentera certifikatutfärdarna

Certifikatutfärdarhierarkins dokumentation måste innehålla alla certifikatutfärdare. Detta inkluderar alla relaterade underordnade certifikatutfärdare, överordnade certifikatutfärdare och rotcertifikatutfärdare, även när de inte hanteras av tjänsten. I stället för formell dokumentation kan du tillhandahålla en uttömmande uppsättning certifikatutfärdare som inte har upphört att gälla.

> [!NOTE]
> Exempelprogrammet OPC Vault stöder hämtning av alla certifikat som används och produceras i tjänsten för dokumentation.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Dokumentera de utfärdade certifikaten av alla certifikatutfärdare

Tillhandahålla en uttömmande uppsättning av alla certifikat som utfärdats under de senaste 12 månaderna.

> [!NOTE]
> Exempelprogrammet OPC Vault stöder hämtning av alla certifikat som används och produceras i tjänsten för dokumentation.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>Dokumentera standardproceduren för att ta bort kryptografiska nycklar på ett säkert sätt

Under en certifikatutfärdars livstid kan nyckelborttagning endast ske sällan. Det är därför ingen användare har behörigheten Borttagning av nyckelvalvscertifikat och varför det inte finns några API:er som exponeras för att ta bort ett utfärdarcertifikatutfärdare. Den manuella standardproceduren för säker borttagning av kryptografiska nycklar för certifikatutfärdare är endast tillgänglig genom direkt åtkomst till Key Vault i Azure-portalen. Du kan också ta bort certifikatgruppen i Key Vault. Om du vill säkerställa omedelbar borttagning inaktiverar du funktionen [för mjuk borttagning av Nyckelvalv.](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

## <a name="certificates"></a>Certifikat

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Certifikaten måste uppfylla minsta certifikatprofil

OPC Vault-tjänsten är en onlinecertifikatutfärdare som utfärdar slutentitetscertifikat till prenumeranter. OPC Vault-mikrotjänsten följer dessa riktlinjer i standardimplementeringen.

- Alla certifikat måste innehålla följande X.509-fält enligt nedan:
  - Innehållet i versionsfältet måste vara v3. 
  - Innehållet i serialNumber-fältet måste innehålla minst 8 byte entropy som erhållits från en FIPS (Federal Information Processing Standards) 140 godkänd slumptalsgenerator.<br>
    > [!IMPORTANT]
    > OPC Vault serienummer är som standard 20 byte och erhålls från operativsystemet kryptografiska slumptalsgenerator. Slumptalsgeneratorn är FIPS 140 godkänd på Windows-enheter, men inte på Linux. Tänk på detta när du väljer en tjänstdistribution som använder Linux virtuella datorer eller Linux docker-behållare, där den underliggande tekniken OpenSSL inte är FIPS 140 godkänd.
  - Fälten IssuerUniqueID och subjectUniqueID får inte finnas.
  - Slutenhetscertifikat måste identifieras med tillägget grundläggande begränsningar, i enlighet med IETF RFC 5280.
  - Fältet pathLenConstraint måste anges till 0 för certifikatutfärdarcertifikatet Utfärdad. 
  - Tillägget Utökad nyckelanvändning måste finnas och måste innehålla den minsta uppsättningen OID-identifierare (Extended Key Usage Object Identifiers). AnyExtendedKeyUsage OID (2.5.29.37.0) får inte anges. 
  - CDP-tillägget (CRL Distribution Point) måste finnas i certifikatutfärdarens utfärdare.<br>
    > [!IMPORTANT]
    > CDP-tillägget finns i OPC Vault CA-certifikat. OPC UA-enheter använder dock anpassade metoder för att distribuera CRLs.
  - Tillägget Informationsåtkomst för myndigheten måste finnas i abonnentcertifikaten.<br>
    > [!IMPORTANT]
    > Tillägget Information Access för myndighet finns i OPC Vault-abonnentcertifikat. OPC UA-enheter använder dock anpassade metoder för att distribuera information om Utfärdare CA.
- Godkända asymmetriska algoritmer, nyckellängder, hash-funktioner och utfyllnadslägen måste användas.
  - RSA och SHA-2 är de enda algoritmer som stöds.
  - RSA kan användas för kryptering, nyckelutbyte och signatur.
  - RSA-kryptering får endast använda utfyllnadslägena OAEP, RSA-KEM eller RSA-PSS.
  - Nyckellängder som är större än eller lika med 2048 bitar krävs.
  - Använd SHA-2-familjen av hash-algoritmer (SHA256, SHA384 och SHA512).
  - RSA Root CA-nycklar med en typisk livslängd som är större än eller lika med 20 år måste vara 4096 bitar eller mer.
  - RSA Issuer CA-nycklar måste vara minst 2048 bitar. Om förfallodatumet för certifikatutfärdarcertifikatet är efter 2030 måste ca-nyckeln vara 4096 bitar eller mer.
- Certifikatets livstid
  - Rotcertifikatutfärdare: Den maximala certifikatgiltighetsperioden för rotcertifikatutfärdare får inte överstiga 25 år.
  - Certifikat för certifikatutfärdare eller certifikat för utfärdare online: Den maximala certifikatgiltighetsperioden för certifikatutfärdare som är online och utfärdar endast abonnentcertifikat får inte överstiga 6 år. För dessa certifikatutfärdare får den relaterade privata signaturnyckeln inte användas längre än 3 år för att utfärda nya certifikat.<br>
    > [!IMPORTANT]
    > Utfärdarcertifikatet, eftersom det genereras i standardmispc Vault-mikrotjänsten utan extern rotcertifikatutfärdare, behandlas som en online-sub-certifikatutfärdare, med respektive krav och livstid. Standardlivstiden är inställd på 5 år, med en nyckellängd som är större än eller lika med 2048.
  - Alla asymmetriska nycklar måste ha en maximal livslängd på 5 år och en rekommenderad livslängd på 1 år.<br>
    > [!IMPORTANT]
    > Som standard har livslängden för programcertifikat som utfärdats med OPC Vault en livstid på 2 år och bör ersättas varje år. 
  - När ett certifikat förnyas förnyas det med en ny nyckel.
- OPC UA-specifika tillägg i certifikat för programinstanser
  - ÄmnetAltName-tillägget innehåller programmet Uri och värdnamn. Dessa kan också omfatta FQDN-, IPv4- och IPv6-adresser.
  - KeyUsage inkluderar digitalSignature, nonRepudiation, keyEncipherment och dataEncipherment.
  - ExtendedKeyUsage innehåller serverAuth och clientAuth.
  - AuthorityKeyIdentifier anges i signerade certifikat.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>Ca-nycklar och certifikat måste uppfylla minimikraven

- **Privata nycklar**: RSA-nycklar måste vara minst 2048 bitar. Om förfallodatumet för certifikatutfärdarcertifikatet är efter 2030 måste ca-nyckeln vara 4096 bitar eller mer.
- **Livslängd**: Den maximala giltighetstiden för certifikat som är online och utfärdar endast abonnentcertifikat får inte överstiga sex år. För dessa certifikatutfärdare får den relaterade privata signaturnyckeln inte användas längre än 3 år för att utfärda nya certifikat.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>CA-nycklar skyddas med hjälp av maskinvarusäkerhetsmoduler

OpcVault använder Azure Key Vault Premium och nycklar skyddas av FIPS 140-2 Hardware Security Modules (HSM). 

De kryptografiska moduler som Key Vault använder, oavsett om det är HSM eller programvara, valideras. Nycklar som skapas eller importeras som HSM-skyddade bearbetas i en HSM, validerad till FIPS 140-2 Nivå 2. Nycklar som skapas eller importeras som programvaruskyddade bearbetas inuti kryptografiska moduler som validerats till FIPS 140-2 Nivå 1.

## <a name="operational-practices"></a>Operativ praxis

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Dokumentera och underhålla standardpraxis för operativa PKI-metoder för certifikatregistrering

Dokumentera och upprätthålla standardrutiner för hur certifikatutfärdare utfärdar certifikat, inklusive: 
- Hur abonnenten identifieras och autentiseras. 
- Hur certifikatbegäran bearbetas och valideras (i förekommande fall, även hur certifikatförnyelse och begäranden om nycklar bearbetas). 
- Hur utfärdade certifikat distribueras till prenumeranterna. 

OPC Vault microservice SOP beskrivs i [OPC Vault-arkitekturen](overview-opc-vault-architecture.md) och [hantera OPC Vault-certifikattjänsten](howto-opc-vault-manage.md). Metoderna följer "OPC Unified Architecture Specification Part 12: Discovery and Global Services".


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Dokumentera och upprätthålla standard operativa PKI-metoder för återkallande av certifikat

Processen för återkallning av certifikat beskrivs i [OPC Vault-arkitekturen](overview-opc-vault-architecture.md) och [hantera OPC Vault-certifikattjänsten](howto-opc-vault-manage.md).
    
### <a name="document-ca-key-generation-ceremony"></a>Dokument CA nyckelgenerering ceremoni 

Nyckelgenereringen utfärdare i OPC Vault-mikrotjänsten förenklas på grund av det säkra lagringsutrymmet i Azure Key Vault. Mer information finns [i Hantera OPC Vault-certifikattjänsten](howto-opc-vault-manage.md).

Men när du använder en extern root-certifikatutfärdare måste en certifikatutgenereringsceremoni för certifikatutfärdare uppfylla följande krav.

Certifikatutdelningsceremonin för ca-nyckel måste utföras mot ett dokumenterat skript som innehåller minst följande objekt: 
- Definition av roller och deltagaransvar.
- Godkännande för genomförande av CA nyckelgenerering ceremoni.
- Kryptografisk hårdvara och aktiveringsmaterial som krävs för ceremonin.
- Maskinvaruförberedelse (inklusive uppdatering av tillgångs-/konfigurationsinformation och signering).
- Installation av operativsystem.
- Specifika steg som utförs under certifikatutfärdarens nyckelgenereringsceremoni, till exempel: 
  - Installation och konfiguration av CA-program.
  - CA nyckelgenerering.
  - Säkerhetskopiera CA-nyckel.
  - Signering av CA-certifikat.
  - Import av signerade nycklar i tjänstens skyddade HSM.
  - CA-systemet stängs av.
  - Beredning av material för förvaring.


## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig att hantera OPC Vault på ett säkert sätt kan du:

> [!div class="nextstepaction"]
> [Säkra OPC UA-enheter med OPC Vault](howto-opc-vault-secure.md)
