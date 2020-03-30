---
title: Få åtkomst till Key Vault med batch - Azure Batch på ett säkert sätt
description: Lär dig hur du programmässigt kommer åt dina autentiseringsuppgifter från Key Vault med Azure Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.topic: article
ms.date: 02/13/2020
ms.author: lahugh
ms.openlocfilehash: 0134e7d92ddca9bd3b45abaf642f33de9d209b33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192310"
---
# <a name="securely-access-key-vault-with-batch"></a>Säker åtkomst till Key Vault med Batch

I den här artikeln får du lära dig hur du konfigurerar Batch-noder för att komma åt autentiseringsuppgifter som lagras i Azure Key Vault på ett säkert sätt. Det är ingen idé att placera dina administratörsautentiseringsuppgifter i Key Vault och sedan autentiseringsuppgifter för hårdkodning för att komma åt Key Vault från ett skript. Lösningen är att använda ett certifikat som ger dina batchnoder åtkomst till Key Vault. Med några få steg kan vi implementera säker nyckellagring för Batch.

Om du vill autentisera till Azure Key Vault från en batchnod behöver du:

- En Azure Active Directory-autentisering (Azure AD)
- Ett certifikat
- Ett batchkonto
- En batchpool med minst en nod

## <a name="obtain-a-certificate"></a>Skaffa ett certifikat

Om du inte redan har ett certifikat är det enklaste sättet att skaffa `makecert` ett att generera ett självsignerat certifikat med kommandoradsverktyget.

Du hittar `makecert` vanligtvis i `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`den här sökvägen: . Öppna en kommandotolk som `makecert` administratör och navigera till med hjälp av följande exempel.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Använd sedan `makecert` verktyget för att skapa självsignerade certifikatfiler som anropas `batchcertificate.cer` och `batchcertificate.pvk`. Det vanliga namnet (CN) som används är inte viktigt för det här programmet, men det är bra att göra det något som talar om för dig vad certifikatet används för.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Batch kräver `.pfx` en fil. Använd [verktyget pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) för `.cer` `.pvk` att konvertera `makecert` och `.pfx` filer som skapats av en enda fil.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Åtkomst till Key Vault beviljas antingen en **användare** eller ett **tjänsthuvudnamn**. Använd ett tjänsthuvudnamn med certifikatet som vi skapade föregående steg för att komma åt Key Vault programmässigt.

Mer information om Azure-tjänsthuvudnamn finns [i Principobjekt för program och tjänst i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

> [!NOTE]
> Tjänstens huvudnamn måste finnas i samma Azure AD-klient som Nyckelvalvet.

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

Url:erna för programmet är inte viktiga eftersom vi bara använder dem för åtkomst till Key Vault.

## <a name="grant-rights-to-key-vault"></a>Bevilja rättigheter till Key Vault

Tjänstens huvudnamn som skapades i föregående steg behöver behörighet för att hämta hemligheterna från Key Vault. Behörighet kan beviljas antingen via Azure-portalen eller med PowerShell-kommandot nedan.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Tilldela ett certifikat till ett batchkonto

Skapa en batchpool och gå sedan till certifikatfliken i poolen och tilldela certifikatet som du skapade. Certifikatet finns nu på alla batchnoder.

Därefter måste vi tilldela certifikatet till batch-kontot. Genom att tilldela certifikatet till kontot kan vi tilldela det till poolerna och sedan till noderna. Det enklaste sättet att göra detta är att gå till ditt Batch-konto i portalen, navigera till **Certifikat**och välja **Lägg till**. Ladda `.pfx` upp filen vi genererade i [Skaffa ett certifikat](#obtain-a-certificate) och ange lösenordet. När du är klar läggs certifikatet till i listan och du kan verifiera tumavtrycket.

Nu när du skapar en batchpool kan du navigera till **Certifikat** i poolen och tilldela certifikatet som du skapade till poolen. När du gör det, se till att du väljer **LocalMachine** för butiken plats. Certifikatet läses in på alla batchnoder i poolen.

## <a name="install-azure-powershell"></a>Installera Azure PowerShell

Om du planerar att komma åt Key Vault med PowerShell-skript på dina noder måste Azure PowerShell-biblioteket installerat. Det finns några sätt att göra detta, om dina noder har Windows Management Framework (WMF) 5 installerat, kan du använda kommandot installera-modul för att hämta den. Om du använder noder som inte har WMF 5 är det enklaste sättet `.msi` att installera det att paketera Azure PowerShell-filen med dina Batch-filer och sedan anropa installationsprogrammet som den första delen av batchstartskriptet. Se det här exemplet för mer information:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Komma åt Key Vault

Nu är vi alla inställda för att komma åt Key Vault i skript som körs på Batch-noder. För att komma åt Key Vault från ett skript behöver du bara skriptet för att autentisera mot Azure AD med hjälp av certifikatet. Det gör du i PowerShell genom att använda följande exempelkommandon. Ange lämpligt GUID för **tumavtryck**, **app-ID** (ID för tjänstens huvudnamn) och **klient-ID** (klienten där tjänstens huvudnamn finns).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

När du har autentiserats får du tillgång till KeyVault som vanligt.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Det här är autentiseringsuppgifterna som ska användas i skriptet.
