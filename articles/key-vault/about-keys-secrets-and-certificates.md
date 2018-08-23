---
title: Om nycklar, hemligheter och certifikat
description: Översikt över REST-gränssnittet och information om developer KV
services: key-vault
documentationcenter: ''
author: BryanLa
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: abd1b743-1d58-413f-afc1-d08ebf93828a
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2018
ms.author: bryanla
ms.openlocfilehash: 962f9be53ce0b2022e8e5490bdeb04b2eefb4d7c
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2018
ms.locfileid: "42054369"
---
# <a name="about-keys-secrets-and-certificates"></a>Om nycklar, hemligheter och certifikat
Azure Key Vault kan du lagra och använda kryptografiska nycklar i Microsoft Azure-miljön. Key Vault stöder flera nyckeltyper och algoritmer och möjliggör användning av maskinvarusäkerhetsmodul moduler (HSM) för högt värderade nycklar. Dessutom kan Key Vault du lagra hemligheter säkert. Hemligheter är begränsad storlek oktetten objekt med inga specifika semantik. Key Vault har också stöd för certifikat som är byggda på nycklar och hemligheter och Lägg till en funktion för automatisk förnyelse.

Mer allmän information om Azure Key Vault finns i [vad är Azure Key Vault?](/azure/key-vault/key-vault-whatis)

**Allmän information för Key Vault**

-   [Stöd för standarder](#BKMK_Standards)
-   [Datatyper](#BKMK_DataTypes)  
-   [Objekt-ID: n och versionshantering](#BKMK_ObjId)  

**Om nycklar**

-   [Nycklar och nyckeltyper](#BKMK_KeyTypes)  
-   [RSA-algoritmer](#BKMK_RSAAlgorithms)  
-   [RSA-HSM algoritmer](#BKMK_RSA-HSMAlgorithms)  
-   [Kryptering](#BKMK_Cryptographic)
-   [Viktiga aktiviteter](#BKMK_KeyOperations)  
-   [Nyckelattribut](#BKMK_KeyAttributes)  
-   [Viktiga taggar](#BKMK_Keytags)  

**Om hemligheter** 

-   [Arbeta med hemligheter](#BKMK_WorkingWithSecrets)  
-   [Hemlig attribut](#BKMK_SecretAttrs)  
-   [Hemlig taggar](#BKMK_SecretTags)  
-   [Åtkomstkontroll till hemlighet](#BKMK_SecretAccessControl)  

**Om certifikat**

-   [Sammansättning av ett certifikat](#BKMK_CompositionOfCertificate)  
-   [Certifikatattribut och taggar](#BKMK_CertificateAttributesAndTags)  
-   [Certifikatprincip](#BKMK_CertificatePolicy)  
-   [Certifikatutfärdare](#BKMK_CertificateIssuer)  
-   [Certifikatskontakter](#BKMK_CertificateContacts)  
-   [Åtkomstkontroll för certifikat](#BKMK_CertificateAccessControl)  

--

## <a name="key-vault-general-details"></a>Allmän information för Key Vault

I följande avsnitt ger allmän information som gäller för implementeringen av Azure Key Vault-tjänsten.

###  <a name="BKMK_Standards"></a> Stöd för standarder

JavaScript Object Notation (JSON) och JavaScript-objekt signering och kryptering (JOSE) specifikationer finns viktig grundläggande information.  

-   [JSON-Webbnyckeln (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web-kryptering (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web algoritmer (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web signatur (JWS)](http://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="BKMK_DataTypes"></a> datatyper

Referera till den [JOSE specifikationer](#BKMK_Standards) för relevanta datatyper för nycklar, kryptering och signering.  

-   **algoritmen** – en algoritm som stöds för en nyckelåtgärd, till exempel RSA1_5  
-   **krypterad / värde-** -cipher text oktetter, kodad med Base64URL  
-   **Sammanfattad / värde-** -utdata från en hash-algoritm kodad med Base64URL  
-   **nyckeltypen** – en av de viktiga typerna som stöds, till exempel RSA.  
-   **klartext / värde-** -klartext oktetter, kodad med Base64URL  
-   **signaturens värde** - utdata för en signaturalgoritm kodad med Base64URL  
-   **base64URL** -en Base64URL [RFC4648] kodad binärt värde  
-   **booleska** -true eller false  
-   **Identitet** – en identitet från Azure Active Directory (AAD).  
-   **IntDate** – ett JSON-decimalvärde som representerar antalet sekunder från 1970-01-01T0:0:0Z UTC fram till angivet datum och tid i UTC. Se [RFC3339] information om datum/tider i allmänhet och i synnerhet.  

###  <a name="BKMK_ObjId"></a> Objekt-ID: n och versionshantering

Objekt som lagras i Azure Key Vault behåller versioner när en ny instans av ett objekt skapas och varje version har en unik identifierare och URL: en. När ett objekt skapas, ges ett unikt versions-ID och markeras som den aktuella versionen av objektet. Skapa en ny instans med samma objektnamn och ger det nya objektet en unik versions-ID och gör att den blir den aktuella versionen.  

Objekt i Azure Key Vault kan lösas med hjälp av det aktuella ID: t eller en versionsspecifika identifierare. Till exempel medför med en nyckel med namnet ”MasterKey” kan utföra åtgärder med det aktuella ID: t att använda den senaste tillgängliga versionen. Utför åtgärder med versionsspecifika identifierare medför att använda den här specifika versionen av objektet.  

Objekt identifieras unikt inom Azure Key Vault med hjälp av en URL så att inga två objekt i systemet, oavsett geografiska plats, har samma URL. Den fullständiga URL: en till ett objekt som kallas för objekt-ID och består av en del av prefix som identifierar Key Vault, typen av objekt, en användare som objektnamn och en Version av objektet. Objektnamnet är skiftlägeskänsliga och kan ändras. ID: n som inte innehåller den versionen kallas Base identifierare.  

Mer information finns i [autentisering, begäranden och svar](authentication-requests-and-responses.md)

En objektidentifierare har följande allmänna format:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Där:  

|||  
|-|-|  
|`keyvault-name`|Namnet för ett nyckelvalv i Microsoft Azure Key Vault-tjänsten.<br /><br /> Key Vault-namn har valts av användaren och är globalt unikt.<br /><br /> Key Vault-namnet måste vara en sträng med 3–24 tecken som innehåller endast (0–9, a–z, A–Z och -).|  
|`object-type`|Typ av objekt, ”nycklar” eller ”hemligheter”.|  
|`object-name`|En `object-name` är ett tillhandahålls användarnamn för och måste vara unika inom ett Key Vault. Namnet måste vara en sträng 1 127 tecken långt och endast 0-9, a – z, A – Z, - och.|  
|`object-version`|En `object-version` är en systemgenererad, sträng ID på 32 tecken som du kan också används för att adressera en unik version av ett objekt.|  

## <a name="key-vault-keys"></a>Key Vault-nycklar

###  <a name="BKMK_KeyTypes"></a> Nycklar och nyckeltyper

Kryptografiska nycklar i Azure Key Vault representeras som JSON-Webbnyckeln [JWK]-objekt. Grundläggande JWK/JWA specifikationer också utökas för att aktivera nyckeltyper som är unika för Azure Key Vault-implementering, till exempel import av nycklar till Azure Key Vault använder HSM-leverantör (Thales) specifika paketering för att aktivera säker transport av nycklar, till exempel den de kan endast användas i Azure Key Vault HSM.  

- **”Soft” nycklar**: en nyckel behandlas i programvaran av Key Vault, men krypteras i viloläge med hjälp av en systemnyckel som är i en HSM. Klienter kan importera en befintlig RSA eller EG nyckel eller be att Azure Key Vault Generera en.
- **”Hård” nycklar**: en nyckel som bearbetas i en HSM (maskinvarusäkerhetsmodul). Dessa nycklar skyddas i ett Azure Key Vault HSM-Säkerhetsvärldar (det finns en Säkerhetsvärld per geografisk plats för att bibehålla isolering). Klienter kan importera en RSA eller EG nyckel, antingen i mjuk formuläret eller genom att exportera från en kompatibel HSM-enhet eller be att Azure Key Vault Generera en. Den här nyckeltypen lägger till attributet T till i JWK skaffa om du vill utföra HSM nyckelmaterial.

     Mer information om geografiska gränser finns [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)  

Azure Key Vault har stöd för RSA och Elliptic Curve nycklar. kommande versioner stöder andra nyckeltyper som symmetriska.

-   **EG**: ”Soft” Elliptic Curve nyckel.
-   **EG HSM**: ”Hard” Elliptic Curve nyckel.
-   **RSA**: ”Soft” RSA-nyckel.
-   **RSA-HSM**: ”hård” RSA-nyckel.

Azure Key Vault har stöd för RSA-nycklar med storlekar 2048, 3072 och 4096 och Elliptic Curve nycklarna för skriver P-256, p-384, p 521 och P-256_K.

### <a name="BKMK_Cryptographic"></a> Kryptering

De kryptografiska moduler som använder Azure Key Vault, om HSM eller programvara är FIPS-verifierade. Du behöver inte göra något speciellt för att köra i FIPS-läge. Om du **skapa** eller **importera** nycklar som HSM-skyddad de garanterat som ska bearbetas i HSM: er som verifierats till FIPS 140-2 nivå 2 eller högre. Om du **skapa** eller **importera** nycklar som programvaruskyddad och sedan de bearbetas i kryptografiska moduler verifierade enligt standarderna FIPS 140-2 nivå 1 eller senare. Mer information finns i [nycklar och nyckeltyper](#BKMK_KeyTypes).

###  <a name="BKMK_ECAlgorithms"></a> EG algoritmer
 Följande algoritm identifierare stöds med EG och EG HSM-nycklar i Azure Key Vault. 

#### <a name="signverify"></a>INLOGGNING/KONTROLLERA

-   **ES256** – ECDSA för SHA-256 Överför sammanfattningar och nycklar som skapats med kurvan p-256. Den här algoritmen beskrivs på [RFC7518].
-   **ES256K** – ECDSA för SHA-256 Överför sammanfattningar och nycklar som skapats med kurvan P-256_K. Den här algoritmen väntar på standardisering.
-   **ES384** – ECDSA för SHA-384 Överför sammanfattningar och nycklar som skapats med kurvan p-384. Den här algoritmen beskrivs på [RFC7518].
-   **ES512** – ECDSA för SHA-512 Överför sammanfattningar och nycklar som skapats med kurvan p 521. Den här algoritmen beskrivs på [RFC7518].

###  <a name="BKMK_RSAAlgorithms"></a> RSA-algoritmer  
 Följande algoritm identifierare stöds med RSA- och RSA-HSM-nycklar i Azure Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, KRYPTERING/DEKRYPTERING

-   **RSA1_5** -RSAES PKCS1 V1_5 [RFC3447] krypteringsnyckel  
-   **RSA-OAEP** – RSAES med standardparametrarna som anges i RFC 3447 i avsnittet A.2.1 optimala asymmetrisk kryptering utfyllnad (OAEP) [RFC3447]. Dessa standardparametrar använder en hash-funktionen SHA-1 och en maskeringsfunktion generation av MGF1 med SHA-1.  

#### <a name="signverify"></a>INLOGGNING/KONTROLLERA

-   **RS256** – RSASSA-PKCS-v1_5 med SHA-256. Program som angetts digest-värdet måste beräknas med hjälp av SHA-256 och måste vara 32 byte långt.  
-   **RS384** – RSASSA-PKCS-v1_5 med hjälp av SHA-384. Program som angetts digest-värdet måste beräknas med hjälp av SHA-384 och måste vara 48 byte långt.  
-   **RS512** – RSASSA-PKCS-v1_5 med hjälp av SHA-512. Program som angetts sammanfattad värdet måste beräknas med hjälp av SHA-512 och måste vara 64 byte långt.  
-   **RSNULL** -Se [RFC2437] specialiserade användningsfall att aktivera vissa TLS-scenarier.  

###  <a name="BKMK_KeyOperations"></a> Viktiga aktiviteter

Azure Key Vault stöder följande åtgärder på objekt med nycklar:  

-   **Skapa**: gör att en klient att skapa en nyckel i Azure Key Vault. Värdet för nyckeln genereras av Azure Key Vault och lagras och inte har släppts till klienten. Asymmetrisk (och i framtiden, Elliptic Curve och Symmetric) kan du skapa nycklar i Azure Key Vault.  
-   **Importera**: gör att en klient att importera en befintlig nyckel till Azure Key Vault. Asymmetrisk (och i framtiden, Elliptic Curve och Symmetric) nycklar kan importeras till Azure Key Vault med ett antal olika paketering metoder i JWK-konstruktion.  
-   **Uppdatera**: gör att en klient med tillräcklig behörighet för att ändra metadata (nyckelattribut) som är associerade med en nyckel som tidigare lagras i Azure Key Vault.  
-   **Ta bort**: gör att en klient med tillräcklig behörighet för att ta bort en nyckel från Azure Key Vault.  
-   **Lista**: gör att en klient att lista alla nycklar i en viss Azure Key Vault.  
-   **Lista över versioner**: gör att en klient att lista alla versioner av en viss nyckel i en viss Azure Key Vault.  
-   **Hämta**: gör att en klient hämta de offentliga delarna av en viss nyckel i ett Azure Key Vault.  
-   **Backup**: exporterar en nyckel i ett skyddat format.  
-   **Återställa**: importerar en tidigare säkerhetskopierade nyckel.  

Mer information finns i [viktiga åtgärder i den Key Vault REST API-referensen](/rest/api/keyvault).  

När en nyckel har skapats i Azure Key Vault, kan följande kryptografiska åtgärder utföras med hjälp av nyckeln:  

-   **Logga in och kontrollera**: strikt, den här åtgärden är ”logga hash” eller ”verifiera hash” eftersom Azure Key Vault inte har stöd för hashing av innehåll som en del av signaturen har skapats. Program bör hash-data för att vara signerade lokalt och sedan begära att Azure Key Vault-loggar hash-värdet. Verifiering av signerad hashvärden stöds som en bekvämlighet åtgärd för program som inte kan ha åtkomst till [public] nyckelmaterial; Vi rekommenderar att, för bästa programprestanda, kontrollera åtgärder utförs lokalt.  
-   **Nyckelkryptering / Wrapping**: en nyckel som lagras i Azure Key Vault kan användas för att skydda en annan nyckel, vanligtvis en symmetrisk innehåll krypteringsnyckel (CEK). När nyckeln i Azure Key Vault är asymmetriska nyckelkryptering används, till exempel RSA-OAEP och WRAPKEY/UNWRAPKEY-åtgärder är likvärdiga med kryptera/DEKRYPTERA. När nyckeln i Azure Key Vault är symmetriska används nyckel som wrapping; till exempel AES-KW. WRAPKEY-åtgärden stöds för att underlätta för för program som inte kan ha åtkomst till [public] nyckelmaterial; Du rekommenderas att, för bästa programprestanda, WRAPKEY åtgärder utförs lokalt.  
-   **Kryptera och dekryptera**: en nyckel som lagras i Azure Key Vault kan användas för att kryptera eller dekryptera ett enda block av data, storleken på vilket avgörs av nyckeltyp och valda krypteringsalgoritm. Åtgärden Encrypt har angetts för att underlätta för program som inte kan ha åtkomst till [public] nyckelmaterial; Vi rekommenderar att, för bästa programprestanda, kryptera åtgärder utföras lokalt.  

Medan WRAPKEY/UNWRAPKEY med asymmetriska nycklar kan verka överflödiga som åtgärden motsvarar att kryptera/DEKRYPTERA, användning av olika åtgärder anses vara viktigt att tillhandahålla semantiska och auktorisering uppdelning av dessa åtgärder och konsekvens När andra viktiga typer stöds av tjänsten.  

Azure Key Vault har inte stöd för exportåtgärder: när en nyckel har etablerats i systemet inte kan extraheras eller dess nyckelmaterial ändras.  Men användare av Azure Key Vault som kan kräva sin nyckel för andra användningsfall eller när den har tagits bort i Azure Key Vault kan använda åtgärder för säkerhetskopiering och återställning för att exportera/importera nyckeln i ett skyddat format. Nycklar som skapas av BACKUP-åtgärden kan inte användas utanför Azure Key Vault. Importen kan också användas mot flera instanser av Azure Key Vault.  

Användarna kan begränsa någon av de kryptografiska åtgärder som har stöd för Azure Key Vault på basis av per nyckel med hjälp av egenskapen key_ops i JWK-objektet.  

Mer information om JWK objekt finns i [JSON Web nyckel (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="BKMK_KeyAttributes"></a> Nyckelattribut

Förutom nyckelmaterial, kan följande attribut anges. I en JSON-begäran, attribut nyckelord och klammerparenteser, ' {' '}', krävs även om det finns inga attribut har angetts.  

- *aktiverad*: boolesk, valfritt, standardvärdet är **SANT**. Anger om nyckeln är aktiverade och riktlinje för kryptografiska åtgärder. Den *aktiverat* attributet används tillsammans med *nbf* och *exp*. När en åtgärd som sker mellan *nbf* och *exp*, kommer endast tillåtna om *aktiverat* är inställd på **SANT**. Åtgärder utanför den *nbf* / *exp* fönster automatiskt tillåts inte, utom för vissa åtgärden under [särskilda villkor](#BKMK_key-date-time-ctrld-ops).
- *NBF*: IntDate, valfritt, som standard är nu. Den *nbf* (inte före) attributet anger den tid som inte får nyckeln användas för kryptografiska åtgärder, utom för vissa åtgärden under [särskilda villkor](#BKMK_key-date-time-ctrld-ops). Bearbetningen av den *nbf* attributet kräver att aktuellt datum och tid måste vara efter eller lika med inte-före datum/tid som anges i den *nbf* attribut. Azure Key Vault kan ge för vissa små spelrum vanligtvis inte mer än ett par minuter till kontot för klocka skeva. Värdet måste vara ett tal som innehåller ett IntDate-värde.  
- *EXP*: IntDate, valfritt, standardvärdet är ”alltid”. Den *exp* (upphör att gälla) attributet anger förfallotid eller senare som nyckeln får inte användas för en kryptografisk åtgärd, utom för vissa åtgärden under [särskilda villkor](#BKMK_key-date-time-ctrld-ops). Bearbetningen av den *exp* attributet kräver att aktuellt datum och tid måste vara innan det upphör att gälla datum/tid som anges i den *exp* attribut. Azure Key Vault kan ge för vissa små spelrum vanligtvis inte mer än ett par minuter till kontot för klocka skeva. Värdet måste vara ett tal som innehåller ett IntDate-värde.  

Det finns ytterligare skrivskyddade attribut som ingår i alla svar som innehåller viktiga punkter:  

- *skapade*: IntDate, valfritt. Den *skapade* attributet anger när den här versionen av nyckeln skapades. Det här värdet är null för nycklar som har skapats innan det här attributet. Värdet måste vara ett tal som innehåller ett IntDate-värde.  
- *Uppdatera*: IntDate, valfritt. Den *uppdateras* attributet anger när den här versionen av nyckeln uppdaterades. Det här värdet är null för nycklar som senast uppdaterades innan det här attributet. Värdet måste vara ett tal som innehåller ett IntDate-värde.  

Läs mer på IntDate och andra datatyper, [datatyper](#BKMK_DataTypes)  

#### <a name="BKMK_key-date-time-ctrld-ops"></a> Datum / tid-kontrollerad åtgärder

Inte ännu giltiga och har upphört att gälla nycklar, dessa utanför den *nbf* / *exp* fönstret fungerar för **dekryptera**, **unwrap** och **Kontrollera** åtgärder (inte returnerar 403, förbjuden). Anledningen till att med tillståndet som inte ännu giltig är att tillåta en nyckel som ska testas innan användning i produktion. Anledningen till att använda gått ut är att tillåta återställningsåtgärder på data som skapades när nyckeln är giltig. Dessutom kan du inaktivera åtkomst till en nyckel med Key Vault-principer eller genom att uppdatera den *aktiverat* nyckelattributet till **FALSKT**.

Läs mer på data i typer, [datatyper](#BKMK_DataTypes).

Mer information om andra möjliga attribut finns i den [JSON Web nyckel (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="BKMK_Keytags"></a> Viktiga taggar

Du kan ange ytterligare programspecifik metadata i form av taggar. Azure Key Vault har stöd för upp till 15 taggar, som kan ha ett namn för 256 tecken och ett värde med 256 tecken.  

>[!Note]
>Taggar är läsas av en anropare om de har den *lista* eller *hämta* behörighet till den objekttypen; nycklar, hemligheter eller certifikat.

###  <a name="BKMK_KeyAccessControl"></a> Nyckelåtkomstkontroll

Åtkomstkontroll för nycklar som hanteras av Key Vault har angetts på nivån för ett Nyckelvalv som fungerar som behållare av nycklar. Det finns en principer för åtkomstkontroll för nycklar som skiljer sig från principen för åtkomstkontroll för hemligheter i samma Key Vault. Användare kan skapa en eller flera valv för att lagra nycklar och krävs för att underhålla scenariot rätt segmentering och hantering av nycklar. Åtkomstkontroll för nycklar är oberoende av åtkomstkontroll för hemligheter.  

Följande behörigheter kan beviljas, på en per användare / service principal-basis i nycklar-åtkomstkontrollpost på ett valv. De här behörigheterna speglar nära de åtgärder som tillåts på ett nyckelobjekt:  

-   *Skapa*: skapa nya nycklar
-   *Hämta*: läsa den offentliga delen av en nyckel, plus dess attribut
-   *lista*: lista nycklar eller versioner av en nyckel som lagras i key vault
-   *Uppdatera*: uppdatera attribut för en nyckel
-   *ta bort*: ta bort nyckelobjektet
-   *logga*: Använd nyckeln för att signera Överför sammanfattningar
-   *Kontrollera*: Använd för att verifiera sammandrag
-   *wrapKey*: Använd för att skydda en symmetrisk nyckel
-   *unwrapKey*: Använd nyckeln om du vill ta bort skyddet från omslutna symmetriska nycklar
-   *kryptera*: Använd för att skydda en godtycklig sekvens av byte
-   *dekryptera*: Använd för att ta bort skyddet från en sekvens av byte
-   *importera*: importera en nyckel till ett nyckelvalv
-   *säkerhetskopiering*: Säkerhetskopiera en nyckel i ett nyckelvalv
-   *återställa*: återställa en säkerhetskopierad nyckel till ett nyckelvalv
-   *alla*: alla behörigheter

## <a name="key-vault-secrets"></a>Key Vault-hemligheter 

###  <a name="BKMK_WorkingWithSecrets"></a> Arbeta med hemligheter

Hemligheter i Azure Key Vault är oktetten sekvenser med en maximal storlek på 25 kB som varje. Azure Key Vault-tjänsten innehåller inte någon semantik för hemligheter; det bara tar emot data, krypterar och lagrar den returnera en hemlig identifierare ”id” som kan användas för att hämta hemligheten vid ett senare tillfälle.  

För mycket känsliga data bör klienter ytterligare skyddslager för data som lagras i Azure Key Vault; till exempel använder genom att kryptera data före du en separat protection-nyckel.  

Azure Key Vault stöder också fältet contentType för hemligheter. Klienter kan ange typen av innehåll ”contentType” av en hemlighet Aktivitetsanteckningar tolka hemliga data när de hämtas. Den maximala längden på det här fältet är 255 tecken. Det finns inga fördefinierade värden. Den föreslagna användningen är som ett tips för att tolka den hemliga data. Exempelvis kan en implementering kan lagra både lösenord och certifikat som hemligheter och sedan använda det här fältet för att ange vilka. Det finns inga fördefinierade värden.  

###  <a name="BKMK_SecretAttrs"></a> Hemlig attribut

Följande attribut kan anges förutom de hemliga data:  

- *EXP*: IntDate, valfritt, som standard är **alltid**. Den *exp* (upphör att gälla) attributet anger förfallotid eller senare som den hemliga bör inte att hämta data, utom i [viss situationer](#BKMK_secret-date-time-ctrld-ops). Det här fältet är för **endast i informationssyfte** syfte endast som informerar användare av key vault-tjänsten en särskild hemlighet inte får användas. Värdet måste vara ett tal som innehåller ett IntDate-värde.   
- *NBF*: IntDate, valfritt, som standard är **nu**. Den *nbf* (inte före) attributet anger den tid som hemliga data inte hämtas, utom i [viss situationer](#BKMK_secret-date-time-ctrld-ops). Det här fältet är för **endast i informationssyfte** i utvärderingssyfte. Värdet måste vara ett tal som innehåller ett IntDate-värde. 
- *aktiverad*: boolesk, valfritt, standardvärdet är **SANT**. Det här attributet anger huruvida hemliga data kan hämtas. Aktiverade attributet används tillsammans med och *exp* när en åtgärd som sker mellan och exp, det ska endast beviljas om aktiverat är inställt på **SANT**. Åtgärder utanför den *nbf* och *exp* fönstret är automatiskt otillåtna, utom i [viss situationer](#BKMK_secret-date-time-ctrld-ops).  

Det finns ytterligare skrivskyddade attribut som ingår i alla svar som innehåller hemliga attribut:  

- *skapade*: IntDate, valfritt. Skapade attributet anger när den här versionen av hemligheten har skapats. Det här värdet är null för hemligheter som skapats innan det här attributet. Värdet måste vara ett tal som innehåller ett IntDate-värde.  
- *Uppdatera*: IntDate, valfritt. Uppdaterade attributet anger när den här versionen av hemligheten uppdaterades. Det här värdet är null för hemligheter som senast uppdaterades innan det här attributet. Värdet måste vara ett tal som innehåller ett IntDate-värde.

#### <a name="BKMK_secret-date-time-ctrld-ops"></a> Datum / tid-kontrollerad åtgärder

En hemlighet **hämta** åtgärden kommer att fungera för inte ännu giltiga och har upphört att gälla hemligheter, utanför den *nbf* / *exp* fönster. Anropa en hemlighet **hämta** åtgärden för en hemlighet som inte ännu giltiga kan användas för testning. Hämtar (**hämta**ing) en hemlighet som har upphört att gälla, kan användas för återställningsåtgärder.

Läs mer på data i typer, [datatyper](#BKMK_DataTypes).  

###  <a name="BKMK_SecretAccessControl"></a> Åtkomstkontroll till hemlighet

Åtkomstkontroll för hemligheter som hanteras i Azure Key Vault har angetts på nivån för ett Nyckelvalv som fungerar som behållare av dessa hemligheter. Det finns en principer för åtkomstkontroll för hemligheter som skiljer sig från principen för åtkomstkontroll för nycklar i samma Key Vault. Användare kan skapa en eller flera valv för att lagra hemligheter och krävs för att underhålla scenariot rätt segmentering och hantering av hemligheter. Åtkomstkontroller för hemligheter är oberoende av åtkomstkontroll för nycklar.  

Följande behörigheter kan användas på basis av per huvudnamn, i den hemligheter posten på ett valv och spegla nära de åtgärder som tillåts på hemliga objekt:  

-   *Ange*: skapa nya hemligheter  
-   *Hämta*: läsa en hemlighet  
-   *lista*: lista hemligheter eller versioner av en hemlighet som lagras i Key Vault  
-   *ta bort*: ta bort hemligheten  
-   *alla*: alla behörigheter  

Läs mer om hur du arbetar med hemligheter [åtgärder med hemligheter i den Key Vault REST API-referensen](/rest/api/keyvault).  

###  <a name="BKMK_SecretTags"></a> Hemlig taggar  
Du kan ange ytterligare programspecifik metadata i form av taggar. Azure Key Vault har stöd för upp till 15 taggar, som kan ha ett namn för 256 tecken och ett värde med 256 tecken.  

>[!Note]
>Taggar är läsas av en anropare om de har den *lista* eller *hämta* behörighet till den objekttypen; nycklar, hemligheter eller certifikat.

## <a name="key-vault-certificates"></a>Key Vault-certifikat

Support för Key Vault-certifikat ger för hantering av din x509 certifikat och följande:  

-   Ger en certifikat-ägaren att skapa ett certifikat via en process för att skapa Key Vault eller import av ett befintligt certifikat. Detta omfattar både självsignerade och certifikatutfärdare genererade certifikat.
-   Ger en Key Vault-certifikat ägaren att implementera säker lagring och hantering av X509 certifikat utan interaktion med privat nyckelmaterial.  
-   Ger en certifikat-ägaren att skapa en princip som dirigerar Key Vault för att hantera livscykeln för ett certifikat.  
-   Gör att certifikatet ägare att tillhandahålla kontaktinformation för meddelande om livscykeln för händelser för förfallodatum och förnyelse av certifikat.  
-   Har stöd för automatisk förnyelse med valda utfärdare - Key Vault partner X509 certifikat providers / certifikatutfärdare.

>[!Note]
>Icke-samarbetade providers/myndigheter är också tillåtna men stöder inte funktionen för automatisk förnyelse.

###  <a name="BKMK_CompositionOfCertificate"></a> Sammansättning av ett certifikat

När ett Key Vault-certifikat skapas, skapas också en adresserbara nyckel och hemlighet med samma namn. Key Vault-nyckeln tillåter viktiga åtgärder och hemlighet för Key Vault gör att hämta certifikatvärdet för som en hemlighet. Ett Key Vault-certifikat innehåller även offentliga x509 certifikat metadata.  

Identifierare och versionen av certifikat liknar den för nycklar och hemligheter. En specifik version av en adresserbara nyckel och hemlighet som skapats med Key Vault-certifikat-versionen är tillgänglig i svaret för Key Vault-certifikat.
 
![Certifikat är komplexa objekt](media/azure-key-vault.png)

###  <a name="BKMK_CertificateExportableOrNonExportableKey"></a> Nyckeln kan exporteras eller inte kan exporteras

När ett Key Vault-certifikat skapas, kan det hämtas från den adresserbara hemligheten med den privata nyckeln i PFX eller PEM-format om principen används för att skapa certifikatet anges att nyckeln kan exporteras. Om principen används för att skapa Key Vault-certifikat som anges för att vara inte kan exporteras, sedan är den privata nyckeln inte en del av värdet när du hämtar som en hemlighet.  

Den adresserbara nyckeln blir mer relevant med certifikat som inte kan exporteras KV. Den adresserbara KV nyckeln operations mappas från *keyusage* i KV certifikatprincip som används för att skapa certifikatet KV.  

Två typer av nyckeln stöds – *RSA* eller *RSA HSM* med certifikat. Exportera tillåts endast med RSA, stöds inte av RSA HSM.  

###  <a name="BKMK_CertificateAttributesAndTags"></a> Certifikatattribut och taggar

Förutom certifikatmetadata en adresserbara nyckel och en adresserbara hemlighet innehåller också ett Key Vault-certifikat attribut och taggar.  

#### <a name="attributes"></a>Attribut

Certifikatattribut är speglad till attributen för den adresserbara nyckel och hemlighet som skapas när KV certifikat skapas.  

Ett Key Vault-certifikat har följande attribut:  

-   *aktiverad*: boolesk, valfritt, standardvärdet är **SANT**. Det här attributet kan anges för att indikera om certifikatdata hämtas som hemlig eller operativt som en nyckel. Det här används tillsammans med *nbf* och *exp* när en åtgärd som sker mellan *nbf* och *exp*, kommer bara tillåtet om aktiverat har angetts till true. Åtgärder utanför den *nbf* och *exp* fönster tillåts inte automatiskt.  

Det finns ytterligare skrivskyddade attribut som ingår i svaret:

-   *skapade*: IntDate: Anger när den här versionen av certifikatet skapades.  
-   *Uppdatera*: IntDate: Anger när den här versionen av certifikatet uppdaterades.  
-   *EXP*: IntDate: innehåller värdet för giltighetstiden för x509 certifikat.  
-   *NBF*: IntDate: innehåller värdet för datumet då x509 certifikat.  

> [!Note] 
> Om ett Key Vault-certifikat upphör att gälla, är det adresserbara nyckel och hemlighet sluta fungera.  

#### <a name="tags"></a>Taggar

 Klienten angivna ordlista med nyckelvärdepar, liknar taggar i nycklar och hemligheter.  

 > [!Note]
> Taggar är läsas av en anropare om de har den *lista* eller *hämta* behörighet till den objekttypen; nycklar, hemligheter eller certifikat.

###  <a name="BKMK_CertificatePolicy"></a> Certifikatprincip

En certifikatprincip innehåller information om hur du skapar och hanterar livscykeln för ett KV certifikat. När ett certifikat med privat nyckel har importerats till key vault, en standardprincip skapas genom att läsa x509 certifikat.  

När ett KV certifikat skapas från grunden, måste en princip anges till Key Vault för att informera om hur du skapar den här KV certifikat-versionen eller nästa KV certifikatversion. När en princip har upprättats, det är inte obligatoriskt med efterföljande skapa åtgärder för att skapa nästa KV certifikat versioner.  

Det finns endast en instans av en princip för alla versioner av ett KV certifikat.  

På en hög nivå innehåller en certifikatprincip följande:  

-   X509 certifikat egenskaper: innehåller namn på certifikatmottagare, alternativa ämnesnamn osv. används för att skapa en x509 certifikatbegäran.  
-   Nyckelegenskaper: innehåller nyckeltyp nyckellängd, kan exporteras och återanvända nyckelfält. De här fälten instruera nyckelvalv om hur du skapar en nyckel.  
-   Egenskaper för hemlighet: innehåller egenskaper för hemlighet, till exempel innehållstyp adresserbara hemlighet att generera hemligt värde, för att hämta certifikatet som en hemlighet.  
-   Livslängd åtgärder: innehåller åtgärder för livslängd för KV certifikatet. Varje livstidsåtgärd innehåller:  

     - Utlösare: angetts via dagar före förfallodatum eller livslängd span procent  

     - Åtgärd: Ange åtgärdstypen – *emailContacts* eller *automatisk förnyelse*  

-   Utgivare: Parametrar om certifikatutfärdaren som du använder för att utfärda x509 certifikat.  
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

###  <a name="BKMK_CertificateIssuer"></a> Certifikatutfärdare

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

Mer information om hur du skapar utfärdarobjekt från Certificates-portalen finns i den [Nyckelvalvscertifikat blogg](http://aka.ms/kvcertsblog)  

Key Vault gör för att skapa flera utfärdarobjekt med olika utfärdare providerkonfigurationen. När ett utfärdare-objekt skapas, kan dess namn refereras i en eller flera certifikatprinciper för. Refererar till objektet utfärdare instruerar Key Vault för att använda konfigurationen som anges i objektet utfärdaren när du begär x509 certifikat från CA-providern under skapandet av certifikat och förnyelse.  

Utfärdarobjekt skapas i valvet och kan bara användas med KV certifikat i samma valv.  

###  <a name="BKMK_CertificateContacts"></a> Certifikatskontakter

Certifikatskontakterna innehåller kontaktuppgifter för att skicka meddelanden som utlöses av certifikat livslängd för händelser. Kontakter informationen delas av alla certifikat i nyckelvalvet. Ett meddelande skickas till den angivna kontakter för en händelse för alla certifikat i nyckelvalvet.  

Om en certifikatprincip anges för automatisk förnyelse, skickas ett meddelande om följande händelser.  

-   Innan du certifikatförnyelse
-   Efter certifikatförnyelse, anger om certifikatet har förnyats, eller om ett fel uppstod, kräver manuell förnyelse av certifikatet.  

 Om en certifikatprincip är inställt på manuellt skickas förnyade (endast email) och ett meddelande när det är dags att förnya certifikatet.  

###  <a name="BKMK_CertificateAccessControl"></a> Åtkomstkontroll för certifikat

 Åtkomstkontroll för certifikat hanteras av Key Vault och tillhandahålls på nivån för ett Nyckelvalv som fungerar som behållare för de certifikaten. Det finns en principer för åtkomstkontroll för certifikat som skiljer sig från principen för åtkomstkontroll för nycklar och hemligheter i samma Key Vault. Användare kan skapa en eller flera valv för att lagra certifikat och krävs för att underhålla scenariot rätt segmentering och hantering av certifikat.  

 Följande behörigheter kan användas på basis av per huvudnamn, i åtkomstkontrollpost för hemligheter i key vault och noggrant speglingar de åtgärder som tillåts på hemliga objekt:  

-   *Hämta*: gör att get för den aktuella versionen för certifikat eller någon version av ett certifikat 
-   *lista*: gör att lista över aktuella certifikat eller versioner av ett certifikat  
-   *ta bort*: gör att ta bort ett certifikat, dess princip och alla dess versioner  
-   *Skapa*: gör att skapa ett Key Vault-certifikat.  
-   *importera*: kan importera certifikat material i ett Key Vault-certifikat.  
-   *Uppdatera*: tillåter uppdatering av ett certifikat.  
-   *managecontacts*: låter dig hantera certifikatskontakterna för Key Vault  
-   *getissuers*: gör att hämta en certifikatutfärdare  
-   *listissuers*: gör att listan över certifikatets utfärdare  
-   *setissuers*: gör att skapa eller uppdatera Key Vault-certifikatutfärdare  
-   *deleteissuers*: tillåter borttagning av Key Vault-certifikatutfärdare  
-   *alla*: beviljar alla behörigheter  

Mer information finns i den [certifikatåtgärder i den Key Vault REST API-referensen](/rest/api/keyvault). 

## <a name="see-also"></a>Se även

- [Autentisering, begäranden och svar](authentication-requests-and-responses.md)
- [Key Vault-versioner](key-vault-versions.md)
- [Utvecklarguide för Vault-nyckeln](/azure/key-vault/key-vault-developers-guide)
