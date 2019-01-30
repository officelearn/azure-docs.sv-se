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
ms.topic: get-started-article
ms.date: 12/04/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: 861784a2d22d15253c61884bfab62667c0560bcd
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250656"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Start-AzsReadinessChecker cmdlet-referens

Modul: Microsoft.AzureStack.ReadinessChecker

Den här modulen innehåller endast en enda cmdlet.  Denna cmdlet utför en eller flera före eller före Underhåll funktioner för Azure Stack.

## <a name="syntax"></a>Syntax

```PowerShell
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

```PowerShell
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

```PowerShell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
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

```PowerShell
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

```PowerShell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
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

```PowerShell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
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

```PowerShell
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

```PowerShell
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

Den **Start AzsReadinessChecker** cmdlet verifierar certifikat, Azure-konton, Azure-prenumerationer och Azure Active kataloger. Köra verifiering innan du distribuerar Azure Stack eller före Azure Stack servicing åtgärder som att hemligheten Rotation. Cmdlet: en kan också användas för att generera loggar certifikatförfrågningar för infrastruktur för certifikat och du kan också PaaS-certifikat.  Cmdlet: en kan slutligen Paketera om PFX-certifikat för att åtgärda vanliga problem med paketering.

## <a name="examples"></a>Exempel

### <a name="example-generate-certificate-signing-request"></a>Exempel: Skapa förfrågan om Certifikatsignering

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

I det här exemplet genererar Start AzsReadinessChecker flera certifikatsignering begäranden (CSR) för certifikat som är lämpliga för en AD FS Azure Stack-distribution med ett regionnamn med ”östra” och ”azurestack.contoso.com” externa FQDN

### <a name="example-validate-certificates"></a>Exempel: Verifiera certifikat

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

I det här exemplet PFX-lösenord uppmanas att ange på ett säkert sätt och Start-AzsReadinessChecker kontrollerar den relativa mappen ”certifikat” för certifikat som är giltig för en AAD-distribution med ett regionnamn ”östra” och ”azurestack.contoso.com” externa FQDN

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Exempel: Verifiera certifikat med distributionsdata (distribution och support)

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

I det här exemplet för distribution och support, PFX-lösenord uppmanas att ange på ett säkert sätt och Start-AzsReadinessChecker kontrollerar den relativa mappen ”certifikat” för certifikat som är giltig för en distribution där identitet, region och externa FQDN läses från den distribution data JSON-fil genererats för distribution. 

### <a name="example-validate-paas-certificates"></a>Exempel: Verifiera certifikat för PaaS

```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

I det här exemplet skapas en hash-tabell med sökvägar och lösenord för att varje PaaS-certifikat. Certifikat kan utelämnas. Start-AzsReadinessChecker kontrollerar varje PFX-sökvägen finns och verifierar dem med hjälp av regionen ”östra” och externa FQDN 'azurestack.contoso.com'.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Exempel: Certifikatsverifiering med data för PaaS

```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

I det här exemplet skapas en hash-tabell med sökvägar och lösenord för att varje PaaS-certifikat. Certifikat kan utelämnas. Start-AzsReadinessChecker kontrollerar varje PFX-sökvägen finns och verifierar dem med hjälp av regionen och externa FQDN läsa från JSON-filen distribution data genereras för distribution. 

### <a name="example-validate-azure-identity"></a>Exempel: Verifiera Azure identity

```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

I det här exemplet kontoautentiseringsuppgifter tjänstadministratören uppmanas på ett säkert sätt och Start-AzsReadinessChecker kontrollerar Azure-konto och Azure Active Directory är giltiga för ett AAD-distribution med namnet på en klient av ”azurestack.contoso.com”

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Exempel: Verifiera Azure identitet med hjälp av distributionsdata (distributionsstöd för)

```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

I det här exemplet kontoautentiseringsuppgifter tjänstadministratören uppmanas på ett säkert sätt och Start-AzsReadinessChecker kontrollerar Azure-konto och Azure Active Directory är giltiga för en AAD-distribution där AzureCloud och TenantName läses från distributionsdata JSON-fil som genererats för distributionen.

### <a name="example-validate-azure-registration"></a>Exempel: Verifiera Azure-registrering

```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

I det här exemplet Prenumerationens ägare autentiseringsuppgifter uppmanas på ett säkert sätt och Start-AzsReadinessChecker utför sedan verifieras mot det angivna kontot och prenumeration för att kontrollera att den kan användas för Azure Stack-registrering. 

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Exempel: Verifiera Azure-registrering med distributionsdata (distributionsgruppen)

```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

I det här exemplet Prenumerationens ägare autentiseringsuppgifter uppmanas på ett säkert sätt och Start-AzsReadinessChecker utför sedan verifieras mot det angivna kontot och prenumeration för att kontrollera att den kan användas för Azure Stack-registrering där ytterligare information är läsa från den distribution data JSON-fil som genererats för distributionen.

### <a name="example-importexport-pfx-package"></a>Exempel: Importera/exportera PFX-paket

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

I det här exemplet PFX-lösenord uppmanas att ange på ett säkert sätt. Filen ssl.pfx ska importeras till lokala datorns certifikatarkiv och exporteras igen med samma lösenord och sparas som ssl_new.pfx.  Den här proceduren är för användning när certifikatsverifiering som har flaggats som en privat nyckel har inte den lokala datorn attributuppsättningen, certifikatkedjan är bruten, irrelevanta certifikaten finns i PFX eller certifikatkedjan är i fel ordning.

### <a name="example-view-validation-report-deployment-support"></a>Exempel: Visa verifieringsrapport (distributionsstöd för)

```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

I det här exemplet distributions- eller support-teamet får beredskapsrapporten från kund (Contoso) och Använd Start AzsReadinessChecker för att visa status för den verifiering körningar Contoso utförs.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Exempel: Visa verifieringsrapport sammanfattning för certifikatet verifiering endast (distribution och support)

```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

I det här exemplet distributions- eller support-teamet får beredskapsrapporten från kunden Contoso och använda Start AzsReadinessChecker för att visa en sammanfattande status för de certifikatet verifiering körningar Contoso utförs.

## <a name="required-parameters"></a>Obligatoriska parametrar

> -RegionName

Anger namn på Azure Stack-distributioner område.
|  |  |
|----------------------------|--------------|
|Ange:                       |Sträng        |
|Position:                   |med namnet         |
|Standardvärde:              |Ingen          |
|Acceptera indata från pipeline:      |False         |
|Acceptera jokertecken: |False         |

> -FQDN

Anger Azure Stack-distributioner externa FQDN, även ett alias som ExternalFQDN och ExternalDomainName.
|  |  |
|----------------------------|--------------|
|Ange:                       |Sträng        |
|Position:                   |med namnet         |
|Standardvärde:              |ExternalFQDN, ExternalDomainName |
|Acceptera indata från pipeline:      |False         |
|Acceptera jokertecken: |False         |

> -IdentitySystem

Anger respektive Azure Stack-distributioner identitetssystem giltiga värden, AAD eller ADFS, för Azure Active Directory och Active Directory Federation Services.
|  |  |
|----------------------------|--------------|
|Ange:                       |Sträng        |
|Position:                   |med namnet         |
|Standardvärde:              |Ingen          |
|Giltiga värden:               |'AAD', 'ADFS'  |
|Acceptera indata från pipeline:      |False         |
|Acceptera jokertecken: |False         |

> -PfxPassword

Anger lösenordet som associeras med certifikat PFX-filer.
|  |  |
|----------------------------|---------|
|Ange:                       |SecureString |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -PaaSCertificates

Anger hash-tabellen som innehåller sökvägar och lösenord till PaaS-certifikat.
|  |  |
|----------------------------|---------|
|Ange:                       |Hash-tabell |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -DeploymentDataJSONPath

Anger Azure Stack-distribution data JSON-konfigurationsfil. Den här filen skapas för distribution.
|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -PfxPath

Anger sökvägen till ett problematiska certifikat som kräver import/export-rutin att åtgärda, som anges av certifikatsverifiering i det här verktyget.
|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -ExportPFXPath  

Anger målsökvägen för filen PFX från import/export-rutin.  
|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -Subject

Anger en ordnad ordlista med ämnet för den certifikat begäran generationen.
|  |  |
|----------------------------|---------|
|Ange:                       |OrderedDictionary   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -RequestType

Anger den SAN-typ av certifikatförfrågan. Giltiga värden MultipleCSR, SingleCSR.

- *MultipleCSR* genererar flera certifikatbegäranden, en för varje tjänst.
- *SingleCSR* genererar en certifikatbegäran för alla tjänster.

|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Giltiga värden:               |'MultipleCSR','SingleCSR' |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -OutputRequestPath

Anger sökvägen för begäran certifikatfiler, katalogen måste finnas.
|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -AADServiceAdministrator

Anger tjänstadministratör för Azure Active Directory som ska användas för Azure Stack-distributioner.
|  |  |
|----------------------------|---------|
|Ange:                       |PSCredential   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -AADDirectoryTenantName

Anger namnet för Azure Active Directory som ska användas för Azure Stack-distributioner.
|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -AzureEnvironment

Anger en instans av Azure-tjänster som innehåller konton, kataloger och prenumerationer som ska användas för Azure Stack-distribution och registrering.
|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Giltiga värden:               |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -RegistrationAccount

Anger det registrerings-konto som ska användas för Azure Stack-registrering.
|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -RegistrationSubscriptionID

Anger registrerings-ID för prenumerationen som ska användas för Azure Stack-registrering.
|  |  |
|----------------------------|---------|
|Ange:                       |GUID     |
|Position:                   |med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -ReportPath

Anger sökvägen för beredskapsrapporten, aktuell katalog- och standard rapportnamn som standard.
|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |med namnet    |
|Standardvärde:              |Alla      |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

## <a name="optional-parameters"></a>Valfria parametrar

> -CertificatePath

Anger sökvägen som endast certifikat krävs certifikat mappar finns.

Nödvändiga mappar för Azure Stack-distribution med Azure Active Directory-identitetssystemet är:

ACSBlob, ACSQueue, ACSTable, Admin Portal, ARM Admin, ARM Public, KeyVault, KeyVaultInternal, Public Portal

Krav på mappen för Azure Stack-distribution med Active Directory Federation Services-identitetssystemet är:

ACSBlob, ACSQueue, ACSTable, ADFS, Admin Portal, ARM Admin, ARM Public, Graph, KeyVault, KeyVaultInternal, Public Portal

|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |med namnet    |
|Standardvärde:              |. \Certificates |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -IncludePaaS  

Anger om PaaS-tjänster/värdnamn ska läggas till certifikat-begäranden.

|  |  |
|----------------------------|------------------|
|Ange:                       |SwitchParameter   |
|Position:                   |med namnet             |
|Standardvärde:              |False             |
|Acceptera indata från pipeline:      |False             |
|Acceptera jokertecken: |False             |

> -ReportSections

Anger om du endast vill visa rapporten Sammanfattning utesluter detalj.
|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |med namnet    |
|Standardvärde:              |Alla      |
|Giltiga värden:               |'Certificate','AzureRegistration','AzureIdentity','Jobs','All' |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -Sammanfattning

Anger om du endast vill visa rapporten Sammanfattning utesluter detalj.
|  |  |
|----------------------------|------------------|
|Ange:                       |SwitchParameter   |
|Position:                   |med namnet             |
|Standardvärde:              |False             |
|Acceptera indata från pipeline:      |False             |
|Acceptera jokertecken: |False             |

> -CleanReport

Tar bort tidigare historik för körning och verifiering och skriver verifieringar till en ny rapport.
|  |  |
|----------------------------|------------------|
|Ange:                       |SwitchParameter   |
|Alias:                    |CF                |
|Position:                   |med namnet             |
|Standardvärde:              |False             |
|Acceptera indata från pipeline:      |False             |
|Acceptera jokertecken: |False             |

> -OutputPath

Anger anpassad sökväg för att spara beredskap JSON-rapporten och detaljerad loggfil.  Om sökvägen inte redan finns, försöker verktyget att skapa katalogen.

|  |  |
|----------------------------|------------------|
|Ange:                       |Sträng            |
|Position:                   |med namnet             |
|Standardvärde:              |$ENV:TEMP\AzsReadinessChecker  |
|Acceptera indata från pipeline:      |False             |
|Acceptera jokertecken: |False             |

> -Confirm

Frågar efter bekräftelse innan du kör cmdlet: en.
|  |  |
|----------------------------|------------------|
|Ange:                       |SwitchParameter   |
|Alias:                    |CF                |
|Position:                   |med namnet             |
|Standardvärde:              |False             |
|Acceptera indata från pipeline:      |False             |
|Acceptera jokertecken: |False             |

> -WhatIf

Visar vad som skulle hända om cmdleten kördes. Cmdleten körs inte.
|  |  |
|----------------------------|------------------|
|Ange:                       |SwitchParameter   |
|Alias:                    |wi                |
|Position:                   |med namnet             |
|Standardvärde:              |False             |
|Acceptera indata från pipeline:      |False             |
|Acceptera jokertecken: |False             |
