---
title: Skapa Azure Stack Public Key Infrastructure-certifikat för distribution av integrerade Azure Stack-system | Microsoft Docs
description: Beskriver distributionsprocessen för Azure Stack PKI-certifikat för integrerade Azure Stack-system.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 602517f13b762f5dd7a13e652a5e8bf5de56e403
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245641"
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Azure Stack-certifikat signering begäran generation

Du kan använda verktyget Azure Stack-beredskap för installation för att skapa signering certifikatförfrågningar (CSRs) som är lämpliga för distribution av Azure Stack. Certifikat bör beställda, genereras och verifierats med tillräckligt med tid för att testa före distributionen. Du kan hämta verktyget [från PowerShell-galleriet](https://aka.ms/AzsReadinessChecker).

Du kan använda verktyget Azure Stack-beredskap för installation (AzsReadinessChecker) för att begära följande certifikat:

 - **Standard certifikatförfrågningar** enligt [generera PKI-certifikat för Azure Stack-distribution](azure-stack-get-pki-certs.md).
 - **Platform-as-a-Service**  
    Du kan begära platform as a service (PaaS) namn för certifikat som anges i [certifikatkrav för Azure Stack Public Key Infrastructure - certifikat för valfritt PaaS](azure-stack-pki-certs.md#optional-paas-certificates).

## <a name="prerequisites"></a>Förutsättningar

Datorn måste uppfylla följande krav innan du genererar CSR(s) för PKI-certifikat för distribution av Azure Stack:

 - Microsoft Azure Stack-beredskap för installation
 - Certifikatattribut:
    - Regionnamn
    - Externa fullständigt kvalificerade domännamnet (FQDN)
    - Subjekt
 - Windows 10 eller Windows Server 2016
 
  > [!NOTE]  
  > När du tar emot certifikat från din certifikatutfärdare tillbaka stegen i [förbereda Azure Stack-PKI-certifikat](azure-stack-prepare-pki-certs.md) måste slutföras på samma system!

## <a name="generate-certificate-signing-requests"></a>Generera begäranden om Certifikatsignering

Följ dessa steg för att förbereda och kontrollera Azure Stack PKI-certifikat: 

1.  Installera AzsReadinessChecker från en PowerShell-kommandotolk (5.1 eller senare) genom att köra följande cmdlet:

    ```PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2.  Deklarera de **ämne** som en sorterad ordlista. Exempel: 

    ```PowerShell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"} 
    ```
    > [!note]  
    > Om ett eget namn (CN) anges detta kommer att skrivas över av certifikatförfrågan första DNS-namn.

3.  Deklarera en utdatakatalog som redan finns. Exempel:

    ```PowerShell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```
4.  Deklarera ID-system

    Azure Active Directory

    ```PowerShell
    $IdentitySystem = "AAD"
    ```

    Active Directory Federation Services

    ```PowerShell
    $IdentitySystem = "ADFS"
    ```

5. Deklarera **Regionsnamn** och en **externa FQDN** avsedd för Azure Stack-distributioner.

    ```PowerShell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!note]  
    > `<regionName>.<externalFQDN>` utgör grunden som alla externa DNS-namn i Azure Stack skapas i det här exemplet blir portalen `portal.east.azurestack.contoso.com`.  

6. Så här genererar certifikatsignering begäranden för varje DNS-namn:

    ```PowerShell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Ange växeln om du vill inkludera PaaS-tjänster ```-IncludePaaS```

7. Alternativt kan du för Dev/Test-miljöer, för att skapa en enkel certifikatbegäran med flera alternativa namn för certifikatmottagare ger **- RequestType SingleCSR** parametern och värdet (**inte** rekommenderas för produktionsmiljöer):

    ```PowerShell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Ange växeln om du vill inkludera PaaS-tjänster ```-IncludePaaS```
    
8. Granska utdata:

    ```PowerShell  
    New-AzsCertificateSigningRequest v1.1809.1005.1 started.
    
    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com*dn2=*.adminhosting.east.azurestack.contoso.com@dns=*.hosting.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    New-AzsCertificateSigningRequest Completed
    ```

9.  Skicka den **. BEG** fil som skapats på din CA (intern eller offentlig).  Utdatakatalogen av **New-AzsCertificateSigningRequest** innehåller CSR(s) krävs för att skicka till en certifikatutfärdare.  Katalogen innehåller också, referens, en underordnad katalog som innehåller INF-fil som används vid skapande av certifikat-begäran. Se till att din CA genererar certifikat med hjälp av din begäran som skapats och som uppfyller de [PKI-kraven för Azure Stack](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Nästa steg

[Förbered Azure Stack PKI-certifikat](azure-stack-prepare-pki-certs.md)
