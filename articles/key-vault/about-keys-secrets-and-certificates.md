---
title: Om Azure Key Vault-nycklar, hemligheter och certifikat – Azure Key Vault
description: Översikt över Azure Key Vault REST-gränssnittet och utvecklare information om nycklar, hemligheter och certifikat.
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.assetid: abd1b743-1d58-413f-afc1-d08ebf93828a
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 01d9f763983da2415aba0f9bae81414017bc2f02
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842574"
---
# <a name="about-keys-secrets-and-certificates"></a>Om nycklar, hemligheter och certifikat

Azure Key Vault kan Microsoft Azure-program och användare du lagrar och använder flera typer av data för hemlighet/nyckel:

- Kryptografiska nycklar: Har stöd för flera nyckeltyper och algoritmer och möjliggör användning av maskinvarusäkerhetsmodul moduler (HSM) för högt värderade nycklar. 
- Hemligheter: Tillhandahåller säker lagring av hemligheter som lösenord och databasanslutningssträngar.
- Certifikat: Har stöd för certifikat som är byggda på nycklar och hemligheter och Lägg till en funktion för automatisk förnyelse.
- Azure Storage: Kan hantera nycklar för ett Azure Storage-konto för dig. Internt, Key Vault kan lista nycklar (sync) med Azure Storage-kontot och återskapa (rotera) nycklarna regelbundet. 

Mer allmän information om Key Vault finns i [vad är Azure Key Vault?](/azure/key-vault/key-vault-whatis)

## <a name="azure-key-vault"></a>Azure Key Vault

I följande avsnitt ger allmän information som gäller för implementeringen av Key Vault-tjänsten.

### <a name="supporting-standards"></a>Stöd för standarder

JavaScript Object Notation (JSON) och JavaScript-objekt signering och kryptering (JOSE) specifikationer finns viktig grundläggande information.  

-   [JSON-Webbnyckeln (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web-kryptering (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web algoritmer (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web signatur (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="data-types"></a>Datatyper

Avse JOSE specifikationerna för relevanta datatyper för nycklar, kryptering och signering.  

-   **algoritmen** – en algoritm som stöds för en nyckelåtgärd, till exempel RSA1_5  
-   **krypterad / värde-** -cipher text oktetter, kodad med Base64URL  
-   **Sammanfattad / värde-** -utdata från en hash-algoritm kodad med Base64URL  
-   **nyckeltypen** – en av de viktiga typerna som stöds, till exempel RSA (Rivest-Shamir-Adleman).  
-   **klartext / värde-** -klartext oktetter, kodad med Base64URL  
-   **signaturens värde** - utdata för en signaturalgoritm kodad med Base64URL  
-   **base64URL** -en Base64URL [RFC4648] kodad binärt värde  
-   **booleska** -true eller false  
-   **Identitet** – en identitet från Azure Active Directory (AAD).  
-   **IntDate** – ett JSON-decimalvärde som representerar antalet sekunder från 1970-01-01T0:0:0Z UTC fram till angivet datum och tid i UTC. Se RFC3339 för information om date/times i allmänhet särskilt UTC.  

### <a name="objects-identifiers-and-versioning"></a>Objekt-ID: n och versionshantering

Objekt som lagras i Key Vault är en ny version varje gång en ny instans av ett objekt skapas. Varje version tilldelas en unik identifierare och URL: en. När ett objekt skapas, har den får en unik versions-ID och markerats som den aktuella versionen av objektet. Skapa en ny instans med samma objektnamn ger det nya objektet en unik versions-ID, vilket gör att den blir den aktuella versionen.  

Objekt i Key Vault kan lösas med hjälp av det aktuella ID: t eller en versionsspecifika identifierare. Till exempel få en nyckel med namnet `MasterKey`, utföra åtgärder med det aktuella ID: t gör systemet att använda den senaste tillgängliga versionen. Utför åtgärder med versionsspecifika identifierare medför att använda den här specifika versionen av objektet.  

Objekt identifieras unikt i Key Vault med en URL. Inga två objekt i systemet har samma Webbadress, oavsett geografiska plats. Den fullständiga URL: en till ett objekt kallas objektidentifieraren. URL: en som består av ett prefix som identifierar Key Vault, objekttyp, användare som objektnamn och en Version av objektet. Objektnamnet är skiftlägeskänsliga och kan ändras. ID: n som inte innehåller den versionen kallas Base identifierare.  

Mer information finns i [autentisering, begäranden och svar](authentication-requests-and-responses.md)

En objektidentifierare har följande allmänna format:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Där:  

|||  
|-|-|  
|`keyvault-name`|Namnet för ett nyckelvalv i Microsoft Azure Key Vault-tjänsten.<br /><br /> Key Vault-namn har valts av användaren och är globalt unikt.<br /><br /> Key Vault-namn måste vara en 3 och 24 tecken lång sträng, som innehåller bara 0-9, a – z, A-Z, - och.|  
|`object-type`|Typ av objekt, ”nycklar” eller ”hemligheter”.|  
|`object-name`|En `object-name` är ett tillhandahålls användarnamn för och måste vara unika inom ett Key Vault. Namnet måste vara en 1-127 tecken lång sträng, som innehåller bara 0-9, a – z, A-Z, - och.|  
|`object-version`|En `object-version` är ett systemgenererat, 32 tecken sträng-ID som du kan också används för att adressera en unik version av ett objekt.|  

## <a name="key-vault-keys"></a>Key Vault-nycklar

### <a name="keys-and-key-types"></a>Nycklar och nyckeltyper

Kryptografiska nycklar i Key Vault representeras som JSON-Webbnyckeln [JWK]-objekt. Grundläggande JWK/JWA specifikationer också utökas för att aktivera nyckeltyper som är unika för Key Vault-implementering. Importera med hjälp av HSM leverantörsspecifika paketering kan till exempel säker transport av nycklar som endast kan användas i Key Vault HSM.  

- **"Soft" keys**: En nyckel behandlas i programvaran av Key Vault, men krypteras i viloläge med hjälp av en systemnyckel som är i en HSM. Klienter kan importera en befintlig nyckel för RSA eller EG (Elliptic Curve) eller begära att Key Vault Generera en.
- **"Hard" keys**: En nyckel som bearbetas i en HSM (maskinvarusäkerhetsmodul). Dessa nycklar skyddas i ett av Key Vault HSM-Säkerhetsvärldar (det finns en Säkerhetsvärld per geografisk plats för att bibehålla isolering). Klienter kan importera en RSA eller EG nyckel i mjuk formuläret eller genom att exportera från en kompatibel HSM-enhet. Klienter kan också begära Key Vault för att generera en nyckel. Den här nyckeltypen lägger till attributet T till i JWK skaffa om du vill utföra HSM nyckelmaterial.

     Mer information om geografiska gränser finns [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)  

Key Vault har stöd för RSA och Elliptic Curve nycklar. 

-   **EC**: ”Soft” Elliptic Curve nyckel.
-   **EC-HSM**: ”Hård” Elliptic Curve nyckel.
-   **RSA**: ”Soft” RSA-nyckel.
-   **RSA-HSM**: "Hard" RSA key.

Key Vault har stöd för RSA-nycklar med storlekar 2048, 3072 och 4096. Key Vault stöder Elliptic Curve nyckel skriver P-256, p-384, p 521 och P-256_K (SECP256K1).

### <a name="cryptographic-protection"></a>Kryptering

De kryptografiska moduler som använder Key Vault, om HSM eller programvara är FIPS (Federal Information Processing Standards)-verifierade. Du behöver inte göra något speciellt för att köra i FIPS-läge. Nycklar **skapade** eller **importeras** som HSM-skyddad är bearbetas inuti en HSM, verifierade enligt standarderna FIPS 140-2 nivå 2. Nycklar **skapade** eller **importeras** som programvaruskyddad, bearbetas i kryptografiska moduler som har verifierats att FIPS 140-2 nivå 1. Mer information finns i [nycklar och nyckeltyper](#keys-and-key-types).

###  <a name="ec-algorithms"></a>EG algoritmer
 Följande algoritm identifierare stöds med EG och EG HSM-nycklar i Key Vault. 

#### <a name="curve-types"></a>Kurvan typer

-   **P-256** – The NIST kurvan p-256, har definierats på [DSS FIPS PUB 186 4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256_K** – The sek kurvan SECP256K1 som definierats på [sek 2: Rekommenderade elliptisk kurva domänparametrar](https://www.secg.org/sec2-v2.pdf).
-   **P-384** – The NIST kurvan p-384, har definierats på [DSS FIPS PUB 186 4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** – The NIST kurvan p 521, som definierats på [DSS FIPS PUB 186 4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>INLOGGNING/KONTROLLERA

-   **ES256** – ECDSA för SHA-256 Överför sammanfattningar och nycklar som skapats med kurvan p-256. Den här algoritmen beskrivs på [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** – ECDSA för SHA-256 Överför sammanfattningar och nycklar som skapats med kurvan P-256_K. Den här algoritmen väntar på standardisering.
-   **ES384** – ECDSA för SHA-384 Överför sammanfattningar och nycklar som skapats med kurvan p-384. Den här algoritmen beskrivs på [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** – ECDSA för SHA-512 Överför sammanfattningar och nycklar som skapats med kurvan p 521. Den här algoritmen beskrivs på [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>RSA-algoritmer  
 Följande algoritm identifierare stöds med RSA- och RSA-HSM-nycklar i Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, KRYPTERING/DEKRYPTERING

-   **RSA1_5** -RSAES PKCS1 V1_5 [RFC3447] krypteringsnyckel  
-   **RSA-OAEP** – RSAES med standardparametrarna som anges i RFC 3447 i avsnittet A.2.1 optimala asymmetrisk kryptering utfyllnad (OAEP) [RFC3447]. Dessa standardparametrar använder en hash-funktionen SHA-1 och en maskeringsfunktion generation av MGF1 med SHA-1.  

#### <a name="signverify"></a>INLOGGNING/KONTROLLERA

-   **RS256** – RSASSA-PKCS-v1_5 med SHA-256. Program som angetts digest-värdet måste beräknas med hjälp av SHA-256 och måste vara 32 byte långt.  
-   **RS384** – RSASSA-PKCS-v1_5 med hjälp av SHA-384. Program som angetts digest-värdet måste beräknas med hjälp av SHA-384 och måste vara 48 byte långt.  
-   **RS512** – RSASSA-PKCS-v1_5 med hjälp av SHA-512. Program som angetts sammanfattad värdet måste beräknas med hjälp av SHA-512 och måste vara 64 byte långt.  
-   **RSNULL** -Se [RFC2437] specialiserade användningsfall att aktivera vissa TLS-scenarier.  

###  <a name="key-operations"></a>Viktiga aktiviteter

Key Vault har stöd för följande åtgärder på objekt med nycklar:  

-   **Skapa**: Kan en klient för att skapa en nyckel i Key Vault. Värdet för nyckeln genereras av Key Vault och lagras, och inte är släppt till klienten. Asymmetriska nycklar skapas i Key Vault.  
-   **Importera**: Kan en klient för att importera en befintlig nyckel till Nyckelvalvet. Asymmetriska nycklar kan importeras till Key Vault med ett antal olika paketering metoder i JWK-konstruktion. 
-   **Uppdatera**: Kan en klient med tillräcklig behörighet för att ändra metadata (nyckelattribut) som är associerade med en nyckel som tidigare lagras i Key Vault.  
-   **Ta bort**: Kan en klient med tillräcklig behörighet för att ta bort en nyckel från Key Vault.  
-   **Lista**: Kan en klient att lista alla nycklar i ett visst Nyckelvalv.  
-   **Lista över versioner**: Kan en klient att lista alla versioner av en viss nyckel i en viss Key Vault.  
-   **Hämta**: Kan en klient hämta de offentliga delarna av en viss nyckel i Key Vault.  
-   **Backup**: Exporterar en nyckel i ett skyddat format.  
-   **Återställa**: Importerar en tidigare säkerhetskopierade nyckel.  

Mer information finns i [viktiga åtgärder i Key Vault REST API-referensen](/rest/api/keyvault).  

När du har skapat en nyckel i Key Vault, kan följande kryptografiska åtgärder utföras med hjälp av nyckeln:  

-   **Logga och kontrollera**: Den här åtgärden är strikt, ”logga hash” eller ”verifiera hash”, eftersom Key Vault har inte stöd för hashing av innehåll som en del av signaturen har skapats. Program bör hash-data signeras lokalt och sedan begära att Key Vault-loggar hash-värdet. Verifiering av signerad hashvärden stöds som en bekvämlighet åtgärd för program som inte kan ha åtkomst till [public] nyckelmaterial. För bästa programprestanda, kontrollerar du att åtgärder utförs lokalt.  
-   **Nyckeln kryptering / Wrapping**: En nyckel som lagras i Key Vault kan användas för att skydda en annan nyckel, vanligtvis en symmetrisk innehåll krypteringsnyckel (CEK). När det är asymmetrisk nyckel i Key Vault, används nyckelkryptering. Till exempel är RSA-OAEP och WRAPKEY/UNWRAPKEY-åtgärder likvärdiga med kryptera/DEKRYPTERA. När nyckeln i Key Vault är symmetriska används nyckeln som wrapping. Till exempel AES-KW. WRAPKEY-åtgärden stöds för att underlätta för för program som inte kan ha åtkomst till [public] nyckelmaterial. För bästa programprestanda, bör WRAPKEY åtgärder utföras lokalt.  
-   **Kryptera och dekryptera**: En nyckel som lagras i Key Vault kan användas för att kryptera eller dekryptera ett enda block av data. Storleken på blocket bestäms av nyckeltyp och valda krypteringsalgoritm. Åtgärden Encrypt har angetts för att underlätta för program som inte kan ha åtkomst till [public] nyckelmaterial. För bästa programprestanda, kryptera åtgärder bör utföras lokalt.  

När WRAPKEY/UNWRAPKEY med asymmetriska nycklar kan verka överflödiga (som åtgärden motsvarar att kryptera/DEKRYPTERA), är viktigt att använda olika åtgärder. Skillnaden ger semantiska och auktorisering uppdelning av dessa åtgärder och konsekvens när andra viktiga typer som stöds av tjänsten.  

Key Vault stöder inte exportåtgärder. När en nyckel har etablerats i systemet inte kan extraheras eller dess nyckelmaterial ändras. Användare av Key Vault kan dock kräva sin nyckel för andra användningsfall, till exempel som när det har tagits bort. I det här fallet kan de använda åtgärder för säkerhetskopiering och återställning för att exportera/importera nyckeln i ett skyddat format. Nycklar som skapas av BACKUP-åtgärden kan inte användas utanför Key Vault. Importen kan också användas mot flera instanser av Key Vault.  

Användarna kan begränsa någon av de kryptografiska åtgärder som har stöd för Key Vault på basis av per nyckel med hjälp av egenskapen key_ops i JWK-objektet.  

Mer information om JWK objekt finns i [JSON Web nyckel (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="key-attributes"></a>Nyckelattribut

Förutom nyckelmaterial, kan följande attribut anges. I en JSON-begäran, attribut nyckelord och klammerparenteser, ' {' '}', krävs även om det finns inga attribut har angetts.  

- *aktiverad*: boolesk, valfritt, standardvärdet är **SANT**. Anger om nyckeln är aktiverade och riktlinje för kryptografiska åtgärder. Den *aktiverat* attributet används tillsammans med *nbf* och *exp*. När en åtgärd som sker mellan *nbf* och *exp*, kommer endast tillåtna om *aktiverat* är inställd på **SANT**. Åtgärder utanför den *nbf* / *exp* fönster automatiskt tillåts inte, utom för vissa åtgärden under [särskilda villkor](#date-time-controlled-operations).
- *nbf*: Valfritt, IntDate, som standard är nu. Den *nbf* (inte före) attributet anger den tid som inte får nyckeln användas för kryptografiska åtgärder, utom för vissa åtgärden under [särskilda villkor](#date-time-controlled-operations). Bearbetningen av den *nbf* attributet kräver att aktuellt datum och tid måste vara efter eller lika med inte-före datum/tid som anges i den *nbf* attribut. Key Vault kan ha för vissa små spelrum normalt mer än ett par minuter att kompensera för klockan skeva. Värdet måste vara ett tal som innehåller ett IntDate-värde.  
- *EXP*: IntDate, valfritt, standardvärdet är ”alltid”. Den *exp* (upphör att gälla) attributet anger förfallotid eller senare som nyckeln får inte användas för en kryptografisk åtgärd, utom för vissa åtgärden under [särskilda villkor](#date-time-controlled-operations). Bearbetningen av den *exp* attributet kräver att aktuellt datum och tid måste vara innan det upphör att gälla datum/tid som anges i den *exp* attribut. Key Vault kan ha för vissa små spelrum vanligtvis mer än ett par minuter att kompensera för klockan skeva. Värdet måste vara ett tal som innehåller ett IntDate-värde.  

Det finns ytterligare skrivskyddade attribut som ingår i alla svar som innehåller viktiga punkter:  

- *skapade*: IntDate valfritt. Den *skapade* attributet anger när den här versionen av nyckeln skapades. Värdet är null för nycklar som har skapats innan det här attributet. Värdet måste vara ett tal som innehåller ett IntDate-värde.  
- *Uppdatera*: IntDate valfritt. Den *uppdateras* attributet anger när den här versionen av nyckeln uppdaterades. Värdet är null för nycklar som senast uppdaterades innan det här attributet. Värdet måste vara ett tal som innehåller ett IntDate-värde.  

Läs mer på IntDate och andra datatyper, [datatyper](#data-types)  

#### <a name="date-time-controlled-operations"></a>Datum / tid-kontrollerad åtgärder

Inte ännu giltig och har upphört att gälla nycklar, utanför den *nbf* / *exp* fönstret fungerar för **dekryptera**, **unwrap**, och **Kontrollera** åtgärder (inte returnerar 403, förbjuden). Anledningen till att med tillståndet som inte ännu giltig är att tillåta en nyckel som ska testas innan användning i produktion. Anledningen till att använda gått ut är att tillåta återställningsåtgärder på data som skapades när nyckeln är giltig. Dessutom kan du inaktivera åtkomst till en nyckel med Key Vault-principer eller genom att uppdatera den *aktiverat* nyckelattributet till **FALSKT**.

Mer information om datatyper finns i [datatyper](#data-types).

Mer information om andra möjliga attribut finns i den [JSON Web nyckel (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="key-tags"></a>Viktiga taggar

Du kan ange ytterligare programspecifik metadata i form av taggar. Nyckelvalv stöder upp till 15 taggar, som kan ha ett namn för 256 tecken och ett värde med 256 tecken.  

>[!Note]
>Taggar är läsas av en anropare om de har den *lista* eller *hämta* behörighet till den objekttypen (nycklar, hemligheter eller certifikat).

###  <a name="key-access-control"></a>Nyckelåtkomstkontroll

Åtkomstkontroll för nycklar som hanteras av Key Vault har angetts på nivån för ett Nyckelvalv som fungerar som behållare av nycklar. Principen för åtkomstkontroll för nycklar, skiljer sig från principen för åtkomstkontroll för hemligheter i samma Key Vault. Användare kan skapa en eller flera valv för att lagra nycklar och krävs för att underhålla scenariot rätt segmentering och hantering av nycklar. Åtkomstkontroll för nycklar är oberoende av åtkomstkontroll för hemligheter.  

Följande behörigheter kan beviljas, på en per användare / service principal-basis i nycklar-åtkomstkontrollpost på ett valv. De här behörigheterna speglar nära de åtgärder som tillåts på ett nyckelobjekt:  

- Behörigheter för nyckelhanteringsåtgärder
  - *Hämta*: Läs den offentliga delen av en nyckel, plus dess attribut
  - *list*: Lista nycklar eller versioner av en nyckel som lagras i key vault
  - *update*: Uppdatera attribut för en nyckel
  - *Skapa*: Skapa nya nycklar
  - *Importera*: Importera en nyckel till ett nyckelvalv
  - *Ta bort*: Ta bort nyckeln objektet
  - *recover*: Återställa en borttagen nyckel
  - *Backup*: Säkerhetskopiera en nyckel i key vault
  - *Återställa*: Återställa en säkerhetskopierad nyckel till ett nyckelvalv

- Behörigheter för kryptografiska åtgärder
  - *dekryptera*: Använd för att ta bort skyddet från en sekvens av byte
  - *kryptera*: Använd för att skydda en godtycklig sekvens av byte
  - *unwrapKey*: Använd för att ta bort skyddet från omslutna symmetriska nycklar
  - *wrapKey*: Använd för att skydda en symmetrisk nyckel
  - *Kontrollera*: Använd för att verifiera sammandrag  
  - *sign*: Använd för att logga sammandrag
    
- Behörigheter för privilegierade åtgärder
  - *Rensa*: Rensa (ta bort permanent) en nyckel som har tagits bort

Mer information om hur du arbetar med nycklar finns i [viktiga åtgärder i Key Vault REST API-referensen](/rest/api/keyvault). Mer information om hur du etablerar behörigheter finns i [valv – skapa eller uppdatera](/rest/api/keyvault/vaults/createorupdate) och [valv – uppdatera åtkomstprincipen](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="key-vault-secrets"></a>Key Vault-hemligheter 

### <a name="working-with-secrets"></a>Arbeta med hemligheter

Från en utvecklares perspektiv, Key Vault-API: er för att acceptera och returnera hemliga värden som strängar. Internt, Key Vault lagrar och hanterar hemligheter som sekvenser av oktetter (8-bitars byte), med en maximal storlek på 25 kB som varje. Key Vault-tjänsten tillhandahåller inte semantik för hemligheter. Det bara tar emot data, krypterar dem, lagrar den och returnerar en hemlig identifierare (”id”). Identifieraren kan användas för att hämta hemligheten vid ett senare tillfälle.  

För mycket känsliga data bör klienter för ytterligare skyddslager för data. Kryptera data med hjälp av en Skyddsnyckel för separat före lagring i Key Vault är ett exempel.  

Key Vault stöder också fältet contentType för hemligheter. Klienter kan ange innehållstypen för en hemlighet Aktivitetsanteckningar tolka hemliga data när de hämtas. Den maximala längden på det här fältet är 255 tecken. Det finns inga fördefinierade värden. Den föreslagna användningen är som ett tips för att tolka den hemliga data. Exempelvis kan kan en implementering lagra både lösenord och certifikat som hemligheter och sedan använda det här fältet för att differentiera. Det finns inga fördefinierade värden.  

### <a name="secret-attributes"></a>Hemlig attribut

Följande attribut kan anges förutom de hemliga data:  

- *EXP*: Valfritt, IntDate, som standard är **alltid**. Den *exp* (upphör att gälla) attributet anger förfallotid eller senare som den hemliga bör inte att hämta data, utom i [viss situationer](#date-time-controlled-operations). Det här fältet är för **endast i informationssyfte** syfte endast som informerar användare av key vault-tjänsten en särskild hemlighet inte får användas. Värdet måste vara ett tal som innehåller ett IntDate-värde.   
- *nbf*: Valfritt, IntDate, som standard är **nu**. Den *nbf* (inte före) attributet anger den tid som hemliga data inte hämtas, utom i [viss situationer](#date-time-controlled-operations). Det här fältet är för **endast i informationssyfte** i utvärderingssyfte. Värdet måste vara ett tal som innehåller ett IntDate-värde. 
- *aktiverad*: boolesk, valfritt, standardvärdet är **SANT**. Det här attributet anger om hemliga data kan hämtas. Aktiverade attributet används tillsammans med *nbf* och *exp* när en åtgärd som sker mellan *nbf* och *exp*, kommer bara att tillåtna om aktiverat är inställt på **SANT**. Åtgärder utanför den *nbf* och *exp* fönstret är automatiskt otillåtna, utom i [viss situationer](#date-time-controlled-operations).  

Det finns ytterligare skrivskyddade attribut som ingår i alla svar som innehåller hemliga attribut:  

- *skapade*: IntDate valfritt. Skapade attributet anger när den här versionen av hemligheten har skapats. Det här värdet är null för hemligheter som skapats innan det här attributet. Värdet måste vara ett tal som innehåller ett IntDate-värde.  
- *Uppdatera*: IntDate valfritt. Uppdaterade attributet anger när den här versionen av hemligheten uppdaterades. Det här värdet är null för hemligheter som senast uppdaterades innan det här attributet. Värdet måste vara ett tal som innehåller ett IntDate-värde.

#### <a name="date-time-controlled-operations"></a>Datum / tid-kontrollerad åtgärder

En hemlighet **hämta** åtgärden kommer att fungera för inte ännu giltiga och har upphört att gälla hemligheter, utanför den *nbf* / *exp* fönster. Anropa en hemlighet **hämta** åtgärden för en hemlighet som inte ännu giltiga kan användas för testning. Hämtar (**hämta**cering) en hemlighet som har upphört att gälla, kan användas för återställningsåtgärder.

Mer information om datatyper finns i [datatyper](#data-types).  

### <a name="secret-access-control"></a>Åtkomstkontroll till hemlighet

Åtkomstkontroll för hemligheter som hanteras i Key Vault, har angetts på nivån för det Nyckelvalv som innehåller dessa hemligheter. Principen för åtkomstkontroll för hemligheter, skiljer sig från principen för åtkomstkontroll för nycklar i samma Key Vault. Användare kan skapa en eller flera valv för att lagra hemligheter och krävs för att underhålla scenariot rätt segmentering och hantering av hemligheter.   

Följande behörigheter kan användas på basis av per huvudnamn, i den hemligheter posten på ett valv och spegla nära de åtgärder som tillåts på hemliga objekt:  

- Behörigheter för hemlighetshanteringsåtgärder
  - *Hämta*: Läsa en hemlighet  
  - *list*: Lista hemligheter eller versioner av en hemlighet som lagras i Key Vault  
  - *Ange*: Skapa en hemlighet  
  - *Ta bort*: Ta bort en hemlighet  
  - *recover*: Återställa en borttagen hemlighet
  - *Backup*: Säkerhetskopiera en hemlighet i key vault
  - *Återställa*: Återställa en säkerhetskopia hemlighet till ett nyckelvalv

- Behörigheter för privilegierade åtgärder
  - *Rensa*: Rensa (ta bort permanent) en hemlighet som har tagits bort

Läs mer om hur du arbetar med hemligheter [åtgärder med hemligheter i Key Vault REST API-referensen](/rest/api/keyvault). Mer information om hur du etablerar behörigheter finns i [valv – skapa eller uppdatera](/rest/api/keyvault/vaults/createorupdate) och [valv – uppdatera åtkomstprincipen](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Hemlig taggar  
Du kan ange ytterligare programspecifik metadata i form av taggar. Nyckelvalv stöder upp till 15 taggar, som kan ha ett namn för 256 tecken och ett värde med 256 tecken.  

>[!Note]
>Taggar är läsas av en anropare om de har den *lista* eller *hämta* behörighet till den objekttypen (nycklar, hemligheter eller certifikat).

## <a name="key-vault-certificates"></a>Key Vault-certifikat

Support för Key Vault-certifikat ger för hantering av din x509 certifikat och följande:  

-   Ger en certifikat-ägaren att skapa ett certifikat via en process för att skapa Key Vault eller import av ett befintligt certifikat. Innehåller både självsignerade och certifikatutfärdare genererade certifikat.
-   Ger en Key Vault-certifikat ägaren att implementera säker lagring och hantering av X509 certifikat utan interaktion med privat nyckelmaterial.  
-   Ger en certifikat-ägaren att skapa en princip som dirigerar Key Vault för att hantera livscykeln för ett certifikat.  
-   Gör att certifikatet ägare att tillhandahålla kontaktinformation för meddelande om livscykeln för händelser för förfallodatum och förnyelse av certifikat.  
-   Har stöd för automatisk förnyelse med valda utfärdare - Key Vault partner X509 certifikat providers / certifikatutfärdare.

>[!Note]
>Icke-samarbetade providers/myndigheter är också tillåtna men stöder inte funktionen för automatisk förnyelse.

### <a name="composition-of-a-certificate"></a>Sammansättning av ett certifikat

När ett Key Vault-certifikat skapas, skapas också en adresserbara nyckel och hemlighet med samma namn. Key Vault-nyckeln tillåter viktiga åtgärder och hemlighet för Key Vault gör att hämta certifikatvärdet för som en hemlighet. Ett Key Vault-certifikat innehåller även offentliga x509 certifikat metadata.  

Identifierare och versionen av certifikat liknar den för nycklar och hemligheter. En specifik version av en adresserbara nyckel och hemlighet som skapats med Key Vault-certifikat-versionen är tillgänglig i svaret för Key Vault-certifikat.
 
![Certifikat är komplexa objekt](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Nyckeln kan exporteras eller inte kan exporteras

När ett Key Vault-certifikat har skapats, kan det hämtas från den adresserbara hemligheten med den privata nyckeln i PFX eller PEM-format. Principen som används för att skapa certifikatet måste uppge att nyckeln kan exporteras. Om principen anger inte kan exporteras, inte en del av värdet när du hämtar som en hemlighet med den privata nyckeln.  

Den adresserbara nyckeln blir mer relevant med certifikat som inte kan exporteras KV. Den adresserbara KV nyckeln operations mappas från *keyusage* i KV certifikatprincip som används för att skapa certifikatet KV.  

Två typer av nyckeln stöds – *RSA* eller *RSA HSM* med certifikat. Exportera tillåts endast med RSA, stöds inte av RSA HSM.  

### <a name="certificate-attributes-and-tags"></a>Certifikatattribut och taggar

Förutom certifikatmetadata en adresserbara nyckel och hemlighet adresserbara innehåller också ett Key Vault-certifikat attribut och taggar.  

#### <a name="attributes"></a>Attribut

Certifikatattribut är speglad till attributen för den adresserbara nyckel och hemlighet som skapas när KV certifikat skapas.  

Ett Key Vault-certifikat har följande attribut:  

-   *aktiverad*: boolesk, valfritt, standardvärdet är **SANT**. Du kan ange att indikera om certifikatdata hämtas som hemlig eller operativt som en nyckel. Också används tillsammans med *nbf* och *exp* när en åtgärd som sker mellan *nbf* och *exp*, och endast beviljas om aktiverat har angetts till true. Åtgärder utanför den *nbf* och *exp* fönster tillåts inte automatiskt.  

Det finns ytterligare skrivskyddade attribut som ingår i svaret:

-   *skapade*: IntDate: Anger när den här versionen av certifikatet skapades.  
-   *Uppdatera*: IntDate: Anger när den här versionen av certifikatet uppdaterades.  
-   *EXP*: IntDate: innehåller värdet för giltighetstiden för x509 certifikat.  
-   *nbf*: IntDate: innehåller värdet för datumet då x509 certifikat.  

> [!Note] 
> Om ett Key Vault-certifikat upphör att gälla, är det adresserbara nyckel och hemlighet sluta fungera.  

#### <a name="tags"></a>Taggar

 Klienten angivna ordlista med nyckelvärdepar, liknar taggar i nycklar och hemligheter.  

 > [!Note]
> Taggar är läsas av en anropare om de har den *lista* eller *hämta* behörighet till den objekttypen (nycklar, hemligheter eller certifikat).

### <a name="certificate-policy"></a>Certifikatprincip

En certifikatprincip innehåller information om hur du skapar och hanterar livscykeln för ett Key Vault-certifikat. När ett certifikat med privat nyckel har importerats till key vault, en standardprincip skapas genom att läsa x509 certifikat.  

När ett Key Vault-certifikat skapas från grunden, måste en princip anges. Principen anger hur du skapar den här versionen för Key Vault-certifikat eller nästa version av Key Vault-certifikat. När en princip har upprättats, det är inget krav med efterföljande skapa åtgärder för framtida versioner. Det finns endast en instans av en princip för alla versioner av ett Key Vault-certifikat.  

En certifikatprincip innehåller följande information på en hög nivå:  

-   X509 certifikat egenskaper: Innehåller namn på certifikatmottagare, Alternativt ämnesnamn och andra egenskaper som används för att skapa en x509 certifikatbegäran.  
-   Nyckelegenskaper: innehåller nyckeltyp nyckellängd, kunna exporteras, och återanvända nyckelfält. De här fälten instruera nyckelvalv om hur du skapar en nyckel.  
-   Egenskaper för hemlighet: innehåller egenskaper för hemlighet, till exempel innehållstyp adresserbara hemlighet att generera hemligt värde, för att hämta certifikatet som en hemlighet.  
-   Livslängd åtgärder: innehåller åtgärder för livslängd för KV certifikatet. Varje livstidsåtgärd innehåller:  

     - Utlösare: angetts via dagar före förfallodatum eller livslängd span procent  

     - Åtgärd: Ange åtgärdstypen – *emailContacts* eller *automatisk förnyelse*  

-   Utgivare: Parametrar för certifikatutfärdaren som du använder för att utfärda x509 certifikat.  
-   Attribut: innehåller attribut som är associerade med principen  

#### <a name="x509-to-key-vault-usage-mapping"></a>X509 för Key Vault-mappning för användning

I följande tabell representerar mappningen av x509 nyckelanvändning principen till effektiva nyckelåtgärder av en nyckel som skapats som en del av en Key Vault-certifikat har skapats.

|**X509 flaggor för nyckelanvändning**|**Key Vault viktiga ops**|**Standardbeteende**|
|----------|--------|--------|
|DataEncipherment|kryptera, dekryptera| Gäller inte |
|DecipherOnly|dekryptera| Gäller inte  |
|DigitalSignature|signera, verifiera| Key Vault standard utan en specifikation för användning vid tidpunkten för skapandet av certifikat | 
|EncipherOnly|encrypt| Gäller inte |
|KeyCertSign|signera, verifiera|Gäller inte|
|KeyEncipherment|wrapKey, unwrapKey| Key Vault standard utan en specifikation för användning vid tidpunkten för skapandet av certifikat | 
|Oavvislighet|signera, verifiera| Gäller inte |
|crlsign|signera, verifiera| Gäller inte |

### <a name="certificate-issuer"></a>Certifikatutfärdare

Ett objekt för Key Vault-certifikat innehåller en konfiguration som används för att kommunicera med en provider för utfärdare av valda certifikatet att ordning x509 certifikat.  

-   Key Vault samarbetar med följande utfärdare certifikatproviders för SSL-certifikat

|**Providernamn**|**Platser**|
|----------|--------|
|DigiCert|Stöds på alla nyckelvalv serviceplatser i offentliga moln och Azure Government|
|GlobalSign|Stöds på alla nyckelvalv serviceplatser i offentliga moln och Azure Government|

Innan du kan skapa en certifikatutfärdare i Key Vault, måste följande nödvändiga steg 1 och 2 åstadkommas har.  

1. Kom igång med Certificate Authority (CA)-Providers  

    -   En organisation-administratör måste integrera företaget (t.ex. Contoso) med minst en CA-provider.  

2. Administratören skapar som begär autentiseringsuppgifter för Key Vault för att registrera (och förnya) SSL-certifikat  

    -   Innehåller konfigurationen som ska användas för att skapa ett objekt för utfärdare av providern i nyckelvalvet  

Mer information om hur du skapar utfärdarobjekt från Certificates-portalen finns i den [Nyckelvalvscertifikat blogg](https://aka.ms/kvcertsblog)  

Key Vault gör för att skapa flera utfärdarobjekt med olika utfärdare providerkonfigurationen. När ett utfärdare-objekt skapas, kan dess namn refereras i en eller flera certifikatprinciper för. Refererar till objektet utfärdare instruerar Key Vault för att använda konfigurationen som anges i objektet utfärdaren när du begär x509 certifikat från CA-providern under skapandet av certifikat och förnyelse.  

Utfärdarobjekt skapas i valvet och kan bara användas med KV certifikat i samma valv.  

### <a name="certificate-contacts"></a>Certifikatskontakter

Certifikatskontakterna innehåller kontaktuppgifter för att skicka meddelanden som utlöses av certifikat livslängd för händelser. Kontakter informationen delas av alla certifikat i nyckelvalvet. Ett meddelande skickas till den angivna kontakter för en händelse för alla certifikat i nyckelvalvet.  

Om en certifikatprincip anges för automatisk förnyelse, skickas ett meddelande om följande händelser.  

- Innan du certifikatförnyelse
- Efter certifikatförnyelse, anger om certifikatet har förnyats, eller om ett fel uppstod, kräver manuell förnyelse av certifikatet.  

  När en certifikatprincip för som är inställt på manuellt förnyas (endast email), skickas ett meddelande när det är dags att förnya certifikatet.  

### <a name="certificate-access-control"></a>Åtkomstkontroll för certifikat

 Åtkomstkontroll för certifikat hanteras av Key Vault och tillhandahålls av Key Vault som innehåller dessa certifikat. Principen för åtkomstkontroll för certifikat skiljer sig från principerna för åtkomstkontroll för nycklar och hemligheter i samma Key Vault. Användare kan skapa en eller flera valv för att lagra certifikat, att underhålla scenariot rätt segmentering och hantering av certifikat.  

 Följande behörigheter kan användas på basis av per huvudnamn, i åtkomstkontrollpost för hemligheter i key vault och noggrant speglingar de åtgärder som tillåts på hemliga objekt:  

- Behörigheter för certifikatshanteringsåtgärder
  - *Hämta*: Hämta den aktuella versionen av certifikat eller någon version av ett certifikat 
  - *list*: Lista de aktuella certifikat eller versioner av ett certifikat  
  - *update*: Uppdatera ett certifikat
  - *Skapa*: Skapa ett Key Vault-certifikat
  - *Importera*: Importera certifikat material till ett Key Vault-certifikat
  - *Ta bort*: Ta bort ett certifikat, dess princip och alla dess versioner  
  - *recover*: Återställa ett borttaget certifikat
  - *Backup*: Säkerhetskopiera ett certifikat i key vault
  - *Återställa*: Återställa en säkerhetskopierad certifikat till ett nyckelvalv
  - *managecontacts*: Hantera kontakter för Key Vault-certifikat  
  - *manageissuers*: Hantera Key Vault myndigheter/certifikatutfärdare
  - *getissuers*: Hämta ett certifikat myndigheter/utfärdare
  - *listissuers*: Lista över en certifikatets utfärdare/utfärdare  
  - *setissuers*: Skapa eller uppdatera en Key Vault-certifikat myndigheter/utfärdare  
  - *deleteissuers*: Ta bort ett Key Vault-certifikat myndigheter/utfärdare  
 
- Behörigheter för privilegierade åtgärder
  - *Rensa*: Rensa (ta bort permanent) ett certifikat som har tagits bort

Mer information finns i den [certifikatåtgärder i Key Vault REST API-referensen](/rest/api/keyvault). Mer information om hur du etablerar behörigheter finns i [valv – skapa eller uppdatera](/rest/api/keyvault/vaults/createorupdate) och [valv – uppdatera åtkomstprincipen](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="azure-storage-account-key-management"></a>Azure Storage-konto nyckelhantering

Key Vault kan hantera Azure storage-kontonycklar:

- Key Vault kan internt lista nycklar (sync) med ett Azure storage-konto. 
- Key Vault återskapar (roterar) nycklarna regelbundet.
- Nyckelvärden returneras aldrig i svaret till anroparen.
- Key Vault hanterar nycklarna för både storage-konton och klassiska lagringskonton.

Mer information finns i [Azure Key Vault-Lagringskontonycklar](key-vault-ovw-storage-keys.md)

### <a name="storage-account-access-control"></a>Åtkomstkontroll för Storage-konto

Följande behörigheter kan användas när auktorisera en användare eller program huvudnamn för att utföra åtgärder på ett hanterat lagringskonto:  

- Behörigheter för hanterat lagringskonto och SaS-definitionen åtgärder
  - *Hämta*: Hämtar information om ett lagringskonto 
  - *list*: Lista lagringskonton som hanteras av ett Key Vault
  - *update*: Uppdatera ett storage-konto
  - *Ta bort*: Ta bort ett lagringskonto  
  - *recover*: Återställa ett borttaget lagringskonto
  - *Backup*: Säkerhetskopiera en storage-konto
  - *Återställa*: Återställa en säkerhetskopierad storage-konto till ett Nyckelvalv
  - *Ange*: Skapa eller uppdatera ett storage-konto
  - *regeneratekey*: Återskapa en angivna nyckelvärdet för ett lagringskonto
  - *getsas*: Få information om en SAS-definitionen för ett lagringskonto
  - *listsas*: Lista storage SAS-definitioner för ett lagringskonto
  - *deletesas*: Ta bort en SAS-definition från ett lagringskonto
  - *setsas*: Skapa eller uppdatera en ny SAS-definitionen/attribut för ett lagringskonto

- Behörigheter för privilegierade åtgärder
  - *Rensa*: Rensa (ta bort permanent) ett hanterat lagringskonto

Mer information finns i den [konto lagringsåtgärder i Key Vault REST API-referensen](/rest/api/keyvault). Mer information om hur du etablerar behörigheter finns i [valv – skapa eller uppdatera](/rest/api/keyvault/vaults/createorupdate) och [valv – uppdatera åtkomstprincipen](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Se även

- [Autentisering, begäranden och svar](authentication-requests-and-responses.md)
- [Key Vault-versioner](key-vault-versions.md)
- [Utvecklarguide för Key Vault](/azure/key-vault/key-vault-developers-guide)
