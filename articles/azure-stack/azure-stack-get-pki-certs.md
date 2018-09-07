---
title: Skapa Azure Stack Public Key Infrastructure-certifikat för distribution av integrerade Azure Stack-system | Microsoft Docs
description: Beskriver distributionsprocessen för Azure Stack PKI-certifikat för integrerade Azure Stack-system.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 698e044aea6bbd78847cb209160c1fa6b2edcdbf
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44023427"
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Azure Stack-certifikat signering begäran generation

Verktyget Azure Stack-beredskap för installation som beskrivs i den här artikeln är tillgänglig [från PowerShell-galleriet](https://aka.ms/AzsReadinessChecker). Verktyget skapar signering certifikatförfrågningar (CSRs) passar för distribution av Azure Stack. Certifikat bör beställda, genereras och verifierats med tillräckligt med tid för att testa före distributionen.

Verktyget Azure Stack-beredskap för installation (AzsReadinessChecker) utför följande certifikatbegäranden:

 - **Standard certifikatförfrågningar**  
    Begär enligt [generera PKI-certifikat för Azure Stack-distribution](azure-stack-get-pki-certs.md).
 - **Platform as a Service**  
    Du kan också begära platform as a service (PaaS) som ska certifikat som anges i [certifikatkrav för Azure Stack Public Key Infrastructure - certifikat för valfritt PaaS](azure-stack-pki-certs.md#optional-paas-certificates).



## <a name="prerequisites"></a>Förutsättningar

Datorn måste uppfylla följande krav innan du genererar CSR(s) för PKI-certifikat för distribution av Azure Stack:

 - Microsoft Azure Stack-beredskap för installation
 - Certifikatattribut:
    - Regionnamn
    - Externa fullständigt kvalificerade domännamnet (FQDN)
    - Ämne
 - Windows 10 eller Windows Server 2016
 
  > [!NOTE]
  > När du tar emot certifikat från din certifikatutfärdare tillbaka stegen i [förbereda Azure Stack-PKI-certifikat](azure-stack-prepare-pki-certs.md) måste slutföras på samma system!

## <a name="generate-certificate-signing-requests"></a>Generera begäranden om Certifikatsignering

Följ dessa steg för att förbereda och kontrollera Azure Stack PKI-certifikat: 

1.  Installera AzsReadinessChecker från en PowerShell-kommandotolk (5.1 eller senare) genom att köra följande cmdlet:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ````

2.  Deklarera de **ämne** som en sorterad ordlista. Exempel: 

    ````PowerShell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"} 
    ````
    > [!note]  
    > Om ett eget namn (CN) anges detta kommer att skrivas över av certifikatförfrågan första DNS-namn.

3.  Deklarera en utdatakatalog som redan finns. Exempel:

    ````PowerShell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ````
4.  Deklarera identifiera system

    Azure Active Directory

    ```PowerShell
    $IdentitySystem = "AAD"
    ````

    Active Directory Federation Services

    ```PowerShell
    $IdentitySystem = "ADFS"
    ````

5. Deklarera **Regionsnamn** och en **externa FQDN** avsedd för Azure Stack-distributioner.

    ```PowerShell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ````

    > [!note]  
    > `<regionName>.<externalFQDN>` utgör grunden som alla externa DNS-namn i Azure Stack skapas i det här exemplet blir portalen `portal.east.azurestack.contoso.com`.  

6. Så här genererar certifikatsignering begäranden för varje DNS-namn:

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    Om du vill inkludera använder PaaS-tjänster växeln ```-IncludePaaS```

7. Du kan också för Dev/Test-miljöer. För att skapa en enkel certifikatbegäran med flera alternativa namn för certifikatmottagare Lägg till **- RequestType SingleCSR** parametern och värdet (**inte** rekommenderas för produktionsmiljöer):

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    Om du vill inkludera använder PaaS-tjänster växeln ```-IncludePaaS```
    
8. Granska utdata:

    ````PowerShell  
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Request Generation

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com*dn2=*.adminhosting.east.azurestack.contoso.com@dns=*.hosting.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Finished Certificate Request Generation

    AzsReadinessChecker Log location: C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\1.1803.405.3\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

9.  Skicka den **. BEG** fil som skapats på din CA (intern eller offentlig).  Utdatakatalogen av **Start AzsReadinessChecker** innehåller CSR(s) krävs för att skicka till en certifikatutfärdare.  Den innehåller också en underordnad katalog som innehåller INF-fil som används under certifikatsgenereringen begäran som referens. Se till att din CA genererar certifikat med hjälp av din begäran som skapats och som uppfyller de [PKI-kraven för Azure Stack](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Nästa steg

[Förbered Azure Stack PKI-certifikat](azure-stack-prepare-pki-certs.md)
