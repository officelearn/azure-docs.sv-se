---
title: Kryptografi - Microsoft Hotmodelleringsverktyg – Azure | Microsoft Docs
description: åtgärder för hot som exponeras i Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 608792d8389a87bad3521d3a48947b20dd036d67
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121225"
---
# <a name="security-frame-cryptography--mitigations"></a>Security ram: Kryptografi | Åtgärder 

| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Webbprogram** | <ul><li>[Använd endast godkända symmetriska blockchiffer och nyckellängder](#cipher-length)</li><li>[Använd godkända block chiffer lägen och initiering angreppsmetoderna symmetriska chiffer](#vector-ciphers)</li><li>[Använd godkända asymmetriska algoritmer, nyckellängder och utfyllnad](#padding)</li><li>[Använd godkända slumpmässigt nummer generatorer](#numgen)</li><li>[Använd inte symmetriska stream-chiffer](#stream-ciphers)</li><li>[Använd godkända MAC/HMAC/registreras hash-algoritmer](#mac-hash)</li><li>[Använd endast godkända kryptografiska hash-funktioner](#hash-functions)</li></ul> |
| **Databas** | <ul><li>[Använda stark krypteringsalgoritmer för att kryptera data i databasen](#strong-db)</li><li>[SSIS-paket ska krypteras och digitalt signerade](#ssis-signed)</li><li>[Lägg till digital signatur i kritiska databasen skyddbara objekt](#securables-db)</li><li>[Använda SQLServer EKM för att skydda krypteringsnycklar](#ekm-keys)</li><li>[Använda AlwaysEncrypted funktionen om krypteringsnycklar inte ska lämnas ut till databasmotorn](#keys-engine)</li></ul> |
| **IoT-enheter** | <ul><li>[Store kryptografiska nycklar på ett säkert sätt på IoT-enheter](#keys-iot)</li></ul> | 
| **IoT Cloud Gateway** | <ul><li>[Generera en slumpmässig symmetrisk nyckel med tillräckligt med längden för autentisering till IoT Hub](#random-hub)</li></ul> | 
| **Dynamics CRM mobila klienten** | <ul><li>[Se till att en princip för hantering av enheter som kräver en PIN-kod för användning och tillåter fjärråtkomst rensas](#pin-remote)</li></ul> | 
| **Dynamics CRM Outlook Client** | <ul><li>[Se till att en princip för hantering av enheten är på plats som kräver en PIN-kod/lösenord/Autolås och krypterar alla data (t.ex. BitLocker)](#bitlocker)</li></ul> | 
| **Identitetsserver** | <ul><li>[Se till att Signeringsnycklar har flyttats över när du använder Identity Server](#rolled-server)</li><li>[Se till att kryptografiskt stark klient-ID, klienthemlighet är används i Identity Server](#client-server)</li></ul> | 

## <a id="cipher-length"></a>Använd endast godkända symmetriska blockchiffer och nyckellängder

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Produkter måste använda dessa symmetriska blockchiffer och associerade nyckellängder som uttryckligen har godkänts av Crypto Advisor i din organisation. Godkända symmetriska algoritmer på Microsoft är blockchiffer som följande:</p><ul><li>För nya koden accepteras AES-128, AES-192 och AES-256</li><li>För bakåtkompatibilitet med befintlig kod tillåts tre-key 3DES</li><li>För produkter med symmetriska blockchiffer:<ul><li>Standard AES (Advanced Encryption) krävs för ny kod</li><li>Tre-key-trippel datakrypteringsstandard (3DES) är tillåtet i befintlig kod för bakåtkompatibilitet</li><li>Alla andra blockchiffer, inklusive RC2, DES, 2 nyckel 3DES, DESX och bonit, kan endast användas för dekryptering av gamla data och måste ersättas om används för kryptering</li></ul></li><li>För symmetrisk block krypteringsalgoritmer krävs en minsta nyckellängd på 128 bitar. Endast block krypteringsalgoritmen som rekommenderas för ny kod är AES (AES-128, AES-192 och AES-256 accepteras alla)</li><li>Tre-key 3DES är för närvarande godkända om redan används i befintlig kod. övergången till AES rekommenderas. DES, DESX, RC2 och bonit är inte längre att vara säker. Dessa algoritmer kan endast användas för dekryptering av befintliga data för bakåtkompatibilitet och data ska krypteras igen med ett rekommenderade blockchiffer</li></ul><p>Observera att alla symmetriska blockchiffer måste användas med en godkänd cipher-läge, vilket kräver att en lämplig initieringsvektor (IV). En lämplig IV är vanligtvis ett slumptal och aldrig ett konstant värde</p><p>Användningen av äldre eller på annat sätt ej godkända krypterade algoritmer och mindre nyckellängd för läsning av befintliga data (i stället för skrivning av nya data) kan tillåtas efter din organisations Crypto tavla granska. Du måste dock filen för ett undantag mot det här kravet. Dessutom i enterprise-distributioner bör produkter varning administratörer när Svag kryptering används för att läsa data. Sådana varningar ska vara beskrivande och användbara. I vissa fall kan det vara lämpligt att ha en grupprincip som styr användningen av Svag kryptering</p><p>Tillåtna .NET algoritmer för hanterade krypto smidighet (i prioritetsordning)</p><ul><li>AesCng (FIPS-kompatibel)</li><li>AuthenticatedAesCng (FIPS-kompatibel)</li><li>AESCryptoServiceProvider (FIPS-kompatibel)</li><li>AESManaged (icke-FIPS-kompatibel)</li></ul><p>Observera att ingen av dessa algoritmer kan anges den `SymmetricAlgorithm.Create` eller `CryptoConfig.CreateFromName` metoder utan att göra ändringar i filen Machine.config. Observera också att AES i versioner av .NET före .NET 3.5 har namnet `RijndaelManaged`, och `AesCng` och `AuthenticatedAesCng` är > tillgängliga via CodePlex och kräver CNG i det underliggande Operativsystemet</p>

## <a id="vector-ciphers"></a>Använd godkända block chiffer lägen och initiering angreppsmetoderna symmetriska chiffer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Alla symmetriska blockchiffer måste användas med en godkänd symmetriska cipher-läge. De enda godkända lägena är CBC och CTS. I synnerhet bör elektronisk kod boken (ECB)-läget för åtgärden undvikas. användningen av ECB kräver granskning för din organisations Crypto tavla. All användning av OFB, CFB, CTR, CCM, och GCM eller andra Krypteringsläge måste granskas av din organisations Crypto tavla. Återanvända samma Initieringsvektorn (IV) med blockchiffer med ”strömmande chiffer lägen”, till exempel CTR, kan det orsaka krypterade data att visas. Alla symmetriska blockchiffer måste också användas med en lämplig initieringsvektor (IV). En lämplig IV är ett kryptografiskt starkt, slumpmässigt nummer och aldrig ett konstant värde. |

## <a id="padding"></a>Använd godkända asymmetriska algoritmer, nyckellängder och utfyllnad

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Användning av förbjudna krypteringsalgoritmer medför betydande risk att produkten säkerhet och måste undvikas. Produkter måste använda endast de krypteringsalgoritmer och associerade nyckellängder och utfyllnad som uttryckligen har godkänts av din organisations Crypto tavla.</p><ul><li>**RSA -** kan användas för kryptering, nyckelutbyte och signatur. RSA-kryptering måste använda endast OAEP eller RSA-KEM utfyllnad lägen. Befintlig kod kan använda PKCS #1 v1.5 utfyllnad läge för endast kompatibilitet. Användning av null utfyllnad är uttryckligen förbjuden. Nycklar > = 2 048 bitar för ny kod. Befintlig kod stöder nycklar < 2 048 bitar endast för bakåtkompatibilitet kompatibilitet efter en granskning av din organisations Crypto tavla. Nycklar < 1 024 bitar kan endast användas för dekryptering/verifierar gamla data och måste ersatta om användas för kryptering eller signeringsåtgärder</li><li>**ECDSA -** kan användas för endast signatur. ECDSA med > = 256-bitars nycklar krävs för ny kod. ECDSA-baserade signaturer måste använda en av tre NIST godkända kurvor (p-256, p-384 eller P521). Kurvor som har analyserats noggrant kan användas förrän en granskning med din organisations Crypto tavla.</li><li>**ECDH -** kan användas för nyckelutbyte endast. ECDH med > = 256-bitars nycklar krävs för ny kod. ECDH-baserade nyckelutbyte måste använda en av tre NIST godkända kurvor (p-256, p-384 eller P521). Kurvor som har analyserats noggrant kan användas förrän en granskning med din organisations Crypto tavla.</li><li>**DSA -** kan vara godtagbar efter granskning och godkännande från din organisations Crypto tavla. Kontakta din säkerhet advisor för att schemalägga organisationens Crypto tavla granskning. Om din användning av DSA godkänns, Observera att du måste förhindra användning av nycklar som mindre än 2 048 bitar långt. CNG stöder 2048-bitars och större nyckellängd från och med Windows 8.</li><li>**Diffie-Hellman -** kan användas för sessionen key management. Nyckellängd > = 2 048 bitar för ny kod. Befintlig kod stöder nyckellängder < 2 048 bitar endast för bakåtkompatibilitet kompatibilitet efter en granskning av din organisations Crypto tavla. Nycklar < 1 024 bitar får inte användas.</li><ul>

## <a id="numgen"></a>Använd godkända slumpmässigt nummer generatorer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Produkter måste använda godkända slumpmässigt nummer generatorer. Pseudoslumpvisa funktioner, till exempel C runtime funktionen rand, .NET Framework-klass System.Random eller systemfunktioner, till exempel GetTickCount måste därför aldrig användas i sådan kod. Användning av den dubbla elliptisk kurva generator (DUAL_EC_DRBG) algoritmen är förbjuden</p><ul><li>**CNG -** BCryptGenRandom (användning av flaggan BCRYPT_USE_SYSTEM_PREFERRED_RNG rekommenderas om anroparen kan köras på alla IRQL som är större än 0 [det vill säga PASSIVE_LEVEL])</li><li>**CAPI -** cryptGenRandom</li><li>**Win32/64-** RtlGenRandom (nya implementeringar bör använda BCryptGenRandom eller CryptGenRandom) * rand_s * SystemPrng (för kernelläge)</li><li>**. NET -** RNGCryptoServiceProvider eller RNGCng</li><li>**Windows Store Apps -** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom eller. GenerateRandomNumber</li><li>**Apple OS X (10.7+)/iOS(2.0+) -** int SecRandomCopyBytes (SecRandomRef slumpmässigt size_t count, uint8_t \*byte)</li><li>**Apple OS X (< 10.7)-** använder/dev/slumpmässiga för att hämta slumptal</li><li>**Java(including Google Android Java Code) -** java.security.SecureRandom klass. Observera att för Android 4.3 (gelé böna) utvecklare måste följa Android rekommenderad lösning och uppdatera program att uttryckligen initiera PRNG med entropi från /dev/urandom eller /dev/random</li></ul>|

## <a id="stream-ciphers"></a>Använd inte symmetriska stream-chiffer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Symmetrisk stream chiffer, till exempel RC4, får inte användas. I stället för symmetrisk stream chiffer, bör produkter använda ett blockchiffer, särskilt AES med en nyckellängd på minst 128 bitar. |

## <a id="mac-hash"></a>Använd godkända MAC/HMAC/registreras hash-algoritmer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Produkter måste använda endast godkända meddelandeautentiseringskod (MAC) eller hash-baserade kod (HMAC) för meddelandeautentisering.</p><p>En message authentication code (MAC) är en del av informationen som är kopplat till ett meddelande som tillåter mottagaren att verifiera både avsändarens giltighet och integriteten i meddelandet med hjälp av en hemlig nyckel. Användning av antingen en hash-baserade MAC ([HMAC](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) eller [cipher-blockbaserade MAC](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) är tillåten som länge som alla underliggande hash eller symmetrisk kryptering algoritmer också är godkända för användning; Detta omfattar för närvarande HMAC SHA2-funktioner (HMAC-SHA256, HMAC-SHA384 och SHA512 av HMAC) och CMAC/OMAC1 och OMAC2 blockera cipher-baserade Mac-datorer (de bygger på AES).</p><p>Användning av HMAC-SHA1 kan det vara tillåtet för plattformskompatibilitet, men du kommer att behöva filen ett undantag till den här proceduren och genomgå krypto granskning i din organisation. Trunkering av HMAC till mindre än 128 bitar är inte tillåtet. Med hjälp av kunden metoder för att hash en nyckel och data inte är godkänd och måste genomgå organisationens Crypto tavla granska före användning.</p>|

## <a id="hash-functions"></a>Använd endast godkända kryptografiska hash-funktioner

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Produkter måste använda SHA-2-familjen för hash-algoritmer (SHA256, SHA384 och SHA512). Om en kortare hash behov, till exempel en längd på 128-bitars utdata för att anpassa en datastruktur som utformats med kortare MD5-hashen i åtanke, trunkera produktteam en av SHA2-hashvärden (vanligtvis SHA256). Observera att SHA384 är en trunkerade version av SHA512. Trunkering av kryptografiska hash-värden av säkerhetsskäl till mindre än 128 bitar är inte tillåtet. Ny kod får inte använda MD2, MD4, MD5, SHA-0, SHA-1 eller RIPEMD hash-algoritmer. Hash-kollisioner kan beräknas för dessa algoritmer som effektivt delar.</p><p>Tillåten .NET hash-algoritmer för hanterade krypto smidighet (i önskad ordning):</p><ul><li>SHA512Cng (FIPS-kompatibel)</li><li>SHA384Cng (FIPS-kompatibel)</li><li>SHA256Cng (FIPS-kompatibel)</li><li>SHA512Managed (icke-FIPS-kompatibel) (Använd SHA512 som algoritmnamnet i anrop till HashAlgorithm.Create eller CryptoConfig.CreateFromName)</li><li>SHA384Managed (icke-FIPS-kompatibel) (Använd SHA384 som algoritmnamnet i anrop till HashAlgorithm.Create eller CryptoConfig.CreateFromName)</li><li>SHA256Managed (icke-FIPS-kompatibel) (Använd SHA256 som algoritmnamnet i anrop till HashAlgorithm.Create eller CryptoConfig.CreateFromName)</li><li>SHA512CryptoServiceProvider (FIPS-kompatibel)</li><li>SHA256CryptoServiceProvider (FIPS-kompatibel)</li><li>SHA384CryptoServiceProvider (FIPS-kompatibel)</li></ul>| 

## <a id="strong-db"></a>Använda stark krypteringsalgoritmer för att kryptera data i databasen

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Välja en krypteringsalgoritm](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Steg** | Krypteringsalgoritmer definiera Datatransformationer som enkelt inte kan ångras av obehöriga användare. SQL Server kan administratörer och utvecklare kan välja bland flera algoritmer, inklusive DES, trippel-DES, TRIPLE_DES_3KEY, RC2, RC4, 128-bitars RC4, DESX, 128-bitars AES, 192-bitars AES och AES 256-bitars |

## <a id="ssis-signed"></a>SSIS-paket ska krypteras och digitalt signerade

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Identifiera källan till paket med digitala signaturer](https://msdn.microsoft.com/library/ms141174.aspx), [hot och säkerhetsrisker minskning (Integration Services)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Steg** | Källan för ett paket är den person eller organisation som skapade paketet. Kan vara riskabelt att köra ett paket från en okänd eller ej betrodd källa. För att förhindra obehörig manipulation av SSIS-paket, bör du använda digitala signaturer. Dessutom för att säkerställa sekretess paket under storage/överföring, har SSIS-paket krypteras |

## <a id="securables-db"></a>Lägg till digital signatur i kritiska databasen skyddbara objekt

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [ADD SIGNATURE (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Steg** | I fall där integriteten hos en kritisk databas skyddbara måste verifieras ska digitala signaturer användas. Databasen skyddbara objekt, till exempel en lagrad procedur, funktion, sammansättningen eller utlösare kan signeras digitalt. Nedan visas ett exempel på när det kan vara användbart: Låt oss anta att en ISV (oberoende programvaruleverantör) har lagt till stöd för en programvara som levereras till en av sina kunder. Innan den ger stöd för kan ISV: er Se till att en databas som skyddas i programvaran inte har ändrats av misstag eller genom att en angripare. Om den aktuella digitalt signerade ISV verifiera den digitala signaturen och verifiera dess integritet.| 

## <a id="ekm-keys"></a>Använda SQLServer EKM för att skydda krypteringsnycklar

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Extensible Key Management för SQL Server (EKM)](https://msdn.microsoft.com/library/bb895340), [utökningsbar nyckelhantering med Azure Key Vault (SQLServer)](https://msdn.microsoft.com/library/dn198405) |
| **Steg** | SQL Server Extensible Key Management gör att krypteringsnycklarna som skyddar databasfilerna ska lagras i en av box-enhet, till exempel ett smartkort, en USB-enhet eller en EKM/HSM-modul. Detta kan också dataskydd från databasadministratörer (med undantag för medlemmar i sysadmin-gruppen). Data kan krypteras med hjälp av krypteringsnycklar som att endast databasanvändaren har åtkomst till på den externa EKM/HSM-modulen. |

## <a id="keys-engine"></a>Använda AlwaysEncrypted funktionen om krypteringsnycklar inte ska lämnas ut till databasmotorn

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | SQL Azure, OnPrem |
| **Attribut**              | SQL-Version - V12 MsSQL2016 |
| **Referenser**              | [Always Encrypted (databasmotor)](https://msdn.microsoft.com/library/mt163865) |
| **Steg** | Always är Encrypted en funktion som utformats för att skydda känsliga data, till exempel kreditkortsnummer eller olika typer av nationella siffror (t.ex. USA personnummer), lagras i Azure SQL Database eller SQL Server-databaser. Always kan Encrypted klienter kryptera känsliga data i klientprogram och aldrig avslöja krypteringsnycklarna till databasmotorn (SQL Database eller SQL Server). Därför Always Encrypted ger dig en åtskillnad mellan den som äger data (och kan visa den) och de som hanterar data (men inte har åtkomsträttigheter) |

## <a id="keys-iot"></a>Store kryptografiska nycklar på ett säkert sätt på IoT-enheter

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enheter | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Enhetens OS - Windows IoT Core enhetsanslutning - SDK: er för Azure IoT-enheter |
| **Referenser**              | [TPM på Windows IoT Core](https://developer.microsoft.com/windows/iot/docs/tpm), [konfigurera TPM på Windows IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/setuptpm), [Azure IoT Device SDK TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Steg** | Symmetric eller certifikatets privata nycklar på ett säkert sätt i en maskinvara skyddad lagring som TPM eller smartkort-kretsar. Windows 10 IoT Core har stöd för användare med en TPM och det finns flera kompatibla TPM: er som kan användas: https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpm. Det rekommenderas att använda en inbyggd programvara eller diskret TPM. Programvara TPM bör endast användas för utveckling och testning. När en TPM är tillgänglig och nycklarna har etablerats i den koden som skapar token ska skrivas utan att hårdkoda känslig information i den. | 

### <a name="example"></a>Exempel
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Se, finns inte den primära nyckeln för enheten i koden. I stället lagras i TPM i fack 0. TPM-enhet genererar en tillfällig SAS-token som används för att ansluta till IoT Hub. 

## <a id="random-hub"></a>Generera en slumpmässig symmetrisk nyckel med tillräckligt med längden för autentisering till IoT Hub

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gateway - valet i Azure IoT Hub |
| **Referenser**              | Gäller inte  |
| **Steg** | IoT Hub innehåller en enhet Identitetsregistret och samtidigt etablera en enhet genererar automatiskt en slumpmässig symmetrisk nyckel. Det rekommenderas att använda den här funktionen i Azure IoT Hub-Identitetsregistret för att skapa den nyckel som används för autentisering. IoT Hub kan även användas för en nyckel anges när du skapar enheten. Om en nyckel genereras vid etablering av enheter utanför IoT Hub, rekommenderas det att skapa en slumpmässig symmetrisk nyckel eller minst 256 bitar. |

## <a id="pin-remote"></a>Se till att en princip för hantering av enheter som kräver en PIN-kod för användning och tillåter fjärråtkomst rensas

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM Mobile Client | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Se till att en princip för hantering av enheter som kräver en PIN-kod för användning och tillåter fjärråtkomst rensas |

## <a id="bitlocker"></a>Se till att en princip för hantering av enheten är på plats som kräver en PIN-kod/lösenord/Autolås och krypterar alla data (t.ex. BitLocker)

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM Outlook Client | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Se till att en princip för hantering av enheten är på plats som kräver en PIN-kod/lösenord/Autolås och krypterar alla data (t.ex. BitLocker) |

## <a id="rolled-server"></a>Se till att Signeringsnycklar har flyttats över när du använder Identity Server

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identitetsserver | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Identitetsserver - nycklar, signaturer och kryptering](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Steg** | Se till att Signeringsnycklar har flyttats över när du använder Identity Server. Länken i referensavsnittet förklarar hur detta bör planeras utan att orsaka avbrott i program som förlitar sig på Identity Server. |

## <a id="client-server"></a>Se till att kryptografiskt stark klient-ID, klienthemlighet är används i Identity Server

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identitetsserver | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Se till att kryptografiskt stark klient-ID, klienthemlighet är används i Identity Server. Följande riktlinjer ska användas när du genererar ett klient-ID och hemlighet:</p><ul><li>Generera en slumpmässig GUID som klient-ID</li><li>Generera en kryptografiskt slumpmässig 256-bitars nyckel som hemligheten</li></ul>|
