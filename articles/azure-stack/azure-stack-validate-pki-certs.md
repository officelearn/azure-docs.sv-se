---
title: Certifikatsverifiering Azure Stack infrastruktur för offentliga nycklar för distribution av Azure-stacken integrerat system | Microsoft Docs
description: Beskriver hur du verifierar Azure Stack PKI-certifikat för Azure-stacken integrerat system. Beskriver med verktyget Azure Stack certifikat layout.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: e381d2ed3c6a972d776dd31f311fcebe2e35823a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605618"
---
# <a name="validate-azure-stack-pki-certificates"></a>Verifiera Azure Stack PKI-certifikat

Verktyget Azure Stack beredskap för installation som beskrivs i den här artikeln finns [från PowerShell-galleriet](https://aka.ms/AzsReadinessChecker). Du kan använda verktyget för att kontrollera att den [genereras PKI-certifikat](azure-stack-get-pki-certs.md) är lämpliga för distributionen. Du bör verifiera certifikat genom att låta tillräckligt med tid för att testa och återutfärda certifikat om det behövs.

Verktyget beredskap Checker utför följande verifieringar för certifikat:

- **Läsa PFX**  
    Söker efter giltiga PFX-fil rätt lösenord, och varnar om offentlig information inte skyddas av lösenordet. 
- **Signaturalgoritm**  
    Kontrollerar att signaturalgoritmen inte är SHA1.
- **Privat nyckel**  
    Kontrollerar att den privata nyckeln finns och har exporterats med attributet lokal dator. 
- **Kedja**  
    Kontrollerar certifikatkedjan är intakt inklusive en kontroll för självsignerade certifikat.
- **DNS-namn**  
    Kontrollerar SAN innehåller relevant DNS-namn för varje slutpunkt eller om en stöder jokertecken är tillgänglig.
- **Nyckelanvändning**  
    Kontrollerar om nyckelanvändningen innehåller digitala signatur och nyckelchiffrering och utökad nyckelanvändning innehåller serverautentisering och klientautentisering.
- **Nyckelstorleken**  
    Kontrollerar om nyckelstorleken 2048 eller större.
- **Kedjans ordning**  
    Kontrollerar ordningen för de certifikat som verifierar att ordningen är korrekt.
- **Andra certifikat**  
    Se till att inga andra certifikat har paketerats i PFX än relevanta lövcertifikatet och kedjan.
- **Ingen profil**  
    Kontrollerar att en ny användare kan läsa in PFX data utan en användarprofil som har lästs in, frihandsbilden beteendet för gMSA konton under behandlingen av certifikat.

> [!IMPORTANT]  
> PKI-certifikat är en PFX-fil och lösenord ska behandlas som känslig information.

## <a name="prerequisites"></a>Förutsättningar

Systemet bör uppfyller följande krav innan du verifierar PKI-certifikat för distribution av en Azure-Stack:

- Microsoft Azure-stacken beredskap layout
- SSL-certifikat exporteras efter den [förberedelseinstruktionerna](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 eller Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Utföra certifikatsverifiering för core services

Följ dessa steg för att förbereda och validera Azure Stack PKI-certifikat för distribution och hemliga rotation:

1. Installera **AzsReadinessChecker** från en PowerShell-kommandotolk (5.1 eller senare), genom att köra följande cmdlet:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ````

2. Skapa katalogstrukturen certifikat. I exemplet nedan kan du ändra `<c:\certificates>` till en ny sökväg som du väljer.

    ````PowerShell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob','ACSQueue','ACSTable','ADFS','Admin Portal','ARM Admin','ARM Public','Graph','KeyVault','KeyVaultInternal','Public Portal'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ````
    
    > [!Note]  
    > AD FS och diagram krävs om du använder AD FS som systemet identitet.
    
     - Placera ditt certifikat i rätt kataloger skapade i föregående steg. Exempel:  
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx`

3. I PowerShell-fönstret Ändra värdena för **RegionName** och **FQDN** lämpliga Azure Stack-miljön och kör du följande:

    ````PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD 

    ````

4. Kontrollera utdata och alla certifikat klara alla tester. Exempel:

    ````PowerShell
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Validation

    Starting Azure Stack Certificate Validation 1.1803.405.3
    Testing: ARM Public\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: ACSBlob\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location: 
    C:\AzsReadinessChecker\AzsReadinessReport.json
    AzsReadinessChecker Completed
    ````

### <a name="known-issues"></a>Kända problem

**Symtom**: tester hoppas över

**Orsak**: AzsReadinessChecker hoppar över vissa undersökningar om ett beroende inte är uppfyllt:

 - Andra certifikat hoppas över om certifikatkedja misslyckas.

    ````PowerShell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

**Lösning**: Följ verktygets riktlinjerna i informationsavsnittet under varje uppsättning med tester för varje certifikat.

## <a name="perform-platform-as-a-service-certificate-validation"></a>Utför plattform som en tjänst certifikatvalidering

Följ dessa steg för att förbereda och verifiera Azure Stack PKI-certifikat för plattform som en tjänst (PaaS)-certifikat om SQL/MySQL eller Apptjänster distributioner planeras.

1.  Installera **AzsReadinessChecker** från en PowerShell-kommandotolk (5.1 eller senare), genom att köra följande cmdlet:

    ````PowerShell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ````

2.  Skapa en kapslad hash-tabell som innehåller sökvägar och lösenord för varje PaaS-certifikat som behöver verifiering. I PowerShell-fönstret som kör:

    ```PowerShell
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  Ändra värdena för **RegionName** och **FQDN** så att den matchar Azure-stacken miljön om du vill starta verifieringen. Kör sedan:

    ```PowerShell
    Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  Kontrollera att alla certifikat utdata och att klara alla tester.

    ```PowerShell
    AzsReadinessChecker v1.1805.425.2 started
    Starting PaaS Certificate Validation
    
    Starting Azure Stack Certificate Validation 1.0 
    Testing: PaaSCerts\wildcard.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\api.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\wildcard.dbadapter.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\sso.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
    ```

## <a name="using-validated-certificates"></a>Med validerade certifikat

När dina certifikat har godkänts av AzsReadinessChecker, är du redo att använda dem i Azure-stacken distributionen eller för Azure-stacken hemliga rotation. 

 - För distribution, på ett säkert sätt överföra ditt certifikat till distribution-tekniker så att de kan kopiera dem till värden som anges i distribution av [Azure Stack PKI krav dokumentationen](azure-stack-pki-certs.md).
 - För hemliga rotation du använda certifikat för att uppdatera gammalt certifikat för infrastruktur för offentliga slutpunkter för din Azure Stack-miljö genom att följa den [Azure Stack hemlighet Rotation dokumentationen](azure-stack-rotate-secrets.md).
 - För PaaS-tjänster du använder certifikat för att installera SQL, MySQL och Resursproviders för App-tjänster i Azure-stacken genom att följa den [översikt över erbjuda tjänster i Azure-stacken dokumentationen](azure-stack-offer-services-overview.md).

## <a name="next-steps"></a>Nästa steg

[Datacenter identitetsintegrering](azure-stack-integrate-identity.md)