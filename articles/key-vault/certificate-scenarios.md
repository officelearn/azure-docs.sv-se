---
title: Kom igång med Key Vault certifikat
description: I följande scenarier beskrivs flera av de primära användningarna av Key Vaults hanterings tjänst för certifikat, inklusive de ytterligare steg som krävs för att skapa ditt första certifikat i ditt nyckel valv.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271010"
---
# <a name="get-started-with-key-vault-certificates"></a>Kom igång med Key Vault certifikat
I följande scenarier beskrivs flera av de primära användningarna av Key Vaults hanterings tjänst för certifikat, inklusive de ytterligare steg som krävs för att skapa ditt första certifikat i ditt nyckel valv.

Följande beskrivs:
- Skapa ditt första Key Vault-certifikat
- Skapa ett certifikat med en certifikat utfärdare som är partner med Key Vault
- Skapa ett certifikat med en certifikat utfärdare som inte är partner med Key Vault
- Importera ett certifikat

## <a name="certificates-are-complex-objects"></a>Certifikat är komplexa objekt
Certifikat består av tre relaterade resurser som är länkade tillsammans som ett Key Vault certifikat. certifikatets metadata, en nyckel och en hemlighet.


![Certifikat är komplexa](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Skapa ditt första Key Vault-certifikat  
 Innan ett certifikat kan skapas i en Key Vault (KV) måste nödvändiga steg 1 och 2 slutföras och det måste finnas ett nyckel valv för den här användaren/organisationen.  

**Steg 1** – certifikat utfärdare (ca)  
-   Som IT-administratör, PKI-administratör eller någon som hanterar konton med certifikat utfärdare, för ett specifikt företag (t. ex. Contoso) är ett krav för att använda Key Vault certifikat.  
    Följande ca: er är de aktuella partner leverantörer som har Key Vault:  
    -   DigiCert-Key Vault erbjuder OV TLS/SSL-certifikat med DigiCert.  
    -   GlobalSign-Key Vault erbjuder OV TLS/SSL-certifikat med GlobalSign.  

**Steg 2** – en konto administratör för en ca-Provider skapar autentiseringsuppgifter som ska användas av Key Vault för att registrera, förnya och använda TLS/SSL-certifikat via Key Vault.

**Steg 3** – en Contoso-administratör, tillsammans med en Contoso-anställd (Key Vault användare) som äger certifikat, beroende på certifikat utfärdaren, kan hämta ett certifikat från administratören eller direkt från kontot med certifikat utfärdaren.  

- Påbörja åtgärden Lägg till autentiseringsuppgift till ett nyckel valv genom att [Ange en resurs för certifikat utfärdare](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) . En certifikat utfärdare är en entitet som representeras i Azure Key Vault (KV) som en CertificateIssuer-resurs. Den används för att tillhandahålla information om källan till ett KV-certifikat; utfärdarens namn, Provider, autentiseringsuppgifter och annan administrativ information.
  - t.ex. MyDigiCertIssuer  
    -   Leverantör  
    -   Autentiseringsuppgifter – autentiseringsuppgifter för CA-konto. Varje certifikat utfärdare har sina egna specifika data.  

    Mer information om hur du skapar konton med CA-leverantörer finns i det relaterade inlägget på [Key Vault blogg](https://aka.ms/kvcertsblog).  

**Steg 3,1** – konfigurera [certifikat kontakter](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) för meddelanden. Detta är kontakten för den Key Vault användaren. Key Vault tvingar inte det här steget.  

OBS! den här processen, genom steg 3,1, är en Databasmigrering-åtgärd.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Skapa ett certifikat med en CA som samarbetar med Key Vault

![Skapa ett certifikat med en Key Vault-partner certifikat utfärdare](media/certificate-authority-2.png)

**Steg 4** – följande beskrivningar motsvarar de gröna numrerade stegen i föregående diagram.  
  (1) – i diagrammet ovan skapar ditt program ett certifikat som börjar med att skapa en nyckel i ditt nyckel valv.  
  (2)-Key Vault skickar en TLS/SSL-certifikatbegäran till certifikat utfärdaren.  
  (3) – dina program avsöker, i en slinga och väntar, för din Key Vault för slut för ande av certifikat. Certifikatet har skapats när Key Vault tar emot CA: ns svar med x509-certifikat.  
  (4) – CA: n svarar på Key Vault TLS/SSL-certifikatbegäran med ett X509 TLS/SSL-certifikat.  
  (5) – ditt nya certifikat har skapats med sammanslagningen av X509-certifikatet för CA: n.  

  Key Vault användare – skapar ett certifikat genom att ange en princip

  -   Upprepa vid behov  
  -   Princip begränsningar  
      -   X509-egenskaper  
      -   Nyckelegenskaper  
      -   Leverantörs Referens-> ex. MyDigiCertIssure  
      -   Förnyelse information – > ex. 90 dagar före förfallo datum  

  - En process för att skapa certifikat är vanligt vis en asynkron process och innebär att du kan avsöka ditt nyckel valv efter status för åtgärden Skapa certifikat.  
[Hämta certifikats åtgärd](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Status: slutförd, misslyckades med fel information eller, avbruten  
      -   En avbrotts åtgärd kan initieras på grund av fördröjningen att skapa. Det går inte att avbryta eller så är det inte effektivt.  

## <a name="import-a-certificate"></a>Importera ett certifikat  
 Alternativt – ett certifikat kan importeras till Key Vault – PFX eller PEM.  

 Mer information om PEM-format finns i avsnittet certifikat i [om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md).  

 Importera certifikat – kräver att en PEM eller PFX är på disk och har en privat nyckel. 
-   Du måste ange: valv namn och certifikat namn (principen är valfritt)

-   PEM/PFX-filer innehåller attribut som KV kan parsa och använda för att fylla i certifikat principen. Om en certifikat princip redan har angetts försöker KV matcha data från PFX/PEM-filen.  

-   När importen är slutgiltig använder efterföljande åtgärder den nya principen (nya versioner).  

-   Om det inte finns några ytterligare åtgärder skickas ett meddelande om att det första är Key Vault. 

-   Användaren kan också redigera principen, som fungerar vid tidpunkten för importen, men som innehåller standardinställningar där ingen information har angetts vid import. t.ex. ingen information om utfärdare  

### <a name="formats-of-import-we-support"></a>Format för import vi stöder
Vi stöder följande typ av import för fil formatet PEM. Ett enda PEM-kodat certifikat tillsammans med en PKCS # 8-kodad, okrypterad nyckel som har följande

-----BEGIN CERTIFICATE----------END CERTIFICATE-----

-----STARTA PRIVAT NYCKEL----------SLUTEN PRIVAT NYCKEL-----

Vid koppling av certifikat stöder vi 2 PEM-baserade format. Du kan antingen sammanfoga ett enda PKCS # 8-kodat certifikat eller en Base64-kodad P7B-fil. -----BEGIN CERTIFICATE----------END CERTIFICATE-----

Vi stöder för närvarande inte EG-nycklar i PEM-format.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Skapa ett certifikat med en CA som inte samarbetar med Key Vault  
 Den här metoden gör det möjligt att arbeta med andra certifikat utfärdare än Key Vault partner leverantörer, vilket innebär att organisationen kan arbeta med en valfri CA.  

![Skapa ett certifikat med din egen certifikat utfärdare](media/certificate-authority-1.png)  

 Följande steg beskrivningar motsvarar de gröna bokstavs stegen i föregående diagram.  

  (1) – i diagrammet ovan skapar ditt program ett certifikat som internt börjar genom att skapa en nyckel i ditt nyckel valv.  

  (2) – Key Vault återgår till programmet en certifikat signerings förfrågan (CSR).  

  (3) – ditt program skickar CSR till din valda CA.  

  (4) – din valda CA svarar med ett X509-certifikat.  

  (5) – ditt program Slutför den nya skapande av certifikatet med en sammanslagning av X509-certifikatet från din certifikat utfärdare.

## <a name="see-also"></a>Se även

- [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
