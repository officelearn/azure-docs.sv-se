---
title: Kryptografi - Microsoft Threat Modeling Tool - Azure | Microsoft-dokument
description: mildrande åtgärder för hot som exponeras i hotmodelleringsverktyget
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: c9116472af5b400ded0fea24f98b07bad9d9039b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728193"
---
# <a name="security-frame-cryptography--mitigations"></a>Säkerhetsram: Kryptografi | Mitigations 

| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Webbprogram** | <ul><li>[Använd endast godkända symmetriska blockchiffer och nyckellängder](#cipher-length)</li><li>[Använd godkända blockchifferlägen och initieringsvektorer för symmetriska chiffer](#vector-ciphers)</li><li>[Använda godkända asymmetriska algoritmer, nyckellängder och utfyllnad](#padding)</li><li>[Använd godkända slumptalsgeneratorer](#numgen)</li><li>[Använd inte symmetriska strömchiffer](#stream-ciphers)</li><li>[Använda godkända MAC/HMAC/keyed hash-algoritmer](#mac-hash)</li><li>[Använd endast godkända kryptografiska hash-funktioner](#hash-functions)</li></ul> |
| **Databas** | <ul><li>[Använda starka krypteringsalgoritmer för att kryptera data i databasen](#strong-db)</li><li>[SSIS-paket ska krypteras och signeras digitalt](#ssis-signed)</li><li>[Lägga till digital signatur i kritiska databasförsebara säkerhetsreferenser](#securables-db)</li><li>[Använda SQL-servern EKM för att skydda krypteringsnycklar](#ekm-keys)</li><li>[Använd alltidkrypterad funktion om krypteringsnycklar inte ska visas för databasmotorn](#keys-engine)</li></ul> |
| **IoT-enhet** | <ul><li>[Lagra kryptografiska nycklar säkert på IoT-enhet](#keys-iot)</li></ul> | 
| **IoT Cloud Gateway** | <ul><li>[Generera en slumpmässig symmetrisk nyckel med tillräcklig längd för autentisering till IoT Hub](#random-hub)</li></ul> | 
| **Mobile klient för Dynamics CRM** | <ul><li>[Se till att en enhetshanteringsprincip finns på plats som kräver en användnings-PIN-kod och tillåter fjärrtorkning](#pin-remote)</li></ul> | 
| **Dynamics CRM Outlook-klient** | <ul><li>[Se till att en enhetshanteringsprincip finns på plats som kräver en PIN-kod/lösenord/automatisk låsning och krypterar alla data (t.ex.](#bitlocker)</li></ul> | 
| **Identitetsserver** | <ul><li>[Kontrollera att signeringsnycklar rullas över när identity server använder](#rolled-server)</li><li>[Se till att kryptografiskt starkt klient-ID, klienthemlighet används i Identity Server](#client-server)</li></ul> | 

## <a name="use-only-approved-symmetric-block-ciphers-and-key-lengths"></a><a id="cipher-length"></a>Använd endast godkända symmetriska blockchiffer och nyckellängder

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Produkter får endast använda de symmetriska blockchiffer och tillhörande nyckellängder som uttryckligen har godkänts av Crypto Advisor i din organisation. Godkända symmetriska algoritmer på Microsoft inkluderar följande blockchiffer:</p><ul><li>För ny kod AES-128, AES-192 och AES-256 är acceptabla</li><li>För bakåtkompatibilitet med befintlig kod är trenyckel 3DES godtagbart</li><li>För produkter som använder symmetriska blockchiffer:<ul><li>Advanced Encryption Standard (AES) krävs för ny kod</li><li>Tre-nyckel triple Data Encryption Standard (3DES) är tillåtet i befintlig kod för bakåtkompatibilitet</li><li>Alla andra blockchiffer, inklusive RC2, DES, 2 Key 3DES, DESX och Skipjack, får endast användas för att dekryptera gamla data och måste bytas ut om de används för kryptering</li></ul></li><li>För symmetriska blockkrypteringsalgoritmer krävs en minsta nyckellängd på 128 bitar. Den enda blockkrypteringsalgoritm som rekommenderas för ny kod är AES (AES-128, AES-192 och AES-256 är alla acceptabla)</li><li>Tre-nyckel 3DES är för närvarande acceptabelt om den redan används i befintlig kod; övergång till AES rekommenderas. DES, DESX, RC2 och Skipjack anses inte längre vara säkra. Dessa algoritmer får endast användas för att dekryptera befintliga data för bakåtkompatibilitet, och data bör krypteras på nytt med hjälp av ett rekommenderat blockchiffer</li></ul><p>Observera att alla symmetriska blockchiffer måste användas med ett godkänt chifferläge, vilket kräver användning av en lämplig initieringsvektor (IV). En lämplig IV, är vanligtvis ett slumptal och aldrig ett konstant värde</p><p>Användning av äldre eller på annat sätt icke godkända kryptoalgoritmer och mindre nyckellängder för att läsa befintliga data (i motsats till att skriva nya data) kan tillåtas efter organisationens Crypto Board-granskning. Du måste dock ansöka om ett undantag mot det här kravet. Dessutom bör produkter i företagsdistributioner överväga att varna administratörer när svag krypto används för att läsa data. Sådana varningar bör vara förklarande och genomförbara. I vissa fall kan det vara lämpligt att ha grupprincipen kontroll över användningen av svaga</p><p>Tillåtna .NET-algoritmer för hanterad kryptogility (i prioritetsordning)</p><ul><li>AesCng (FIPS-kompatibel)</li><li>AuthenticatedAesCng (FIPS-kompatibel)</li><li>AESCryptoServiceProvider (FIPS-kompatibel)</li><li>AESManaged (icke-FIPS-kompatibel)</li></ul><p>Observera att ingen av dessa algoritmer kan `SymmetricAlgorithm.Create` anges `CryptoConfig.CreateFromName` via eller metoder utan att göra ändringar i filen machine.config. Observera också att AES i versioner av .NET innan .NET `RijndaelManaged`3.5 heter och `AesCng` `AuthenticatedAesCng` >är tillgängliga via CodePlex och kräver CNG i det underliggande operativsystemet</p>

## <a name="use-approved-block-cipher-modes-and-initialization-vectors-for-symmetric-ciphers"></a><a id="vector-ciphers"></a>Använd godkända blockchifferlägen och initieringsvektorer för symmetriska chiffer

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Alla symmetriska blockchiffer måste användas med ett godkänt symmetriskt chifferläge. De enda godkända lägena är CBC och CTS. I synnerhet bör det elektroniska kodbokssättet (ECB) undvikas. användning av ECB kräver din organisations Crypto Board granskning. All användning av OFB, CFB, CTR, CCM och GCM eller något annat krypteringsläge måste granskas av organisationens Crypto Board. Återanvända samma initieringsvektor (IV) med blockchiffer i "strömmande chifferlägen", till exempel CTR, kan orsaka att krypterade data avslöjas. Alla symmetriska blockchiffer måste också användas med en lämplig initieringsvektor (IV). En lämplig IV är ett kryptografiskt starkt, slumptal och aldrig ett konstant värde. |

## <a name="use-approved-asymmetric-algorithms-key-lengths-and-padding"></a><a id="padding"></a>Använda godkända asymmetriska algoritmer, nyckellängder och utfyllnad

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Användningen av förbjudna kryptografiska algoritmer medför betydande risk för produktsäkerhet och måste undvikas. Produkter får endast använda de kryptografiska algoritmer och tillhörande nyckellängder och utfyllnad som uttryckligen har godkänts av organisationens Crypto Board.</p><ul><li>**RSA-** kan användas för kryptering, nyckelutbyte och signatur. RSA-kryptering får endast använda utfyllnadslägena OAEP eller RSA-KEM. Befintlig kod får endast använda PKCS #1 v1.5-utfyllnadsläge för kompatibilitet. Användning av null-utfyllnad är uttryckligen förbjuden. Nycklar >= 2048 bitar krävs för ny kod. Befintlig kod kan stödja nycklar < 2048 bitar endast för bakåtkompatibilitet efter en granskning av organisationens Crypto Board. Nycklar < 1024 bitar får endast användas för dekryptering/verifiering av gamla data och måste bytas ut om de används för kryptering eller signeringsåtgärder</li><li>**ECDSA-** får endast användas för underskrift. ECDSA med >=256-bitarsnycklar krävs för ny kod. ECDSA-baserade signaturer måste använda en av de tre NIST-godkända kurvorna (P-256, P-384 eller P521). Kurvor som har analyserats noggrant får endast användas efter en granskning med organisationens Crypto Board.</li><li>**ECDH-** får endast användas för nyckelutbyte. ECDH med >=256-bitarsnycklar krävs för ny kod. ECDH-baserade nyckelutbyte måste använda en av de tre NIST godkända kurvorna (P-256, P-384 eller P521). Kurvor som har analyserats noggrant får endast användas efter en granskning med organisationens Crypto Board.</li><li>**DSA-** kan vara acceptabelt efter granskning och godkännande från din organisations Crypto Board. Kontakta din säkerhetsrådgivare för att schemalägga din organisations Crypto Board-granskning. Om din användning av DSA är godkänd måste du se till att du måste förbjuda användning av nycklar som är kortare än 2048 bitar. CNG stöder 2048-bitars och större nyckellängder från och med Windows 8.</li><li>**Diffie-Hellman-** får endast användas för sessionsnyckelhantering. Nyckellängd >= 2048 bitar krävs för ny kod. Befintlig kod kan stödja nyckellängder < 2048 bitar endast för bakåtkompatibilitet efter en granskning av organisationens Crypto Board. Nycklar < 1024 bitar kanske inte används.</li><ul>

## <a name="use-approved-random-number-generators"></a><a id="numgen"></a>Använd godkända slumptalsgeneratorer

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Produkterna måste använda godkända slumptalsgeneratorer. Pseudorandom-funktioner som C-körningsfunktionsrion, .NET Framework-klassen System.Random eller systemfunktioner som GetTickCount får därför aldrig användas i sådan kod. Det är förbjudet att använda den dubbla elliptiska kurvgeneratorn (DUAL_EC_DRBG)</p><ul><li>**CNG-** BCryptGenRandom (användning av den BCRYPT_USE_SYSTEM_PREFERRED_RNG flagga rekommenderas om inte den som ringer kan köras på någon IRQL större än 0 [det vill säga PASSIVE_LEVEL])</li><li>**CAPI-** cryptGenRandom</li><li>**Win32/64-** RtlGenRandom (nya implementeringar bör använda BCryptGenRandom eller CryptGenRandom) * rand_s * SystemPrng (för kernel-läge)</li><li>**. NET-** RNGCryptoServiceProvider eller RNGCng</li><li>**Windows Store-appar-** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom eller . GenereraRandomNumber</li><li>**Apple OS X (10,7+)/iOS(2,0+)-** int SecRandomCopyBytes (SecRandomRef random, size_t \*antal, uint8_t byte )</li><li>**Apple OS X (<10,7)-** Använd /dev/random för att hämta slumptal</li><li>**Java(inklusive Google Android Java-kod) -** java.security.SecureRandom klass. Observera att för Android 4.3 (Jelly Bean), utvecklare måste följa Android rekommenderade lösningen och uppdatera sina program för att uttryckligen initiera PRNG med entropi från / dev / urandom eller /dev / random</li></ul>|

## <a name="do-not-use-symmetric-stream-ciphers"></a><a id="stream-ciphers"></a>Använd inte symmetriska strömchiffer

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Symmetriska strömchiffer, såsom RC4, får inte användas. I stället för symmetriska strömchiffer bör produkterna använda ett blockchiffer, särskilt AES med en nyckellängd på minst 128 bitar. |

## <a name="use-approved-machmackeyed-hash-algorithms"></a><a id="mac-hash"></a>Använda godkända MAC/HMAC/keyed hash-algoritmer

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Produkter får endast använda godkända meddelandeautentiseringskod (MAC) eller HMAC-algoritmer (Hash-baserad meddelandeautentiseringskod).</p><p>En MAC (Message Authentication Code) är en information som är kopplad till ett meddelande som gör det möjligt för mottagaren att verifiera både avsändarens äkthet och meddelandets integritet med hjälp av en hemlig nyckel. Användning av antingen en hash-baserad MAC ([HMAC](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) eller [block-chifferbaserad MAC](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) är tillåten så länge alla underliggande hash- eller symmetriska krypteringsalgoritmer också är godkända för användning. För närvarande omfattar detta HMAC-SHA2-funktionerna (HMAC-SHA256, HMAC-SHA384 och HMAC-SHA512) och CMAC/OMAC1- och OMAC2-blockchifferbaserade MACs (dessa är baserade på AES).</p><p>Användning av HMAC-SHA1 kan vara tillåten för plattformskompatibilitet, men du måste lämna in ett undantag från den här proceduren och genomgå organisationens Crypto-granskning. Trunkering av HMACs till mindre än 128 bitar är inte tillåtet. Använda kundmetoder för hash en nyckel och data är inte godkänd, och måste genomgå din organisations Crypto Board granskning före användning.</p>|

## <a name="use-only-approved-cryptographic-hash-functions"></a><a id="hash-functions"></a>Använd endast godkända kryptografiska hash-funktioner

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Produkterna måste använda SHA-2-familjen av hash-algoritmer (SHA256, SHA384 och SHA512). Om en kortare hash behövs, till exempel en 128-bitars utdatalängd för att passa en datastruktur som utformats med den kortare MD5-hashen i åtanke, kan produktteamen trunkera en av SHA2-hasharen (vanligtvis SHA256). Observera att SHA384 är en trunkerad version av SHA512. Trunkering av kryptografiska hashar för säkerhetsändamål till mindre än 128 bitar är inte tillåtet. Ny kod får inte använda m2-, MD4-, MD5-, SHA-0-, SHA-1- eller RIPEMD-hash-algoritmerna. Hash kollisioner är computationally genomförbart för dessa algoritmer, som effektivt bryter dem.</p><p>Tillåten .NET-hashalgoritmer för hanterad kryptogility (i prioritetsordning):</p><ul><li>SHA512Cng (FIPS-kompatibel)</li><li>SHA384Cng (FIPS-kompatibel)</li><li>SHA256Cng (FIPS-kompatibel)</li><li>SHA512Hantaged (icke-FIPS-kompatibel) (använd SHA512 som algoritmnamn i anrop till HashAlgorithm.Create eller CryptoConfig.CreateFromName)</li><li>SHA384Hantaged (icke-FIPS-kompatibel) (använd SHA384 som algoritmnamn i anrop till HashAlgorithm.Create eller CryptoConfig.CreateFromName)</li><li>SHA256Hantaged (icke-FIPS-kompatibel) (använd SHA256 som algoritmnamn i anrop till HashAlgorithm.Create eller CryptoConfig.CreateFromName)</li><li>SHA512CryptoServiceProvider (FIPS-kompatibel)</li><li>SHA256CryptoServiceProvider (FIPS-kompatibel)</li><li>SHA384CryptoServiceProvider (FIPS-kompatibel)</li></ul>| 

## <a name="use-strong-encryption-algorithms-to-encrypt-data-in-the-database"></a><a id="strong-db"></a>Använda starka krypteringsalgoritmer för att kryptera data i databasen

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Välja en krypteringsalgoritm](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Steg** | Krypteringsalgoritmer definierar dataomvandlingar som inte enkelt kan återföras av obehöriga användare. SQL Server gör det möjligt för administratörer och utvecklare att välja mellan flera algoritmer, inklusive DES, Triple DES, TRIPLE_DES_3KEY, RC2, RC4, 128-bitars RC4, DESX, 128-bitars AES, 192-bitars AES och 256-bitars AES |

## <a name="ssis-packages-should-be-encrypted-and-digitally-signed"></a><a id="ssis-signed"></a>SSIS-paket ska krypteras och signeras digitalt

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Identifiera källan till paket med digitala signaturer,](https://msdn.microsoft.com/library/ms141174.aspx) [hot- och sårbarhetsreducering (Integrationstjänster)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Steg** | Källan till ett paket är den person eller organisation som skapade paketet. Det kan vara riskabelt att köra ett paket från en okänd eller ej betrodd källa. För att förhindra obehörig manipulering av SSIS-paket bör digitala signaturer användas. För att säkerställa paketens konfidentialitet under lagring/transitering måste SSIS-paketen också krypteras |

## <a name="add-digital-signature-to-critical-database-securables"></a><a id="securables-db"></a>Lägga till digital signatur i kritiska databasförsebara säkerhetsreferenser

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [LÄGG TILL SIGNATUR (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Steg** | I de fall där integriteten hos en kritisk databas som kan skyddas måste verifieras bör digitala signaturer användas. Databasöverskörbara funktioner som en lagrad procedur, funktion, sammansättning eller utlösare kan signeras digitalt. Nedan är ett exempel på när detta kan vara användbart: Låt oss säga att en ISV (Oberoende programvaruleverantör) har gett stöd till en programvara som levereras till en av deras kunder. Innan du ger stöd, skulle ISV vill se till att en databas som kan säkerhetsstÃ¶jas i programvaran inte manipulerades antingen av misstag eller av ett skadligt försök. Om det säkerhetsvärde som kan vara digitalt signerat kan ISV verifiera sin digitala signatur och verifiera dess integritet.| 

## <a name="use-sql-server-ekm-to-protect-encryption-keys"></a><a id="ekm-keys"></a>Använda SQL-servern EKM för att skydda krypteringsnycklar

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [SQL Server Extensible Key Management (EKM)](https://msdn.microsoft.com/library/bb895340), [Utökningsbar nyckelhantering med hjälp av SQL Server (Azure Key Vault)](https://msdn.microsoft.com/library/dn198405) |
| **Steg** | Med SQL Server Extensible Key Management kan krypteringsnycklarna som skyddar databasfilerna lagras i en offlineenhet, till exempel en smartcard-, USB-enhet eller EKM/HSM-modul. Detta möjliggör också dataskydd från databasadministratörer (utom medlemmar i sysadmingruppen). Data kan krypteras med hjälp av krypteringsnycklar som endast databasanvändaren har åtkomst till på den externa EKM/HSM-modulen. |

## <a name="use-alwaysencrypted-feature-if-encryption-keys-should-not-be-revealed-to-database-engine"></a><a id="keys-engine"></a>Använd alltidkrypterad funktion om krypteringsnycklar inte ska visas för databasmotorn

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | SQL Azure, OnPrem |
| **Attribut**              | SQL-version - V12, MsSQL2016 |
| **Referenser**              | [Alltid krypterad (databasmotor)](https://msdn.microsoft.com/library/mt163865) |
| **Steg** | Alltid krypterad är en funktion som är utformad för att skydda känsliga data, till exempel kreditkortsnummer eller nationella id-nummer (t.ex. amerikanska personnummer), som lagras i Azure SQL Database- eller SQL Server-databaser. Alltid krypterad gör det möjligt för klienter att kryptera känsliga data i klientprogram och aldrig avslöja krypteringsnycklarna till Database Engine (SQL Database eller SQL Server). Därför ger Alltid krypterad en separation mellan dem som äger data (och kan visa dem) och de som hanterar data (men bör inte ha någon tillgång) |

## <a name="store-cryptographic-keys-securely-on-iot-device"></a><a id="keys-iot"></a>Lagra kryptografiska nycklar säkert på IoT-enhet

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enhet | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Device OS - Windows IoT Core, Enhetsanslutning - Azure IoT-enhet SDK:er |
| **Referenser**              | [TPM på Windows IoT Core](https://developer.microsoft.com/windows/iot/docs/tpm), [Konfigurera TPM på Windows IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/setuptpm), Azure [IoT Device SDK TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Steg** | Symmetriska eller certifikat privata nycklar säkert i en maskinvaruskyddad lagring som TPM eller Smart Card chips. Windows 10 IoT Core stöder användaren av en TPM och det finns https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpmflera kompatibla TPMs som kan användas: . Vi rekommenderar att du använder en inbyggd eller diskret TPM. En TPM för programvara bör endast användas för utvecklings- och testningsändamål. När en TPM är tillgänglig och nycklarna etableras i den, ska koden som genererar token skrivas utan hård kodning av känslig information i den. | 

### <a name="example"></a>Exempel
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Som framgår finns inte enhetens primärnyckel i koden. Istället lagras den i TPM:en vid plats 0. TPM-enheten genererar en kortlivad SAS-token som sedan används för att ansluta till IoT Hub. 

## <a name="generate-a-random-symmetric-key-of-sufficient-length-for-authentication-to-iot-hub"></a><a id="random-hub"></a>Generera en slumpmässig symmetrisk nyckel med tillräcklig längd för autentisering till IoT Hub

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Gateway val - Azure IoT Hub |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | IoT Hub innehåller ett enhetsidentitetsregister och när du etablerar en enhet genererar automatiskt en slumpmässig symmetrisk nyckel. Vi rekommenderar att du använder den här funktionen i Azure IoT Hub Identity Registry för att generera nyckeln som används för autentisering. IoT Hub gör det också möjligt att ange en nyckel när enheten skapas. Om en nyckel genereras utanför IoT Hub under enhetsetablering rekommenderas att du skapar en slumpmässig symmetrisk nyckel eller minst 256 bitar. |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-use-pin-and-allows-remote-wiping"></a><a id="pin-remote"></a>Se till att en enhetshanteringsprincip finns på plats som kräver en användnings-PIN-kod och tillåter fjärrtorkning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobile klient för Dynamics CRM | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Se till att en enhetshanteringsprincip finns på plats som kräver en användnings-PIN-kod och tillåter fjärrtorkning |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-pinpasswordauto-lock-and-encrypts-all-data-eg-bitlocker"></a><a id="bitlocker"></a>Se till att en enhetshanteringsprincip finns på plats som kräver en PIN-kod/lösenord/automatisk låsning och krypterar alla data (t.ex.

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM Outlook-klient | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Se till att en enhetshanteringsprincip finns på plats som kräver en PIN-kod/lösenord/automatisk låsning och krypterar alla data (t.ex. |

## <a name="ensure-that-signing-keys-are-rolled-over-when-using-identity-server"></a><a id="rolled-server"></a>Kontrollera att signeringsnycklar rullas över när identity server använder

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identitetsserver | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Identity Server - nycklar, signaturer och kryptografi](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Steg** | Kontrollera att signeringsnycklarna rullas över när identity server använder. Länken i referensavsnittet förklarar hur detta ska planeras utan att orsaka avbrott för program som är beroende av Identity Server. |

## <a name="ensure-that-cryptographically-strong-client-id-client-secret-are-used-in-identity-server"></a><a id="client-server"></a>Se till att kryptografiskt starkt klient-ID, klienthemlighet används i Identity Server

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identitetsserver | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Kontrollera att kryptografiskt starkt klient-ID, klienthemlighet används i Identity Server. Följande riktlinjer bör användas när ett klient-ID och ett hemligt användar-ID skapas:</p><ul><li>Generera ett slumpmässigt GUID som klient-ID</li><li>Generera en kryptografiskt slumpmässig 256-bitarsnyckel som hemlighet</li></ul>|
