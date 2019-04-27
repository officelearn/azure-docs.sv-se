---
title: Kom igång med Key Vault-certifikat
description: Följande scenarier beskriver flera av de primära användningarna av Key Vault-certifikat management-tjänsten, inklusive de ytterligare steg som krävs för att skapa din första certifikatet i ditt nyckelvalv.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 20c05bddddce4c7748e29551fe78d3e5609b2fa5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60641049"
---
# <a name="get-started-with-key-vault-certificates"></a>Kom igång med Key Vault-certifikat
Följande scenarier beskriver flera av de primära användningarna av Key Vault-certifikat management-tjänsten, inklusive de ytterligare steg som krävs för att skapa din första certifikatet i ditt nyckelvalv.

Nedan beskrivs:
- Skapa din första Key Vault-certifikat
- Skapa ett certifikat med en certifikatutfärdare som har inlett ett samarbete med Key Vault
- Skapa ett certifikat med en certifikatutfärdare som samarbetade inte med Key Vault
- Importera ett certifikat

## <a name="certificates-are-complex-objects"></a>Certifikat är komplexa objekt
Certifikat består av tre relaterade resurser som länkas samman som ett Key Vault-certifikat; certifikatet metadata, en nyckel och en hemlighet.


![Certifikat är komplexa](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Skapa din första Key Vault-certifikat  
 Innan ett certifikat kan skapas i en Key Vault (KV), nödvändiga steg 1 och 2 måste utföras har och ett nyckelvalv måste finnas för den här användaren / organisation.  

**Steg 1** -Certificate Authority (CA)-Providers  
-   Registreringen som IT-administratören, PKI-administratör eller vem som helst hantera konton med CA: er för ett visst företag (ex.) Contoso) krävs för att använda Key Vault-certifikat.  
    Följande certifikatutfärdare är de aktuella samarbetade providers med Key Vault:  
    -   DigiCert - Key Vault erbjuder OV SSL-certifikat med DigiCert.  
    -   GlobalSign - Key Vault erbjuder OV SSL-certifikat med GlobalSign  
    -   WoSign - Key Vault erbjuder OV SSL eller EV SSL-certifikat med WoSign baserat på inställningen som konfigurerats av kunden i WoSign kontot på WoSign portal.  

**Steg 2** -kontoadministratör för en CA-providern skapar autentiseringsuppgifter som ska användas av Key Vault och registrera, förnya och använder SSL-certifikat via Key Vault.

**Steg 3** -A Contoso admin, tillsammans med Contoso-anställda (Key Vault-användare) som äger certifikat, beroende på ca: N, kan få ett certifikat från administratören eller direkt från kontot med CA: N.  

- Starta en åtgärd med Lägg till autentiseringsuppgifter till ett nyckelvalv av [ställa in en certifikatutfärdare](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) resurs. En certifikatutfärdare är en entitet som representeras i Azure Key Vault (KV) som en CertificateIssuer resurs. Används för att ange information om källan för ett certifikat för KV; Utfärdarens namn, leverantör, autentiseringsuppgifter och andra administrativa uppgifter.
  - t.ex. MyDigiCertIssuer  
    -   Leverantör  
    -   Autentiseringsuppgifter – autentiseringsuppgifter för CA: N. Varje Certifikatutfärdare har sina egna specifika data.  

    Mer information om hur du skapar konton med CA-providrar finns i relaterade inlägg på den [Key Vault-bloggen](https://aka.ms/kvcertsblog).  

**Steg 3.1** -konfigurera [certifikat kontakter](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) för meddelanden. Det här är kontakten för Key Vault-användaren. Key Vault påtvingar inte det här steget.  

Obs! – den här processen via steg 3.1 är en onetime åtgärd.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Skapa ett certifikat med en Certifikatutfärdare som inlett ett samarbete med Key Vault

![Skapa ett certifikat med en certifikatutfärdare för Key Vault inlett ett samarbete](media/certificate-authority-2.png)

**Steg 4** -med följande motsvarar grön stegen i föregående diagram.  
  (1) – i diagrammet ovan programmet skapar ett certifikat som internt börjar genom att skapa en nyckel i ditt nyckelvalv.  
  (2) - Key Vault skickar en SSL certifikatbegäran till Certifikatutfärdaren.  
  (3) - programmet, i en loop och vänta process avsökningar efter ändrade ditt Nyckelvalv för certifikat slutförande. Skapandet av certifikat har slutförts när Key Vault får Certifikatutfärdarens svar med x509 certifikat.  
  (4) - CA: N som svarar på Key Vault begäran av SSL-certifikat med en X509 SSL-certifikat.  
  (5) - genereringen av din nya certifikatet har slutförts med en sammanslagning av X509 certifikatet för Certifikatutfärdaren.  

  Key Vault-användare – skapar ett certifikat genom att ange en princip

  -   Upprepa efter behov  
  -   Principbegränsningar  
      -   X509 egenskaper  
      -   Nyckelegenskaper  
      -   Referens för provider - > t.ex. MyDigiCertIssure  
      -   Information om förnyelse - > t.ex. 90 dagar innan certifikatet går ut  

  - Process för att skapa ett certifikat är vanligtvis en asynkron åtgärd och omfattar avsökning nyckelvalvet för status för åtgärd för att skapa certifikatet.  
[Åtgärd för hämtning av certifikat](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Status: slutfört, misslyckades. information om fel eller avbruten  
      -   På grund av hur lång tid att skapa kan en avbrottsåtgärd initieras. Knappen Avbryt kanske eller kanske inte effektiva.  

## <a name="import-a-certificate"></a>Importera ett certifikat  
 Du kan också – ett certifikat kan importeras till Key Vault – PFX eller PEM.  

 Mer information om PEM-format finns i avsnittet certifikat i [om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md).  

 Importera certifikat – kräver en PEM- eller PFX på disken och ha en privat nyckel. 
-   Du måste ange: valv namnet och certifikatets namn (princip är valfri)

-   PEM / PFX-filer innehåller attribut som KV kan parsa och använda för att fylla i certifikatprincipen. Om en certifikatprincip har redan angetts, KV görs ett försök att matcha data från PFX / PEM-fil.  

-   När importen är klar efterföljande åtgärder ska använda den nya principen (nya versioner).  

-   Om det finns inga ytterligare åtgärder, är det första Key Vault så skicka ett meddelande om utgångsdatum. 

-   Du kan också redigera principen som fungerar vid tidpunkten för import, men innehåller standardinställningar där ingen information angavs vid import. t.ex. Ingen utfärdare-information  

### <a name="formats-of-import-we-support"></a>Format för vi stöder Import
Vi stöder följande typ av Import för PEM-filformatet. Ett enda PEM-kodat certifikat tillsammans med en PKCS #8-kodat, okrypterade nyckeln som har följande

---BEGIN CERTIFICATE------END CERTIFICATE---

---BEGIN PRIVATE KEY------END PRIVAT NYCKEL---

Om certifikatet koppla stöder vi 2 baserat PEM-format. Du kan antingen koppla ett enda kodad PKCS #8-certifikat eller en base64-kodad P7B-fil. ---BEGIN CERTIFICATE------END CERTIFICATE---

Vi stöder för närvarande inte EG nycklar i PEM-format.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Skapa ett certifikat med en Certifikatutfärdare som inte har gått samman med Key Vault  
 Den här metoden kan arbeta med andra certifikatutfärdare än Key Vault samarbetade providers, vilket innebär att din organisation kan utgå från en Certifikatutfärdare för eget val.  

![Skapa ett certifikat med en egen certifikatutfärdare](media/certificate-authority-1.png)  

 Med följande steg motsvarar grön bokstäver stegen i föregående diagram.  

  (1) – i diagrammet ovan programmet skapar ett certifikat, som internt börjar genom att skapa en nyckel i ditt nyckelvalv.  

  (2) - Key Vault återgår till programmet en begäran om certifikatsignering (CSR).  

  (3) – ditt program skickar CSR till vald CA: N.  

  (4) - vald CA svarar med en X509 certifikat.  

  (5) - programmet har nya certifikat skapat med en sammanslagning av X509 certifikat från Certifikatutfärdaren.

## <a name="see-also"></a>Se även

- [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
