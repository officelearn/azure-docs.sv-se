---
title: Om Azure Key Vault nycklar, hemligheter och certifikat – Azure Key Vault
description: Översikt över Azure Key Vault REST-gränssnittet och information om utvecklare för nycklar, hemligheter och certifikat.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 9bbbcc38116c5681e3b5c867690c296f60507ad1
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196118"
---
# <a name="about-keys-secrets-and-certificates"></a>Om nycklar, hemligheter och certifikat

Azure Key Vault gör det möjligt för Microsoft Azure program och användare att lagra och använda flera typer av hemliga/viktiga data:

- Kryptografiska nycklar: stöder flera nyckel typer och algoritmer och möjliggör användning av HSM (Hardware Security modules) för nycklar med hög värde. 
- Hemligheter: ger säker lagring av hemligheter, t. ex. lösen ord och databas anslutnings strängar.
- Certifikat: stöder certifikat, som bygger på nycklar och hemligheter och lägger till en automatiserad förnyelse funktion.
- Azure Storage: kan hantera nycklar för ett Azure Storage konto åt dig. Internt Key Vault kan ange (Sync) nycklar med ett Azure Storage konto och återskapa (rotera) nycklarna med jämna mellanrum. 

Mer allmän information om Key Vault finns i [Vad är Azure Key Vault?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>Azure Key Vault

I följande avsnitt finns allmän information som gäller för implementeringen av den Key Vault tjänsten.

### <a name="supporting-standards"></a>Stöd för standarder

JOSE-specifikationerna (JSON) och Java Script Object signing and Encryption () är viktiga bakgrunds information. JavaScript Object Notation  

-   [JSON-webbnyckel (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)  
-   [JSON-webbkryptering (JWE)](https://tools.ietf.org/html/draft-ietf-jose-json-web-encryption-40)  
-   [JSON-webbalgoritmer (JWA)](https://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms-40)  
-   [JSON-webbsignatur (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41)  

### <a name="data-types"></a>Datatyper

Se JOSE-specifikationerna för relevanta data typer för nycklar, kryptering och signering.  

-   **algoritm** – en algoritm som stöds för en nyckel åtgärd, till exempel RSA1_5  
-   **chiffertexten-värde** -cipher text-oktetter, kodade med Base64URL  
-   **sammandrag-värde** – utdata från en hash-algoritm kodas med hjälp av Base64URL  
-   **nyckel typ** – en av de nyckel typer som stöds, till exempel RSA (Rivest-Shamir-Adleman).  
-   **klartext – värde** – oformaterade oktetter, kodade med Base64URL  
-   **Signature-värde** – utdata från en Signeringsalgoritm som är kodad med Base64URL  
-   **base64URL** – a BASE64URL [RFC4648] kodat binärt värde  
-   **boolesk** -antingen sant eller falskt  
-   **Identitet** – en identitet från Azure Active Directory (AAD).  
-   **IntDate** – ett JSON-standardvärde som representerar antalet sekunder från 1970-01-01T0:0: 0Z UTC tills angivet UTC-datum/tid. Se RFC3339 för information om datum/tider, i allmänhet och UTC i synnerhet.  

### <a name="objects-identifiers-and-versioning"></a>Objekt, identifierare och versions hantering

Objekt som lagras i Key Vault versioner skapas när en ny instans av ett objekt skapas. Varje version tilldelas en unik identifierare och URL. När ett objekt skapas första gången får den ett unikt versions-ID och markerat som den aktuella versionen av objektet. Om du skapar en ny instans med samma objekt namn får det nya objektet ett unikt versions-ID, vilket gör att den blir den aktuella versionen.  

Objekt i Key Vault kan åtgärdas med den aktuella identifieraren eller en versions bestämd identifierare. Till exempel kan en nyckel med namnet `MasterKey`utföra åtgärder med den aktuella identifieraren så att systemet använder den senaste tillgängliga versionen. Att utföra åtgärder med den versions bara identifierade identifieraren gör att systemet använder den aktuella versionen av objektet.  

Objekt identifieras unikt inom Key Vault med hjälp av en URL. Inga två objekt i systemet har samma URL, oavsett Geo-plats. Den fullständiga URL: en till ett objekt kallas för objekt-ID. URL: en består av ett prefix som identifierar Key Vault, objekt typ, användardefinierat objekt namn och en objekt version. Objekt namnet är Skift läges okänsligt och oföränderligt. Identifierare som inte innehåller objekt versionen kallas för bas identifierare.  

Mer information finns i [autentisering, begär Anden och svar](authentication-requests-and-responses.md)

En objekt identifierare har följande allmänna format:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Där:  

|||  
|-|-|  
|`keyvault-name`|Namnet på ett nyckel valv i Microsoft Azure Key Vault-tjänsten.<br /><br /> Key Vault namn väljs av användaren och är globalt unika.<br /><br /> Key Vault namn måste vara en 3-24-tecken sträng som bara innehåller 0-9, a-z, A-Z och-.|  
|`object-type`|Objektets typ, antingen "nycklar" eller "hemligheter".|  
|`object-name`|Ett `object-name` är ett namn som användaren anger och måste vara unikt inom en Key Vault. Namnet måste vara en 1-127-tecken sträng som bara innehåller 0-9, a-z, A-Z och-.|  
|`object-version`|En `object-version` är en systemgenererad 32 tecken Strängs identifierare som används för att adressera en unik version av ett objekt.|  

## <a name="key-vault-keys"></a>Key Vault nycklar

### <a name="keys-and-key-types"></a>Nycklar och nyckel typer

Kryptografiska nycklar i Key Vault representeras som JSON-webbnyckel [JWK]-objekt. De grundläggande JWK/JWA-specifikationerna utökas också för att aktivera nyckel typer som är unika för Key Vault implementeringen. Om du exempelvis importerar nycklar med hjälp av HSM-leverantörsspecifik paketering, möjliggör säker transport av nycklar som bara kan användas i Key Vault HSM: er.  

- **"Mjuka" nycklar**: en nyckel som bearbetas i program vara med Key Vault, men krypteras i vila med en system nyckel som finns i en HSM. Klienter kan importera en befintlig RSA-eller EC-nyckel (Elliptic Curve) eller begära att Key Vault generera en.
- **"Hårda" nycklar**: en nyckel som bearbetas i en HSM-modul (Hardware Security Module). Nycklarna skyddas i en av Key Vault HSM-säkerhetsvärldaren (det finns en säkerhets värld per geografi för att underhålla isoleringen). Klienter kan importera en RSA-eller EC-nyckel, i mjuk form eller genom att exportera från en kompatibel HSM-enhet. Klienter kan också begära Key Vault att generera en nyckel. Den här nyckel typen lägger till key_hsm-attributet i JWK för att hämta HSM-nyckel materialet.

     Mer information om geografiska gränser finns [Microsoft Azure säkerhets Center](https://azure.microsoft.com/support/trust-center/privacy/)  

Key Vault stöder endast RSA-och Elliptic-kurv nycklar. 

-   **EG**: "mjuk" Elliptic kurv nyckel.
-   **EC-HSM**: "hård" Elliptic kurv nyckel.
-   **RSA**: "mjuk" RSA-nyckel.
-   **RSA-HSM**: "hård" RSA-nyckel.

Key Vault stöder RSA-nycklar av storlekarna 2048, 3072 och 4096. Key Vault stöder Elliptic kurv nyckel typer P-256, P-384, P-521 och P-256 KB (SECP256K1).

### <a name="cryptographic-protection"></a>Kryptografiskt skydd

De kryptografiska moduler som Key Vault använder, om HSM eller program vara, är FIPS (Federal Information Processing Standards) som verifieras. Du behöver inte göra något särskilt för att köra i FIPS-läge. Nycklar som **skapas** eller **importeras** som HSM-skyddade bearbetas i en HSM, verifieras till FIPS 140-2 nivå 2. Nycklar som **skapas** eller **importeras** som program varu skyddade bearbetas i kryptografiska moduler validerade till FIPS 140-2 nivå 1. Mer information finns i [nycklar och nyckel typer](#keys-and-key-types).

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

-   **RS256** -RSASSA-PKCS-V1_5 använder SHA-256. Det program som angavs Digest-värde måste beräknas med SHA-256 och måste vara 32 byte långt.  
-   **RS384** -RSASSA-PKCS-V1_5 använder SHA-384. Det program som angavs Digest-värde måste beräknas med SHA-384 och måste vara 48 byte långt.  
-   **RS512** -RSASSA-PKCS-V1_5 använder SHA-512. Det program som angavs Digest-värde måste beräknas med SHA-512 och måste vara 64 byte långt.  
-   **RSNULL** – se [RFC2437], ett specialiserat användnings fall för att aktivera vissa TLS-scenarier.  

###  <a name="key-operations"></a>Nyckel åtgärder

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

-   **Logga och verifiera**: den här åtgärden är "signera hash" eller "verifiera hash", eftersom Key Vault inte stöder hashing av innehåll som en del av att skapa signaturer. Program ska Hasha data för att signeras lokalt och sedan begära att Key Vault signera hashen. Verifiering av signerade hash-värden stöds som en bekvämlighets åtgärd för program som kanske inte har åtkomst till [offentligt] nyckel material. För bästa program prestanda kontrollerar du att åtgärder utförs lokalt.  
-   **Nyckel kryptering/inbäddning**: en nyckel som lagras i Key Vault kan användas för att skydda en annan nyckel, vanligt vis en symmetrisk innehålls krypterings nyckel (CEK). När nyckeln i Key Vault är asymmetrisk, används nyckel kryptering. Till exempel motsvarar RSA-OAEP och WRAPKEY/UNWRAPKEY-åtgärderna dekryptera/DEKRYPTERA. När nyckeln i Key Vault är symmetrisk används nyckel brytning. Till exempel AES-KW. WRAPKEY-åtgärden stöds som en bekvämlighet för program som kanske inte har åtkomst till [offentligt] nyckel material. För bästa prestanda bör WRAPKEY åtgärder utföras lokalt.  
-   **Kryptera och dekryptera**: en nyckel som lagras i Key Vault kan användas för att kryptera eller dekryptera ett enda data block. Storleken på blocket bestäms av nyckel typen och den valda krypteringsalgoritmen. Krypterings åtgärden tillhandahålls för enkelhetens skull, för program som kanske inte har åtkomst till [offentligt] nyckel material. För bästa prestanda bör krypterings åtgärder utföras lokalt.  

Även om WRAPKEY/UNWRAPKEY med asymmetriska nycklar kan verka överflödig (eftersom åtgärden motsvarar kryptering/dekryptering) är det viktigt att använda DISTINCT-åtgärder. Distinktionen ger semantisk och avskiljande av dessa åtgärder och konsekvens när andra nyckel typer stöds av tjänsten.  

Key Vault stöder inte EXPORT åtgärder. När en nyckel har skapats i systemet går den inte att extrahera eller så har dess nyckel material ändrats. Användare av Key Vault kan dock kräva sin nyckel för andra användnings fall, till exempel när det har tagits bort. I det här fallet kan de använda säkerhets kopierings-och återställnings åtgärderna för att exportera/importera nyckeln i ett skyddat formulär. Nycklar som skapats av säkerhets kopierings åtgärden kan inte användas utanför Key Vault. Alternativt kan IMPORT åtgärden användas mot flera Key Vault instanser.  

Användare kan begränsa de kryptografiska åtgärder som Key Vault stöder per nyckel med hjälp av egenskapen key_ops för JWK-objektet.  

Mer information om JWK-objekt finns i [JSON-webbnyckel (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

###  <a name="key-attributes"></a>Nyckelattribut

Förutom nyckel materialet kan följande attribut anges. I en JSON-begäran krävs attributen nyckelord och klammerparenteser, {}, även om det inte finns några angivna attribut.  

- *aktive rad*: boolesk, valfritt, standardvärdet är **True**. Anger om nyckeln är aktive rad och användbar för kryptografiska åtgärder. Det *aktiverade* attributet används tillsammans med *NBF* och *exp*. När en åtgärd sker mellan *NBF* och *exp*, tillåts den endast om *aktive rad* är inställd på **True**. Åtgärder utanför *nbf* / *exp* -fönstret tillåts inte automatiskt, förutom vissa åtgärds typer under [särskilda villkor](#date-time-controlled-operations).
- *NBF*: IntDate, valfritt, standard är nu. Attributet *NBF* (inte före) anger tiden före vilken nyckeln inte får användas för kryptografiska åtgärder, förutom vissa åtgärds typer under [särskilda villkor](#date-time-controlled-operations). Bearbetningen av *NBF* -attributet kräver att aktuellt datum/tid måste vara efter eller lika med det icke-före-datum/-tid som anges i *NBF* -attributet. Key Vault kan ge vissa små Leeway, vanligt vis inte fler än några minuter, för att kontona ska kunna användas för klock skevning. Värdet måste vara ett tal som innehåller ett IntDate-värde.  
- *exp*: IntDate, valfritt, standard är "alltid". Attributet *exp* (förfallo tid) anger förfallo tid för eller efter vilken nyckeln inte får användas för kryptografisk drift, förutom vissa åtgärds typer under [särskilda villkor](#date-time-controlled-operations). Bearbetningen av *exp* -attributet kräver att aktuellt datum/tid måste vara före det datum/tid som anges i attributet *exp* . Key Vault kan vara en del liten Leeway, vanligt vis inte mer än några minuter, för att kunna använda klock skevning. Värdet måste vara ett tal som innehåller ett IntDate-värde.  

Det finns ytterligare skrivskyddade attribut som ingår i alla svar som innehåller viktiga attribut:  

- *skapad*: IntDate, valfritt. Attributet *create* anger när den här versionen av nyckeln skapades. Värdet är null för nycklar som skapats innan det här attributet läggs till. Värdet måste vara ett tal som innehåller ett IntDate-värde.  
- *uppdaterad*: IntDate, valfritt. Det *uppdaterade* attributet indikerar när den här versionen av nyckeln uppdaterades. Värdet är null för nycklar som senast uppdaterades innan det här attributet lades till. Värdet måste vara ett tal som innehåller ett IntDate-värde.  

Mer information om IntDate och andra data typer finns i [data typer](#data-types)  

#### <a name="date-time-controlled-operations"></a>Kontrollerade åtgärder för datum/tid

Nycklar som inte är giltiga och som har upphört att gälla utanför *nbf* / *exp* -fönstret fungerar för att **dekryptera**, **packa**upp och **Verifiera** åtgärder (returnerar inte 403, förbjudet). Rationellt för att använda tillstånd som inte är giltigt är att tillåta att en nyckel testas före produktions användningen. Rationellt för att använda tillståndet upphör att gälla är att tillåta återställnings åtgärder för data som skapades när nyckeln var giltig. Du kan också inaktivera åtkomst till en nyckel med Key Vault-principer eller genom att uppdatera det *aktiverade* nyckelattributet till **falskt**.

Mer information om data typer finns i [data typer](#data-types).

Mer information om andra möjliga attribut finns i [JSON-webbnyckeln (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

### <a name="key-tags"></a>Nyckel etiketter

Du kan ange ytterligare programspecifika metadata i form av taggar. Key Vault stöder upp till 15 taggar, som var och en kan ha ett 256-namn och ett värde på 256.  

>[!Note]
>Taggarna kan läsas av en anropare om de har *listan* eller *får* behörighet till den objekt typen (nycklar, hemligheter eller certifikat).

###  <a name="key-access-control"></a>Nyckelåtkomstkontroll

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

## <a name="key-vault-secrets"></a>Key Vault hemligheter 

### <a name="working-with-secrets"></a>Arbeta med hemligheter

I ett utvecklings perspektiv kan Key Vault API: er acceptera och returnera hemliga värden som strängar. Internt Key Vault lagrar och hanterar hemligheter som sekvenser av oktetter (8-bitars byte), med en maximal storlek på 25k byte. Tjänsten Key Vault tillhandahåller inte semantik för hemligheter. Den accepterar bara data, krypterar den, lagrar den och returnerar en hemlig identifierare ("ID"). Identifieraren kan användas för att hämta hemligheten vid ett senare tillfälle.  

För mycket känsliga data bör klienterna överväga ytterligare skydds nivåer för data. Ett exempel är att kryptera data med hjälp av en separat skydds nyckel före lagring i Key Vault.  

Key Vault stöder också ett contentType-fält för hemligheter. Klienter kan ange innehålls typen för en hemlighet för att under lätta tolkningen av hemliga data när den hämtas. Den maximala längden för det här fältet är 255 tecken. Det finns inga fördefinierade värden. Den föreslagna användningen är som ett tips för att tolka hemliga data. Till exempel kan en implementering lagra både lösen ord och certifikat som hemligheter, och sedan använda det här fältet för att skilja. Det finns inga fördefinierade värden.  

### <a name="secret-attributes"></a>Hemliga attribut

Utöver hemliga data kan följande attribut anges:  

- *exp*: IntDate, valfritt, standard är för **alltid**. Attributet *exp* (förfallo tid) anger förfallo tid för eller efter vilken hemliga data inte ska hämtas, förutom i [särskilda situationer](#date-time-controlled-operations). Det här fältet används endast i **informations** syfte eftersom det informerar användare av Key Vault-tjänsten att en viss hemlighet inte får användas. Värdet måste vara ett tal som innehåller ett IntDate-värde.   
- *NBF*: IntDate, valfritt, standard är **nu**. Attributet *NBF* (inte före) anger den tid före vilken hemliga data inte ska hämtas, förutom i [särskilda situationer](#date-time-controlled-operations). Det här fältet används endast i **informations** syfte. Värdet måste vara ett tal som innehåller ett IntDate-värde. 
- *aktive rad*: boolesk, valfritt, standardvärdet är **True**. Det här attributet anger om hemliga data kan hämtas. Det aktiverade attributet används tillsammans med *NBF* och *exp* när en åtgärd sker mellan *NBF* och *exp*, men det är bara tillåtet om aktive rad är inställt på **Sant**. Åtgärder utanför *NBF* och *exp* -fönstret tillåts inte automatiskt, förutom i [vissa situationer](#date-time-controlled-operations).  

Det finns ytterligare skrivskyddade attribut som ingår i alla svar som innehåller hemliga attribut:  

- *skapad*: IntDate, valfritt. Attributet created anger när den här versionen av hemligheten skapades. Det här värdet är null för hemligheter som skapats innan det här attributet läggs till. Värdet måste vara ett tal som innehåller ett IntDate-värde.  
- *uppdaterad*: IntDate, valfritt. Det uppdaterade attributet indikerar när den här versionen av hemligheten uppdaterades. Det här värdet är null för hemligheter som senast uppdaterades innan det här attributet lades till. Värdet måste vara ett tal som innehåller ett IntDate-värde.

#### <a name="date-time-controlled-operations"></a>Kontrollerade åtgärder för datum/tid

En hemlig åtgärd för **hämtning** fungerar för ej ännu giltiga och utgångna hemligheter, utanför *NBF* / *exp* -fönstret. Att anropa en hemlig åtgärd för **hämtning** , för en icke-giltig hemlighet, kan användas i test syfte. Hämtning av (**få fram**) en utgånget hemligt kan användas för återställnings åtgärder.

Mer information om data typer finns i [data typer](#data-types).  

### <a name="secret-access-control"></a>Åtkomstkontroll till hemlighet

Access Control för hemligheter som hanteras i Key Vault ges på nivån för den Key Vault som innehåller dessa hemligheter. Principen för åtkomst kontroll för hemligheter är distinkt från åtkomst kontroll principen för nycklar i samma Key Vault. Användare kan skapa ett eller flera valv för att hålla hemligheter, och de krävs för att upprätthålla lämplig segmentering och hantering av hemligheter.   

Följande behörigheter kan användas, per huvud konto, i åtkomst kontroll posten för hemligheter i ett valv, och i nära spegling av de åtgärder som tillåts på ett hemligt objekt:  

- Behörigheter för hemliga hanterings åtgärder
  - *Hämta*: Läs en hemlighet  
  - *lista*: visar en lista över hemligheter eller versioner av en hemlighet som lagras i en Key Vault  
  - *Ange*: skapa en hemlighet  
  - *ta bort*: ta bort en hemlighet  
  - *återställa*: Återställ en borttagen hemlighet
  - *säkerhets kopiering*: säkerhetskopiera en hemlighet i ett nyckel valv
  - *återställning*: återställa en säkerhetskopierad hemlighet till ett nyckel valv

- Behörigheter för privilegierade åtgärder
  - *Rensa*: Rensa (ta bort permanent) en borttagen hemlighet

Mer information om hur du arbetar med hemligheter finns [i avsnittet om hemliga åtgärder i referensen Key Vault REST API](/rest/api/keyvault). Information om hur du etablerar behörigheter finns i [valv – skapa eller uppdatera](/rest/api/keyvault/vaults/createorupdate) och [valv – uppdatera åtkomst princip](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Hemliga Taggar  
Du kan ange ytterligare programspecifika metadata i form av taggar. Key Vault stöder upp till 15 taggar, som var och en kan ha ett 256-namn och ett värde på 256.  

>[!Note]
>Taggarna kan läsas av en anropare om de har *listan* eller *får* behörighet till den objekt typen (nycklar, hemligheter eller certifikat).

## <a name="key-vault-certificates"></a>Key Vault certifikat

Stöd för Key Vault certifikat ger till gång till hantering av x509-certifikat och följande beteenden:  

-   Tillåter en certifikat ägare att skapa ett certifikat via en Key Vault skapande process eller genom att importera ett befintligt certifikat. Innehåller både självsignerade och certifikat utfärdare som genererade certifikat.
-   Tillåter en Key Vault certifikat ägare att implementera säker lagring och hantering av X509-certifikat utan interaktion med privat nyckel material.  
-   Tillåter en certifikat ägare att skapa en princip som dirigerar Key Vault för att hantera livs cykeln för ett certifikat.  
-   Tillåter certifikat ägare att tillhandahålla kontakt information för meddelanden om livs cykel händelser för förfallo datum och förnyelse av certifikat.  
-   Har stöd för automatisk förnyelse med valda utfärdare – Key Vault partner X509 certifikat leverantörer/certifikat utfärdare.

>[!Note]
>Leverantörer/myndigheter som inte är partner tillåtna är också tillåtna, men stöder inte funktionen för automatisk förnyelse.

### <a name="composition-of-a-certificate"></a>Sammansättning av ett certifikat

När ett Key Vault certifikat skapas, skapas även en adresserad nyckel och hemlighet med samma namn. Key Vault-nyckeln tillåter nyckel åtgärder och Key Vault hemligheten kan hämta certifikatets värde som en hemlighet. Ett Key Vault certifikat innehåller även metadata för offentliga x509-certifikat.  

Identifieraren och versionen av certifikat liknar nycklarna och hemligheterna. En speciell version av en adresserad nyckel och hemlighet som skapats med Key Vault certifikat version är tillgänglig i Key Vault certifikatets svar.
 
![Certifikat är komplexa objekt](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Export bar eller icke-export bar nyckel

När ett Key Vault-certifikat skapas, kan det hämtas från den adresser bara hemligheten med den privata nyckeln i antingen PFX-eller PEM-format. Principen som används för att skapa certifikatet måste ange att nyckeln kan exporteras. Om principen anger att det inte går att exportera, är den privata nyckeln inte en del av värdet när den hämtas som en hemlighet.  

Den adresser bara nyckeln blir mer relevant med icke exporter bara KV-certifikat. Den adresser bara KV-nyckelns åtgärder mappas från fältet nyckel *användning* i kv-certifikat policyn som används för att skapa kv-certifikatet.  

Två typer av nycklar stöds – *RSA* eller *RSA HSM* med certifikat. Exportable tillåts endast med RSA, stöds inte av RSA HSM.  

### <a name="certificate-attributes-and-tags"></a>Certifikatets attribut och Taggar

Förutom certifikatets metadata, en adresserad nyckel och adresserbar hemlighet, innehåller ett Key Vault certifikat också attribut och taggar.  

#### <a name="attributes"></a>Attribut

Certifikatets attribut speglas till attribut för den adress bara nyckel och hemlighet som skapas när KV-certifikatet skapas.  

Ett Key Vault certifikat har följande attribut:  

-   *aktive rad*: boolesk, valfritt, standardvärdet är **True**. Kan anges för att ange om certifikat data kan hämtas som hemliga eller fungerar som en nyckel. Används också tillsammans med *NBF* och *exp* när en åtgärd sker mellan *NBF* och *exp*, och är bara tillåten om aktive rad är inställd på True. Åtgärder utanför *NBF* och *exp* -fönstret tillåts inte automatiskt.  

Det finns ytterligare skrivskyddade attribut som ingår i svaret:

-   *skapad*: IntDate: anger när den här versionen av certifikatet skapades.  
-   *uppdaterad*: IntDate: anger när den här versionen av certifikatet uppdaterades.  
-   *exp*: IntDate: innehåller värdet för det utgångna datumet för x509-certifikatet.  
-   *NBF*: IntDate: innehåller värdet för datumet för x509-certifikatet.  

> [!Note] 
> Om ett Key Vault certifikat upphör att gälla, är det adresser bara nyckeln och hemligheten blir oanvändbar.  

#### <a name="tags"></a>Taggar

 Klientens angivna ord lista med nyckel värdes par, liknar Taggar i nycklar och hemligheter.  

 > [!Note]
> Taggarna kan läsas av en anropare om de har *listan* eller *får* behörighet till den objekt typen (nycklar, hemligheter eller certifikat).

### <a name="certificate-policy"></a>Certifikat princip

En certifikat princip innehåller information om hur du skapar och hanterar livs cykeln för ett Key Vault certifikat. När ett certifikat med privat nyckel importeras till nyckel valvet skapas en standard princip genom att läsa in x509-certifikatet.  

När ett Key Vault-certifikat skapas från grunden måste en princip anges. Principen anger hur du skapar den här Key Vault certifikat versionen eller nästa Key Vault certifikat version. När en princip har upprättats krävs det inte med efterföljande skapande åtgärder för framtida versioner. Det finns bara en instans av en princip för alla versioner av ett Key Vault certifikat.  

En certifikat princip innehåller följande information på hög nivå:  

-   Egenskaper för X509-certifikat: innehåller ämnes namn, alternativa namn för certifikat mottagare och andra egenskaper som används för att skapa en x509-certifikatbegäran.  
-   Nyckel egenskaper: innehåller nyckel typer, nyckel längd, exporter bara och återanvända nyckel fält. Dessa fält instruerar nyckel valvet om hur man genererar en nyckel.  
-   Hemliga egenskaper: innehåller hemliga egenskaper som innehålls typ för adresser bar hemlighet för att generera det hemliga värdet, för att hämta certifikat som en hemlighet.  
-   Livs längds åtgärder: innehåller livs längds åtgärder för KV-certifikatet. Varje livs längds åtgärd innehåller:  

     - Utlösare: anges via dagar före förfallo datum eller livs längds intervall i procent  

     - Åtgärd: Ange åtgärds typ – *emailContacts* eller *autoförnyelse*  

-   Utfärdare: parametrar om den certifikat utfärdare som ska användas för att utfärda x509-certifikat.  
-   Princip-attribut: innehåller attribut som är associerade med principen  

#### <a name="x509-to-key-vault-usage-mapping"></a>X509 för att Key Vault användnings mappning

Följande tabell visar mappningen av principen för x509-nyckel användning till effektiva nyckel åtgärder i en nyckel som skapats som en del av ett Key Vault-certifikat skapas.

|**Användnings flaggor för X509-nyckel**|**Key Vault Key OPS**|**Standard beteende**|
|----------|--------|--------|
|DataEncipherment|kryptera, dekryptera| Saknas |
|DecipherOnly|innehållet| Saknas  |
|DigitalSignature|signera, verifiera| Key Vault standard utan användnings specifikation när certifikat skapas | 
|EncipherOnly|encrypt| Saknas |
|KeyCertSign|signera, verifiera|Saknas|
|KeyEncipherment|wrapKey, unwrapKey| Key Vault standard utan användnings specifikation när certifikat skapas | 
|Oavvislig het|signera, verifiera| Saknas |
|cRLSign|signera, verifiera| Saknas |

### <a name="certificate-issuer"></a>Certifikatutfärdare

Ett Key Vault certifikat objekt innehåller en konfiguration som används för att kommunicera med en vald certifikat utfärdare-Provider för att beställa x509-certifikat.  

-   Key Vault partner med följande providers för certifikat utfärdare för TLS/SSL-certifikat

|**Providernamn**|**Platser**|
|----------|--------|
|DigiCert|Stöds i alla Key Vault service-platser i det offentliga molnet och Azure Government|
|GlobalSign|Stöds i alla Key Vault service-platser i det offentliga molnet och Azure Government|

Innan du kan skapa en certifikat utfärdare i en Key Vault måste du utföra följande steg 1 och 2 för att kunna utföra åtgärder.  

1. Publicera till certifikat utfärdare (CA)-leverantörer  

    -   En organisations administratör måste ombord på företaget (t. ex. Contoso) med minst en CA-Provider.  

2. Admin skapar beställarens autentiseringsuppgifter för Key Vault att registrera (och förnya) TLS/SSL-certifikat  

    -   Tillhandahåller den konfiguration som ska användas för att skapa ett Issuer-objekt för providern i nyckel valvet  

Mer information om hur du skapar Issuer-objekt från certifikat portalen finns i [bloggen för Key Vault certifikat](https://aka.ms/kvcertsblog)  

Key Vault gör det möjligt att skapa flera Issuer-objekt med en annan konfiguration av Issuer-providern. När ett Issuer-objekt har skapats kan namnet refereras till i en eller flera certifikat principer. Om du refererar till Issuer-objektet instrueras Key Vault att använda konfigurationen som anges i Issuer-objektet när du begär x509-certifikatet från CA-providern när certifikatet skapas och förnyas.  

Issuer-objekt skapas i valvet och kan bara användas med KV-certifikat i samma valv.  

### <a name="certificate-contacts"></a>Certifikat kontakter

Certifikat kontakter innehåller kontakt information för att skicka meddelanden som utlöses av händelser för certifikat livs längd. Kontakt information delas av alla certifikat i nyckel valvet. Ett meddelande skickas till alla angivna kontakter för en händelse för ett certifikat i nyckel valvet.  

Om ett certifikats princip ställs in på automatisk förnyelse skickas ett meddelande vid följande händelser.  

- Innan certifikat förnyas
- Efter förnyelse av certifikat, anger om certifikatet har förnyats eller om det uppstod ett fel, vilket kräver manuell förnyelse av certifikatet.  

  När en certifikat princip som anges ska förnyas manuellt (endast e-post) skickas ett meddelande när det är dags att förnya certifikatet.  

### <a name="certificate-access-control"></a>Certifikat Access Control

 Åtkomst kontroll för certifikat hanteras av Key Vault och tillhandahålls av Key Vault som innehåller dessa certifikat. Åtkomst kontroll principen för certifikat skiljer sig från principerna för åtkomst kontroll för nycklar och hemligheter i samma Key Vault. Användare kan skapa ett eller flera valv för att lagra certifikat, för att upprätthålla lämplig segmentering och hantering av certifikat.  

 Följande behörigheter kan användas, per huvud konto, i åtkomst kontroll posten hemligheter i ett nyckel valv och speglar noggrant de åtgärder som tillåts för ett hemligt objekt:  

- Behörigheter för certifikat hanterings åtgärder
  - *Hämta*: hämta den aktuella certifikat versionen eller en version av ett certifikat 
  - *lista*: visar aktuella certifikat eller versioner av ett certifikat  
  - *Uppdatera*: uppdatera ett certifikat
  - *skapa*: skapa ett Key Vault certifikat
  - *Importera*: Importera certifikat material till ett Key Vault certifikat
  - *ta bort*: ta bort ett certifikat, dess princip och alla dess versioner  
  - *återställa*: Återställ ett borttaget certifikat
  - *säkerhets kopiering*: säkerhetskopiera ett certifikat i ett nyckel valv
  - *återställa*: Återställ ett säkerhetskopierat certifikat till ett nyckel valv
  - *managecontacts*: hantera Key Vault certifikats kontakter  
  - *manageissuers*: hantera Key Vault certifikat utfärdare/utfärdare
  - *getissuers*: Hämta ett certifikats myndigheter/utfärdare
  - *listissuers*: Visa en lista över certifikatets myndigheter/utfärdare  
  - *setissuers*: skapa eller uppdatera ett Key Vault certifikats myndigheter/utfärdare  
  - *deleteissuers*: ta bort en Key Vault certifikatets myndigheter/utfärdare  
 
- Behörigheter för privilegierade åtgärder
  - *Rensa*: Rensa (ta bort permanent) ett borttaget certifikat

Mer information finns i [certifikat åtgärderna i Key Vault REST API referens](/rest/api/keyvault). Information om hur du etablerar behörigheter finns i [valv – skapa eller uppdatera](/rest/api/keyvault/vaults/createorupdate) och [valv – uppdatera åtkomst princip](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="azure-storage-account-key-management"></a>Azure Storage konto nyckel hantering

Key Vault kan hantera Azure Storage-konto nycklar:

- Internt Key Vault kan ange (Sync) nycklar med ett Azure Storage-konto. 
- Key Vault återskapas (roterar) nycklarna med jämna mellanrum.
- Nyckel värden returneras aldrig som svar på anroparen.
- Key Vault hanterar nycklar för både lagrings konton och klassiska lagrings konton.

Mer information finns i [Azure Key Vault lagrings konto nycklar](key-vault-ovw-storage-keys.md)

### <a name="storage-account-access-control"></a>Åtkomst kontroll för lagrings konto

Följande behörigheter kan användas när du auktoriserar en användare eller ett programs huvud konto för att utföra åtgärder på ett hanterat lagrings konto:  

- Behörigheter för hanterat lagrings konto och SaS-definitions åtgärder
  - *Get*: hämtar information om ett lagrings konto 
  - *lista*: visar en lista över lagrings konton som hanteras av en Key Vault
  - *Uppdatera*: uppdatera ett lagrings konto
  - *ta bort*: ta bort ett lagrings konto  
  - *återställa*: Återställ ett borttaget lagrings konto
  - *säkerhets kopiering*: säkerhetskopiera ett lagrings konto
  - *återställa*: Återställ ett säkerhetskopierat lagrings konto till en Key Vault
  - *Ange*: skapa eller uppdatera ett lagrings konto
  - *regeneratekey*: återskapa ett angivet nyckel värde för ett lagrings konto
  - *hämtar*till: Hämta information om en SAS-definition för ett lagrings konto
  - *lister*: lista över lagrings-SAS-definitioner för ett lagrings konto
  - *borttagningar*: ta bort en SAS-definition från ett lagrings konto
  - *Setas*: skapa eller uppdatera en ny SAS-definition/-attribut för ett lagrings konto

- Behörigheter för privilegierade åtgärder
  - *Rensa*: Rensa (ta bort permanent) ett hanterat lagrings konto

Mer information finns i [lagrings konto åtgärder i referensen Key Vault REST API](/rest/api/keyvault). Information om hur du etablerar behörigheter finns i [valv – skapa eller uppdatera](/rest/api/keyvault/vaults/createorupdate) och [valv – uppdatera åtkomst princip](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Se även

- [Autentisering, begär Anden och svar](authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](/azure/key-vault/key-vault-developers-guide)
