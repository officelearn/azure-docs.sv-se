---
title: Kryptografi - verktyget Microsoft Threat modellering - Azure | Microsoft Docs
description: "ändringar för hot som exponeras i verktyget Modeling hot"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 96e74371fe51a8050a91c86215e3eefab07bbed8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="security-frame-cryptography--mitigations"></a>Säkerhet ram: Kryptering | Åtgärder 
| Produkter eller tjänster | Artikel |
| --------------- | ------- |
| **Webbprogram** | <ul><li>[Använd endast godkända symmetriska block chiffer och nyckellängder](#cipher-length)</li><li>[Använd godkända block cipher-lägena och initiering angreppsmetoderna symmetriska chiffer](#vector-ciphers)</li><li>[Använd godkända asymmetriska algoritmer, nyckellängder och utfyllnad](#padding)</li><li>[Använd godkända slumpmässiga nummer generatorer](#numgen)</li><li>[Använd inte symmetriska dataströmmen chiffer](#stream-ciphers)</li><li>[Använd godkända MAC/HMAC/Nyckelbaserade hash-algoritmer](#mac-hash)</li><li>[Använd endast godkända kryptografiska hash-funktioner](#hash-functions)</li></ul> |
| **Databas** | <ul><li>[Använd stark krypteringsalgoritmer för att kryptera data i databasen](#strong-db)</li><li>[SSIS-paket ska krypteras och digitalt signerade](#ssis-signed)</li><li>[Lägga till digital signatur kritiska databasen objekt](#securables-db)</li><li>[Använda SQLServer EKM för att skydda krypteringsnycklar](#ekm-keys)</li><li>[Använda AlwaysEncrypted-funktionen om krypteringsnycklar inte lämnas ut till databasmotor](#keys-engine)</li></ul> |
| **IoT-enhet** | <ul><li>[Lagra kryptografiska nycklar på ett säkert sätt på IoT-enhet](#keys-iot)</li></ul> | 
| **Gateway för IoT-moln** | <ul><li>[Generera en slumpmässig symmetrisk nyckel tillräckligt långa för autentisering till IoT-hubb](#random-hub)</li></ul> | 
| **Mobila Dynamics CRM-klienten** | <ul><li>[Se till att en princip för hantering av enheter som kräver en PIN-kod och tillåter fjärråtkomst Rensa](#pin-remote)</li></ul> | 
| **Dynamics CRM Outlook-klienten** | <ul><li>[Kontrollera en princip för hantering av enheter som kräver en PIN-kod/lösenord/automatiskt Lås och krypterar alla data (t.ex. Bitlocker)](#bitlocker)</li></ul> | 
| **Identity Server** | <ul><li>[Se till att Signeringsnycklar har flyttats över när du använder Identity Server](#rolled-server)</li><li>[Se till att kryptografiskt starkt klient-ID-klienthemlighet som används i Identity Server](#client-server)</li></ul> | 

## <a id="cipher-length"></a>Använd endast godkända symmetriska block chiffer och nyckellängder

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | <p>Produkter måste använda endast de symmetriska block chiffer och associerade nyckellängder som uttryckligen har godkänts av Crypto Advisor i din organisation. Godkända symmetriska algoritmer på Microsoft inkluderar följande block chiffer:</p><ul><li>För nya koden accepteras AES-128, 192 AES och AES 256</li><li>För bakåtkompatibilitet med befintlig kod tillåts tre nyckeln 3DES</li><li>För produkter som använder symmetriska block chiffer:<ul><li>Standard AES (Advanced Encryption) krävs för nya koden</li><li>Tre nyckeln trippel datakrypteringsstandard (3DES) är tillåtna i befintlig kod för bakåtkompatibilitet</li><li>Alla andra block chiffer, inklusive RC2, DES, 2 nyckeln 3DES, DESX och bonit, kan endast användas för att dekryptera gamla data och måste ersättas om används för kryptering</li></ul></li><li>En minsta nyckellängd på 128-bitars krävs för symmetrisk block krypteringsalgoritmer. Endast block-krypteringsalgoritmen som rekommenderas för nya kod är AES (AES-128, 192 AES och AES 256 accepteras alla)</li><li>Tre nyckeln 3DES tillåts för närvarande om upptaget i befintlig kod. övergång till AES rekommenderas. DES, DESX, RC2 och bonit är inte längre att vara säker. Dessa algoritmer får endast användas för att dekryptera befintliga data för bakåtkompatibilitet och data ska krypteras igen med ett rekommenderade blockchiffer</li></ul><p>Observera att alla symmetriska block chiffer måste användas med en godkänd chiffreringsläget som kräver användning av en lämplig initieringsvektor (IV). En lämplig IV är vanligtvis ett slumptal och aldrig ett konstant värde</p><p>Användningen av äldre eller på annat sätt obehörig krypterade algoritmer och nyckellängder mindre för att läsa in befintliga data (i stället för skrivning av nya data) kan tillåtas efter din organisations Crypto Board granska. Du måste dock fil för ett undantag mot det här kravet. Dessutom i enterprise-distributioner bör produkter varning administratörer när Svag kryptering används för att läsa data. Sådana varningar ska vara förklarande och rekommenderade åtgärder. I vissa fall kan det vara lämpligt att ha en grupprincip som styr användningen av Svag kryptering</p><p>Tillåtna .NET algoritmer för hanterade kryptografi smidighet (i prioritetsordning)</p><ul><li>AesCng (FIPS-kompatibel)</li><li>AuthenticatedAesCng (FIPS-kompatibel)</li><li>AESCryptoServiceProvider (FIPS-kompatibel)</li><li>AESManaged (icke-FIPS-kompatibel)</li></ul><p>Observera att ingen av dessa algoritmer kan anges den `SymmetricAlgorithm.Create` eller `CryptoConfig.CreateFromName` metoder utan att göra ändringar i filen Machine.config. Observera också att AES i versioner av .NET innan .NET 3.5 heter `RijndaelManaged`, och `AesCng` och `AuthenticatedAesCng` är > tillgängliga via CodePlex och kräver CNG i den underliggande OS</p>

## <a id="vector-ciphers"></a>Använd godkända block cipher-lägena och initiering angreppsmetoderna symmetriska chiffer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | Alla symmetriska block chiffer måste användas med en symmetrisk chiffreringsläget som är godkända. De enda godkända lägena är CBC och CTS. I synnerhet bör elektroniska koden book (ECB) läge i undvikas. användningen av ECB kräver organisationens Crypto Board granska. All användning av OFB, CFB, CTR, CCM, och GCM eller andra Krypteringsläge måste granskas av din organisations Crypto-kort. Återanvända samma Initieringsvektorn (IV) med block chiffer med ”strömmande chiffer lägen”,, till exempel CTR, kan det orsaka krypterade data som ska visas. Alla symmetriska block chiffer måste också användas med en lämplig initieringsvektor (IV). En lämplig IV är ett kryptografiskt starkt, slumpmässigt tal och aldrig ett konstant värde. |

## <a id="padding"></a>Använd godkända asymmetriska algoritmer, nyckellängder och utfyllnad

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | <p>Användning av förbjudna krypteringsalgoritmer introducerar betydande risk att produkten säkerhet och måste undvikas. Produkter måste använda endast de krypteringsalgoritmer och associerade nyckellängder och utfyllnad som uttryckligen har godkänts av din organisations Crypto-kort.</p><ul><li>**RSA -** kan användas för kryptering, nyckelutbyte och signatur. RSA-kryptering måste använda endast OAEP eller RSA-KEM utfyllnad lägen. Befintlig kod kan använda PKCS #1 v1.5 utfyllnadsläget för endast kompatibilitet. Använd null utfyllnad är uttryckligen förbjuden. Nycklar > = 2 048 bitar för ny kod. Befintlig kod stöder nycklar < 2 048 bitar endast för bakåtkompatibilitet kompatibilitet efter en granskning av din organisations Crypto-kort. Nycklar < 1 024 bitar kan endast användas för dekryptering/verifierar gamla data och ersatta om används för kryptering eller signeringsåtgärder</li><li>**ECDSA -** kan användas för endast signaturen. ECDSA med > = 256-bitarsnycklar krävs för ny kod. ECDSA-baserade signaturer måste använda en av tre godkända NIST-kurvor (p-256, p-384 eller P521). Kurvor som har analyserats noggrant kan användas endast efter en granskning med din organisations Crypto-kort.</li><li>**ECDH -** kan användas för för nyckelutbyte. ECDH med > = 256-bitarsnycklar krävs för ny kod. ECDH-baserade utbyte av nycklar måste använda en av tre godkända NIST-kurvor (p-256, p-384 eller P521). Kurvor som har analyserats noggrant kan användas endast efter en granskning med din organisations Crypto-kort.</li><li>**DSA -** kan användas efter granskning och godkännande från din organisations Crypto-kort. Kontakta din security advisor för att schemalägga organisationens Crypto Board granska. Om din användning av DSA godkänns Observera att du måste förhindra att nycklar mindre än 2 048 bitar långt. CNG stöder 2048-bitars och större nyckellängder från och med Windows 8.</li><li>**Diffie-Hellman -** kan användas för sessionen key management. Nyckellängd > = 2 048 bitar för ny kod. Befintlig kod stöder nyckellängder < 2 048 bitar endast för bakåtkompatibilitet kompatibilitet efter en granskning av din organisations Crypto-kort. Nycklar < 1 024 bitar får inte användas.</li><ul>

## <a id="numgen"></a>Använd godkända slumpmässiga nummer generatorer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | <p>Produkter måste använda godkända slumpmässiga nummer generatorer. Pseudoslumpvisa funktioner, till exempel C runtime funktionen SLUMP, .NET Framework-klass System.Random eller systemfunktioner, till exempel GetTickCount måste därför aldrig användas i sådan kod. Användning av den dubbla elliptisk kurva generator (DUAL_EC_DRBG) algoritmen är förbjuden</p><ul><li>**CNG -** BCryptGenRandom (användning av flaggan BCRYPT_USE_SYSTEM_PREFERRED_RNG rekommenderas om anroparen kan köras på alla IRQL som är större än 0 [som är PASSIVE_LEVEL])</li><li>**CAPI -** cryptGenRandom</li><li>**Win32/64-** RtlGenRandom (nya implementeringar bör använda BCryptGenRandom eller CryptGenRandom) * rand_s * SystemPrng (för kernel-läge)</li><li>**. NET -** RNGCryptoServiceProvider eller RNGCng</li><li>**Windows Store Apps -** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom eller. GenerateRandomNumber</li><li>**Apple OS X (10.7+)/iOS(2.0+) -** int SecRandomCopyBytes (SecRandomRef slumpmässigt size_t antal, uint8_t *byte)</li><li>**Apple OS X (< 10.7)-** använder/dev/slumpmässiga för att hämta slumptal</li><li>**Java(including Google Android Java Code) -** java.security.SecureRandom klass. Observera att för Android 4.3 (gelé böna) utvecklare måste följa den rekommenderade lösningen Android och uppdatera sina program att uttryckligen initiera PRNG med entropi från /dev/urandom eller /dev/random</li></ul>|

## <a id="stream-ciphers"></a>Använd inte symmetriska dataströmmen chiffer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | Symmetrisk dataströmmen chiffer, till exempel RC4, får inte användas. I stället för symmetrisk dataströmmen chiffer bör produkter använda blockchiffer specifikt AES med en nyckellängd på minst 128 bitar. |

## <a id="mac-hash"></a>Använd godkända MAC/HMAC/Nyckelbaserade hash-algoritmer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | <p>Produkter måste använda endast godkända message authentication code (MAC) eller hash-baserad kod (HMAC) algoritmer för meddelandeautentisering.</p><p>En message authentication code (MAC) är en typ av information som är kopplad till ett meddelande som tillåter mottagaren att verifiera både avsändarens giltighet och integriteten för meddelandet med en hemlig nyckel. Användning av antingen en hash-baserad MAC ([HMAC](http://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) eller [blockbaserade med cipher MAC](http://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) är tillåtna så länge alla underliggande hash eller symmetriska krypteringsalgoritmer är också godkänd för användning; för tillfället detta inkluderar HMAC SHA2-funktioner (HMAC SHA256, HMAC SHA384 och SHA512 HMAC) och CMAC/OMAC1 och OMAC2 blockera cipher-baserade Mac-datorer (de bygger på AES).</p><p>Användning av HMAC-SHA1 kan det vara tillåtet för plattformskompatibilitet, men du kommer att behöva filen ett undantag till den här proceduren och genomgå kryptografi granska för din organisation. Trunkering av HMAC till mindre än 128 bitar är inte tillåtet. Med customer metoder för att hash-nyckeln data inte är godkänd och ett måste genomgå organisationens Crypto Board granska före användning.</p>|

## <a id="hash-functions"></a>Använd endast godkända kryptografiska hash-funktioner

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | <p>Produkter måste använda SHA-2-familjen för hash-algoritmer (SHA256, SHA384 och SHA512). Om en kortare hash behov, till exempel en 128-bitars utdata längd för att anpassa en datastruktur som utformats med kortare MD5-hash ihåg trunkera produktteam en SHA2 hashvärden (vanligtvis SHA256). Observera att SHA384 är en trunkerade version av SHA512. Trunkering av kryptografiska hash-värden till mindre än 128 bitar av säkerhetsskäl tillåts inte. Ny kod får inte använda MD2, MD4, MD5, SHA-0, SHA-1 eller RIPEMD hash-algoritmer. Hash-kollisioner kan beräknas för dessa algoritmer som effektivt bryter.</p><p>Tillåten .NET hash-algoritmer för hanterade kryptografi smidighet (i ordning):</p><ul><li>SHA512Cng (FIPS-kompatibel)</li><li>SHA384Cng (FIPS-kompatibel)</li><li>SHA256Cng (FIPS-kompatibel)</li><li>SHA512Managed (icke-FIPS-kompatibel) (Använd SHA512 som Algoritmnamn i anrop till HashAlgorithm.Create eller CryptoConfig.CreateFromName)</li><li>SHA384Managed (icke-FIPS-kompatibel) (Använd SHA384 som Algoritmnamn i anrop till HashAlgorithm.Create eller CryptoConfig.CreateFromName)</li><li>SHA256Managed (icke-FIPS-kompatibel) (Använd SHA256 som Algoritmnamn i anrop till HashAlgorithm.Create eller CryptoConfig.CreateFromName)</li><li>SHA512CryptoServiceProvider (FIPS-kompatibel)</li><li>SHA256CryptoServiceProvider (FIPS-kompatibel)</li><li>SHA384CryptoServiceProvider (FIPS-kompatibel)</li></ul>| 

## <a id="strong-db"></a>Använd stark krypteringsalgoritmer för att kryptera data i databasen

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | [Välja en krypteringsalgoritm](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Steg** | Krypteringsalgoritmer definiera Datatransformationer som enkelt inte kan ångras av obehöriga användare. SQL Server kan administratörer och utvecklare kan välja mellan flera algoritmer, inklusive DES, trippel-DES, TRIPLE_DES_3KEY, RC2, RC4, 128-bitars RC4, DESX, 128-bitars AES, 192-bitars AES och 256-bitars AES |

## <a id="ssis-signed"></a>SSIS-paket ska krypteras och digitalt signerade

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | [Identifiera källan till paket med digitala signaturer](https://msdn.microsoft.com/library/ms141174.aspx), [hot och säkerhetsrisker minskning (Integration Services)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Steg** | Källan för ett paket är den person eller organisation som skapade paketet. Det kan vara riskabelt att köra ett paket från en okänd eller ej betrodd källa. Digitala signaturer ska användas för att förhindra obehörig manipulation av SSIS-paket. Dessutom för att säkerställa sekretess för paketen under lagring/överföring, måste SSIS-paket krypteras |

## <a id="securables-db"></a>Lägga till digital signatur kritiska databasen objekt

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | [Lägg till en SIGNATUR (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Steg** | Digitala signaturer ska användas i fall där integriteten hos en kritisk databas skyddbara har verifieras. Objekt i databasen som en lagrad procedur, funktion, sammansättningen eller utlösare kan signeras digitalt. Nedan visas ett exempel på när det kan vara användbart: Låt oss säga en ISV (oberoende programvaruleverantör) tillhandahåller stöd för att en programuppdatering som levereras till en av sina kunder. Innan du ger support vill ISV säkerställa att en databas som skyddas av programvaran inte har ändrats av misstag eller genom att en angripare. Om det skyddbara digitalt signerade kan ISV verifiera den digitala signaturen och bekräfta sin integritet.| 

## <a id="ekm-keys"></a>Använda SQLServer EKM för att skydda krypteringsnycklar

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | [Extensible Key Management för SQL Server (EKM)](https://msdn.microsoft.com/library/bb895340), [Extensible Key Management med hjälp av Azure Key Vault (SQLServer)](https://msdn.microsoft.com/library/dn198405) |
| **Steg** | SQL Server Extensible Key Management gör att krypteringsnycklarna som skyddar databasfilerna ska lagras i en av rutan enhet, till exempel ett smartkort, USB-enhet eller EKM/HSM-modul. Detta kan också dataskydd från databasadministratörer (med undantag för medlemmar i sysadmin-gruppen). Data kan krypteras med hjälp av krypteringsnycklar att databasanvändaren har åtkomst till på den externa EKM/HSM-modulen. |

## <a id="keys-engine"></a>Använda AlwaysEncrypted-funktionen om krypteringsnycklar inte lämnas ut till databasmotor

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | SQL Azure bör OnPrem |
| **Attribut**              | SQL-versionen - V12, MsSQL2016 |
| **Referenser**              | [Always Encrypted (Database Engine)](https://msdn.microsoft.com/library/mt163865) |
| **Steg** | Krypterad är alltid en funktion som utformats för att skydda känsliga data, till exempel kreditkortsnummer eller nationella ID-nummer (t.ex. amerikanska personnummer), lagras i Azure SQL Database eller SQL Server-databaser. Alltid tillåter krypterat klienter att kryptera känsliga data i klientprogram och aldrig avslöja krypteringsnycklarna till databasmotorn (SQL Database eller SQL Server). Därför Always Encrypted ger dig en åtskillnad mellan dem som äger data (och visa det) och de som hanterar data (men bör har ingen åtkomst) |

## <a id="keys-iot"></a>Lagra kryptografiska nycklar på ett säkert sätt på IoT-enhet

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enhet | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Enhetens operativsystem - Windows IoT Core enhetsanslutning - SDK: er för Azure IoT-enhet |
| **Referenser**              | [TPM på Windows IoT Core](https://developer.microsoft.com/windows/iot/docs/tpm), [konfigurera TPM på Windows IoT Core](https://developer.microsoft.com/windows/iot/win10/setuptpm), [Azure IoT-enhet SDK TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Steg** | Symmetric eller certifikatets privata nycklar på ett säkert sätt i en maskinvara skyddad lagring som kretsar TPM eller smartkort. Windows 10 IoT Core stöder användaren med en TPM och det finns flera kompatibel TPM: er som kan användas: https://developer.microsoft.com/windows/iot/win10/tpm. Det rekommenderas att använda en inbyggd programvara eller diskret TPM. TPM programvara bör endast användas för utveckling och testning. När en TPM är tillgänglig och nycklarna har etablerats i den koden som skapar token ska skrivas utan hårda kodning känslig information i den. | 

### <a name="example"></a>Exempel
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Som kan ses finns inte den primära nyckeln för enheten i koden. I stället lagras i TPM: en i uttag 0. TPM-enhet genererar en tillfällig SAS-token som sedan används för att ansluta till IoT-hubben. 

## <a id="random-hub"></a>Generera en slumpmässig symmetrisk nyckel tillräckligt långa för autentisering till IoT-hubb

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Gateway för IoT-moln | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Val av gateway - Azure IoT-hubb |
| **Referenser**              | Saknas  |
| **Steg** | IoT-hubb innehåller en enhet Identitetsregistret och vid etablering av en enhet genererar automatiskt en slumpmässig symmetrisk nyckel. Det rekommenderas att använda funktionen Identitetsregistret Azure IoT-hubb för att skapa den nyckel som används för autentisering. IoT-hubb kan även användas för en nyckel anges när du skapar enheten. Om en nyckel skapas utanför IoT-hubb under etablering av enheter, rekommenderas det att skapa en slumpmässig symmetrisk nyckel eller minst 256 bitar. |

## <a id="pin-remote"></a>Se till att en princip för hantering av enheter som kräver en PIN-kod och tillåter fjärråtkomst Rensa

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobila Dynamics CRM-klienten | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | Se till att en princip för hantering av enheter som kräver en PIN-kod och tillåter fjärråtkomst Rensa |

## <a id="bitlocker"></a>Kontrollera en princip för hantering av enheter som kräver en PIN-kod/lösenord/automatiskt Lås och krypterar alla data (t.ex. Bitlocker)

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM Outlook-klienten | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | Kontrollera en princip för hantering av enheter som kräver en PIN-kod/lösenord/automatiskt Lås och krypterar alla data (t.ex. Bitlocker) |

## <a id="rolled-server"></a>Se till att Signeringsnycklar har flyttats över när du använder Identity Server

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identity Server | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | [Identity Server - nycklar, signaturer och kryptering](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Steg** | Se till att Signeringsnycklar har flyttats över när du använder Identity Server. Länken i referensavsnittet förklarar hur detta bör planeras utan att orsaka avbrott för program som förlitar sig på Identity Server. |

## <a id="client-server"></a>Se till att kryptografiskt starkt klient-ID-klienthemlighet som används i Identity Server

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identity Server | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | <p>Se till att kryptografiskt starkt klient-ID-klienthemlighet som används i Identity Server. Följande riktlinjer ska användas när en klient-ID och Hemlig:</p><ul><li>Generera en slumpmässig GUID som klient-ID</li><li>Generera en slumpmässig kryptografiskt 256-bitars-nyckel som hemligheten</li></ul>|