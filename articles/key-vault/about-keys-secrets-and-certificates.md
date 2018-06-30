---
title: Om nycklar och hemligheter certifikat
description: Översikt över REST-gränssnittet och KV information för utvecklare
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: abd1b743-1d58-413f-afc1-d08ebf93828a
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: dd1bb6117c0360e67783434c980c56b5f6ae7f9f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110252"
---
# <a name="about-keys-secrets-and-certificates"></a>Om nycklar och hemligheter certifikat
Azure Key Vault kan du lagra och använda kryptografiska nycklar i Microsoft Azure-miljön. Key Vault stöder flera nyckeltyper och algoritmer och möjliggör användning av maskinvara säkerhet moduler (HSM) för högt värderade nycklar. Dessutom kan Key Vault användare på ett säkert sätt lagra hemligheter. Hemligheter är begränsad storlek oktett-objekt med några särskilda semantik. Key Vault stöder även certifikat som är byggda på nycklar och hemligheter och lägga till en funktion för automatisk förnyelse.

Mer allmän information om Azure Key Vault finns [vad är Azure Key Vault?](/azure/key-vault/key-vault-whatis)

**Key Vault allmän information**

-   [Stöder standarder](about-keys-secrets-and-certificates.md#BKMK_Standards)
-   [Datatyper](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  
-   [Objekt-ID: n och versionshantering](about-keys-secrets-and-certificates.md#BKMK_ObjId)  

**Om nycklar**

-   [Nycklar och nyckeltyper](about-keys-secrets-and-certificates.md#BKMK_KeyTypes)  
-   [RSA-algoritmer](about-keys-secrets-and-certificates.md#BKMK_RSAAlgorithms)  
-   [RSA-HSM algoritmer](about-keys-secrets-and-certificates.md#BKMK_RSA-HSMAlgorithms)  
-   [Kryptografiska skydd](about-keys-secrets-and-certificates.md#BKMK_Cryptographic)
-   [Åtgärder med nyckel](about-keys-secrets-and-certificates.md#BKMK_KeyOperations)  
-   [Nyckelattribut](about-keys-secrets-and-certificates.md#BKMK_KeyAttributes)  
-   [Nyckeltaggar](about-keys-secrets-and-certificates.md#BKMK_Keytags)  

**Om hemligheter** 

-   [Arbeta med hemligheter](about-keys-secrets-and-certificates.md#BKMK_WorkingWithSecrets)  
-   [Hemlig attribut](about-keys-secrets-and-certificates.md#BKMK_SecretAttrs)  
-   [Hemlig taggar](about-keys-secrets-and-certificates.md#BKMK_SecretTags)  
-   [Hemlig åtkomstkontroll](about-keys-secrets-and-certificates.md#BKMK_SecretAccessControl)  

**Om certifikat**

-   [Sammansättning av ett certifikat](#BKMK_CompositionOfCertificate)  
-   [Certifikat-attribut och taggar](#BKMK_CertificateAttributesAndTags)  
-   [Certifikatprincip](#BKMK_CertificatePolicy)  
-   [Certifikatutfärdare](#BKMK_CertificateIssuer)  
-   [Certifikat för kontakter](#BKMK_CertificateContacts)  
-   [Åtkomstkontroll för certifikat](#BKMK_CertificateAccessControl)  

--

## <a name="key-vault-general-details"></a>Key Vault allmän information

I följande avsnitt ger allmän information som är tillämpliga över implementeringen av Azure Key Vault-tjänsten.

###  <a name="BKMK_Standards"></a> Stöder standarder

JavaScript Object Notation (JSON) och JavaScript Object signering och kryptering (JOSE) specifikationer är viktig bakgrundsinformation.  

-   [JSON Web nyckel (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web-kryptering (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web algoritmer (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web signatur (JWS)](http://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="BKMK_DataTypes"></a> Datatyper

Referera till den [JOSE specifikationer](#BKMK_Standards) för relevanta datatyper för nycklar, kryptering och signering.  

-   **algoritmen** -en algoritm som stöds för en nyckel åtgärden, till exempel RSA1_5  
-   **cipherText-värdet** -cipher text oktetter, kodad med Base64URL  
-   **kondensatvärdet** -resultatet av en hash-algoritm kodad med Base64URL  
-   **nyckeltypen** -en av de viktiga typerna som stöds, till exempel RSA.  
-   **värdet för oformaterad text** -klartext oktetter, kodad med Base64URL  
-   **signaturen värdet** - utdata för en signaturalgoritm kodad med Base64URL  
-   **base64URL** -en Base64URL [RFC4648] kodade binärt värde  
-   **Booleskt** -true eller false  
-   **Identity** - en identitet från Azure Active Directory (AAD).  
-   **IntDate** – ett decimalt JSON-värde som representerar antalet sekunder från 1970-01-01T0:0:0Z UTC tills angivet datum/tid i UTC. Mer information om datum finns [RFC3339] / gånger i allmänhet och i synnerhet.  

###  <a name="BKMK_ObjId"></a> Objekt-ID: n och versionshantering

Objekt som lagras i Azure Key Vault behåller versioner när en ny instans av ett objekt skapas och varje version har en unik identifierare och URL: en. När ett objekt skapas på en unik identifierare och har markerats som den aktuella versionen av objektet. Skapa en ny instans med samma objektnamn ger en unik identifierare för det nya objektet och gör att den blir den aktuella versionen.  

Objekt i Azure Key Vault kan åtgärdas med den aktuella identifieraren eller identifierare med en specifik version. Till exempel medför ges en nyckel med namnet ”MasterKey”, utför åtgärder med den aktuella identifieraren att använda den senaste tillgängliga versionen. Utför åtgärder med identifieraren versionsspecifika medför att använda den specifika versionen av objektet.  

Objekt som identifieras i Azure Key Vault med en URL så att inga två objekt i systemet, oavsett geografiska plats, har samma Webbadress. Fullständig URL till ett objekt som kallas objekt-ID och består av en del av prefix som identifierar Nyckelvalvet objekttypen, en användare som objektnamn och en Version av objektet. Objektnamnet är skiftlägeskänsliga och inte ändras. Identifierare som inte innehåller den versionen kallas Base identifierare.  

Mer information finns i [autentisering, förfrågningar och svar](authentication-requests-and-responses.md)

En objektidentifierare har följande allmänna format:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Där:  

|||  
|-|-|  
|`keyvault-name`|Namnet för nyckelvalvet i Microsoft Azure Key Vault-tjänsten.<br /><br /> Key Vault namn väljs av användaren och är globalt unika.<br /><br /> Key Vault-namn måste vara en 3 till 24 strängtecken längd som endast innehåller (0-9, a – z, A-Z, och -).|  
|`object-type`|Typ av objektet ”nycklar” eller ”hemligheter”.|  
|`object-name`|En `object-name` är ett som användarnamn för och måste vara unika inom ett Nyckelvalv. Namnet måste vara en sträng 1 127 tecken som innehåller bara 0-9, a-z, A-Z,.|  
|`object-version`|En `object-version` är en systemgenererad, 32 tecken sträng-ID som kan användas för att adressera en unik version av ett objekt.|  

## <a name="key-vault-keys"></a>Key Vault-nycklar

###  <a name="BKMK_KeyTypes"></a> Nycklar och nyckeltyper

Kryptografiska nycklar i Azure Key Vault representeras som JSON Web nyckel [JWK]-objekt. Grundläggande JWK/JWA specifikationer också har utökats för att aktivera nyckeltyper som är unikt i Azure Key Vault-implementeringen, till exempel import av nycklar till Azure Key Vault använder HSM-leverantör (Thales) specifika paketering för att aktivera säker transport av nycklar så att de kan endast användas i Azure Key Vault HSM.  

Den första Azure Key Vault-versionen stöder RSA-nycklar. kommande versioner stöder andra nyckeltyper, till exempel symmetriska och elliptisk kurva.  

-   **RSA**: en 2048-bitars RSA-nyckel. Det här är en ”soft” nyckel som bearbetas i programvaran av Key Vault men lagras krypterat i vila med hjälp av en systemnyckel som är i en HSM. Klienter kan importera en befintlig RSA-nyckel eller begära att Azure Key Vault Generera en.  
-   **RSA-HSM**: en RSA-nyckel som bearbetas i en HSM. HSM RSA-nycklar är skyddade i ett Säkerhetsvärldar för Azure Key Vault HSM (det finns en Säkerhetsvärld per geografisk plats för att underhålla isolering). Klienter kan importera en RSA-nyckel i mjuka form eller genom att exportera från en kompatibel HSM-enheten, eller begära att Azure Key Vault Generera en. Den här nyckeltypen lägger till attributet T till JWK skaffa om du vill utföra HSM nyckelmaterial.  

     Mer information om geografisk gränser finns [Microsoft Azure Trust Center](https://azure.microsoft.com/en-us/support/trust-center/privacy/)  

###  <a name="BKMK_RSAAlgorithms"></a> RSA-algoritmer  
 Följande algoritm identifierare stöds med RSA-nycklar i Azure Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY KRYPTERING/DEKRYPTERING

-   **RSA1_5** -RSAES PKCS1 V1_5 [RFC3447] krypteringsnyckel  
-   **RSA-OAEP** - RSAES med standardparametrarna som anges i RFC 3447 i avsnittet A.2.1 optimala asymmetrisk kryptering utfyllnad (OAEP) [RFC3447]. De standardparametrarna som använder hash-funktion SHA-1 och generation mask funktion av MGF1 med SHA-1.  

#### <a name="signverify"></a>TECKNET/KONTROLLERA

-   **RS256** - RSASSA-PKCS-v1_5 med SHA-256. Kondensatvärdet för program som har angetts måste beräknas med hjälp av SHA-256 och måste vara 32 byte i längd.  
-   **RS384** - RSASSA-PKCS-v1_5 med hjälp av SHA-384. Kondensatvärdet för program som har angetts måste beräknas med hjälp av SHA-384 och måste vara 48 byte i längd.  
-   **RS512** - RSASSA-PKCS-v1_5 med hjälp av SHA-512. Kondensatvärdet för program som har angetts måste beräknas med hjälp av SHA-512 och måste vara 64 byte i längd.  
-   **RSNULL** -Se [RFC2437] en särskild användningsfall att aktivera vissa scenarier med TLS.  

###  <a name="BKMK_RSA-HSMAlgorithms"></a> RSA-HSM algoritmer  
Följande algoritm identifierare stöds med RSA-HSM nycklar i Azure Key Vault.  

### <a name="BKMK_Cryptographic"></a> Kryptografiska skydd

De kryptografiska modulerna som används för Azure Key Vault om HSM eller programvara är FIPS verifieras. Du behöver inte göra något speciellt för körning i FIPS-läge. Om du **skapa** eller **importera** nycklar som HSM-skyddad, de är garanterat bearbetas i HSM verifieras att FIPS 140-2 Level 2 eller senare. Om du **skapa** eller **importera** nycklar som programvaruskyddad och sedan de bearbetas i kryptografimoduler verifieras att FIPS 140-2-nivå 1 eller senare. Mer information finns i [nycklar och nyckeltyper](about-keys-secrets-and-certificates.md#BKMK_KeyTypes).

#### <a name="wrapunwrap-encryptdecrypt"></a>OMSLUTNING/UNWRAP, KRYPTERING/DEKRYPTERING

-   **RSA1_5** -RSAES PKCS1 V1_5 [RFC3447] nyckelkryptering.  
-   **RSA-OAEP** - RSAES med standardparametrarna som anges i RFC 3447 i avsnittet A.2.1 optimala asymmetrisk kryptering utfyllnad (OAEP) [RFC3447]. De standardparametrarna som använder hash-funktion SHA-1 och generation mask funktion av MGF1 med SHA-1.  

 #### <a name="signverify"></a>TECKNET/KONTROLLERA  

-   **RS256** - RSASSA-PKCS-v1_5 med SHA-256. Kondensatvärdet för program som har angetts måste beräknas med hjälp av SHA-256 och måste vara 32 byte i längd.  
-   **RS384** - RSASSA-PKCS-v1_5 med hjälp av SHA-384. Kondensatvärdet för program som har angetts måste beräknas med hjälp av SHA-384 och måste vara 48 byte i längd.  
-   **RS512** - RSASSA-PKCS-v1_5 med hjälp av SHA-512. Kondensatvärdet för program som har angetts måste beräknas med hjälp av SHA-512 och måste vara 64 byte i längd.  
-   RSNULL: Se [RFC2437] en särskild användningsfall att aktivera vissa scenarier med TLS.  

###  <a name="BKMK_KeyOperations"></a> Åtgärder med nyckel

Azure Key Vault stöder följande åtgärder på objekt nycklar:  

-   **Skapa**: gör att en klient kan skapa en nyckel i Azure Key Vault. Värdet för nyckeln genereras av Azure Key Vault och lagras och har inte publicerats till klienten. Asymmetriska (och i framtiden, Elliptic Curve och Symmetric) nycklar kan skapas i Azure Key Vault.  
-   **Importera**: gör att en klient att importera en befintlig nyckel till Azure Key Vault. Asymmetriska (och i framtiden, Elliptic Curve och Symmetric) nycklar kan importeras till Azure Key Vault med hjälp av ett antal olika paketering metoder inom en JWK konstruktion.  
-   **Uppdatera**: gör att en klient med behörighet att ändra metadata (nyckelattribut) som associeras med en nyckel som tidigare lagras i Azure Key Vault.  
-   **Ta bort**: gör att en klient med behörighet att ta bort en nyckel från Azure Key Vault.  
-   **Listan**: gör att en klient att lista alla nycklar i en viss Azure Key Vault.  
-   **Visa en lista över versioner**: gör att en klient att lista alla versioner av en viss nyckel i en viss Azure Key Vault.  
-   **Hämta**: gör att en klient hämta de offentliga delarna av en viss nyckel i en Azure Key Vault.  
-   **Säkerhetskopiering**: exporterar en nyckel i ett skyddat format.  
-   **Återställa**: importerar en tidigare säkerhetskopierade nyckel.  

Mer information finns i [nyckeln operations](/rest/api/keyvault/key-operations).  

När en nyckel har skapats i Azure Key Vault kan kan följande kryptografiska åtgärder utföras med hjälp av nyckeln:  

-   **Logga och kontrollera**: strikt, den här åtgärden är ”logga hash” eller ”verifiera hash” som Azure Key Vault inte har stöd för hashing av innehåll som en del av signaturen har skapats. Program ska hash-data för att vara signerade lokalt och begär sedan den Azure Key Vault logga hash-algoritmen. Verifiering av signerad hashvärden stöds som en bekvämlighet åtgärd för program som inte kanske har åtkomst till [public] nyckelmaterial; Vi rekommenderar att för bästa prestanda, kontrollerar du att utföras lokalt.  
-   **Nyckeln / Wrapping**: en nyckel som lagras i Azure Key Vault kan användas för att skydda en annan nyckel, vanligtvis en symmetrisk innehåll krypteringsnyckel (CEK). När nyckeln i Azure Key Vault är asymmetriska nyckelkryptering används, till exempel RSA-OAEP och WRAPKEY/UNWRAPKEY åtgärder är likvärdiga med kryptera/DEKRYPTERA. När nyckeln i Azure Key Vault är symmetrisk används nyckel wrapping; till exempel AES KW. Åtgärden WRAPKEY stöds i syfte att underlätta för program som inte kanske har åtkomst till [public] nyckelmaterial; Du rekommenderas att, för bästa prestanda, WRAPKEY åtgärder utförs lokalt.  
-   **Kryptera och dekryptera**: en nyckel som lagras i Azure Key Vault kan användas för att kryptera eller dekryptera ett enda block av data, storleken på som bestäms av nyckeltypen och valda krypteringsalgoritm. Kryptera åtgärden tillhandahålls av praktiska skäl för program som inte kanske har åtkomst till [public] nyckelmaterial; Det rekommenderas att för bästa prestanda, kryptera åtgärder utföras lokalt.  

Medan WRAPKEY/UNWRAPKEY med asymmetriska nycklar kan verka överflödiga som åtgärden motsvarar att kryptera/DEKRYPTERA, användning av olika åtgärder anses vara viktigt att ange semantiska och auktorisering avgränsning av dessa åtgärder och konsekvenskontroll När andra viktiga typer stöds av tjänsten.  

Azure Key Vault stöder inte exportåtgärder: när en nyckel har etablerats i systemet inte kan extraheras eller dess nyckelmaterial ändras.  Dock användare av Azure Key Vault som kan kräva sin nyckel för andra användningsfall eller när den har tagits bort i Azure Key Vault kan använda åtgärder för säkerhetskopiering och återställning för att exportera/importera nyckeln i ett skyddat format. Nycklar som skapades under SÄKERHETSKOPIERINGEN är inte användas utanför Azure Key Vault. Importen kan också användas mot flera instanser av Azure Key Vault.  

Användare kan begränsa någon av de kryptografiska åtgärder som har stöd för Azure Key Vault på grundval av per nyckeln med hjälp av egenskapen key_ops för JWK-objektet.  

Mer information om JWK objekt finns [JSON Web nyckel (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="BKMK_KeyAttributes"></a> Nyckelattribut

Förutom nyckelmaterial, kan följande attribut anges. I en JSON-begäran, attribut nyckelord och klammerparenteserna, ' {' '}', krävs även om det finns inga attribut.  

- *aktiverad*: boolean, valfritt, standardvärdet är **SANT**. Anger om nyckeln är aktiverade och användbar för kryptografiska åtgärder. Den *aktiverat* attributet används tillsammans med *nbf* och *exp*. När en åtgärd som sker mellan *nbf* och *exp*, kommer endast tillåtas om *aktiverat* är inställd på **SANT**. Genomförs utanför den *nbf* / *exp* fönstret automatiskt tillåts inte, utom vissa åtgärdstyper under [särskilda villkor](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops).
- *NBF*: IntDate, valfritt, som standard är nu. Den *nbf* (inte före) attributet anger den tid som nyckeln inte får använda för kryptografiska åtgärder, utom vissa åtgärdstyper under [särskilda villkor](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). Bearbetningen av den *nbf* attribut kräver att aktuellt datum och tid måste vara efter eller att inte är lika med-före datum/tid som anges i den *nbf* attribut. Azure Key Vault kan ge för vissa små handtag vanligtvis inte fler än några minuter för att redovisa klockan skeva. Värdet måste vara ett tal med ett värde för IntDate.  
- *EXP*: IntDate, valfritt, standardvärdet är ”alltid”. Den *exp* (förfallotiden) attribut identifierar förfallotid eller senare som nyckeln får inte användas för krypteringsåtgärd utom vissa åtgärdstyper under [särskilda villkor](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). Bearbetningen av den *exp* attribut kräver att aktuellt datum och tid måste vara innan förfallodatum för datum/tid som anges i den *exp* attribut. Azure Key Vault kan ge för vissa små handtag vanligtvis inte fler än några minuter för att redovisa klockan skeva. Värdet måste vara ett tal med ett värde för IntDate.  

Det finns ytterligare skrivskyddad attribut som ingår i alla svar som inte innehåller nyckelattribut:  

- *Skapa*: IntDate, valfria. Den *skapade* attributet anger när den här versionen av nyckeln har skapats. Det här värdet är null för nycklar som skapades före för att lägga till det här attributet. Värdet måste vara ett tal med ett värde för IntDate.  
- *Uppdatera*: IntDate, valfria. Den *uppdateras* attributet anger när den här versionen av nyckeln uppdaterades. Det här värdet är null för nycklar som senast uppdaterades innan för att lägga till det här attributet. Värdet måste vara ett tal med ett värde för IntDate.  

Mer information om IntDate och andra datatyper finns [datatyper](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  

#### <a name="BKMK_key-date-time-ctrld-ops"></a> Datum och tid kontrollerade åtgärder

Inte ännu giltiga och har upphört att gälla nycklar, dessa utanför den *nbf* / *exp* fönstret fungerar för **dekryptera**, **unwrap** och **Kontrollera** operations (inte returnera 403, förbjuden). Motiveringen för att använda inte har giltig status är att tillåta en nyckel som ska testas innan produktion. Motiveringen för att använda gått ut är att tillåta återställningsåtgärder på data som skapades när nyckeln är giltig. Dessutom kan du inaktivera åtkomst till en nyckel med principer för Key Vault eller genom att uppdatera den *aktiverat* nyckelattributet till **FALSKT**.

Mer information om data finns typer, [datatyper](about-keys-secrets-and-certificates.md#BKMK_DataTypes).

Mer information om andra möjliga attribut finns i [JSON Web nyckel (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="BKMK_Keytags"></a> Nyckeltaggar

Du kan ange ytterligare programspecifik metadata i form av taggar. Azure Key Vault stöder upp till 15 taggar, som kan ha ett namn på 256 tecken och ett värde 256 tecken.  

>[!Note]
>Taggar är läsas av en anropare om de har den *lista* eller *hämta* behörighet för den objekttypen; nycklar, hemligheter eller certifikat.

###  <a name="BKMK_KeyAccessControl"></a> Viktiga åtkomstkontroll

Åtkomstkontroll för nycklar som hanteras av Key Vault finns på nivån för ett Nyckelvalv som fungerar som behållare för nycklarna. Det finns en principer för åtkomstkontroll för nycklar som skiljer sig från principer för åtkomstkontroll för hemligheter i samma Nyckelvalvet. Användare kan skapa en eller flera valv för att lagra nycklar och krävs för att upprätthålla lämpliga segmentering av scenariot och hantering av nycklar. Åtkomstkontroll för nycklar är oberoende av åtkomstkontroll för hemligheter.  

Följande behörigheter kan beviljas, på en per användare / service principal villkor nycklar åtkomstkontrollpost i ett valv. Dessa behörigheter spegling nära tillåtna på ett nyckelobjekt åtgärder:  

-   *Skapa*: skapa nya nycklar
-   *Hämta*: läsa den offentliga delen av en nyckel plus dess attribut
-   *listan*: lista nycklar eller versioner av en nyckel som lagras i ett nyckelvalv
-   *Uppdatera*: uppdatera attribut för en nyckel
-   *ta bort*: ta bort nyckeln objektet
-   *logga*: Använd nyckel för att signera Överför sammanfattningar
-   *Kontrollera*: Använd för att verifiera sammandrag
-   *wrapKey*: Använd för att skydda en symmetrisk nyckel
-   *unwrapKey*: Använd nyckeln för att ta bort skyddet omslutna symmetriska nycklar
-   *kryptera*: Använd för att skydda en godtycklig sekvens med byte
-   *dekryptera*: Använd för att ta bort skyddet från en byte-sekvens
-   *importera*: importera en nyckel till en nyckelvalv
-   *säkerhetskopiering*: Säkerhetskopiera en nyckel i ett nyckelvalv
-   *återställa*: Återställ en säkerhetskopierade nyckeln till en nyckelvalv
-   *alla*: alla behörigheter

## <a name="key-vault-secrets"></a>Key Vault hemligheter 

###  <a name="BKMK_WorkingWithSecrets"></a> Arbeta med hemligheter

Hemligheter i Azure Key Vault är oktett sekvenser med en maximal storlek på 25 k byte. Azure Key Vault-tjänsten tillhandahåller inte någon semantik för hemligheter; den endast accepterar data, krypterar och lagrar den returnera en hemlig identifierare ”id” som kan användas för att hämta hemligheten vid ett senare tillfälle.  

För känsliga data bör klienter ytterligare skyddslager för data som lagras i Azure Key Vault; till exempel använder genom före kryptering av data du en separat Skyddsnyckel.  

Azure Key Vault stöder också fältet contentType för hemligheter. Klienter kan ange innehållstyp, ”contentType” en hemlighet hjälp vid tolkning av hemliga data när de hämtas. Den maximala längden på det här fältet är 255 tecken. Det finns några fördefinierade värden. Den föreslagna användningen är ett tips för att tolka hemlig information. Till exempel en implementering kan lagra både lösenord och certifikat som hemligheter och sedan använda det här fältet anger vilket. Det finns några fördefinierade värden.  

###  <a name="BKMK_SecretAttrs"></a> Hemlig attribut

Följande attribut kan anges förutom hemlig information:  

- *EXP*: IntDate, valfritt, som standard är **alltid**. Den *exp* (förfallotiden) attribut identifierar förfallotid eller senare som hemliga data får inte kan hämtas, förutom i [vissa situationer](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). Bearbetningen av den *exp* attribut kräver att aktuellt datum och tid måste vara innan förfallodatum för datum/tid som anges i den *exp* attribut. Azure Key Vault kan ge för vissa små handtag vanligtvis inte fler än några minuter för att redovisa klockan skeva. Värdet måste vara ett tal med ett värde för IntDate.  
- *NBF*: IntDate, valfritt, som standard är **nu**. Den *nbf* (inte före) attributet anger den tid som den hemliga data inte kan hämtas, förutom i [vissa situationer](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). Bearbetningen av den *nbf* attribut kräver att aktuellt datum och tid måste vara efter eller att inte är lika med-före datum/tid som anges i den *nbf* attribut. Azure Key Vault kan ge för vissa små handtag vanligtvis inte fler än några minuter för att redovisa klockan skeva. Värdet måste vara ett tal med ett värde för IntDate.  
- *aktiverad*: boolean, valfritt, standardvärdet är **SANT**. Det här attributet anger huruvida hemlig information kan hämtas. Attributet enabled används tillsammans med och *exp* när en åtgärd som sker mellan och exp, den endast tillåts om aktiverad är inställd på **SANT**. Genomförs utanför den *nbf* och *exp* fönstret är automatiskt otillåten, förutom i [vissa situationer](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops).  

Det finns ytterligare skrivskyddad attribut som ingår i alla svar som innehåller hemliga attribut:  

- *Skapa*: IntDate, valfria. Skapade attributet anger när den här versionen av hemligheten som har skapats. Det här värdet är null för hemligheter som skapades före för att lägga till det här attributet. Värdet måste vara ett tal med ett värde för IntDate.  
- *Uppdatera*: IntDate, valfria. Uppdaterade attributet anger när den här versionen av hemligheten som har uppdaterats. Det här värdet är null för hemligheter som senast uppdaterades innan för att lägga till det här attributet. Värdet måste vara ett tal med ett värde för IntDate.

#### <a name="BKMK_secret-date-time-ctrld-ops"></a> Datum och tid kontrollerade åtgärder

En hemlighet **hämta** åtgärden fungerar för inte ännu giltiga och har upphört att gälla hemligheter utanför den *nbf* / *exp* fönster. Anropar en hemlighet **hämta** åtgärd för en hemlighet som inte har giltig kan användas för testning. Hämtar (**hämta**ing) en hemlighet som har upphört att gälla, kan användas för återställningsåtgärder.

Mer information om data finns typer, [datatyper](about-keys-secrets-and-certificates.md#BKMK_DataTypes).  

###  <a name="BKMK_SecretAccessControl"></a> Hemlig åtkomstkontroll

Åtkomstkontroll för hemligheter som hanteras i Azure Key Vault finns på nivån för ett Nyckelvalv som fungerar som behållare för dessa hemligheter. Det finns en principer för åtkomstkontroll för hemligheter som skiljer sig från principer för åtkomstkontroll för nycklar i samma Nyckelvalvet. Användare kan skapa en eller flera valv Håll hemligheter och krävs för att upprätthålla lämpliga segmentering av scenariot och hantering av hemligheter. Åtkomstkontroll för hemligheter är oberoende av åtkomstkontroll för nycklar.  

Följande behörigheter kan användas för per huvudnamn, i hemligheter åtkomstkontrollpost i ett valv och spegla nära de åtgärder som tillåts på hemliga objekt:  

-   *Ange*: skapa nya  
-   *Hämta*: läsa en hemlighet  
-   *listan*: lista hemligheter eller versioner av en hemlighet som lagras i ett Nyckelvalv  
-   *ta bort*: ta bort hemligheten  
-   *alla*: alla behörigheter  

Mer information om hur du arbetar med hemligheter finns [hemliga operations](/rest/api/keyvault/secret-operations).  

###  <a name="BKMK_SecretTags"></a> Hemlig taggar  
Du kan ange ytterligare programspecifik metadata i form av taggar. Azure Key Vault stöder upp till 15 taggar, som kan ha ett namn på 256 tecken och ett värde 256 tecken.  

>[!Note]
>Taggar är läsas av en anropare om de har den *lista* eller *hämta* behörighet för den objekttypen; nycklar, hemligheter eller certifikat.

## <a name="key-vault-certificates"></a>Key Vault-certifikat

Stöd för Key Vault-certifikat ger hantering av din x509 certifikat och följande:  

-   Gör en certifikat-ägare att skapa ett certifikat via en process för att skapa Nyckelvalvet eller importera ett befintligt certifikat. Detta inkluderar både självsignerat och certifikatutfärdare genereras certifikat.
-   Gör en Key Vault certifikat ägare att implementera säker lagring och hantering av X509 certifikat utan interaktion med material för privat nyckel.  
-   Gör en certifikat-ägare att skapa en princip som leder Key Vault för att hantera livscykeln för ett certifikat.  
-   Gör att certifikatet ägare att tillhandahålla kontaktinformation för meddelande om livscykeln händelser för förfallodatum och förnyelse av certifikat.  
-   Stöder automatisk förnyelse med valda utfärdare - Key Vault partner X509 certifikat providers / certifikatutfärdare.

>[!Note]
>Icke-tillsammans providers/myndigheter kan också men stöder inte automatisk förnyelse av funktionen.

###  <a name="BKMK_CompositionOfCertificate"></a> Sammansättning av ett certifikat

När ett certifikat för Key Vault skapas, skapas också en adresserbara nyckel och Hemlig med samma namn. Key Vault-nyckel kan åtgärder nyckel och hemligheten som Key Vault kan hämta certifikatvärdet för som en hemlighet. Ett certifikat för Key Vault innehåller också offentliga x509 certifikat metadata.  

Identifierare och versionen av certifikat är samma som nycklar och hemligheter. En viss version av en adresserbara nyckel och en hemlighet som skapats med version för Key Vault-certifikat finns i svaret för Key Vault-certifikat.
 
![Cetificates är komplexa objekt](media/azure-key-vault.png)

###  <a name="BKMK_CertificateExportableOrNonExportableKey"></a> Nyckeln kan exporteras eller inte kan exporteras

När ett certifikat för Key Vault skapas, kan det hämtas från den adresserbara hemligheten med den privata nyckeln i PFX eller PEM-format om principen används för att skapa certifikatet anges att nyckeln kan exporteras. Om principen används för att skapa certifikatet Key Vault anges nyckeln ska inte kan exporteras, sedan är den privata nyckeln inte en del av värdet när hämtas som en hemlighet.  

Adresserbara nyckeln blir mer relevant med certifikat som inte kan exporteras KV. Adresserbara KV nyckeln operations mappas från *keyusage* i KV certifikatprincip som används för att skapa certifikatet KV.  

Två typer av nyckeln stöds – *RSA* eller *RSA HSM* med certifikat. Exportera tillåts endast med RSA, stöds inte av RSA HSM.  

###  <a name="BKMK_CertificateAttributesAndTags"></a> Certifikat-attribut och taggar

Förutom certifikatmetadata, en adresserbara nyckel och en adresserbara hemlighet innehåller också ett certifikat för Key Vault attribut och taggar.  

#### <a name="attributes"></a>Attribut

Certifikat-attribut avspeglas attribut för adresserbara nyckel och Hemlig skapas när KV certifikat skapas.  

En Key Vault-certifikatet har följande attribut:  

-   *aktiverad*: boolean, valfritt, standardvärdet är **SANT**. Det här attributet kan anges för att indikera om certifikatdata kan hämtas som hemliga eller kan använda som en nyckel. Detta används tillsammans med *nbf* och *exp* när en åtgärd som sker mellan *nbf* och *exp*, kommer bara tillåtet om aktiverat anges till true. Genomförs utanför den *nbf* och *exp* fönster tillåts inte automatiskt.  

Det finns ytterligare skrivskyddad attribut som ingår i svaret:

-   *Skapa*: IntDate: Anger om den här versionen av certifikatet skapades.  
-   *Uppdatera*: IntDate: Anger när den här versionen av certifikatet uppdaterades.  
-   *EXP*: IntDate: innehåller värdet för utgångsdatum för x509 certifikat.  
-   *NBF*: IntDate: innehåller värdet för dag då x509 certifikat.  

> [!Note] 
> Om Key Vault certifikatet upphör att gälla, är det adresserbara nyckel och Hemlig sluta fungera.  

#### <a name="tags"></a>Taggar

 Klienten angivna ordlista med nyckel/värde-par, liknar taggar i nycklar och hemligheter.  

 > [!Note]
> Taggar är läsas av en anropare om de har den *lista* eller *hämta* behörighet för den objekttypen; nycklar, hemligheter eller certifikat.

###  <a name="BKMK_CertificatePolicy"></a> Certifikatprincip

En certifikatprincip innehåller information om hur du skapar och hanterar livscykeln för ett certifikat för KV. När ett certifikat med privat nyckel har importerats till nyckelvalvet, skapas en standardprincip genom att läsa x509 certifikat.  

När ett certifikat för KV skapas från början, behöver en princip anges till Key Vault att informera om hur du skapar den här KV certifikat version eller den nästa KV certifikat. När en princip har upprättats, det är inte nödvändigt med efterföljande skapa åtgärder för att skapa nästa KV certifikat versioner.  

Det finns endast en instans av en princip för alla versioner av ett certifikat för KV.  

På en hög nivå innehåller en certifikatprincip följande:  

-   X509 certifikat egenskaper: innehåller ämnesnamn, Alternativt ämnesnamn osv. används för att skapa x509 certifikatbegäran.  
-   Nyckelegenskaper: innehåller nyckeltyp, nyckellängd, exporteras och återanvända nyckelfält. De här fälten instruera nyckelvalv om hur du skapar en nyckel.  
-   Hemlig egenskaper: innehåller hemliga egenskaper, till exempel content-type för adresserbara hemligt att generera hemligt värde, för att hämta certifikatet som en hemlighet.  
-   Livslängd åtgärder: innehåller livstid åtgärder för KV certifikatet. Varje livstid-åtgärd innehåller:  

     - Trigger: angetts via dagar före utgången eller livstid span procent  

     - Åtgärd: Ange åtgärdstyp – *emailContacts* eller *förnya*  

-   Utgivare: Parametrar om certifikatutfärdaren som du använder för att utfärda x509 certifikat.  
-   Attribut: innehåller attribut som är associerade med principen  

#### <a name="x509-to-key-vault-usage-mapping"></a>X509 för Key Vault-mappning för användning

I följande tabell representerar mappningen av x509 nyckelanvändning princip effektiva viktiga funktioner i en nyckel som skapats som en del av en Key Vault certifikat skapas.

|**X509 nyckelanvändning flaggor**|**Key Vault viktiga ops**|**Standardbeteende**|
|----------|--------|--------|
|DataEncipherment|kryptera, dekryptera| Gäller inte |
|DecipherOnly|dekryptera| Gäller inte  |
|DigitalSignature|logga in, kontrollera| Key Vault standard utan en specifikation för användning vid tidpunkten för skapandet av certifikat | 
|EncipherOnly|encrypt| Gäller inte |
|KeyCertSign|logga in, kontrollera|Gäller inte|
|KeyEncipherment|wrapKey unwrapKey| Key Vault standard utan en specifikation för användning vid tidpunkten för skapandet av certifikat | 
|Oavvislighet|logga in, kontrollera| Gäller inte |
|crlsign|logga in, kontrollera| Gäller inte |

###  <a name="BKMK_CertificateIssuer"></a> Certifikatutfärdare

Ett certifikat Key Vault-objekt innehåller en konfiguration som används för att kommunicera med en markerade certifikatet utfärdaren provider ordning x509 certifikat.  

-   Key Vault samarbetar med följande utfärdaren certifikatproviders för SSL-certifikat

|**Providernamn**|**Platser**|
|----------|--------|
|DigiCert|Stöds i alla nyckelvalv service-platser i offentliga moln och Azure Government|
|GlobalSign|Stöds i alla nyckelvalv service-platser i offentliga moln och Azure Government|

Innan en certifikatutfärdare kan skapas i ett Nyckelvalv, måste följande nödvändiga steg 1 och 2 åstadkommas har.  

1. Publicera till Certifikatproviders (certifikatutfärdare)  

    -   En administratör i organisationen måste inbyggd företaget (t.ex. Contoso) med minst en CA-provider.  

2. Administratören skapar beställaren autentiseringsuppgifter för Key Vault att registrera (och förnya) SSL-certifikat  

    -   Innehåller konfigurationen som ska användas för att skapa ett objekt för utfärdare av providern i nyckelvalvet  

Mer information om hur du skapar utfärdarobjekt från Certificates-portalen finns i [valvet nyckelcertifikat blogg](http://aka.ms/kvcertsblog)  

Key Vault kan skapa flera utfärdarobjekt med olika utfärdaren Providerkonfiguration. När en utfärdare-objektet har skapats dess namn kan referera till en eller flera principer. Refererar till objektet utfärdaren instruerar Nyckelvalvet ska använda konfigurationen som anges i objektet utfärdaren när du begär x509 certifikat från CA-providern under genereringen av certifikat och förnyelse.  

Utfärdarobjekt skapas i valvet och kan bara användas med KV certifikat i samma valvet.  

###  <a name="BKMK_CertificateContacts"></a> Certifikat för kontakter

Certifikatet kontakter innehåller kontaktinformation för att skicka meddelanden som utlöses av händelser för certifikatets livstid. Informationen om kontakter delas av alla certifikat i nyckelvalvet. Ett meddelande skickas till den angivna kontakter för en händelse för alla certifikat i nyckelvalvet.  

Om en certifikatprincip anges till automatisk förnyelse, skickas ett meddelande på följande händelser.  

-   Innan du förnya certifikat
-   Efter förnyelse av certifikat, som anger om certifikatet har förnyats eller om ett fel uppstod, som kräver manuell förnyat certifikat.  

 Om en certifikatprincip anges till manuellt skickas förnyat (e-post endast) och ett meddelande när det är dags att förnya certifikatet.  

###  <a name="BKMK_CertificateAccessControl"></a> Åtkomstkontroll för certifikat

 Åtkomstkontroll för certifikat som hanteras av Key Vault och tillhandahålls på nivån för ett Nyckelvalv som fungerar som behållare för de certifikaten. Det finns en principer för åtkomstkontroll för certifikat som skiljer sig från principer för åtkomstkontroll för nycklar och hemligheter i samma Nyckelvalvet. Användare kan skapa en eller flera valv för att lagra certifikat och krävs för att underhålla scenariot rätt segmentering och hantering av certifikat.  

 Följande behörigheter kan användas för per huvudnamn, i hemligheter åtkomstkontrollpost på ett nyckelvalv och nära speglar de åtgärder som tillåts på hemliga objekt:  

-   *Hämta*: tillåter get i den aktuella versionen av certifikat eller någon version av ett certifikat 
-   *listan*: gör en lista över aktuella certifikat eller versioner av ett certifikat  
-   *ta bort*: gör att borttagningen av ett certifikat, sin politik och alla dess versioner  
-   *Skapa*: gör att skapa ett certifikat för Nyckelvalvet.  
-   *importera*: gör import av certifikat till ett certifikat för nyckel-valvet.  
-   *Uppdatera*: tillåter uppdatering av ett certifikat.  
-   *manageconnects*: låter dig hantera Key Vault certifikat kontakter  
-   *getissuers*: gör att hämta en certifikatutfärdare  
-   *listissuers*: gör en lista över certifikatets utfärdare  
-   *setissuers*: gör att skapa eller uppdatera Key Vault-certifikatutfärdare  
-   *deleteissuers*: gör att ta bort Key Vault-certifikatutfärdare  
-   *alla*: beviljar alla behörigheter  

## <a name="additional-information-for-certificates"></a>Mer information om certifikat

- [Certifikat och principer](/rest/api/keyvault/certificates-and-policies)
- [Certifikatutfärdare](/rest/api/keyvault/certificate-issuers)
- [Certifikat för kontakter](/rest/api/keyvault/certificate-contacts)

## <a name="see-also"></a>Se även

- [Autentisering, förfrågningar och svar](authentication-requests-and-responses.md)
- [Key Vault-versioner](key-vault-versions.md)
- [Nyckeln Vault-utvecklare](/azure/key-vault/key-vault-developers-guide)
