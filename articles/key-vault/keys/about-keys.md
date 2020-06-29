---
title: Om Azure Key Vault nycklar – Azure Key Vault
description: Översikt över Azure Key Vault REST-gränssnittet och information om utvecklare för nycklar.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: b9803726bf3a54eb31d3c2ebaddce11fb96472be
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85413731"
---
# <a name="about-azure-key-vault-keys"></a>Om Azure Key Vault nycklar

Azure Key Vault stöder flera nyckel typer och algoritmer och aktiverar användning av HSM (Hardware Security modules) för nycklar med hög värde.

Kryptografiska nycklar i Key Vault representeras som JSON-webbnyckel [JWK]-objekt. JOSE-specifikationerna (JSON) och Java Script Object signing and Encryption () är: JavaScript Object Notation

-   [JSON-webbnyckel (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON-webbkryptering (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON-webbalgoritmer (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON-webbsignatur (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

De grundläggande JWK/JWA-specifikationerna utökas också för att aktivera nyckel typer som är unika för Key Vault implementeringen. Om du exempelvis importerar nycklar med hjälp av HSM-leverantörsspecifik paketering, möjliggör säker transport av nycklar som bara kan användas i Key Vault HSM: er. 

Azure Key Vault stöder både program skyddade och HSM-skyddade nycklar:

- **Program skyddade nycklar**: en nyckel som bearbetas i program vara med Key Vault, men krypteras i vila med en system nyckel som finns i en HSM. Klienter kan importera en befintlig RSA-eller EC-nyckel (Elliptic Curve) eller begära att Key Vault generera en.
- **HSM-potected-nycklar**: en nyckel som bearbetas i en HSM-modul (Hardware Security Module). Nycklarna skyddas i en av Key Vault HSM-säkerhetsvärldaren (det finns en säkerhets värld per geografi för att underhålla isoleringen). Klienter kan importera en RSA-eller EC-nyckel, i program varu skydds formulär eller genom att exportera från en kompatibel HSM-enhet. Klienter kan också begära Key Vault att generera en nyckel. Den här nyckel typen lägger till key_hsm-attributet i JWK för att hämta HSM-nyckel materialet.

Mer information om geografiska gränser finns [Microsoft Azure säkerhets Center](https://azure.microsoft.com/support/trust-center/privacy/)  

## <a name="cryptographic-protection"></a>Kryptografiskt skydd

Key Vault stöder endast RSA-och Elliptic-kurv nycklar. 

-   **EG**: Elliptic kurv nyckel för program vara – skyddad.
-   **EC-HSM**: "hård" Elliptic kurv nyckel.
-   **RSA**: programvaru-skyddad RSA-nyckel.
-   **RSA-HSM**: "hård" RSA-nyckel.

Key Vault stöder RSA-nycklar av storlekarna 2048, 3072 och 4096. Key Vault stöder Elliptic kurv nyckel typer P-256, P-384, P-521 och P-256 KB (SECP256K1).

De kryptografiska moduler som Key Vault använder, om HSM eller program vara, är FIPS (Federal Information Processing Standards) som verifieras. Du behöver inte göra något särskilt för att köra i FIPS-läge. Nycklar som **skapas** eller **importeras** som HSM-skyddade bearbetas i en HSM, verifieras till FIPS 140-2 nivå 2. Nycklar som **skapas** eller **importeras** som program varu skyddade bearbetas i kryptografiska moduler validerade till FIPS 140-2 nivå 1.

###  <a name="ec-algorithms"></a>EC-algoritmer
 Följande algoritms identifierare stöds med EG-och EC-HSM-nycklar i Key Vault. 

#### <a name="curve-types"></a>Kurv typer

-   **P-256** – NIST kurva P-256, definieras i [DSS FIPS pub-186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256 k** – SECP256K1 för SEC-kurvan, definierat vid [SEK 2: rekommenderade Elliptic för kurv parametrar](https://www.secg.org/sec2-v2.pdf).
-   **P-384** – NIST kurva P-384, definieras i [DSS FIPS pub-186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** – NIST kurva P-521, definieras i [DSS FIPS pub-186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>SIGNERA/VERIFIERA

-   **ES256** -ECDSA för SHA-256-sammandrag och nycklar som skapats med kurva P-256. Den här algoritmen beskrivs på [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** -ECDSA för SHA-256-sammandrag och nycklar som skapats med kurva P-256 kB. Den här algoritmen väntar på standardisering.
-   **ES384** -ECDSA för SHA-384-sammandrag och nycklar som skapats med kurva P-384. Den här algoritmen beskrivs på [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** -ECDSA för SHA-512-sammandrag och nycklar som skapats med kurva P-521. Den här algoritmen beskrivs på [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>RSA-algoritmer  
 Följande algoritms identifierare stöds med RSA-och RSA-HSM-nycklar i Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, KRYPTERA/DEKRYPTERA

-   **RSA1_5** -RSAES-PKCS1-V1_5 [RFC3447] nyckel kryptering  
-   **RSA-OAEP** – RSAES med optimalt OAEP (asymmetrisk krypterings utfyllnad) [RFC3447], med standard parametrarna som anges i RFC 3447 i avsnitt A. 2.1. Dessa standard parametrar använder en hash-funktion i SHA-1 och en mask-funktion för MGF1 med SHA-1.  

#### <a name="signverify"></a>SIGNERA/VERIFIERA

-   **PS256** -RSASSA-PSS med sha-256 och MGF1 med sha-256, enligt beskrivningen i [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS384** -RSASSA-PSS med sha-384 och MGF1 med sha-384, enligt beskrivningen i [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS512** -RSASSA-PSS med sha-512 och MGF1 med sha-512, enligt beskrivningen i [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **RS256** -RSASSA-PKCS-V1_5 använder SHA-256. Det program som angavs Digest-värde måste beräknas med SHA-256 och måste vara 32 byte långt.  
-   **RS384** -RSASSA-PKCS-V1_5 använder SHA-384. Det program som angavs Digest-värde måste beräknas med SHA-384 och måste vara 48 byte långt.  
-   **RS512** -RSASSA-PKCS-V1_5 använder SHA-512. Det program som angavs Digest-värde måste beräknas med SHA-512 och måste vara 64 byte långt.  
-   **RSNULL** – se [RFC2437], ett specialiserat användnings fall för att aktivera vissa TLS-scenarier.  

##  <a name="key-operations"></a>Nyckel åtgärder

Key Vault stöder följande åtgärder på nyckel objekt:  

-   **Skapa**: tillåter att en klient skapar en nyckel i Key Vault. Nyckelns värde genereras av Key Vault och lagras, och frigörs inte till klienten. Asymmetriska nycklar kan skapas i Key Vault.  
-   **Importera**: tillåter att en klient importerar en befintlig nyckel till Key Vault. Asymmetriska nycklar kan importeras till Key Vault att använda ett antal olika förpacknings metoder inom en JWK-konstruktion. 
-   **Uppdatera**: tillåter en klient med tillräcklig behörighet att ändra de metadata (nyckelattribut) som är associerade med en nyckel som tidigare lagrats i Key Vault.  
-   **Ta bort**: tillåter en klient med tillräcklig behörighet att ta bort en nyckel från Key Vault.  
-   **Lista**: tillåter att en klient listar alla nycklar i en specifik Key Vault.  
-   **List versioner**: tillåter att en klient listar alla versioner av en specifik nyckel i en specifik Key Vault.  
-   **Get**: tillåter att en klient hämtar offentliga delar av en viss nyckel i en Key Vault.  
-   **Säkerhets kopiering**: exporterar en nyckel i ett skyddat formulär.  
-   **Återställ**: importerar en tidigare säkerhetskopierad nyckel.  

Mer information finns i [nyckel åtgärder i referensen Key Vault REST API](/rest/api/keyvault).  

När en nyckel har skapats i Key Vault kan följande kryptografiska åtgärder utföras med nyckeln:  

-   **Logga och verifiera**: den här åtgärden är "signera hash" eller "verifiera hash", eftersom Key Vault inte stöder hashing av innehåll som en del av att skapa signaturer. Program ska Hasha data för att signeras lokalt och sedan begära att Key Vault signera hashen. Verifiering av signerade hash-värden stöds som en bekvämlighets åtgärd för program som kanske inte har åtkomst till [offentligt] nyckel material. För bästa program prestanda bör du kontrol lera att åtgärderna utförs lokalt.  
-   **Nyckel kryptering/inbäddning**: en nyckel som lagras i Key Vault kan användas för att skydda en annan nyckel, vanligt vis en symmetrisk innehålls krypterings nyckel (CEK). När nyckeln i Key Vault är asymmetrisk, används nyckel kryptering. Till exempel motsvarar RSA-OAEP och WRAPKEY/UNWRAPKEY-åtgärderna dekryptera/DEKRYPTERA. När nyckeln i Key Vault är symmetrisk används nyckel brytning. Till exempel AES-KW. WRAPKEY-åtgärden stöds som en bekvämlighet för program som kanske inte har åtkomst till [offentligt] nyckel material. För bästa prestanda bör WRAPKEY åtgärder utföras lokalt.  
-   **Kryptera och dekryptera**: en nyckel som lagras i Key Vault kan användas för att kryptera eller dekryptera ett enda data block. Storleken på blocket bestäms av nyckel typen och den valda krypteringsalgoritmen. Krypterings åtgärden tillhandahålls för enkelhetens skull, för program som kanske inte har åtkomst till [offentligt] nyckel material. För bästa prestanda bör KRYPTERINGs åtgärder utföras lokalt.  

Även om WRAPKEY/UNWRAPKEY med asymmetriska nycklar kan verka överflödig (eftersom åtgärden motsvarar kryptering/dekryptering) är det viktigt att använda DISTINCT-åtgärder. Distinktionen ger semantisk och avskiljande av dessa åtgärder och konsekvens när andra nyckel typer stöds av tjänsten.  

Key Vault stöder inte EXPORT åtgärder. När en nyckel har skapats i systemet går den inte att extrahera eller så har dess nyckel material ändrats. Användare av Key Vault kan dock kräva sin nyckel för andra användnings fall, till exempel när det har tagits bort. I det här fallet kan de använda säkerhets kopierings-och återställnings åtgärderna för att exportera/importera nyckeln i ett skyddat formulär. Nycklar som skapats av säkerhets kopierings åtgärden kan inte användas utanför Key Vault. Alternativt kan IMPORT åtgärden användas mot flera Key Vault instanser.  

Användare kan begränsa de kryptografiska åtgärder som Key Vault stöder per nyckel med hjälp av egenskapen key_ops för JWK-objektet.  

Mer information om JWK-objekt finns i [JSON-webbnyckel (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

## <a name="key-attributes"></a>Nyckelattribut

Utöver nyckelmaterialet kan följande attribut anges. I en JSON-begäran krävs attributen nyckelord och klammerparenteser, {}, även om det inte finns några angivna attribut.  

- *aktive rad*: boolesk, valfritt, standardvärdet är **True**. Anger om nyckeln är aktive rad och användbar för kryptografiska åtgärder. Det *aktiverade* attributet används tillsammans med *NBF* och *exp*. När en åtgärd sker mellan *NBF* och *exp*, tillåts den endast om *aktive rad* är inställd på **True**. Åtgärder utanför *NBF*  /  *exp* -fönstret tillåts inte automatiskt, förutom vissa åtgärds typer under [vissa förhållanden](#date-time-controlled-operations).
- *NBF*: IntDate, valfritt, standard är nu. Attributet *NBF* (inte före) anger tiden före vilken nyckeln inte får användas för kryptografiska åtgärder, förutom vissa åtgärds typer under [särskilda villkor](#date-time-controlled-operations). Bearbetningen av *NBF* -attributet kräver att aktuellt datum/tid måste vara efter eller lika med det icke-före-datum/-tid som anges i *NBF* -attributet. Key Vault kan ge vissa små Leeway, vanligt vis inte fler än några minuter, för att kontona ska kunna användas för klock skevning. Värdet måste vara ett tal som innehåller ett IntDate-värde.  
- *exp*: IntDate, valfritt, standard är "alltid". Attributet *exp* (förfallo tid) anger förfallo tid för eller efter vilken nyckeln inte får användas för kryptografisk drift, förutom vissa åtgärds typer under [särskilda villkor](#date-time-controlled-operations). Bearbetningen av *exp* -attributet kräver att aktuellt datum/tid måste vara före det datum/tid som anges i attributet *exp* . Key Vault kan vara en del liten Leeway, vanligt vis inte mer än några minuter, för att kunna använda klock skevning. Värdet måste vara ett tal som innehåller ett IntDate-värde.  

Det finns ytterligare skrivskyddade attribut som ingår i alla svar som innehåller viktiga attribut:  

- *skapad*: IntDate, valfritt. Attributet *create* anger när den här versionen av nyckeln skapades. Värdet är null för nycklar som skapats innan det här attributet läggs till. Värdet måste vara ett tal som innehåller ett IntDate-värde.  
- *uppdaterad*: IntDate, valfritt. Det *uppdaterade* attributet indikerar när den här versionen av nyckeln uppdaterades. Värdet är null för nycklar som senast uppdaterades innan det här attributet lades till. Värdet måste vara ett tal som innehåller ett IntDate-värde.  

Mer information om IntDate och andra data typer finns i [om nycklar, hemligheter och certifikat: [data typer](../general/about-keys-secrets-certificates.md#data-types).

### <a name="date-time-controlled-operations"></a>Kontrollerade åtgärder för datum/tid

Nycklar som inte är giltiga och som har upphört att gälla utanför *NBF*  /  *exp* -fönstret fungerar för att **dekryptera**, **packa**upp och **Verifiera** åtgärder (returnerar inte 403, förbjudet). Rationellt för att använda tillstånd som inte är giltigt är att tillåta att en nyckel testas före produktions användningen. Rationellt för att använda tillståndet upphör att gälla är att tillåta återställnings åtgärder för data som skapades när nyckeln var giltig. Du kan också inaktivera åtkomst till en nyckel med Key Vault-principer eller genom att uppdatera det *aktiverade* nyckelattributet till **falskt**.

Mer information om data typer finns i [data typer](../general/about-keys-secrets-certificates.md#data-types).

Mer information om andra möjliga attribut finns i [JSON-webbnyckeln (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

## <a name="key-tags"></a>Nyckel etiketter

Du kan ange ytterligare programspecifika metadata i form av taggar. Key Vault stöder upp till 15 taggar, som var och en kan ha ett 256-namn och ett värde på 256.  

>[!Note]
>Taggarna kan läsas av en anropare om de har *listan* eller *får* behörighet till den objekt typen (nycklar, hemligheter eller certifikat).

##  <a name="key-access-control"></a>Nyckelåtkomstkontroll

Åtkomst kontroll för nycklar som hanteras av Key Vault anges på nivån för en Key Vault som fungerar som behållare för nycklar. Åtkomst kontroll principen för nycklar är distinkt från åtkomst kontroll principen för hemligheter i samma Key Vault. Användare kan skapa ett eller flera valv för att hålla nycklar, och de krävs för att upprätthålla lämplig segmentering och hantering av nycklar. Åtkomst kontroll för nycklar är oberoende av åtkomst kontroll för hemligheter.  

Följande behörigheter kan beviljas, per användare/tjänstens huvud konto, i åtkomst kontroll posten i ett valv. Dessa behörigheter speglar noggrant de åtgärder som tillåts för ett nyckel objekt.  Att bevilja åtkomst till ett tjänst huvud namn i Key Vault är en Databasmigrering-åtgärd och det förblir detsamma för alla Azure-prenumerationer. Du kan använda den för att distribuera så många certifikat som du vill. 

- Behörigheter för nyckel hanterings åtgärder
  - *Hämta*: Läs den offentliga delen av en nyckel, plus dess attribut
  - *lista*: visar nycklar eller versioner av en nyckel som lagras i ett nyckel valv
  - *Uppdatera*: uppdatera attributen för en nyckel
  - *skapa*: skapa nya nycklar
  - *Importera*: importera en nyckel till ett nyckel valv
  - *ta bort*: ta bort nyckelobjektet
  - *återställa*: Återställ en borttagen nyckel
  - *säkerhets kopiering*: säkerhetskopiera en nyckel i ett nyckel valv
  - *återställning*: återställa en säkerhets kopie rad nyckel till ett nyckel valv

- Behörigheter för kryptografiska åtgärder
  - *dekryptera*: Använd nyckeln för att ta bort skyddet för en sekvens med byte
  - *kryptera*: Använd nyckeln för att skydda en godtycklig sekvens med byte
  - *unwrapKey*: Använd nyckeln för att ta bort skyddet för figursatta symmetriska nycklar
  - *wrapKey*: Använd nyckeln för att skydda en symmetrisk nyckel
  - *Verifiera*: Använd nyckeln för att verifiera sammandrag  
  - *tecken*: Använd nyckeln för att signera sammandrag
    
- Behörigheter för privilegierade åtgärder
  - *Rensa*: Rensa (ta bort permanent) en borttagen nyckel

Mer information om hur du arbetar med nycklar finns [i nyckel åtgärder i Key Vault REST API referens](/rest/api/keyvault). Information om hur du etablerar behörigheter finns i [valv – skapa eller uppdatera](/rest/api/keyvault/vaults/createorupdate) och [valv – uppdatera åtkomst princip](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="next-steps"></a>Nästa steg

- [Om Key Vault](../general/overview.md)
- [Om nycklar, hemligheter och certifikat](../general/about-keys-secrets-certificates.md)
- [Om hemligheter](../secrets/about-secrets.md)
- [Om certifikat](../certificates/about-certificates.md)
- [Autentisering, begär Anden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
