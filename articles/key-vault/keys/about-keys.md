---
title: Om Azure Key Vault-nycklar – Azure Key Vault
description: Översikt över AZURE Key Vault REST-gränssnittet och utvecklarinformation för nycklar.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 3d89275e1418035fed8aad3ffddd8def2c1d59ce
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686049"
---
# <a name="about-azure-key-vault-keys"></a>Om Azure Key Vault-nycklar

Azure Key Vault stöder flera nyckeltyper och algoritmer och möjliggör användning av HSM (Hardware Security Modules) för nycklar med högt värde.

Kryptografiska nycklar i Key Vault representeras som JSON Web Key [JWK]-objekt. Specifikationerna För JavaScript-objekt notation (JSON) och JavaScript-objektsignering och kryptering (JOSE) är:

-   [JSON webbnyckel (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Webbkryptering (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Webbalgoritmer (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON webbsignatur (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

Bas-JWK/JWA-specifikationerna utökas också för att aktivera nyckeltyper som är unika för Key Vault-implementeringen. Om du till exempel importerar nycklar med HSM-leverantörsspecifika förpackningar kan du begränsa säker transport av nycklar som endast får användas i Key Vault-HSM-moduler. 

Azure Key Vault stöder både mjuka och hårda nycklar:

- **"Soft"-nycklar:** En nyckel som bearbetas i programvara av Key Vault, men krypteras i vila med hjälp av en systemnyckel som finns i en HSM. Klienter kan importera en befintlig RSA- eller EC-nyckel (Elliptic Curve) eller begära att Key Vault genererar en.
- **"Hard"-nycklar:** En nyckel som bearbetas i en HSM (Hardware Security Module). Dessa nycklar är skyddade i en av Key Vault HSM Security Worlds (det finns en Security World per geografi för att upprätthålla isolering). Klienter kan importera en RSA- eller EC-nyckel, i mjuk form eller genom att exportera från en kompatibel HSM-enhet. Klienter kan också begära Key Vault för att generera en nyckel. Den här nyckeltypen lägger till key_hsm-attributet till JWK-erhållet för att bära HSM-nyckelmaterialet.

Mer information om geografiska gränser finns i [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)  

## <a name="cryptographic-protection"></a>Kryptografiskt skydd

Key Vault stöder endast RSA- och Elliptic Curve-tangenter. 

-   **EG**: "Mjuk" Elliptic Curve nyckel.
-   **EC-HSM**: "Hård" Elliptic Curve nyckel.
-   **RSA**: "Mjuk" RSA-nyckel.
-   **RSA-HSM**: "Hård" RSA-nyckel.

Key Vault stöder RSA-nycklar med storlekarna 2048, 3072 och 4096. Key Vault stöder nyckeltyperna P-256, P-384, P-521 och P-256K (SECP256K1).

De kryptografiska moduler som Key Vault använder, oavsett om det är HSM eller programvara, är FIPS (Federal Information Processing Standards) validerade. Du behöver inte göra något speciellt för att köras i FIPS-läge. Nycklar som **skapas** eller **importeras** som HSM-skyddade bearbetas i en HSM, validerad till FIPS 140-2 Nivå 2. Nycklar som **skapas** eller **importeras** som programvaruskyddade bearbetas inuti kryptografiska moduler som validerats till FIPS 140-2 Nivå 1.

###  <a name="ec-algorithms"></a>EG-algoritmer
 Följande algoritmidentifierare stöds med EG- och EC-HSM-nycklar i Key Vault. 

#### <a name="curve-types"></a>Kurvtyper

-   **P-256** - NIST-kurvan P-256, definierad på [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256K** - SEC-kurvan SECP256K1, definierad vid [SEK 2: Rekommenderade elliptiska kurvdomänparametrar](https://www.secg.org/sec2-v2.pdf).
-   **P-384** - NIST-kurvan P-384, definierad på [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** - NIST-kurvan P-521, definierad på [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>UNDERTECKNA/VERIFIERA

-   **ES256** - ECDSA för SHA-256-sammanfattningar och nycklar skapade med kurvan P-256. Denna algoritm beskrivs på [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** - ECDSA för SHA-256 sammanfattningar och nycklar som skapats med kurvan P-256K. Den här algoritmen väntar på standardisering.
-   **ES384** - ECDSA för SHA-384 sammanfattningar och nycklar som skapats med kurvan P-384. Denna algoritm beskrivs på [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** - ECDSA för SHA-512-sammandrag och nycklar skapade med kurvan P-521. Denna algoritm beskrivs på [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>RSA-algoritmer  
 Följande algoritmidentifierare stöds med RSA- och RSA-HSM-nycklar i Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, KRYPTERA/DEKRYPTERA

-   **RSA1_5** - RSAES-PKCS1-V1_5 [RFC3447] nyckelkryptering  
-   **RSA-OAEP** - RSAES med optimal asymmetrisk krypteringsutfyllnad (OAEP) [RFC3447], med standardparametrar som anges av RFC 3447 i avsnitt A.2.1. Dessa standardparametrar använder en hash-funktion av SHA-1 och en maskgenereringsfunktion av MGF1 med SHA-1.  

#### <a name="signverify"></a>UNDERTECKNA/VERIFIERA

-   **PS256** - RSASSA-PSS med SHA-256 och MGF1 med SHA-256, enligt beskrivningen i [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS384** - RSASSA-PSS med SHA-384 och MGF1 med SHA-384, enligt beskrivningen i [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS512** - RSASSA-PSS med SHA-512 och MGF1 med SHA-512, enligt beskrivningen i [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **RS256** - RSASSA-PKCS-v1_5 med SHA-256. Det angivna rötvärdet måste beräknas med SHA-256 och måste vara 32 byte i längd.  
-   **RS384** - RSASSA-PKCS-v1_5 med SHA-384. Det angivna rötvärdet måste beräknas med SHA-384 och måste vara 48 byte i längd.  
-   **RS512** - RSASSA-PKCS-v1_5 med SHA-512. Det angivna rötvärdet måste beräknas med SHA-512 och måste vara 64 byte i längd.  
-   **RSNULL** - Se [RFC2437], ett specialiserat användningsfall för att aktivera vissa TLS-scenarier.  

##  <a name="key-operations"></a>Viktiga operationer

Key Vault stöder följande åtgärder för viktiga objekt:  

-   **Skapa:** Tillåter en klient att skapa en nyckel i Key Vault. Värdet på nyckeln genereras av Key Vault och lagras och frisläpps inte till klienten. Asymmetriska nycklar kan skapas i Key Vault.  
-   **Import**: Tillåter en klient att importera en befintlig nyckel till Key Vault. Asymmetriska nycklar kan importeras till Key Vault med hjälp av ett antal olika förpackningsmetoder i en JWK-konstruktion. 
-   **Uppdatering:** Tillåter en klient med tillräcklig behörighet att ändra metadata (nyckelattribut) som är associerade med en nyckel som tidigare lagrats i Key Vault.  
-   **Ta bort**: Tillåter en klient med tillräcklig behörighet att ta bort en nyckel från Key Vault.  
-   **Lista**: Tillåter en klient att lista alla nycklar i ett visst Nyckelvalv.  
-   **Listversioner**: Tillåter en klient att lista alla versioner av en viss nyckel i ett visst Key Vault.  
-   **Hämta:** Tillåter en klient att hämta de offentliga delarna av en viss nyckel i ett Key Vault.  
-   **Säkerhetskopiering**: Exporterar en nyckel i skyddad form.  
-   **Återställning:** Importerar en tidigare säkerhetskopierad nyckel.  

Mer information finns [i Nyckelåtgärder i REST-API-referensen för Nyckelvalvet](/rest/api/keyvault).  

När en nyckel har skapats i Key Vault kan följande kryptografiska åtgärder utföras med hjälp av nyckeln:  

-   **Signera och verifiera:** Strängt, den här åtgärden är "sign hash" eller "verifiera hash", eftersom Key Vault inte stöder hashing av innehåll som en del av signaturskapandet. Program bör hash data som ska undertecknas lokalt, sedan begära att Key Vault underteckna hash. Verifiering av signerade hashar stöds som en bekvämlighetsåtgärd för program som kanske inte har tillgång till [offentligt] nyckelmaterial. För bästa programprestanda kontrollerar du att åtgärder utförs lokalt.  
-   **Nyckelkryptering / omslag:** En nyckel som lagras i Key Vault kan användas för att skydda en annan nyckel, vanligtvis en symmetrisk innehåll krypteringsnyckel (CEK). När nyckeln i Key Vault är asymmetrisk används nyckelkryptering. RSA-OAEP och WRAPKEY/UNWRAPKEY-åtgärderna motsvarar till exempel KRYPTERA/DEKRYPTERA. När nyckeln i Key Vault är symmetrisk används nyckelomslagning. Till exempel AES-KW. WRAPKEY-åtgärden stöds som en bekvämlighet för program som kanske inte har åtkomst till [offentligt] nyckelmaterial. För bästa programprestanda bör WRAPKEY-åtgärder utföras lokalt.  
-   **Kryptera och dekryptera:** En nyckel som lagras i Key Vault kan användas för att kryptera eller dekryptera ett enda datablock. Storleken på blocket bestäms av nyckeltypen och den valda krypteringsalgoritmen. Kryptera-åtgärden tillhandahålls för enkelhetens skull, för program som kanske inte har tillgång till [offentligt] nyckelmaterial. För bästa programprestanda bör kryptera åtgärder utföras lokalt.  

Medan WRAPKEY/UNWRAPKEY med hjälp av asymmetriska nycklar kan verka överflödigt (eftersom åtgärden motsvarar KRYPTERA/DEKRYPTERA), är det viktigt att använda olika åtgärder. Distinktionen ger semantisk och auktoriseringsseparation av dessa åtgärder och konsekvens när andra nyckeltyper stöds av tjänsten.  

Key Vault stöder inte EXPORTåtgärder. När en nyckel har etablerats i systemet kan den inte extraheras eller dess nyckelmaterial ändras. Användare av Key Vault kan dock kräva sina nycklar för andra användningsfall, till exempel efter att det har tagits bort. I det här fallet kan de använda säkerhetskopierings- och återställningsåtgärderna för att exportera/importera nyckeln i skyddad form. Nycklar som skapats av säkerhetskopieringsåtgärden kan inte kan aktiveras utanför Key Vault. Alternativt kan importåtgärden användas mot flera Key Vault-instanser.  

Användare kan begränsa någon av de kryptografiska åtgärder som Key Vault stöder per nyckel med hjälp av egenskapen key_ops för JWK-objektet.  

Mer information om JWK-objekt finns i [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

## <a name="key-attributes"></a>Nyckelattribut

Utöver nyckelmaterialet kan följande attribut anges. I en JSON-begäran krävs nyckelordet attribut och klammerparenteser, {' '}, även om inga attribut har angetts.  

- *aktiverad:* booleska, valfria, standard är **sant**. Anger om nyckeln är aktiverad och användbar för kryptografiska åtgärder. Attributet *enabled* används tillsammans med *nbf* och *exp*. När en åtgärd inträffar mellan *nbf* och *exp*tillåts den bara om *aktiverad* är inställd på **true**. Åtgärder utanför *nbf* / *exp-fönstret* tillåts automatiskt, med undantag för vissa driftstyper under särskilda [förhållanden](#date-time-controlled-operations).
- *nbf*: IntDate, valfritt, standard är nu. Attributet *nbf* (inte före) identifierar den tid före vilken nyckeln INTE FÅR användas för kryptografiska åtgärder, med undantag för vissa operationstyper under [särskilda förhållanden](#date-time-controlled-operations). Bearbetningen av *nbf-attributet* kräver att det aktuella datumet/tiden måste vara efter eller lika med det datum/tid som anges i *attributet nbf.* Key Vault kan ge några små spelrum, normalt inte mer än några minuter, att ta hänsyn till klocka skeva. Dess värde MÅSTE vara ett tal som innehåller ett intdate-värde.  
- *exp:* IntDate, valfritt, standard är "för evigt". Attributet *exp* (expiration time) identifierar utgångstiden på eller efter vilken nyckeln INTE får användas för kryptografisk drift, med undantag för vissa operationstyper under [särskilda förhållanden](#date-time-controlled-operations). Bearbetningen av *exp-attributet* kräver att det aktuella datumet/tiden måste infalla före utgångsdatumet/tid som anges i *exp-attributet.* Key Vault kan ge några små spelrum, vanligtvis inte mer än några minuter, att ta hänsyn till klocka skeva. Dess värde MÅSTE vara ett tal som innehåller ett intdate-värde.  

Det finns ytterligare skrivskyddade attribut som ingår i alla svar som innehåller nyckelattribut:  

- *skapad*: IntDate, valfritt. Attributet *skapad* anger när den här versionen av nyckeln skapades. Värdet är null för nycklar som skapats före tillägget av det här attributet. Dess värde MÅSTE vara ett tal som innehåller ett intdate-värde.  
- *uppdaterad*: IntDate, valfritt. Det *uppdaterade* attributet anger när den här versionen av nyckeln uppdaterades. Värdet är null för nycklar som senast uppdaterades före tillägget av det här attributet. Dess värde MÅSTE vara ett tal som innehåller ett intdate-värde.  

Mer information om IntDate och andra datatyper finns i [Om nycklar, hemligheter och certifikat: [Datatyper](../general/about-keys-secrets-certificates.md#data-types).

### <a name="date-time-controlled-operations"></a>Datum-tidsstyrda operationer

Ännu inte giltiga och utgångna nycklar, utanför *nbf* / *exp-fönstret,* kommer att fungera för **dekryptera,** packa **upp**och **verifiera** åtgärder (kommer inte tillbaka 403, Förbjudet). Grunden för att använda det ännu inte giltiga tillståndet är att tillåta att en nyckel testas före produktionsanvändning. Grunden för att använda det utgångna tillståndet är att tillåta återställningsåtgärder på data som skapades när nyckeln var giltig. Du kan också inaktivera åtkomst till en nyckel med hjälp av Key Vault-principer eller genom att uppdatera *attributet aktiverad* nyckel till **false**.

Mer information om datatyper finns i [Datatyper](../general/about-keys-secrets-certificates.md#data-types).

Mer information om andra möjliga attribut finns i [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

## <a name="key-tags"></a>Viktiga taggar

Du kan ange ytterligare programspecifika metadata i form av taggar. Key Vault stöder upp till 15 taggar, som alla kan ha ett 256 teckennamn och ett 256 teckenvärde.  

>[!Note]
>Taggar kan läsas av en uppringare om de har *listan* eller *får* behörighet till objekttypen (nycklar, hemligheter eller certifikat).

##  <a name="key-access-control"></a>Nyckelåtkomstkontroll

Åtkomstkontroll för nycklar som hanteras av Key Vault finns på nivån för ett nyckelvalv som fungerar som behållare med nycklar. Åtkomstkontrollprincipen för nycklar skiljer sig från åtkomstkontrollprincipen för hemligheter i samma Nyckelvalv. Användare kan skapa ett eller flera valv för att hålla nycklar och måste underhålla scenariot lämplig segmentering och hantering av nycklar. Åtkomstkontroll för nycklar är oberoende av åtkomstkontroll för hemligheter.  

Följande behörigheter kan beviljas, per användare /tjänst huvudnamn, i nycklarna åtkomstkontroll posten på ett valv. Dessa behörigheter speglar nära de åtgärder som tillåts för ett nyckelobjekt.  Att bevilja åtkomst till ett tjänsthuvudnamn i nyckelvalv är en engångsåtgärd och den förblir densamma för alla Azure-prenumerationer. Du kan använda den för att distribuera så många certifikat du vill. 

- Behörigheter för nyckelhanteringsåtgärder
  - *få:* Läs den offentliga delen av en nyckel, plus dess attribut
  - *lista*: Lista nycklar eller versioner av en nyckel som lagras i ett nyckelvalv
  - *uppdatering:* Uppdatera attributen för en nyckel
  - *skapa:* Skapa nya nycklar
  - *importera*: Importera en nyckel till ett nyckelvalv
  - *ta bort*: Ta bort nyckelobjektet
  - *återställa*: Återställa en borttagen nyckel
  - *backup*: Säkerhetskopiera en nyckel i ett nyckelvalv
  - *återställa:* Återställa en säkerhetskopierad nyckel till ett nyckelvalv

- Behörigheter för kryptografiska åtgärder
  - *dekryptera*: Använd nyckeln för att ta bort en sekvens av byte
  - *kryptera*: Använd nyckeln för att skydda en godtycklig sekvens av byte
  - *unwrapKey*: Använd nyckeln för att ta bort inslagna symmetriska nycklar
  - *wrapKey:* Använd nyckeln för att skydda en symmetrisk nyckel
  - *verifiera*: Använd nyckeln för att verifiera sammanfattningar  
  - *tecken*: Använd nyckeln för att signera sammanfattningar
    
- Behörigheter för privilegierade åtgärder
  - *rensa:* Rensa (permanent ta bort) en borttagen nyckel

Mer information om hur du arbetar med nycklar finns [i Nyckelåtgärder i REST-API-referensen för Nyckelvalvet](/rest/api/keyvault). Information om hur du upprättar behörigheter finns i [Arkiv - Skapa eller Uppdatera](/rest/api/keyvault/vaults/createorupdate) och [Valv - Uppdatera åtkomstprincip](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="next-steps"></a>Nästa steg

- [Om Key Vault](../general/overview.md)
- [Om nycklar, hemligheter och certifikat](../general/about-keys-secrets-certificates.md)
- [Om hemligheter](../secrets/about-secrets.md)
- [Om certifikat](../certificates/about-certificates.md)
- [Autentisering, begäranden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
