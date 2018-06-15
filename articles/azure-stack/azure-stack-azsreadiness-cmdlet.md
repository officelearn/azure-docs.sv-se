---
title: Start-AzsReadinessChecker cmdlet-referens | Microsoft Docs
description: PowerShell-cmdlet-hjälpen för modulen Azure Stack beredskap layout.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 8481fbd6c7cb82b34070f9bc8cc6d7f3f4b2518c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937870"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Start-AzsReadinessChecker cmdlet-referens

Modulen: Microsoft.AzureStack.ReadinessChecker

Den här modulen innehåller bara en enda cmdlet.  Denna cmdlet utför en eller flera före distributionen eller före Underhåll funktioner för Azure-stacken.

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
Den **Start AzsReadinessChecker** cmdlet verifierar certifikat, Azure-konton, Azure-prenumerationer och Azure Active-kataloger. Köra verifiering innan du distribuerar Azure Stack eller före Azure Stack servicing åtgärder som hemlighet Rotation. Cmdlet kan också användas för att generera signering certifikatbegäranden infrastruktur för certifikat och eventuellt PaaS-certifikat.  Slutligen paketera cmdlet PFX-certifikat för att reparera vanliga problem med paketering.

## <a name="examples"></a>Exempel
**Exempel: Skapa en förfrågan om Certifikatsignering**

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subjectName $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

I det här exemplet genererar Start AzsReadinessChecker flera certifikatsignering begäranden (CSR) för certifikat som är lämpliga för en AD FS Azure Stack-distribution med ett regionnamn med ”Öst” och ”azurestack.contoso.com” externa FQDN

**Exempel: Verifiera certifikat**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

I det här exemplet uppge PFX-lösenordet på ett säkert sätt och starta AzsReadinessChecker kontrollerar den relativa mappen ”certifikat” för certifikat för en AAD-distribution med ett regionnamn ”Öst” och ”azurestack.contoso.com” externa FQDN 

**Exempel: Verifiera certifikat med distribution data (distribution och support)**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```
I det här exemplet för distribution och support, PFX-lösenordet uppmanas på ett säkert sätt och Start AzsReadinessChecker kontrollerar den relativa mappen ”certifikat” för certifikat för en distribution där identitet, region och externa FQDN läses från den distribution data JSON-fil genererats för distribution. 

**Exempel: Verifiera PaaS-certifikat**
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

I det här exemplet skapas en hash-tabell med sökvägar och lösenord till varje PaaS-certifikat. Certifikat kan utelämnas. Start-AzsReadinessChecker kontrollerar varje PFX-sökvägen finns och verifierar dem med hjälp av regionen Öst- och externa FQDN 'azurestack.contoso.com'.

**Exempel: Certifikatsverifiering PaaS med data för distribution**
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

I det här exemplet skapas en hash-tabell med sökvägar och lösenord till varje PaaS-certifikat. Certifikat kan utelämnas. Start-AzsReadinessChecker kontrollerar varje PFX-sökvägen finns och verifierar dem med hjälp av regionen och externa FQDN läsa från distribution data JSON-fil genererats för distribution. 

**Exempel: Verifiera Azure identitet**
```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AzureDirectoryTenantName azurestack.contoso.com
```

I det här exemplet kontoautentiseringsuppgifter tjänstadministratör ombeds på ett säkert sätt och starta AzsReadinessChecker kontrollerar Azure-konto och Azure Active Directory är giltiga för ett AAD-distribution med namnet på en klient av ”azurestack.contoso.com”


**Exempel: Verifiera Azure identitet med distribution data (stöd för distribution)**
```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

I det här exemplet kontoautentiseringsuppgifter tjänstadministratör ombeds på ett säkert sätt och starta AzsReadinessChecker kontrollerar Azure-konto och Azure Active Directory är giltig för en AAD-distribution där AzureCloud och TenantName läses från data för distribution JSON-fil som genererats för distributionen.


**Exempel: Verifiera registrering av Azure**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment AzureCloud
```

I det här exemplet Prenumerationsägaren autentiseringsuppgifterna uppmanas att på ett säkert sätt och Start AzsReadinessChecker utför sedan verifieras mot med angivet konto och prenumeration så att den kan användas för registrering av Azure-stacken. 


**Exempel: Verifiera Azure registrering med distribution data (distributionsgruppen)**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

I det här exemplet Prenumerationsägaren autentiseringsuppgifterna ombeds på ett säkert sätt och Start AzsReadinessChecker utför sedan verifieras mot med angivet konto och prenumeration för att säkerställa att den kan användas för registrering av Azure-stacken där ytterligare information är läsa från filen distribution JSON genereras för distributionen.

**Exempel: Importera och exportera PFX-paket**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

I det här exemplet uppmanas PFX-lösenordet på ett säkert sätt. Filen ssl.pfx ska importeras till lokala datorns certifikatarkiv och återexporteras med samma lösenord och sparas som ssl_new.pfx.  Den här proceduren ska användas när valideringen av servercertifikatet flaggade att en privat nyckel har inte den lokala datorn attributuppsättningen, certifikatkedjan har brutits, irrelevanta certifikat finns i PFX eller certifikatkedjan är i fel ordning.


**Exempel: Visa verifieringsrapport (stöd för distribution)**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

I det här exemplet distribution eller support-teamet får beredskapsrapporten från kund (Contoso) och Använd Start AzsReadinessChecker för att visa status för validering-körningar Contoso utförs.

**Exempel: Visa verifieringsrapport sammanfattning certifikat och verifiering av endast (distribution support)**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

I det här exemplet distribution eller support-teamet får beredskapsrapporten från kunden Contoso och Använd Start AzsReadinessChecker för att visa certifikat validering-körningar Contoso utförs sammanfattade statusen.



## <a name="required-parameters"></a>Obligatoriska parametrar
> -RegionName

Anger namnet på distributionen av Azure-stacken region.
|  |  |
|----------------------------|--------------|
|Ange:                       |Sträng        |
|Position:                   |Med namnet         |
|Standardvärde:              |Ingen          |
|Acceptera indata från pipeline:      |False         |
|Acceptera jokertecken: |False         |

> -FQDN    

Anger distributionen av Azure-stacken externa FQDN, även ett alias som ExternalFQDN och ExternalDomainName.
|  |  |
|----------------------------|--------------|
|Ange:                       |Sträng        |
|Position:                   |Med namnet         |
|Standardvärde:              |ExternalFQDN ExternalDomainName |
|Acceptera indata från pipeline:      |False         |
|Acceptera jokertecken: |False         |

 

> -IdentitySystem    

Anger respektive Azure Stack-distributionen identitetssystem giltiga värden, AAD eller AD FS, för Azure Active Directory och Active Directory Federation Services.
|  |  |
|----------------------------|--------------|
|Ange:                       |Sträng        |
|Position:                   |Med namnet         |
|Standardvärde:              |Ingen          |
|Giltiga värden:               |AAD, 'ADFS'  |
|Acceptera indata från pipeline:      |False         |
|Acceptera jokertecken: |False         |

> -PfxPassword    

Anger det lösenord som är associerade med certifikatet PFX-filer.
|  |  |
|----------------------------|---------|
|Ange:                       |SecureString |
|Position:                   |Med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -PaaSCertificates

Anger den hash-tabell som innehåller sökvägar och lösenord för PaaS-certifikat.
|  |  |
|----------------------------|---------|
|Ange:                       |Hash-tabell |
|Position:                   |Med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -DeploymentDataJSONPath

Anger Azure Stack distribution data JSON-konfigurationsfil. Den här filen har genererats för distribution.
|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |Med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -PfxPath

Anger sökvägen till ett problematiskt certifikat som kräver import/export-rutin att åtgärda, som anges av valideringen av servercertifikatet i det här verktyget.
|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |Med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -ExportPFXPath  

Anger sökvägen för den resulterande PFX-filen från import/export-rutin.  
|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |Med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -Ämne

Anger en ordnad uppslagslista av ämnet för certifikatet begäran generering.
|  |  |
|----------------------------|---------|
|Ange:                       |OrderedDictionary-objektet   |
|Position:                   |Med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -RequestType

Anger SAN-typ för certifikatbegäran. Giltiga värden MultipleCSR, SingleCSR.
- *MultipleCSR* genererar flera certifikatbegäranden, ett för varje tjänst.
- *SingleCSR* genererar en certifikatbegäran för alla tjänster.   

|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |Med namnet    |
|Standardvärde:              |Ingen     |
|Giltiga värden:               |MultipleCSR, 'SingleCSR' |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -OutputRequestPath

Anger sökväg för begäran certifikatfiler, katalogen måste finnas.
|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |Med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -AADServiceAdministrator

Anger tjänstadministratör för Azure Active Directory som ska användas för distribution av Azure-stacken.
|  |  |
|----------------------------|---------|
|Ange:                       |PSCredential   |
|Position:                   |Med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -AADDirectoryTenantName

Anger namnet för Azure Active Directory som ska användas för distribution av Azure-stacken.
|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |Med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -AzureEnvironment

Anger instansen av Azure-tjänster som innehåller konton, kataloger och prenumerationer som ska användas för Azure-stacken distribution och registrering.
|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |Med namnet    |
|Standardvärde:              |Ingen     |
|Giltiga värden:               |'AzureCloud', 'AzureChinaCloud', 'AzureGermanCloud' |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -RegistrationAccount

Anger registrering-konto som ska användas för registrering av Azure-stacken.
|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |Med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -RegistrationSubscriptionID

Anger registrering prenumerations-ID som ska användas för registrering av Azure-stacken.
|  |  |
|----------------------------|---------|
|Ange:                       |GUID     |
|Position:                   |Med namnet    |
|Standardvärde:              |Ingen     |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |

> -ReportPath

Anger sökvägen för beredskapsrapporten, aktuella directory och standard rapportnamn som standard.
|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |Med namnet    |
|Standardvärde:              |Alla      |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |



## <a name="optional-parameters"></a>Valfria parametrar
> -CertificatePath     

Anger sökvägen som endast certifikatet som krävs för certifikatet mappar finns.

Krävs är för Azure Stack-distribution med Azure Active Directory identitetssystem:

ACSBlob, ACSQueue, ACSTable, administrationsportalen, ARM Admin ARM offentliga KeyVault, KeyVaultInternal, offentlig Portal

Obligatorisk mapp för Azure-Stack-distribution med Active Directory Federation Services identitetssystem är:

ACSBlob, ACSQueue, ACSTable, AD FS, administrationsportalen, ARM Admin, ARM offentliga, diagram, KeyVault, KeyVaultInternal, offentlig Portal


|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |Med namnet    |
|Standardvärde:              |. \Certificates |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |


> -IncludePaaS  

Anger om PaaS services/värdnamn ska läggas till begäranden för certifikatet.


|  |  |
|----------------------------|------------------|
|Ange:                       |SwitchParameter   |
|Position:                   |Med namnet             |
|Standardvärde:              |False             |
|Acceptera indata från pipeline:      |False             |
|Acceptera jokertecken: |False             |


> -ReportSections        

Anger om du endast vill visa rapporten Sammanfattning utesluter detaljer.
|  |  |
|----------------------------|---------|
|Ange:                       |Sträng   |
|Position:                   |Med namnet    |
|Standardvärde:              |Alla      |
|Giltiga värden:               |'Certifikat', 'AzureRegistration', 'AzureIdentity', jobb, 'All' |
|Acceptera indata från pipeline:      |False    |
|Acceptera jokertecken: |False    |


> – Sammanfattning 

Anger om du endast vill visa rapporten Sammanfattning utesluter detaljer.
|  |  |
|----------------------------|------------------|
|Ange:                       |SwitchParameter   |
|Position:                   |Med namnet             |
|Standardvärde:              |False             |
|Acceptera indata från pipeline:      |False             |
|Acceptera jokertecken: |False             |


> -CleanReport  

Tar bort tidigare körningen eller valideringen historik och skriver verifieringar till en ny rapport.
|  |  |
|----------------------------|------------------|
|Ange:                       |SwitchParameter   |
|Alias:                    |CF                |
|Position:                   |Med namnet             |
|Standardvärde:              |False             |
|Acceptera indata från pipeline:      |False             |
|Acceptera jokertecken: |False             |


> -OutputPath    

Anger anpassad sökväg för att spara rapporten beredskap JSON och detaljerad loggfil.  Om sökvägen inte redan finns, försöker verktyget att skapa katalogen.
|  |  |
|----------------------------|------------------|
|Ange:                       |Sträng            |
|Position:                   |Med namnet             |
|Standardvärde:              |$ENV: TEMP\AzsReadinessChecker  |
|Acceptera indata från pipeline:      |False             |
|Acceptera jokertecken: |False             |


> -Bekräfta  

Anvisningarna för att bekräfta innan du kör cmdleten.
|  |  |
|----------------------------|------------------|
|Ange:                       |SwitchParameter   |
|Alias:                    |CF                |
|Position:                   |Med namnet             |
|Standardvärde:              |False             |
|Acceptera indata från pipeline:      |False             |
|Acceptera jokertecken: |False             |


> -WhatIf  

Visar vad som skulle hända om cmdleten kördes. Cmdleten körs inte.
|  |  |
|----------------------------|------------------|
|Ange:                       |SwitchParameter   |
|Alias:                    |Wi                |
|Position:                   |Med namnet             |
|Standardvärde:              |False             |
|Acceptera indata från pipeline:      |False             |
|Acceptera jokertecken: |False             |

 
