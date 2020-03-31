---
title: Om Azure Key Vault nycklar, hemligheter och certifikat - Azure Key Vault
description: Översikt över Azure Key Vault REST-gränssnittet och utvecklarinformation för nycklar, hemligheter och certifikat.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: dd8be482009e067bf9016cc8e351fc42a2db39c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271738"
---
# <a name="about-keys-secrets-and-certificates"></a>Om nycklar, hemligheter och certifikat

Med Azure Key Vault kan Microsoft Azure-program och användare lagra och använda flera typer av hemliga/viktiga data:

- Kryptografiska nycklar: Stöder flera nyckeltyper och algoritmer och möjliggör användning av HSM (Hardware Security Modules) för nycklar med högt värde. 
- Hemligheter: Ger säker lagring av hemligheter, till exempel lösenord och databasanslutningssträngar.
- Certifikat: Stöder certifikat som bygger ovanpå nycklar och hemligheter och lägger till en automatisk förnyelsefunktion.
- Azure Storage: Kan hantera nycklar för ett Azure Storage-konto åt dig. Internt kan Key Vault lista (synkronisera) nycklar med ett Azure Storage-konto och återskapa (rotera) nycklarna regelbundet. 

Mer allmän information om Key Vault finns i [Vad är Azure Key Vault?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>Azure Key Vault

Följande avsnitt innehåller allmän information som gäller för implementeringen av Key Vault-tjänsten.

### <a name="supporting-standards"></a>Stödjande standarder

Specifikationerna JavaScript Object Notation (JSON) och JavaScript Object Signing and Encryption (JOSE) är viktig bakgrundsinformation.  

-   [JSON webbnyckel (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)  
-   [JSON Webbkryptering (JWE)](https://tools.ietf.org/html/draft-ietf-jose-json-web-encryption-40)  
-   [JSON Webbalgoritmer (JWA)](https://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms-40)  
-   [JSON webbsignatur (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41)  

### <a name="data-types"></a>Datatyper

Se JOSE-specifikationerna för relevanta datatyper för nycklar, kryptering och signering.  

-   **algoritm** - en algoritm som stöds för en nyckelåtgärd, till exempel RSA1_5  
-   **ciphertext-värde** - chiffertextoktetter, kodade med Base64URL  
-   **digest-värde** - utdata från en hash-algoritm, kodad med Base64URL  
-   **nyckeltyp** - en av de nyckeltyper som stöds, till exempel RSA (Rivest-Shamir-Adleman).  
-   **klartextvärde** - oktett i klartext, kodade med Base64URL  
-   **signaturvärde** - utdata från en signaturalgoritm, kodad med Base64URL  
-   **base64URL** - ett Base64URL [RFC4648] kodat binärt värde  
-   **boolesk** - antingen sant eller falskt  
-   **Identity** - en identitet från Azure Active Directory (AAD).  
-   **IntDate** - ett JSON-decimalvärde som representerar antalet sekunder från 1970-01-01T0:0:0Z UTC fram till angivet UTC-datum/-tid. Se RFC3339 för mer information om datum/tider, i allmänhet och UTC i synnerhet.  

### <a name="objects-identifiers-and-versioning"></a>Objekt, identifierare och versionshantering

Objekt som lagras i Key Vault versions när en ny instans av ett objekt skapas. Varje version tilldelas en unik identifierare och URL. När ett objekt först skapas får det en unik versionsidentifierare och markeras som den aktuella versionen av objektet. Skapandet av en ny instans med samma objektnamn ger det nya objektet en unik versionsidentifierare, vilket gör att den blir den aktuella versionen.  

Objekt i Key Vault kan åtgärdas med den aktuella identifieraren eller en versionsspecifik identifierare. Om du till exempel anger `MasterKey`en nyckel med namnet, gör det att utföra åtgärder med den aktuella identifieraren att systemet använder den senaste tillgängliga versionen. Om du utför åtgärder med den versionsspecifika identifieraren används systemet den specifika versionen av objektet.  

Objekt identifieras unikt i Key Vault med hjälp av en URL. Inga två objekt i systemet har samma URL, oavsett geografisk plats. Den fullständiga URL:en till ett objekt kallas objektidentifieraren. URL:en består av ett prefix som identifierar Nyckelvalvet, objekttypen, objektets namn och en objektversion. Objektnamnet är skiftlägesokänsligt och oföränderligt. Identifierare som inte innehåller objektversionen kallas basidentifierare.  

Mer information finns i [Autentisering, begäranden och svar](authentication-requests-and-responses.md)

En objektidentifierare har följande allmänna format:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Där:  

|||  
|-|-|  
|`keyvault-name`|Namnet på ett nyckelvalv i Tjänsten Microsoft Azure Key Vault.<br /><br /> Key Vault-namn väljs av användaren och är globalt unika.<br /><br /> Key Vault-namnet måste vara en 3-24 teckensträng som endast innehåller 0-9, a-z, A-Z och -.|  
|`object-type`|Typen av objekt, antingen "nycklar" eller "hemligheter".|  
|`object-name`|En `object-name` är ett användarnamn för och måste vara unikt i ett Nyckelvalv. Namnet måste vara en 1-127 teckensträng som innehåller endast 0-9, a-z, A-Z och -.|  
|`object-version`|En `object-version` är en systemgenererad 32 teckensträngidentifierare som eventuellt används för att adressera en unik version av ett objekt.|  

## <a name="key-vault-keys"></a>Nyckelvalvsnycklar

### <a name="keys-and-key-types"></a>Nycklar och nyckeltyper

Kryptografiska nycklar i Key Vault representeras som JSON Web Key [JWK]-objekt. Bas-JWK/JWA-specifikationerna utökas också för att aktivera nyckeltyper som är unika för Key Vault-implementeringen. Om du till exempel importerar nycklar med HSM-leverantörsspecifika förpackningar kan du begränsa säker transport av nycklar som endast får användas i Key Vault-HSM-moduler.  

- **"Soft"-nycklar:** En nyckel som bearbetas i programvara av Key Vault, men krypteras i vila med hjälp av en systemnyckel som finns i en HSM. Klienter kan importera en befintlig RSA- eller EC-nyckel (Elliptic Curve) eller begära att Key Vault genererar en.
- **"Hard"-nycklar:** En nyckel som bearbetas i en HSM (Hardware Security Module). Dessa nycklar är skyddade i en av Key Vault HSM Security Worlds (det finns en Security World per geografi för att upprätthålla isolering). Klienter kan importera en RSA- eller EC-nyckel, i mjuk form eller genom att exportera från en kompatibel HSM-enhet. Klienter kan också begära Key Vault för att generera en nyckel. Den här nyckeltypen lägger till key_hsm-attributet till JWK-erhållet för att bära HSM-nyckelmaterialet.

     Mer information om geografiska gränser finns i [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)  

Key Vault stöder endast RSA- och Elliptic Curve-tangenter. 

-   **EG**: "Mjuk" Elliptic Curve nyckel.
-   **EC-HSM**: "Hård" Elliptic Curve nyckel.
-   **RSA**: "Mjuk" RSA-nyckel.
-   **RSA-HSM**: "Hård" RSA-nyckel.

Key Vault stöder RSA-nycklar med storlekarna 2048, 3072 och 4096. Key Vault stöder nyckeltyperna P-256, P-384, P-521 och P-256K (SECP256K1).

### <a name="cryptographic-protection"></a>Kryptografiskt skydd

De kryptografiska moduler som Key Vault använder, oavsett om det är HSM eller programvara, är FIPS (Federal Information Processing Standards) validerade. Du behöver inte göra något speciellt för att köras i FIPS-läge. Nycklar som **skapas** eller **importeras** som HSM-skyddade bearbetas i en HSM, validerad till FIPS 140-2 Nivå 2. Nycklar som **skapas** eller **importeras** som programvaruskyddade bearbetas inuti kryptografiska moduler som validerats till FIPS 140-2 Nivå 1. Mer information finns i [Nycklar och nyckeltyper](#keys-and-key-types).

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

###  <a name="key-operations"></a>Viktiga operationer

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

###  <a name="key-attributes"></a>Nyckelattribut

Utöver nyckelmaterialet kan följande attribut anges. I en JSON-begäran krävs nyckelordet attribut och klammerparenteser, {' '}, även om inga attribut har angetts.  

- *aktiverad:* booleska, valfria, standard är **sant**. Anger om nyckeln är aktiverad och användbar för kryptografiska åtgärder. Attributet *enabled* används tillsammans med *nbf* och *exp*. När en åtgärd inträffar mellan *nbf* och *exp*tillåts den bara om *aktiverad* är inställd på **true**. Åtgärder utanför *nbf* / *exp-fönstret* tillåts automatiskt, med undantag för vissa driftstyper under särskilda [förhållanden](#date-time-controlled-operations).
- *nbf*: IntDate, valfritt, standard är nu. Attributet *nbf* (inte före) identifierar den tid före vilken nyckeln INTE FÅR användas för kryptografiska åtgärder, med undantag för vissa operationstyper under [särskilda förhållanden](#date-time-controlled-operations). Bearbetningen av *nbf-attributet* kräver att det aktuella datumet/tiden måste vara efter eller lika med det datum/tid som anges i *attributet nbf.* Key Vault kan ge några små spelrum, normalt inte mer än några minuter, att ta hänsyn till klocka skeva. Dess värde MÅSTE vara ett tal som innehåller ett intdate-värde.  
- *exp:* IntDate, valfritt, standard är "för evigt". Attributet *exp* (expiration time) identifierar utgångstiden på eller efter vilken nyckeln INTE får användas för kryptografisk drift, med undantag för vissa operationstyper under [särskilda förhållanden](#date-time-controlled-operations). Bearbetningen av *exp-attributet* kräver att det aktuella datumet/tiden måste infalla före utgångsdatumet/tid som anges i *exp-attributet.* Key Vault kan ge några små spelrum, vanligtvis inte mer än några minuter, att ta hänsyn till klocka skeva. Dess värde MÅSTE vara ett tal som innehåller ett intdate-värde.  

Det finns ytterligare skrivskyddade attribut som ingår i alla svar som innehåller nyckelattribut:  

- *skapad*: IntDate, valfritt. Attributet *skapad* anger när den här versionen av nyckeln skapades. Värdet är null för nycklar som skapats före tillägget av det här attributet. Dess värde MÅSTE vara ett tal som innehåller ett intdate-värde.  
- *uppdaterad*: IntDate, valfritt. Det *uppdaterade* attributet anger när den här versionen av nyckeln uppdaterades. Värdet är null för nycklar som senast uppdaterades före tillägget av det här attributet. Dess värde MÅSTE vara ett tal som innehåller ett intdate-värde.  

Mer information om IntDate och andra datatyper finns i [Datatyper](#data-types)  

#### <a name="date-time-controlled-operations"></a>Datum-tidsstyrda operationer

Ännu inte giltiga och utgångna nycklar, utanför *nbf* / *exp-fönstret,* kommer att fungera för **dekryptera,** packa **upp**och **verifiera** åtgärder (kommer inte tillbaka 403, Förbjudet). Grunden för att använda det ännu inte giltiga tillståndet är att tillåta att en nyckel testas före produktionsanvändning. Grunden för att använda det utgångna tillståndet är att tillåta återställningsåtgärder på data som skapades när nyckeln var giltig. Du kan också inaktivera åtkomst till en nyckel med hjälp av Key Vault-principer eller genom att uppdatera *attributet aktiverad* nyckel till **false**.

Mer information om datatyper finns i [Datatyper](#data-types).

Mer information om andra möjliga attribut finns i [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

### <a name="key-tags"></a>Viktiga taggar

Du kan ange ytterligare programspecifika metadata i form av taggar. Key Vault stöder upp till 15 taggar, som alla kan ha ett 256 teckennamn och ett 256 teckenvärde.  

>[!Note]
>Taggar kan läsas av en uppringare om de har *listan* eller *får* behörighet till objekttypen (nycklar, hemligheter eller certifikat).

###  <a name="key-access-control"></a>Nyckelåtkomstkontroll

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

## <a name="key-vault-secrets"></a>Key Vault hemligheter 

### <a name="working-with-secrets"></a>Arbeta med hemligheter

Ur en utvecklares perspektiv accepterar och returnerar Key Vault-API:er hemliga värden som strängar. Internt lagrar och hanterar Key Vault hemligheter som sekvenser av oktetter (8-bitars byte), med en maximal storlek på 25 k byte vardera. Key Vault-tjänsten ger inte semantik för hemligheter. Den accepterar bara data, krypterar den, lagrar den och returnerar en hemlig identifierare ("id"). Identifieraren kan användas för att hämta hemligheten vid ett senare tillfälle.  

För data som är mycket känsliga bör klienter överväga ytterligare skyddslager för data. Ett exempel är kryptering av data med hjälp av en separat skyddsnyckel före lagring i Key Vault.  

Key Vault stöder också ett contentType-fält för hemligheter. Klienter kan ange innehållstypen för en hemlighet som hjälper till att tolka hemliga data när de hämtas. Den maximala längden på det här fältet är 255 tecken. Det finns inga fördefinierade värden. Den föreslagna användningen är som en ledtråd för att tolka hemliga data. En implementering kan till exempel lagra både lösenord och certifikat som hemligheter och sedan använda det här fältet för att skilja. Det finns inga fördefinierade värden.  

### <a name="secret-attributes"></a>Hemliga attribut

Utöver de hemliga uppgifterna kan följande attribut anges:  

- *exp:* IntDate, valfritt, standard är **för evigt**. Attributet *exp* (expiration time) identifierar utgångstiden på eller efter vilken de hemliga data inte ska hämtas, utom i [vissa situationer](#date-time-controlled-operations). Det här fältet är endast i **informationssyfte** eftersom det informerar användare av nyckelvalvstjänsten om att en viss hemlighet inte får användas. Dess värde MÅSTE vara ett tal som innehåller ett intdate-värde.   
- *nbf*: IntDate, valfritt, standard är **nu**. Attributet *nbf* (inte före) identifierar den tid före vilken de hemliga uppgifterna INTE ska hämtas, utom i [vissa situationer](#date-time-controlled-operations). Det här **informational** fältet är endast i informationssyfte. Dess värde MÅSTE vara ett tal som innehåller ett intdate-värde. 
- *aktiverad:* booleska, valfria, standard är **sant**. Det här attributet anger om de hemliga data som kan hämtas. Det aktiverade attributet används tillsammans med *nbf* och *exp* när en åtgärd inträffar mellan *nbf* och *exp,* det kommer bara att tillåtas om aktiverad är inställd på **true**. Operationer utanför *nbf-* och *exp-fönstret* tillåts automatiskt, utom i [vissa situationer](#date-time-controlled-operations).  

Det finns ytterligare skrivskyddade attribut som ingår i alla svar som innehåller hemliga attribut:  

- *skapad*: IntDate, valfritt. Attributet skapad anger när den här versionen av hemligheten skapades. Det här värdet är null för hemligheter som skapats före tillägget av det här attributet. Dess värde måste vara ett tal som innehåller ett intdate-värde.  
- *uppdaterad*: IntDate, valfritt. Det uppdaterade attributet anger när den här versionen av hemligheten uppdaterades. Det här värdet är null för hemligheter som senast uppdaterades före tillägget av det här attributet. Dess värde måste vara ett tal som innehåller ett intdate-värde.

#### <a name="date-time-controlled-operations"></a>Datum-tidsstyrda operationer

En hemlighet s **få** operation kommer att fungera för ännu inte giltiga och utgångna hemligheter, utanför *nbf* / *exp* fönstret. Ringa en hemlighet's **get** operation, för en ännu inte giltig hemlighet, kan användas för teständamål. Hämtar **(få**ting) en utgången hemlighet, kan användas för återställningsåtgärder.

Mer information om datatyper finns i [Datatyper](#data-types).  

### <a name="secret-access-control"></a>Åtkomstkontroll till hemlighet

Åtkomstkontroll för hemligheter som hanteras i Key Vault finns på nivån för Key Vault som innehåller dessa hemligheter. Åtkomstkontrollprincipen för hemligheter skiljer sig från åtkomstkontrollprincipen för nycklar i samma Nyckelvalv. Användare kan skapa ett eller flera valv för att hålla hemligheter och måste upprätthålla scenariot lämplig segmentering och hantering av hemligheter.   

Följande behörigheter kan användas, per huvudmannabas, i åtkomstkontrollposten hemligheter i ett valv och noggrant spegla de åtgärder som tillåts för ett hemligt objekt:  

- Behörigheter för hemliga hanteringsåtgärder
  - *få:* Läs en hemlighet  
  - *lista*: Lista hemligheter eller versioner av en hemlighet som lagras i ett Nyckelvalv  
  - *set*: Skapa en hemlighet  
  - *ta bort*: Ta bort en hemlighet  
  - *återställa*: Återställa en borttagen hemlighet
  - *backup*: Säkerhetskopiera en hemlighet i ett nyckelvalv
  - *återställa:* Återställa en säkerhetskopierad hemlighet till ett nyckelvalv

- Behörigheter för privilegierade åtgärder
  - *rensa:* Rensa (permanent ta bort) en borttagen hemlighet

Mer information om hur du arbetar med hemligheter finns [i Hemliga åtgärder i REST-API-referensen för Key Vault REST](/rest/api/keyvault). Information om hur du upprättar behörigheter finns i [Arkiv - Skapa eller Uppdatera](/rest/api/keyvault/vaults/createorupdate) och [Valv - Uppdatera åtkomstprincip](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Hemliga taggar  
Du kan ange ytterligare programspecifika metadata i form av taggar. Key Vault stöder upp till 15 taggar, som alla kan ha ett 256 teckennamn och ett 256 teckenvärde.  

>[!Note]
>Taggar kan läsas av en uppringare om de har *listan* eller *får* behörighet till objekttypen (nycklar, hemligheter eller certifikat).

## <a name="key-vault-certificates"></a>Certifikat för nyckelvalv

Support för Key Vault-certifikat ger hantering av dina x509-certifikat och följande beteenden:  

-   Gör att en certifikatägare kan skapa ett certifikat genom en skapandeprocess för nyckelvalv eller genom import av ett befintligt certifikat. Innehåller både självsignerade certifikat och certifikatutfärdare genererade certifikat.
-   Gör att en Key Vault-certifikatägare kan implementera säker lagring och hantering av X509-certifikat utan interaktion med privat nyckelmaterial.  
-   Gör att en certifikatägare kan skapa en princip som styr Key Vault för att hantera livscykeln för ett certifikat.  
-   Gör det möjligt för certifikatägare att tillhandahålla kontaktinformation för meddelande om livscykelhändelser för utgångsdatum och förnyelse av certifikat.  
-   Stöder automatisk förnyelse med utvalda utfärdare - Key Vault-partner X509 certifikatleverantörer / certifikatutfärdare.

>[!Note]
>Icke-partnerleverantörer/myndigheter är också tillåtna, men kommer inte att stödja funktionen för automatisk förnyelse.

### <a name="composition-of-a-certificate"></a>Ett certifikats sammansättning

När ett Key Vault-certifikat skapas skapas också en adresserbar nyckel och hemlighet med samma namn. Key Vault-nyckeln tillåter nyckelåtgärder och Key Vault-hemligheten gör det möjligt att hämta certifikatvärdet som en hemlighet. Ett Key Vault-certifikat innehåller också offentliga x509-certifikatmetadata.  

Identifieraren och versionen av certifikat liknar den för nycklar och hemligheter. En specifik version av en adresserbar nyckel och hemlighet som skapats med certifikatversionen av Key Vault finns i certifikatsvaret För Nyckelvalv.
 
![Certifikat är komplexa objekt](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Exporteras eller icke-exportbar nyckel

När ett Key Vault-certifikat skapas kan det hämtas från den adresserbara hemligheten med den privata nyckeln i antingen PFX- eller PEM-format. Principen som används för att skapa certifikatet måste ange att nyckeln kan exporteras. Om principen anger att den inte kan exporteras är den privata nyckeln inte en del av värdet när den hämtas som en hemlighet.  

Den adresserbara nyckeln blir mer relevant med KV-certifikat som inte kan exporteras. Den adresserbara KV-nyckelns åtgärder mappas från *nyckelanvändarfält* i KV-certifikatprincipen som används för att skapa KV-certifikatet.  

Två typer av nyckel stöds – *RSA* eller *RSA HSM* med certifikat. Exportbar är endast tillåten med RSA, som inte stöds av RSA HSM.  

### <a name="certificate-attributes-and-tags"></a>Certifikatattribut och -taggar

Förutom certifikatmetadata, en adresserbar nyckel och adresserbar hemlighet innehåller ett Key Vault-certifikat också attribut och taggar.  

#### <a name="attributes"></a>Attribut

Certifikatattributen speglas till attribut för den adresserbara nyckeln och hemlighet som skapas när KV-certifikat skapas.  

Ett Key Vault-certifikat har följande attribut:  

-   *aktiverad:* booleska, valfria, standard är **sant**. Kan anges för att ange om certifikatdata kan hämtas som hemliga eller fungerande som en nyckel. Används även tillsammans med *nbf* och *exp* när en operation inträffar mellan *nbf* och *exp*, och kommer endast att tillåtas om aktiverad är inställd på true. Åtgärder utanför *nbf-* och *exp-fönstret* tillåts automatiskt.  

Det finns ytterligare skrivskyddade attribut som ingår i svaret:

-   *skapad*: IntDate: anger när den här versionen av certifikatet skapades.  
-   *uppdaterad*: IntDate: anger när den här versionen av certifikatet uppdaterades.  
-   *exp*: IntDate: innehåller värdet för utgångsdatumet för x509-certifikatet.  
-   *nbf*: IntDate: innehåller värdet för datumet för x509-certifikatet.  

> [!Note] 
> Om ett Key Vault-certifikat upphör att gälla är den adresserbar nyckel och hemligheten blir obrukbar.  

#### <a name="tags"></a>Taggar

 Klientens angivna ordlista med nyckelvärdespar, liknande taggar i nycklar och hemligheter.  

 > [!Note]
> Taggar kan läsas av en uppringare om de har *listan* eller *får* behörighet till objekttypen (nycklar, hemligheter eller certifikat).

### <a name="certificate-policy"></a>Certifikatprincip

En certifikatprincip innehåller information om hur du skapar och hanterar livscykeln för ett Key Vault-certifikat. När ett certifikat med privat nyckel importeras till nyckelvalvet skapas en standardprincip genom att läsa x509-certifikatet.  

När ett Key Vault-certifikat skapas från grunden måste en princip anges. Principen anger hur du skapar den här nyckelvalvscertifikatversionen eller nästa nyckelvalvscertifikatversion. När en princip har upprättats krävs det inte med successiva skapa åtgärder för framtida versioner. Det finns bara en instans av en princip för alla versioner av ett Key Vault-certifikat.  

På en hög nivå innehåller en certifikatprincip följande information:  

-   X509-certifikategenskaper: Innehåller ämnesnamn, ämnes alternativa namn och andra egenskaper som används för att skapa en x509-certifikatbegäran.  
-   Nyckelegenskaper: innehåller nyckeltyp, nyckellängd, exportbara och återanvändning av nyckelfält. Dessa fält instruera nyckelvalv om hur du genererar en nyckel.  
-   Hemliga egenskaper: innehåller hemliga egenskaper som innehållstyp av adresserbar hemlighet för att generera det hemliga värdet, för att hämta certifikat som en hemlighet.  
-   Livstidsåtgärder: innehåller livstidsåtgärder för KV-certifikatet. Varje livstidsåtgärd innehåller:  

     - Utlösare: angiven via dagar före utgången eller livstidsintervallprocent  

     - Åtgärd: ange åtgärdstyp – *e-postKontakt eller* *återskapa automatiskt*  

-   Utfärdare: Parametrar om certifikatutfärdaren som ska användas för att utfärda x509-certifikat.  
-   Principattribut: innehåller attribut som är associerade med principen  

#### <a name="x509-to-key-vault-usage-mapping"></a>X509 till användningsmappning för Nyckelvalv

Följande tabell representerar mappningen av x509-nyckelanvändningsprincipen till effektiva nyckelåtgärder för en nyckel som skapats som en del av skapandet av nyckelvalvscertifikat.

|**Flaggor för X509-nyckelanvändning**|**Nyckelvalvs-nyckel ops**|**Standardbeteende**|
|----------|--------|--------|
|DataEncipherment|kryptera, dekryptera| Ej tillämpligt |
|DechiffreraOnly|Dekryptera| Ej tillämpligt  |
|DigitalSignature|tecken, verifiera| Standard för Key Vault utan användningsspecifikation vid skapande av certifikat | 
|EncipherOnly|encrypt| Ej tillämpligt |
|KeyCertSign|tecken, verifiera|Ej tillämpligt|
|KeyEncipherment|wrapKey, ta bort tangenter| Standard för Key Vault utan användningsspecifikation vid skapande av certifikat | 
|Oavvislighet|tecken, verifiera| Ej tillämpligt |
|crlsign (crlsign)|tecken, verifiera| Ej tillämpligt |

### <a name="certificate-issuer"></a>Certifikatutfärdare

Ett Key Vault-certifikatobjekt innehåller en konfiguration som används för att kommunicera med en vald certifikatutfärdare för att beställa x509-certifikat.  

-   Key Vault samarbetar med följande certifikatutfärdare för TLS/SSL-certifikat

|**Leverantörens namn**|**Platser**|
|----------|--------|
|DigiCert|Stöds på alla viktiga valvtjänstplatser i offentliga moln och Azure Government|
|GlobalSign|Stöds på alla viktiga valvtjänstplatser i offentliga moln och Azure Government|

Innan en certifikatutfärdare kan skapas i ett nyckelvalv måste följande nödvändiga steg 1 och 2 utföras.  

1. Ombord på certifikatutfärdare (CA)-leverantörer  

    -   En organisationsadministratör måste vara ombord på sitt företag (t.ex. Contoso) med minst en CA-leverantör.  

2. Admin skapar autentiseringsuppgifter för beställare för Key Vault för att registrera (och förnya) TLS/SSL-certifikat  

    -   Tillhandahåller den konfiguration som ska användas för att skapa ett utfärdarobjekt för providern i nyckelvalvet  

Mer information om hur du skapar Utfärdarobjekt från certifikatportalen finns i [bloggen Nyckelvalvscertifikat](https://aka.ms/kvcertsblog)  

Key Vault gör det möjligt att skapa flera utfärdarobjekt med olika konfiguration av utfärdare provider. När ett utfärdarobjekt har skapats kan dess namn refereras i en eller flera certifikatprinciper. Om du refererar till utfärdarobjektet instrueras Key Vault att använda konfigurationen enligt vad som anges i utfärdarobjektet när x509-certifikatet från CERTIFIKATUTfärdaren begärs från certifikatutfärdaren när certifikatet skapas och förnyas.  

Utfärdarobjekt skapas i valvet och kan endast användas med KV-certifikat i samma valv.  

### <a name="certificate-contacts"></a>Certifikatkontakter

Certifikatkontakter innehåller kontaktinformation för att skicka meddelanden som utlöses av certifikatets livstidshändelser. Kontaktinformationen delas av alla certifikat i nyckelvalvet. Ett meddelande skickas till alla angivna kontakter för en händelse för alla certifikat i nyckelvalvet.  

Om ett certifikats princip är inställd på automatisk förnyelse skickas ett meddelande om följande händelser.  

- Före förnyelse av certifikat
- Efter förnyelse av certifikatet, med angivande av om certifikatet har förnyats, eller om det uppstod ett fel, vilket kräver manuell förnyelse av certifikatet.  

  När en certifikatprincip som har angetts för att förnyas manuellt (endast e-post) skickas ett meddelande när det är dags att förnya certifikatet.  

### <a name="certificate-access-control"></a>Kontroll av certifikatåtkomst

 Åtkomstkontrollen för certifikat hanteras av Key Vault och tillhandahålls av Nyckelvalvet som innehåller dessa certifikat. Åtkomstkontrollprincipen för certifikat skiljer sig från åtkomstkontrollprinciperna för nycklar och hemligheter i samma Nyckelvalv. Användare kan skapa ett eller flera valv för att lagra certifikat, för att upprätthålla scenariot lämplig segmentering och hantering av certifikat.  

 Följande behörigheter kan användas per huvudmansbasis i åtkomstkontrollposten hemligheter i ett nyckelvalv och speglar noggrant de åtgärder som tillåts för ett hemligt objekt:  

- Behörigheter för certifikathanteringsåtgärder
  - *hämta:* Hämta den aktuella certifikatversionen eller någon version av ett certifikat 
  - *lista*: Lista aktuella certifikat eller versioner av ett certifikat  
  - *uppdatering*: Uppdatera ett certifikat
  - *skapa*: Skapa ett Key Vault-certifikat
  - *importera*: Importera certifikatmaterial till ett Key Vault-certifikat
  - *ta bort*: Ta bort ett certifikat, dess princip och alla dess versioner  
  - *återställa*: Återställa ett borttaget certifikat
  - *säkerhetskopiering*: Säkerhetskopiera ett certifikat i ett nyckelvalv
  - *återställning:* Återställa ett säkerhetskopierat certifikat till ett nyckelvalv
  - *hanterakontakter*: Hantera key vault-certifikatkontakter  
  - *manageissuers*: Hantera certifikatutfärdare/utfärdare av key vault-certifikat
  - *getissuers*: Få ett certifikat myndigheter / emittenter
  - *listissuers*: Lista ett certifikat myndigheter/emittenter  
  - *setissuers*: Skapa eller uppdatera ett key vault-certifikats myndigheter/utfärdare  
  - *deleteissuers*: Ta bort en Key Vault-certifikat myndigheter / emittenter  
 
- Behörigheter för privilegierade åtgärder
  - *rensa*: Rensa (permanent ta bort) ett borttaget certifikat

Mer information finns [i certifikatåtgärderna i REST-API-referensen för Nyckelvalvet](/rest/api/keyvault). Information om hur du upprättar behörigheter finns i [Arkiv - Skapa eller Uppdatera](/rest/api/keyvault/vaults/createorupdate) och [Valv - Uppdatera åtkomstprincip](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="azure-storage-account-key-management"></a>Hantering av nyckelhantering för Azure Storage-konton

Key Vault kan hantera Azure-lagringskontonycklar:

- Internt kan Key Vault lista (synkronisera) nycklar med ett Azure-lagringskonto. 
- Key Vault återskapar (roterar) tangenterna med jämna mellanrum.
- Nyckelvärden returneras aldrig som svar på den som ringer.
- Key Vault hanterar nycklar för både lagringskonton och klassiska lagringskonton.

Mer information finns i [Azure Key Vault Storage Account Keys](key-vault-ovw-storage-keys.md)

### <a name="storage-account-access-control"></a>Åtkomstkontroll för lagringskonto

Följande behörigheter kan användas när en användare eller ett programhuvudnamn auktoriseras för att utföra åtgärder på ett hanterat lagringskonto:  

- Behörigheter för hanterade lagringskonto- och SaS-definitionsåtgärder
  - *hämta*: Hämtar information om ett lagringskonto 
  - *lista*: Lista lagringskonton som hanteras av ett Nyckelvalv
  - *uppdatering*: Uppdatera ett lagringskonto
  - *ta bort*: Ta bort ett lagringskonto  
  - *återställa*: Återställa ett borttaget lagringskonto
  - *säkerhetskopiering*: Säkerhetskopiera ett lagringskonto
  - *återställa:* Återställa ett säkerhetskopierat lagringskonto till ett Key Vault
  - *set*: Skapa eller uppdatera ett lagringskonto
  - *regeneratekey*: Återskapa ett angivet nyckelvärde för ett lagringskonto
  - *getsas*: Få information om en SAS-definition för ett lagringskonto
  - *listsas*: Lista SAS-definitioner för lagring för ett lagringskonto
  - *deletesas*: Ta bort en SAS-definition från ett lagringskonto
  - *setas*: Skapa eller uppdatera en ny SAS-definition/attribut för ett lagringskonto

- Behörigheter för privilegierade åtgärder
  - *rensa:* Rensa (permanent ta bort) ett hanterat lagringskonto

Mer information finns [i lagringskontoåtgärderna i REST-API-referensen för Key Vault REST](/rest/api/keyvault). Information om hur du upprättar behörigheter finns i [Arkiv - Skapa eller Uppdatera](/rest/api/keyvault/vaults/createorupdate) och [Valv - Uppdatera åtkomstprincip](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Se även

- [Autentisering, begäranden och svar](authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](/azure/key-vault/key-vault-developers-guide)
