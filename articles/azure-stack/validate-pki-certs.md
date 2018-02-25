---
title: "Certifikatsverifiering Azure Stack infrastruktur för offentliga nycklar för distribution av Azure-stacken integrerat system | Microsoft Docs"
description: "Beskriver hur du verifierar Azure Stack PKI-certifikat för Azure-stacken integrerat system."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: jeffgilb
ms.reviewer: ppacent
ms.openlocfilehash: 86f1b889d83905abfb5ddab2e82f32922409ff5f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>Verifiera Azure Stack PKI-certifikat
Verktyget Azure Stack certifikat layout som beskrivs i den här artikeln tillhandahålls av OEM-tillverkaren som ingår i filen deploymentdata.json för att kontrollera att den [genereras PKI-certifikat](azure-stack-get-pki-certs.md) är lämpliga för distributionen. Certifikat verifieras med tillräckligt med tid för att testa och få certifikat igen om det behövs. 

Verktyget certifikat Checker (Certchecker) utför följande kontroller:

- **Läsa PFX**. Söker efter giltiga PFX-filen, lösenordet och varnar om offentlig information inte skyddas av lösenordet. 
- **Signaturalgoritm**. Kontrollerar signaturalgoritmen inte är SHA1 
- **Privat nyckel**. Kontrollerar den privata nyckeln finns och har exporterats med attributet lokal dator. 
- **Kedja**. Kontrollerar kedja av intakta inklusive för självsignerade certifikat. 
- **DNS-namn**. Kontrollerar SAN innehåller relevant DNS-namn för varje slutpunkt eller om en stöder jokertecken är tillgänglig. 
- **Nyckelanvändning**. Kontrollerar nyckelanvändning innehåller digitala signatur och nyckelchiffrering och förbättrad nyckelanvändning innehåller serverautentisering och klientautentisering. 
- **Nyckelstorlek**. Kontrollerar nyckelstorlek är 2048 eller större 
- **Kedja ordning**. Kontrollerar ordningen för de certifikat som gör kedjan är korrekt. 
- **Andra certifikat**. Se till att inga andra certifikat har paketerats i PFX än relevanta lövcertifikatet och kedjan. 
- **Ingen profil**. Kontrollerar att en ny användare kan läsa in PFX data utan en användarprofil som har lästs in, frihandsbilden beteendet för gMSA konton under behandlingen av certifikat.   

> [!IMPORTANT]
> PFX-filen för PKI-certifikat och lösenord ska behandlas som känslig information.

## <a name="prerequisites"></a>Förutsättningar
Systemet bör uppfyller följande krav innan du verifierar PKI-certifikat för distribution av Azure Stack:
- CertChecker (i PartnerToolKit under \utils\certchecker)
- SSL-certifikat exporteras efter den [förberedelseinstruktionerna](prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 eller Windows Server 2016

## <a name="perform-certificate-validation"></a>Utföra certifikatsverifiering
Följ dessa steg för att förbereda och validera Azure Stack PKI-certifikat: 

1. Extrahera innehållet i <partnerToolkit>\utils\certchecker till en ny katalog, till exempel **c:\certchecker**.

2. Öppna PowerShell som administratör och ändra katalogen till mappen certchecker:

  ```powershell
  cd c:\certchecker
  ```
 
3. Skapa en katalogstruktur för certifikat genom att köra följande PowerShell-kommandon:

  ```powershell 
  $directories = "ACS","ADFS","Admin Portal","ARM Admin","ARM Public","Graph","KeyVault","KeyVaultInternal","Public Portal" 
  $destination = '.\certs' 
  $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
  ```

  >  [!NOTE]
  >  Om identitetsleverantören för distributionen av Azure-stacken är Azure AD, ta bort den **ADFS** och **diagram** kataloger. 

4. Placera ditt certifikat i lämplig kataloger skapade i föregående steg, till exempel: 
  - c:\certchecker\Certs\ACS\CustomerCertificate.pfx,  
  - c:\certchecker\Certs\Admin Portal\CustomerCertificate.pfx  
  - c:\certchecker\Certs\ARM Admin\CustomerCertificate.pfx  
  - och så vidare... 

5. Kopiera **deploymentdata.json** till den **c:\certchecker** directory.

6. Kör följande kommandon i PowerShell-fönstret: 

  ```powershell
  $password = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
  .\CertChecker.ps1 -CertificatePath .\Certs\ -pfxPassword $password -deploymentDataJSONPath .\DeploymentData.json  
  ```

7. Resultatet bör innehålla OK för alla certifikat och alla attribut som kontrolleras: 

  ```powershell
  Starting Azure Stack Certificate Validation 1.1802.221.1
  Testing: ADFS\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: KeyVaultInternal\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: ACS\ContosoSSL.pfx
  WARNING: Pre-1803 certificate structure. The folder structure for Azure Stack 1803 and above is: ACSBlob, ACSQueue, ACSTable instead of ACS folder. Refer to deployment documentation for further informat
  ion.
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Detailed log can be found C:\CertChecker\CertChecker.log 
  ```

### <a name="known-issues"></a>Kända problem 
**Symtom**: Certchecker avslutas för tidigt och följande felmeddelande: 
> Misslyckad

> Information: Det här kommandot kan inte köras på grund av felet: katalognamnet är ogiltigt. 

**Orsak**: kör certchecker.ps1 från en begränsad mapp, till exempel, c:\temp eller % temp % 

**Lösning**: flytta verktyget certchecker till ny katalog, till exempel C:\CertChecker 


**Symtom**: Certchecker ger en varning om att använda Pre 1803 (som i exemplet ovan från steg 7):

> [!WARNING]
> Pre-1803 Certifikatstrukturen. Mappen struktur för Azure-stacken 1803 och senare: ACSBlob, ACSQueue, ACSTable i stället för ACS-mappen. Mer information finns i dokumentationen.

**Orsak**: CertChecker identifierat användning av en enda ACS-mapp detta stämmer för distributioner innan 1803. Azure-stacken version 1803 för och senare distributioner ändras mappstrukturen till ACSTable, ACSQueue, ACSBlob. Certchecker har redan uppdateras för att stödja den här funktionen.

**Lösning**: om distribuerar 1802 ingen åtgärd krävs. Om distribution av 1803 och ovan, Ersätt ACS med ACSTable, ACSQueue, ACSBlob och kopiera ACS-certifikat till mapparna.

**Symtom**: tester hoppas över

**Orsak**: CertChecker hoppar över vissa undersökningar om ett beroende inte är uppfyllt:
- Andra certifikat hoppas över om certifikatkedja misslyckas.
- Ingen profil hoppas över om:
  - Det finns en säkerhetsprincip för att begränsa möjligheten att skapa en tillfällig användare och köra powershell som användaren.
  - Det går inte att kontrollera privat nyckel.

**Lösning**: följer du anvisningarna i informationsavsnittet under varje certifikat uppsättning tester verktyg.


## <a name="prepare-deployment-script-certificates"></a>Förbereda distribution skript certifikat 
Alla certifikat som du har förberett måste placeras i lämplig kataloger på värden för distribution som ett sista steg. Skapa en mapp med namnet på din värd för distribution. Certifikat ** och placera det exporterade certifikatet filer i motsvarande undermappar som anges i den [obligatoriska certifikat](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates) avsnitt:

```
\Certificates
\ACS\ssl.pfx
\Admin Portal\ssl.pfx
\ARM Admin\ssl.pfx
\ARM Public\ssl.pfx
\KeyVault\ssl.pfx
\KeyVaultInternal\ssl.pfx
\Public Portal\ssl.pfx
\ADFS\ssl.pfx*
\Graph\ssl.pfx*
```

<sup>*</sup> Certifikat som är markerade med en asterisk * behövs bara när AD FS används som identitet Arkiv.


## <a name="next-steps"></a>Nästa steg
[Datacenter identitetsintegrering](azure-stack-integrate-identity.md)