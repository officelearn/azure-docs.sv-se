---
title: Kom igång med Key Vault-certifikat
description: Följande scenarier beskriver flera av de primära användningarna Key Vault certifikat management-tjänsten inklusive ytterligare steg krävs för att skapa din första certifikatet i nyckelvalvet.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a788b958-3acb-4bb6-9c94-4776852aeea1
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 665365e06ef02698c10259628f6bf2442ab8d612
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333521"
---
# <a name="get-started-with-key-vault-certificates"></a>Kom igång med Key Vault-certifikat
Följande scenarier beskriver flera av de primära användningarna Key Vault certifikat management-tjänsten inklusive ytterligare steg krävs för att skapa din första certifikatet i nyckelvalvet.

Nedan beskrivs:
- Skapa din första Key Vault-certifikat
- Skapa ett certifikat med en certifikatutfärdare som är tillsammans med Key Vault
- Skapa ett certifikat med en certifikatutfärdare som tillsammans inte med Key Vault
- Importera ett certifikat

## <a name="certificates-are-complex-objects"></a>Certifikat är komplexa objekt
Certifikat som består av tre relaterade resurser länkade tillsammans som en Key Vault-certifikatet. certifikatmetadata, en nyckel och en hemlighet.


![Certifikat är komplexa](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Skapa din första Key Vault-certifikat  
 Innan ett certifikat kan skapas i en Key Vault KV nödvändiga steg 1 och 2 måste utföras har och ett nyckelvalv måste finnas för den här användaren / organisation.  

**Steg 1** -certifikat (certifikatutfärdare) Providers  
-   På boarding som IT-administratören, PKI-administratör eller någon hantera konton med CA: er för ett visst företag (t.ex. Contoso) krävs för att använda Key Vault-certifikat.  
    Följande certifikatutfärdare är de aktuella partnered providers med Key Vault:  
    -   DigiCert - Key Vault erbjuder OV SSL-certifikat med DigiCert.  
    -   GlobalSign - Key Vault erbjuder OV SSL-certifikat med GlobalSign  
    -   WoSign - Key Vault erbjudanden OV SSL eller EV SSL-certifikat med WoSign baserat på inställningar som konfigureras av kund i WoSign kontot på WoSign-portalen.  

**Steg 2** -administratör konto för en CA-providern skapar autentiseringsuppgifter som ska användas av Key Vault och registrera, förnya och använder SSL-certifikat via Nyckelvalvet.

**Steg 3** -A Contoso admin, tillsammans med en Contoso medarbetare (Key Vault-användare) som äger certifikat, beroende på ca: N, kan hämta ett certifikat från administratören eller direkt från ett konto med CA: N.  

-   Starta en åtgärd med Lägg till autentiseringsuppgifter till ett nyckelvalv genom att skapa en [certifikatutfärdare](https://docs.microsoft.com/rest/api/keyvault/certificate-issuers) resurs. 
    -   T.ex. MyDigiCertIssuer  
        -   Leverantör  
        -   Autentiseringsuppgifter – CA-kontouppgifter. Varje Certifikatutfärdare har sin egen specifika data.  

     Mer information om att skapa konton med CA-Providers finns i Närliggande inlägg på den [Nyckelvalvet blogg](http://aka.ms/kvcertsblog).  

**Steg 3.1** – du kan ställa in [certifikat kontakter](https://docs.microsoft.com/rest/api/keyvault/certificate-contacts) för meddelanden. Det här är kontakten för Key Vault-användare. Key Vault tillämpar inte det här steget.  

Obs! – den här processen via steg 3.1 är görs.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Skapa ett certifikat med en Certifikatutfärdare tillsammans med Key Vault

![Skapa ett certifikat med en Key Vault tillsammans certifikatutfärdare](media/certificate-authority-2.png)

**Steg 4** -följande beskrivningar som motsvarar de gröna numrerade stegen i föregående diagram.  
  (1) – i diagrammet ovan programmet skapar ett certifikat som internt börjar genom att skapa en nyckel i nyckelvalvet.  
  (2) - Key Vault skickar en begäran om SSL-certifikatet till ca: N.  
  (3) - programmet, i en loop och vänta process avsökningar efter ändrade ditt Nyckelvalv för slutförande av certifikat. Det certifikatet har skapats när Key Vault får Certifikatutfärdarens svar med x509 certifikat.  
  (4) - CA: N besvarar Key Vault SSL certifikatbegäran med X509 SSL-certifikat.  
  (5) - genereringen av din nya certifikat är klar med en sammanslagning av X509 certifikatet för Certifikatutfärdaren.  

  Key Vault användare – skapar ett certifikat genom att ange en princip

  -   Upprepa efter behov  
  -   Principbegränsningar  
      -   X509 egenskaper  
      -   Nyckelegenskaper  
      -   Providern - > t.ex. MyDigiCertIssure  
      -   Förnyelseinformation om - > t.ex. 90 dagar före förfallodatum  

  - Processen för att skapa ett certifikat är vanligtvis en asynkron åtgärd och innebär avsökning nyckelvalvet av tillståndet för åtgärden för att skapa certifikatet.  
[Åtgärd som certifikatet](https://docs.microsoft.com/en-us/rest/api/keyvault/getcertificateoperation)  
      -   Status: slutfört, misslyckades med information om fel eller avbruten  
      -   På grund av hur lång tid att skapa kan en avbrottsåtgärd initieras. Avbryt kan eller kan inte tillämpas.  

## <a name="import-a-certificate"></a>Importera ett certifikat  
 Ett certifikat kan du också – importeras till Key Vault – PFX eller PEM.  

 Mer information om PEM-format finns i avsnittet certifikat av [om nycklar och hemligheter certifikat](about-keys-secrets-and-certificates.md).  

 Importera certifikat – kräver ett PEM eller PFX på disken och ha en privat nyckel. 
-   Du måste ange: valvet namnet och certifikatets namn (princip är valfri)

-   PEM / PFX-filer innehåller attribut som KV kan parsa och använda för att fylla i certifikatprincipen. Om en certifikatprincip anges KV försöker matcha data från PFX / PEM-filen.  

-   När importen är klar efterföljande åtgärder använder den nya principen (nya versioner).  

-   Om det finns inga fler åtgärder, är det första Key Vault kan skicka en upphör. 

-   Användaren kan också redigera principen som fungerar vid tidpunkten för import, men innehåller standardinställningar där ingen information angavs vid import. T.ex. Ingen utfärdare info  

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Skapa ett certifikat med en Certifikatutfärdare som inte tillsammans med Key Vault  
 Den här metoden kan arbeta med andra certifikatutfärdare än Key Vault tillsammans providers, vilket innebär att din organisation kan arbeta med en Certifikatutfärdare för eget val.  

![Skapa ett certifikat med en egen certifikatutfärdare](media/certificate-authority-1.png)  

 Med följande steg motsvarar grön bokstäver stegen i föregående diagram.  

  (1) – i diagrammet ovan programmet skapar ett certifikat, som internt börjar genom att skapa en nyckel i nyckelvalvet.  

  (2) - returnerar Key Vault för ditt program för en begäran om certifikatsignering (CSR).  

  (3) - programmet skickar Kundservicerepresentanten till vald Certifikatutfärdare.  

  (4) - vald CA svarar med X509 certifikat.  

  (5) - programmet har slutförts nya certifikat skapas med en fusion X509 certifikat från Certifikatutfärdaren.

## <a name="see-also"></a>Se även
- [Certifikatåtgärder](/rest/api/keyvault/certificate-operations.md)
- [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
