---
title: Start-AzsReadinessChecker cmdlet-referens | Microsoft Docs
description: PowerShell-cmdlet-hjälpen för modulen Azure Stack-beredskap för installation.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: f920059f97f43a2ac3c48dad1c8f999833f6add1
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757769"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Start-AzsReadinessChecker cmdlet-referens

Modul: **Microsoft.AzureStack.ReadinessChecker**

Den här modulen innehåller endast en enda cmdlet. Cmdlet: en utför en eller flera före eller före Underhåll funktioner för Azure Stack.

## <a name="syntax"></a>Syntax

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

## <a name="description"></a>Beskrivning

Den **Start AzsReadinessChecker** cmdlet verifierar certifikat, Azure-konton, Azure-prenumerationer och Azure Active kataloger. Köra verifiering innan du distribuerar Azure Stack eller före Azure Stack servicing åtgärder, till exempel hemliga rotation. Cmdlet: en kan också användas för att skapa certifikatsignering certifikatbegäranden infrastruktur, och du kan också PaaS-certifikat. Cmdlet: en kan slutligen Paketera om PFX-certifikat för att åtgärda vanliga problem med paketering.

## <a name="examples"></a>Exempel

### <a name="example-generate-certificate-signing-request"></a>Exempel: Generera en förfrågan om Certifikatsignering

```powershell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

I det här exemplet `Start-AzsReadinessChecker` genererar flera begäranden (CSRs) om certifikatsignering för certifikat som är lämpliga för en AD FS Azure Stack-distribution med ett regionnamn med **östra** och externa fullständiga Domännamnet för  **azurestack.contoso.com**.

### <a name="example-validate-certificates"></a>Exempel: verifiera certifikat

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

I det här exemplet PFX-lösenord måste anges för säkerhet och `Start-AzsReadinessChecker` kontrollerar den relativa mappen **certifikat** för certifikat som är giltig för en AAD-distribution med ett regionnamn med **östra** och en externa FQDN för **azurestack.contoso.com**.

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Exempel: verifiera certifikat med distributionsdata (distribution och support)

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

I det här exemplet för distribution och support, PFX-lösenord måste anges för säkerhet och `Start-AzsReadinessChecker` kontrollerar den relativa mappen **certifikat** för certifikat som är giltig för en distribution där identitet, region och externa FQDN är läsa från JSON-filen distribution data genereras för distribution.

### <a name="example-validate-paas-certificates"></a>Exempel: verifiera certifikat för PaaS

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

I det här exemplet skapas en hash-tabell med sökvägar och lösenord för att varje PaaS-certifikat. Certifikat kan utelämnas. `Start-AzsReadinessChecker` kontrollerar att varje PFX-sökvägen finns och verifierar dem med hjälp av regionen **östra** och externa FQDN **azurestack.contoso.com**.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Exempel: Verifiera PaaS-certifikat med distributionsdata

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

I det här exemplet skapas en hash-tabell med sökvägar och lösenord för att varje PaaS-certifikat. Certifikat kan utelämnas. `Start-AzsReadinessChecker` kontrollerar att varje PFX-sökvägen finns och verifierar dem med hjälp av regionen och externa FQDN läsa från JSON-filen distribution data genereras för distribution.

### <a name="example-validate-azure-identity"></a>Exempel: Bekräfta Azure identitet

```powershell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

I det här exemplet krävs autentiseringsuppgifter för administratörskontot i tjänsten för säkerhet och `Start-AzsReadinessChecker` kontrollerar att Azure-konto och Azure Active Directory är giltiga för en AAD-distribution med en klientnamnet för katalogen för  **azurestack.contoso.com**.

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Exempel: Verifiera Azure identitet med hjälp av distributionsdata (distributionsstöd för)

```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

I det här exemplet krävs autentiseringsuppgifter för administratörskontot i tjänsten för säkerhet och `Start-AzsReadinessChecker` kontrollerar att Azure-konto och Azure Active Directory är giltiga för ett AAD-distribution, där **AzureCloud** och **TenantName** läses från JSON-filen distribution data genereras för distributionen.

### <a name="example-validate-azure-registration"></a>Exempel: Verifiera Azure-registrering

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

I det här exemplet prenumeration ägare autentiseringsuppgifter krävs för säkerhet och `Start-AzsReadinessChecker` utför verifieras mot det angivna kontot och prenumeration för att se till att den kan användas för Azure Stack-registrering.

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Exempel: Verifiera Azure-registrering med distributionsdata (distributionsgruppen)

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

I det här exemplet prenumeration ägare autentiseringsuppgifter krävs för säkerhet och `Start-AzsReadinessChecker` utför verifieras mot det angivna kontot och prenumeration för att se till att den kan användas för Azure Stack-registrering, där ytterligare information läses från den distribution data JSON-fil genererats för distributionen.

### <a name="example-importexport-pfx-package"></a>Exempel: importera/exportera PFX-paket

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

I det här exemplet krävs PFX-lösenord för säkerhet. Filen Ssl.pfx importeras till lokala datorns certifikatarkiv, exporteras igen med samma lösenord och sparas som Ssl_new.pfx. Den här proceduren används när certifikatsverifiering som har flaggats som en privat nyckel inte har den **lokal dator** attributuppsättningen, certifikatkedjan är bruten, irrelevanta certifikaten finns i PFX eller certifikatkedjan är i fel ordning.

### <a name="example-view-validation-report-deployment-support"></a>Exempel: Visa verifieringsrapport (distributionsstöd för)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

I det här exemplet, distribution eller support-teamet tar emot beredskapsrapporten från kunden (Contoso) och använder `Start-AzsReadinessChecker` att visa status för den verifiering körningar Contoso utförs.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Exempel: Visa verifieringsrapport sammanfattning för certifikatet verifiering endast (distribution och support)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

I det här exemplet, distribution eller support-teamet tar emot beredskapsrapporten från kunden (Contoso) och använder `Start-AzsReadinessChecker` att visa en sammanfattande status för de certifikatet verifiering körningar Contoso utförs.

## <a name="required-parameters"></a>Obligatoriska parametrar

### <a name="-regionname"></a>-RegionName

Anger Regionsnamn för Azure Stack-distribution.

|  |  |
|----------------------------|--------------|
|Ange:                       |String        |
|Position:                   |med namnet         |
|Standardvärde:              |Ingen          |
|Acceptera indata från pipeline:      |False         |
|Acceptera jokertecken: |False         |

### <a name="-fqdn"></a>-FQDN

Anger Azure Stack-distributioner externa FQDN, även ett alias som **ExternalFQDN** och **ExternalDomainName**.

|  |  |
|----------------------------|--------------|
|Ange:                       |String        |
|Position:                   |med namnet         |
|Standardvärde:              |ExternalFQDN, ExternalDomainName |
|Acceptera indata från pipeline:      |False         |
|Acceptera jokertecken: |False         |

### <a name="-identitysystem"></a>-IdentitySystem

Anger Azure Stack-distribution identitet system giltiga värden, AAD eller ADFS, för Azure Active Directory och Active Directory Federation Services, respektive.

|  |  |
|----------------------------|--------------|
|Ange:                       |String        |
|Position:                   |med namnet         |
|Standardvärde:              |Ingen          |
|Giltiga värden:               |'AAD', 'ADFS'  |
|Acceptera indata från pipeline:      |False         |
|Acceptera jokertecken: |False         |

### <a name="-pfxpassword"></a>-PfxPassword

Anger lösenordet som associeras med certifikat PFX-filer.

|  |  |
|----------------------------|---------|
|Ange:                       |SecureString |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

### <a name="-paascertificates"></a>-PaaSCertificates

Anger hash-tabellen som innehåller sökvägar och lösenord till PaaS-certifikat.

|  |  |
|----------------------------|---------|
|Ange:                       |Hash-tabell |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

### <a name="-deploymentdatajsonpath"></a>-DeploymentDataJSONPath

Anger Azure Stack-distribution data JSON-konfigurationsfil. Den här filen skapas för distribution.

|  |  |
|----------------------------|---------|
|Ange:                       |String   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

### <a name="-pfxpath"></a>-PfxPath

Anger sökvägen till ett problematiska certifikat som kräver ett import/export-rutin att åtgärda, som anges av certifikatsverifiering i det här verktyget.

|  |  |
|----------------------------|---------|
|Ange:                       |String   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

### <a name="-exportpfxpath"></a>-ExportPFXPath  

Anger målsökvägen för filen PFX av import/export-rutiner.  

|  |  |
|----------------------------|---------|
|Ange:                       |String   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

### <a name="-subject"></a>-Subject

Anger en ordnad ordlista med ämnet för den certifikat begäran generationen.

|  |  |
|----------------------------|---------|
|Ange:                       |OrderedDictionary   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

### <a name="-requesttype"></a>-RequestType

Anger den SAN-typ av certifikatförfrågan. Giltiga värden är **MultipleCSR**, **SingleCSR**.

- **MultipleCSR** genererar flera certifikatbegäranden, en för varje tjänst.
- **SingleCSR** genererar en certifikatbegäran för alla tjänster.

|  |  |
|----------------------------|---------|
|Ange:                       |String   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Giltiga värden:               |'MultipleCSR','SingleCSR' |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

### <a name="-outputrequestpath"></a>-OutputRequestPath

Anger sökvägen för begäran certifikatfiler. Katalogen måste finnas.

|  |  |
|----------------------------|---------|
|Ange:                       |String   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

### <a name="-aadserviceadministrator"></a>-AADServiceAdministrator

Anger tjänstadministratör för Azure Active Directory som ska användas för Azure Stack-distributioner.

|  |  |
|----------------------------|---------|
|Ange:                       |PSCredential   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

### <a name="-aaddirectorytenantname"></a>-AADDirectoryTenantName

Anger namnet för Azure Active Directory som ska användas för Azure Stack-distributioner.

|  |  |
|----------------------------|---------|
|Ange:                       |String   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

### <a name="-azureenvironment"></a>-AzureEnvironment

Anger en instans av Azure-tjänster som innehåller konton, kataloger och prenumerationer som ska användas för Azure Stack-distribution och registrering.

|  |  |
|----------------------------|---------|
|Ange:                       |String   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Giltiga värden:               |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

### <a name="-registrationaccount"></a>-RegistrationAccount

Anger registrerings-konto som ska användas för Azure Stack-registrering.

|  |  |
|----------------------------|---------|
|Ange:                       |String   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

### <a name="-registrationsubscriptionid"></a>-RegistrationSubscriptionID

Anger registrerings-ID för prenumerationen som ska användas för Azure Stack-registrering.

|  |  |
|----------------------------|---------|
|Ange:                       |Guid     |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

### <a name="-reportpath"></a>-ReportPath

Anger sökvägen för beredskapsrapporten, aktuell katalog- och standard rapportnamn som standard.

|  |  |
|----------------------------|---------|
|Ange:                       |String   |
|Position:                   |med namnet    |
|Standardvärde:              |Alla      |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

## <a name="optional-parameters"></a>Valfria parametrar

### <a name="-certificatepath"></a>-CertificatePath

Anger sökvägen som endast certifikat krävs certifikat mappar finns.

Nödvändiga mappar för Azure Stack-distribution med Azure Active Directory-identitetssystemet är:

ACSBlob, ACSQueue, ACSTable, Admin Portal, ARM Admin, ARM Public, KeyVault, KeyVaultInternal, Public Portal

Krav på mappen för Azure Stack-distribution med Active Directory Federation Services-identitetssystemet är:

ACSBlob, ACSQueue, ACSTable, ADFS, Admin Portal, ARM Admin, ARM Public, Graph, KeyVault, KeyVaultInternal, Public Portal

|  |  |
|----------------------------|---------|
|Ange:                       |String   |
|Position:                   |med namnet    |
|Standardvärde:              |. \Certificates |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

### <a name="-includepaas"></a>-IncludePaaS  

Anger om PaaS-tjänster/värdnamn ska läggas till certifikat-begäranden.

|  |  |
|----------------------------|------------------|
|Ange:                       |SwitchParameter   |
|Position:                   |med namnet             |
|Standardvärde:              |False             |
|Acceptera indata från pipeline:      |False             |
|Acceptera jokertecken: |False             |

### <a name="-reportsections"></a>-ReportSections

Anger om du endast vill visa rapporten Sammanfattning utesluter detalj.

|  |  |
|----------------------------|---------|
|Ange:                       |String   |
|Position:                   |med namnet    |
|Standardvärde:              |Alla      |
|Giltiga värden:               |'Certificate','AzureRegistration','AzureIdentity','Jobs','All' |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

### <a name="-summary"></a>-Sammanfattning

Anger om du endast vill visa rapporten Sammanfattning utesluter detalj.

|  |  |
|----------------------------|------------------|
|Ange:                       |SwitchParameter   |
|Position:                   |med namnet             |
|Standardvärde:              |False             |
|Acceptera indata från pipeline:      |False             |
|Acceptera jokertecken: |False             |

### <a name="-cleanreport"></a>-CleanReport

Tar bort tidigare historik för körning och verifiering och skriver verifieringar till en ny rapport.

|  |  |
|----------------------------|------------------|
|Ange:                       |SwitchParameter   |
|Alias:                    |CF                |
|Position:                   |med namnet             |
|Standardvärde:              |False             |
|Acceptera indata från pipeline:      |False             |
|Acceptera jokertecken: |False             |

### <a name="-outputpath"></a>-OutputPath

Anger en anpassad sökväg för att spara rapport om distributionsberedskap JSON och detaljerad loggfil. Om sökvägen inte redan finns, försök kommandot att skapa katalogen.

|  |  |
|----------------------------|------------------|
|Ange:                       |String            |
|Position:                   |med namnet             |
|Standardvärde:              |$ENV:TEMP\AzsReadinessChecker  |
|Acceptera indata från pipeline:      |False             |
|Acceptera jokertecken: |False             |

### <a name="-confirm"></a>-Confirm

Frågar efter bekräftelse innan du kör cmdlet: en.

|  |  |
|----------------------------|------------------|
|Ange:                       |SwitchParameter   |
|Alias:                    |CF                |
|Position:                   |med namnet             |
|Standardvärde:              |False             |
|Acceptera indata från pipeline:      |False             |
|Acceptera jokertecken: |False             |

### <a name="-whatif"></a>-WhatIf

Visar vad som skulle hända om cmdleten kördes. Cmdleten körs inte.

|  |  |
|----------------------------|------------------|
|Ange:                       |SwitchParameter   |
|Alias:                    |wi                |
|Position:                   |med namnet             |
|Standardvärde:              |False             |
|Acceptera indata från pipeline:      |False             |
|Acceptera jokertecken: |False             |
