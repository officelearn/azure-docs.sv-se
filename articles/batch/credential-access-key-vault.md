---
title: Säker åtkomst till Key Vault med Batch
description: Lär dig hur du program mässigt får åtkomst till dina autentiseringsuppgifter från Key Vault med hjälp av Azure Batch.
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: d24904c3a539431e8aff420e9fbd8291cddde78a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82117462"
---
# <a name="securely-access-key-vault-with-batch"></a>Säker åtkomst till Key Vault med Batch

I den här artikeln får du lära dig hur du konfigurerar batch-noder för säker åtkomst till autentiseringsuppgifter som lagras i Azure Key Vault. Det finns ingen punkt där du kan ange autentiseringsuppgifter för din administratör i Key Vault och sedan ange autentiseringsuppgifter för att få åtkomst Key Vault från ett skript. Lösningen är att använda ett certifikat som ger dina batch-noder åtkomst till Key Vault. Med några få steg kan vi implementera säker nyckel lagring för batch.

Om du vill autentisera till Azure Key Vault från en batch-nod behöver du:

- En Azure Active Directory (Azure AD)-autentiseringsuppgift
- Ett certifikat
- Ett batch-konto
- En batch-pool med minst en nod

## <a name="obtain-a-certificate"></a>Skaffa ett certifikat

Om du inte redan har ett certifikat är det enklaste sättet att hämta ett certifikat att generera ett självsignerat certifikat med hjälp `makecert` av kommando rads verktyget.

Du kan vanligt vis `makecert` hitta i den här `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`sökvägen:. Öppna en kommando tolk som administratör och navigera till `makecert` i följande exempel.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Använd sedan `makecert` verktyget för att skapa självsignerade certifikatfiler som heter `batchcertificate.cer` och. `batchcertificate.pvk` Det egna namnet (CN) som används är inte viktigt för det här programmet, men det är bra att göra det något som anger vad certifikatet används för.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Batch kräver en `.pfx` fil. Använd verktyget [pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) `.cer` för att konvertera `.pvk` och-filerna som skapats `makecert` av till en `.pfx` enda fil.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Åtkomst till Key Vault beviljas antingen en **användare** eller ett **huvud namn för tjänsten**. Om du vill komma åt Key Vault program mässigt använder du ett huvud namn för tjänsten med det certifikat vi skapade tidigare steg.

Mer information om tjänstens huvud namn för Azure finns [i program-och tjänst huvud objekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

> [!NOTE]
> Tjänstens huvud namn måste finnas i samma Azure AD-klient som Key Vault.

```powershell
$now = [System.DateTime]::Parse("2020-02-10")
# Set this to the expiration date of the certificate
$expirationDate = [System.DateTime]::Parse("2021-02-10")
# Point the script at the cer file you created $cerCertificateFilePath = 'c:\temp\batchcertificate.cer'
$cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cer.Import($cerCertificateFilePath)
# Load the certificate into memory
$credValue = [System.Convert]::ToBase64String($cer.GetRawCertData())
# Create a new AAD application that uses this certificate
$newADApplication = New-AzureRmADApplication -DisplayName "Batch Key Vault Access" -HomePage "https://batch.mydomain.com" -IdentifierUris "https://batch.mydomain.com" -certValue $credValue -StartDate $now -EndDate $expirationDate
# Create new AAD service principal that uses this application
$newAzureAdPrincipal = New-AzureRmADServicePrincipal -ApplicationId $newADApplication.ApplicationId
```

URL: erna för programmet är inte viktiga eftersom vi bara använder dem för Key Vault åtkomst.

## <a name="grant-rights-to-key-vault"></a>Bevilja rättigheter till Key Vault

Tjänstens huvud namn som skapades i föregående steg måste ha behörighet att hämta hemligheterna från Key Vault. Behörighet kan beviljas antingen via Azure Portal eller med PowerShell-kommandot nedan.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Tilldela ett certifikat till ett batch-konto

Skapa en batch-pool och gå sedan till fliken certifikat i poolen och tilldela det certifikat som du skapade. Certifikatet finns nu på alla batch-noder.

Därefter måste vi tilldela certifikatet till batch-kontot. Genom att tilldela certifikatet till kontot kan vi tilldela det till pooler och sedan till noderna. Det enklaste sättet att göra detta på är att gå till ditt batch-konto i portalen, navigera till **certifikat**och välja **Lägg till**. Överför `.pfx` filen som vi genererade i [Hämta ett certifikat](#obtain-a-certificate) och ange lösen ordet. När du är klar läggs certifikatet till i listan och du kan verifiera tumavtrycket.

Nu när du skapar en batch-pool kan du gå till **certifikat** i poolen och tilldela det certifikat som du har skapat till poolen. När du gör det, se till att du väljer **LocalMachine** för lagrings platsen. Certifikatet har lästs in på alla batch-noder i poolen.

## <a name="install-azure-powershell"></a>Installera Azure PowerShell

Om du planerar att komma åt Key Vault med hjälp av PowerShell-skript på noderna måste du ha Azure PowerShell-biblioteket installerat. Det finns några sätt att göra detta på om noderna har Windows Management Framework (WMF) 5 installerat kan du använda kommandot Install-module för att ladda ned det. Om du använder noder som inte har WMF 5, vilket är det enklaste sättet att installera det, är att paketera `.msi` Azure PowerShell-filen med batch-filerna och anropa sedan installations programmet som första del av ditt Start skript för batch. Se det här exemplet för mer information:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Komma åt Key Vault

Nu har vi alla inställningar för att komma åt Key Vault i skript som körs på batch-noder. För att få åtkomst till Key Vault från ett skript, är allt du behöver för att ditt skript ska kunna autentisera mot Azure AD med hjälp av certifikatet. Använd följande exempel kommandon om du vill göra detta i PowerShell. Ange lämpligt GUID för **tumavtryck**, **app-ID** (ID: t för tjänstens huvud namn) och **klient-ID** (innehavaren där tjänstens huvud namn finns).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

När du har autentiserat får du åtkomst till nyckel valvet som vanligt.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Dessa är de autentiseringsuppgifter som ska användas i skriptet.
