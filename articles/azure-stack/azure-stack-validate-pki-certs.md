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
ms.date: 04/11/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: cd917165804314f6ee4ee006e3f29263d8d4b4c5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
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

> [!IMPORTANT]  
> PKI-certifikat är en PFX-fil och lösenord ska behandlas som känslig information.

## <a name="prerequisites"></a>Förutsättningar

Systemet bör uppfyller följande krav innan du verifierar PKI-certifikat för distribution av en Azure-Stack:

- Microsoft Azure-stacken beredskap layout
- SSL-certifikat exporteras efter den [förberedelseinstruktionerna](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 eller Windows Server 2016

## <a name="perform-certificate-validation"></a>Utföra certifikatsverifiering

Följ dessa steg för att förbereda och validera Azure Stack PKI-certifikat:

1. Installera AzsReadinessChecker från en PowerShell-kommandotolk (5.1 eller senare) genom att köra följande cmdlet:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker 
    ````

2. Skapa katalogstrukturen certifikat. I exemplet nedan kan du ändra `<c:\certificates>` till en ny sökväg som du väljer.

    ````PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','ADFS','Admin Portal','ARM Admin','ARM Public','Graph','KeyVault','KeyVaultInternal','Public Portal' 

    $destination = 'c:\certificates' 

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
    ````

 - Placera ditt certifikat i rätt kataloger skapade i föregående steg. Exempel:  
    - c:\certificates\ACSBlob\CustomerCertificate.pfx 
    - c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx 
    - c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx 
    - och så vidare... 

3. I PowerShell-fönstret som kör:

    ````PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ````

4. Granska utdata för att kontrollera att alla certifikat har överförts testerna. Exempel:

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
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessReport.json
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

## <a name="using-validated-certificates"></a>Med validerade certifikat

När dina certifikat har godkänts av AzsReadinessChecker, är du redo att använda dem i Azure-stacken distributionen eller för Azure-stacken hemliga rotation. 

 - För distribution, på ett säkert sätt överföra ditt certifikat till distribution-tekniker så att de kan kopiera dem till värden som anges i distribution av [Azure Stack PKI krav dokumentationen](azure-stack-pki-certs.md).
 - För hemliga rotation du använda certifikat för att uppdatera gammalt certifikat för infrastruktur för offentliga slutpunkter för din Azure Stack-miljö genom att följa den [Azure Stack hemlighet Rotation dokumentationen](azure-stack-rotate-secrets.md).

## <a name="next-steps"></a>Nästa steg

[Datacenter identitetsintegrering](azure-stack-integrate-identity.md)
