---
title: Skapa och sammanfoga CSR i Azure Key Vault
description: Skapa och sammanfoga CSR i Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: a85656909df5538f9f57e05d79ae768623d7eba6
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289604"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>Skapa och sammanfoga CSR i Key Vault

Azure Key Vault stöder lagring av digitala certifikat som utfärdats av valfri certifikat utfärdare i ditt nyckel valv. Det har stöd för att skapa en begäran om certifikat signering med ett privat offentligt nyckel par som kan signeras av en vald certifikat utfärdare. Det kan vara interna företags certifikat utfärdare eller extern offentlig CA. En begäran om certifikat signering (även CSR eller certifierings förfrågan) är ett meddelande som skickas av användaren till en certifikat utfärdare (CA) för att begära utfärdande av ett digitalt certifikat.

Mer allmän information om certifikat finns i [Azure Key Vault certifikat](./about-certificates.md).

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="adding-certificate-in-key-vault-issued-by-a-non-trusted-ca"></a>Lägga till certifikat i Key Vault som utfärdats av en icke-betrodd certifikat utfärdare

Följande steg hjälper dig att skapa ett certifikat från certifikat utfärdare som inte är partner med Key Vault (till exempel är GoDaddy inte en betrodd Key Vault-certifikatutfärdare) 


### <a name="azure-powershell"></a>Azure PowerShell



1.  Skapa först **certifikat principen**. Key Vault kan inte registrera eller förnya certifikatet från utfärdaren åt användaren eftersom CA: n som valts i det här scenariot inte stöds, och därför har IssuerName angetts till okänd.

    ```azurepowershell
    $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
    ```


2. Skapa en **begäran om certifikat signering**

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. Att hämta CSR- **förfrågan signerad av ca** `$certificateOperation.CertificateSigningRequest` : n är den base4-kodade certifikat signerings förfrågan för certifikatet. Du kan ta denna blob och dumpa till utfärdarens webbplats för certifikat förfrågningar. Det här steget varierar från CA till CA, det bästa sättet är att leta upp din CAs rikt linjer om hur du utför det här steget. Du kan också använda verktyg som CertReq eller openssl för att få en certifikatbegäran signerad och slutföra processen med att skapa ett certifikat.


4. **Sammanslagning av den signerade begäran** i Key Vault efter att certifikatbegäran har signerats av utfärdaren, du kan återställa det signerade certifikatet och slå samman det med det första privata offentliga nyckel paret som skapats i Azure Key Vault

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    Certifikatbegäran har nu slagits samman.

### <a name="azure-portal"></a>Azure Portal

1.  Om du vill skapa en kund service representant för den certifikat utfärdare som du väljer navigerar du till det nyckel valv som du vill lägga till certifikatet i.
2.  Välj **certifikat** på sidan Key Vault egenskaper.
3.  Välj fliken **generera/importera** .
4.  På skärmen **skapa ett certifikat** väljer du följande värden:
    - **Metod för att skapa certifikat:** Skapas.
    - **Certifikat namn:** ContosoManualCSRCertificate.
    - **Typ av certifikat utfärdare (ca):** Certifikat utfärdat av en icke-integrerad certifikat utfärdare
    - **Ämne:**`"CN=www.contosoHRApp.com"`
    - Välj de andra värdena som du vill. Klicka på **Skapa**.

    ![Certifikategenskaper](../media/certificates/create-csr-merge-csr/create-certificate.png)
6.  Du kommer att se att certifikatet nu har lagts till i listan certifikat. Välj det här nya certifikatet som du nyss skapade. Certifikatets aktuella tillstånd är inaktive rad eftersom det inte har utfärdats av certifikat utfärdaren än.
7. Klicka på fliken **certifikat åtgärd** och välj **Hämta CSR**.
 ![Skärm bild som visar knappen Hämta CSR.](../media/certificates/create-csr-merge-csr/download-csr.png)

8.  Ta. CSR-filen till CA: n för begäran om att bli signerad.
9.  När begäran har signerats av certifikat utfärdaren ska du ta tillbaka certifikat filen för att **slå samman den signerade begäran** på skärmen för samma certifikat åtgärd.

Certifikatbegäran har nu slagits samman.

## <a name="adding-more-information-to-csr"></a>Lägga till mer information i CSR

Om du vill lägga till mer information när du skapar CSR, t. ex. 
    - Land:
    - Stad/plats:
    - Region:
    - Organisation
    - Organisations enhet: du kan lägga till all den informationen när du skapar en kund service representant genom att definiera den i subjectName.

Exempel
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```

>[!Note]
>Om du begär ett DV-certifikat med alla dessa uppgifter i CSR kan certifikat utfärdaren avvisa begäran eftersom CA kanske inte kan verifiera all information i begäran. Om du begär ett OV-certifikat blir det mer lämpligt att lägga till all information i CSR.


## <a name="troubleshoot"></a>Felsöka

- **Fel typ: den offentliga nyckeln för certifikatet för slutentiteten i det angivna X. 509-certifikatets innehåll matchar inte den offentliga delen av den angivna privata nyckeln. Kontrol lera att certifikatet är giltigt** . det här felet kan inträffa om du inte slår samman kund service representanten med samma CSR-begäran som initieras. Varje gång en CSR skapas, skapas en privat nyckel som måste matchas vid sammanslagning av den signerade begäran.
    
- När kund service representanten slås samman sammanfogas hela kedjan?
    Ja, den sammanfogar hela kedjan, förutsatt att användaren har återställt P7B-filen att sammanfoga.

- Om certifikatet som har utfärdats är inaktiverat i Azure Portal kan du gå vidare och visa **certifikat åtgärden** för att granska fel meddelandet för det certifikatet.

Mer information finns i [certifikat åtgärderna i Key Vault REST API referens](/rest/api/keyvault). Information om hur du etablerar behörigheter finns i [valv – skapa eller uppdatera](/rest/api/keyvault/vaults/createorupdate) och [valv – uppdatera åtkomst princip](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Nästa steg

- [Autentisering, begär Anden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)