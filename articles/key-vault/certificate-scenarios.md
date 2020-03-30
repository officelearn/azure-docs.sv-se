---
title: Kom igång med Key Vault-certifikat
description: I följande scenarier beskrivs flera av de primära användningarna av Key Vaults certifikathanteringstjänst, inklusive de ytterligare steg som krävs för att skapa ditt första certifikat i nyckelvalvet.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 32a453678fe3702fcb4b77f0b04a8ed5c889ef59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271010"
---
# <a name="get-started-with-key-vault-certificates"></a>Kom igång med Key Vault-certifikat
I följande scenarier beskrivs flera av de primära användningarna av Key Vaults certifikathanteringstjänst, inklusive de ytterligare steg som krävs för att skapa ditt första certifikat i nyckelvalvet.

Följande beskrivs:
- Skapa ditt första Key Vault-certifikat
- Skapa ett certifikat med en certifikatutfärdar som samarbetar med Key Vault
- Skapa ett certifikat med en certifikatutfärdar som inte samarbetar med Key Vault
- Importera ett certifikat

## <a name="certificates-are-complex-objects"></a>Certifikat är komplexa objekt
Certifikat består av tre sammanhängande resurser som är kopplade till varandra som ett key vault-certifikat. certifikatmetadata, en nyckel och en hemlighet.


![Certifikaten är komplexa](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Skapa ditt första Key Vault-certifikat  
 Innan ett certifikat kan skapas i ett Nyckelvalv (KV) måste nödvändiga steg 1 och 2 utföras och ett nyckelvalv måste finnas för den här användaren/organisationen.  

**Steg 1** – certifikatutfärdare  
-   Ombordstigning som IT-administratör, PKI-administratör eller någon som hanterar konton med CIA, för ett visst företag (t.ex. Contoso) är en förutsättning för att använda Key Vault-certifikat.  
    Följande CA:er är de nuvarande partnerleverantörerna med Key Vault:  
    -   DigiCert - Key Vault erbjuder OV TLS/SSL-certifikat med DigiCert.  
    -   GlobalSign - Key Vault erbjuder OV TLS/SSL-certifikat med GlobalSign.  

**Steg 2** - En kontoadministratör för en certifikatutfärdarleverantör skapar autentiseringsuppgifter som ska användas av Key Vault för att registrera, förnya och använda TLS/SSL-certifikat via Key Vault.

**Steg 3** - En Contoso-administratör, tillsammans med en Contoso-anställd (Key Vault-användare) som äger certifikat, beroende på certifikatutfärdaren, kan få ett certifikat från administratören eller direkt från kontot hos certifikatutfärdaren.  

- Påbörja en tilläggsautentiseringsåtgärd i ett nyckelvalv genom [att ange en certifikatutfärdarresurs.](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) En certifikatutfärdare är en entitet som representeras i Azure Key Vault (KV) som en CertificateIssuer-resurs. Den används för att tillhandahålla information om källan till ett KV-certifikat. utfärdarens namn, leverantör, autentiseringsuppgifter och annan administrativ information.
  - t.ex. MyDigiCertIssuer  
    -   Leverantör  
    -   Autentiseringsuppgifter – Autentiseringsuppgifter för CA-konto. Varje certifikatutfärdar har sina egna specifika data.  

    Mer information om hur du skapar konton hos CA-leverantörer finns i det relaterade inlägget på [key vault-bloggen](https://aka.ms/kvcertsblog).  

**Steg 3.1** - Konfigurera [certifikatkontakter](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) för meddelanden. Det här är kontakten för Key Vault-användaren. Key Vault framtvingar inte det här steget.  

Obs - Den här processen, genom steg 3.1, är en engångsåtgärd.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Skapa ett certifikat med en certifikatutfärdar som samarbetar med Key Vault

![Skapa ett certifikat med en certifikatutfärdaren för Key Vault-partner](media/certificate-authority-2.png)

**Steg 4** - Följande beskrivningar motsvarar de gröna numrerade stegen i föregående diagram.  
  (1) - I diagrammet ovan skapar ditt program ett certifikat som internt börjar med att skapa en nyckel i nyckelvalvet.  
  (2) - Key Vault skickar en TLS/SSL-certifikatbegäran till certifikatutfärdaren.  
  (3) - Din ansökan omröstningar, i en loop och vänta process, för din Key Vault för certifikat slutförande. Skapandet av certifikat är klar när Key Vault tar emot certifikatutfärdarens svar med X.509-certifikat.  
  (4) - Certifikatutfärdaren svarar på Key Vaults TLS/SSL-certifikatbegäran med ett X509 TLS/SSL-certifikat.  
  (5) - Din nya licensskapande slutförs med sammanslagningen av X509-certifikatet för certifikatutfärdaren.  

  Key Vault-användare – skapar ett certifikat genom att ange en princip

  -   Upprepa efter behov  
  -   Policybegränsningar  
      -   Egenskaper för X509  
      -   Nyckelegenskaper  
      -   Provider referens - > ex. MyDigiCertIssure  
      -   Förnyelseinformation - > ex. 90 dagar före utgången  

  - En process för att skapa certifikat är vanligtvis en asynkron process och innebär avsökning av nyckelvalvet för tillståndet för åtgärden skapa certifikat.  
[Hämta certifikatåtgärd](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Status: slutförd, misslyckades med felinformation eller, annullerad  
      -   På grund av fördröjningen som ska skapas kan en avbryt-åtgärd initieras. Avbokningen kan vara effektiv eller inte.  

## <a name="import-a-certificate"></a>Importera ett certifikat  
 Alternativt – ett certifikat kan importeras till Key Vault – PFX eller PEM.  

 Mer information om PEM-format finns i avsnittet certifikat i [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md).  

 Importera certifikat – kräver att en PEM eller PFX finns på disken och har en privat nyckel. 
-   Du måste ange: valvets namn och certifikatnamn (principen är valfri)

-   PEM/ PFX-filer innehåller attribut som KV kan tolka och använda för att fylla i certifikatprincipen. Om en certifikatprincip redan har angetts försöker KV matcha data från PFX/PEM-filen.  

-   När importen är slutgiltig kommer efterföljande åtgärder att använda den nya principen (nya versioner).  

-   Om det inte finns några ytterligare åtgärder, är det första key vault gör skicka ett utgångsdatum meddelande. 

-   Användaren kan också redigera principen, som fungerar vid importtillfället, men som innehåller standardvärden där ingen information angavs vid importen. t.ex. ingen information om emittenten  

### <a name="formats-of-import-we-support"></a>Format för import vi stöder
Vi stöder följande typ av import för PEM-filformat. Ett enda PEM-kodat certifikat tillsammans med en PKCS#8-kodad, okrypterad nyckel som har följande

-----BEGIN-CERTIFIKAT----- -----END-CERTIFIKAT-----

-----Begin privat nyckel----- -----END PRIVAT NYCKEL-----

På certifikatsammanslag stöder vi 2 PEM-baserade format. Du kan antingen slå samman ett enda PKCS#8-kodat certifikat eller en base64-kodad P7B-fil. -----BEGIN-CERTIFIKAT----- -----END-CERTIFIKAT-----

Vi stöder för närvarande inte EG-nycklar i PEM-format.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Skapa ett certifikat med en certifikatutfärdar som inte samarbetar med Key Vault  
 Den här metoden gör det möjligt att arbeta med andra certifikatutfärdare än Key Vaults partnerleverantörer, vilket innebär att din organisation kan arbeta med en certifikatutfärdare som de själva väljer.  

![Skapa ett certifikat med din egen certifikatutfärdaren](media/certificate-authority-1.png)  

 Följande stegbeskrivningar motsvarar de gröna stegen i föregående diagram.  

  (1) - I diagrammet ovan skapar ditt program ett certifikat, som internt börjar med att skapa en nyckel i ditt nyckelvalv.  

  (2) - Key Vault returnerar en begäran om certifikatsignering till ditt program.  

  (3) - Din ansökan skickar csr till din valda ca.  

  (4) - Din valda certifikatutfärdare svarar med ett X509-certifikat.  

  (5) - Din ansökan slutför det nya certifikatet med en sammanslagning av X509-certifikatet från din certifikatutfärdare.

## <a name="see-also"></a>Se även

- [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
