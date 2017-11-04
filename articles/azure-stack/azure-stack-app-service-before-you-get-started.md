---
title: "Innan du distribuerar Apptjänst Azure stacken | Microsoft Docs"
description: "Steg för att slutföra innan du distribuerar Apptjänst Azure-stacken"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: anwestg
ms.openlocfilehash: f2e7b5b96b70333ae4ee92d24c354960008c7f00
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Innan du börjar med App Service på Azure-stacken

Azure Apptjänst Azure stacken har ett antal förutvärdering steg som måste utföras före distributionen:

- Hämta Azure App Service på Azure-stacken Helper skript
- Hög tillgänglighet
- Certifikat som krävs för Azure App Service på Azure-stacken
- Förbered filserver
- Förbereda SQLServer
- Skapa ett Azure Active Directory-program
- Skapa ett Active Directory Federation Services-program

## <a name="download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts"></a>Hämta Azure App Service på Azure-stacken Installer och Helper-skript

1. Hämta den [Apptjänst på Azure-stacken distribution helper skript](https://aka.ms/appsvconmashelpers).
2. Hämta den [Apptjänst i Azure-stacken installer](https://aka.ms/appsvconmasinstaller).
3. Extrahera filerna från zip-filen helper skript. Följande filer och mappstrukturer visas:
  - Common.ps1
  - Skapa AADIdentityApp.ps1
  - Skapa ADFSIdentityApp.ps1
  - Skapa AppServiceCerts.ps1
  - Get-AzureStackRootCert.ps1
  - Ta bort AppService.ps1
  - Moduler
    - GraphAPI.psm1
    
## <a name="high-availability"></a>Hög tillgänglighet

Azure Apptjänst Azure-stacken är inte för närvarande kan erbjuda hög tillgänglighet eftersom Azure Stack endast distribuerar arbetsbelastningar till en enda Feldomän.

För att förbereda Azure App Service på Azure-stacken för hög tillgänglighet, måste du distribuera nödvändiga File Server och SQL Server i en konfiguration med hög tillgänglighet. Om Azure-stacken stöder flera feldomäner har ger vi vägledning om hur du aktiverar Azure App Service på Azure-stacken i en konfiguration med hög tillgänglighet.


## <a name="certificates-required-for-azure-app-service-on-azure-stack"></a>Certifikat som krävs för Azure App Service på Azure-stacken

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Certifikat som krävs för Azure-stacken Development Kit

Det här första skriptet fungerar med Azure-stacken certifikatutfärdare att skapa fyra certifikat som krävs av App Service.

| Filnamn | Användning |
| --- | --- |
| _.appservice.Local.azurestack.external.pfx | Apptjänst standard SSL-certifikat |
| API.appservice.Local.azurestack.external.pfx | Apptjänst API SSL-certifikat |
| FTP.appservice.Local.azurestack.external.pfx | Apptjänst Publisher SSL-certifikat |
| SSO.appservice.Local.azurestack.external.pfx | Apptjänst identitetscertifikat program |

Kör skriptet på Azure-stacken Development Kit värden och kontrollera att du kör PowerShell som azurestack\CloudAdmin.

1. Kör skriptet Skapa AppServiceCerts.ps1 från mappen där du extraherade helper-skript i ett PowerShell-session som körs som azurestack\CloudAdmin. Skriptet skapar fyra certifikat i samma mapp som skriptet Skapa certifikat som behöver Apptjänst.
2. Ange ett lösenord för att skydda PFX-filer och anteckna den. Du måste ange den i App Service i Azure-stacken installer.

#### <a name="create-appservicecertsps1-parameters"></a>Skapa AppServiceCerts.ps1 parametrar

| Parameter | Nödvändiga/valfria | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| PfxPassword | Krävs | Null | Lösenordet som används för att skydda den privata nyckeln för certifikatet |
| Domännamn | Krävs | Local.azurestack.external | Azure Stack region och domänens suffix |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Certifikat som krävs för en Produktionsdistribution av Azure App Service på Azure-stacken

För att fungera resursprovidern i produktion, måste du ange följande fyra certifikat:

#### <a name="default-domain-certificate"></a>Standardcertifikat

Standard Domäncertifikat är placerad på frontend-rollen. Den används av program för jokertecken eller standard domän-förfrågningar till Azure App Service. Certifikatet används också för källa kontrollåtgärder (KUDU).

Certifikatet måste vara i PFX-format och ska vara ett jokerteckencertifikat med två ämne. Detta tillåter både standarddomänen och scm-slutpunkt för källa kontroller som omfattas av ett certifikat.

| Format | Exempel |
| --- | --- |
| \*.appservice. \<region\>.\< DomainName\>.\< tillägg\> | \*. appservice.redmond.azurestack.external |
| \*. scm.appservice. <region>. <DomainName>.<extension> | \*. appservice.scm.redmond.azurestack.external |

#### <a name="api-certificate"></a>API-certifikat

API-certifikatet är placerad på rollen och används av resursprovidern för att skydda api-anrop. Certifikatet för publicering måste innehålla ett ämne som matchar API DNS-post:

| Format | Exempel |
| --- | --- |
| API.appservice. \<region\>.\< DomainName\>.\< tillägg\> | API.appservice.Redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Publicera certifikat

Certifikatet för rollen Publisher säkrar FTPS trafiken för programmet ägare när de överför innehåll.  Certifikatet för publicering måste innehålla ett ämne som matchar FTPS DNS-posten.

| Format | Exempel |
| --- | --- |
| FTP.appservice. \<region\>.\< DomainName\>.\< tillägg\> | API.appservice.Redmond.azurestack.external |

#### <a name="identity-certificate"></a>Identitetscertifikat

Certifikatet för programmets identitet kan:
- integration mellan AAD/ADFS-katalogen, Azure-stacken och Apptjänst till stöd för integrering med Compute-Resursprovidern
- Enkel inloggning scenarier för avancerad utvecklingsverktygen i Azure App Service på Azure-stacken.
Certifikat för identitet måste innehålla ett ämne som matchar följande format:

| Format | Exempel |
| --- | --- |
| SSO.appservice. \<region\>.\< DomainName\>.\< tillägg\> | SSO.appservice.Redmond.azurestack.external |

#### <a name="extract-the-azure-stack-azure-resource-manager-root-certificate"></a>Extrahera Azure Stack Azure Resource Manager-rotcertifikat

Kör skriptet för Get-AzureStackRootCert.ps1 från mappen där du extraherade helper-skript i ett PowerShell-session som körs som azurestack\CloudAdmin. Skriptet skapar fyra certifikat i samma mapp som skriptet Skapa certifikat som behöver Apptjänst.

| Parametern Get-AzureStackRootCert.ps1 | Nödvändiga/valfria | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| PrivelegedEndpoint | Krävs | AzS ERCS01 | Privilegierade slutpunkt. |
| CloudAdminCredential | Krävs | AzureStack\CloudAdmin | Azure-stacken molnet Admin konto domänautentiseringsuppgifterna |


## <a name="prepare-the-file-server"></a>Förbereda filservern

Azure Apptjänst kräver användning av en filserver. För Produktionsdistribution, måste filservern vara konfigurerad ska ha hög tillgänglighet och kan hantera fel.

För användning med Azure-stacken Development Kit-distributioner du använder det här exemplet Distributionsmall för Azure Resource Manager för att distribuera en filserver konfigurerad nod: https://aka.ms/appsvconmasdkfstemplate.

### <a name="provision-groups-and-accounts-in-active-directory"></a>Etablera grupper och konton i Active Directory

>[!NOTE]
> Köra alla följande kommandon när du konfigurerar filservern i en administratörskommandotolk session.  **Använd inte PowerShell.**

1. Skapa följande Active Directory globala säkerhetsgrupper:
    - FileShareOwners
    - FileShareUsers
2. Skapa följande Active Directory-konton som tjänstkonton:
    - FileShareOwner
    - FileShareUser

    Som bör bästa praxis bör användare för dessa konton (och för alla roller för Web) skiljer sig från varandra och ha starkt användarnamn och lösenord.
    Ange lösenord med följande villkor:
     - Aktivera **lösenordet upphör aldrig att gälla**.
     - Aktivera **användaren kan inte ändra lösenordet**.
     - Inaktivera **användaren måste byta lösenord vid nästa inloggning**.
3. Lägg till kontona gruppmedlemskap enligt följande:
    - Lägg till **FileShareOwner** till den **FileShareOwners** grupp.
    - Lägg till **FileShareUser** till den **FileShareUsers** grupp.

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Etablera grupper och konton i en arbetsgrupp

På en arbetsgrupp, kör net och WMIC-kommandon för att etablera grupper och konton.

1. Kör följande kommandon för att skapa FileShareOwner och FileShareUser konton. Ersätt <password> med egna värden.
``` DOS
net user FileShareOwner <password> /add /expires:never /passwordchg:no
net user FileShareUser <password> /add /expires:never /passwordchg:no
```
2. Ange lösenorden för kontona aldrig upphör att gälla genom att köra följande WMIC-kommandon:
``` DOS
WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
```
3. Skapa de lokala grupperna FileShareUsers och FileShareOwners och lägga till konton i det första steget i dem.
``` DOS
net localgroup FileShareUsers /add
net localgroup FileShareUsers FileShareUser /add
net localgroup FileShareOwners /add
net localgroup FileShareOwners FileShareOwner /add
```

### <a name="provision-the-content-share"></a>Etablera innehållsdelen

Innehållsdelen innehåller innehållet på klienten. Proceduren för att etablera innehållsdelen på en enskild server är samma för både Active Directory och arbetsgrupp miljöer, men olika för Failover-kluster i Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-ad-or-workgroup"></a>Etablera innehållsdelen på en enskild server (AD eller arbetsgrupp)

Kör följande kommandon i en upphöjd kommandotolk på en enda filserver. Ersätt värdet för < C:\WebSites > med motsvarande sökvägar i din miljö.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=<C:\WebSites>
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="to-enable-winrm-add-the-fileshareowners-group-to-the-local-administrators-group"></a>För att aktivera WinRM, lägger du till gruppen FileShareOwners till den lokala gruppen Administratörer

För Windows Remote Management ska fungera korrekt måste du lägga till gruppen FileShareOwners den lokala gruppen Administratörer.

#### <a name="active-directory"></a>Active Directory

Kör följande kommandon i en upphöjd kommandotolk på filservern eller på varje nod i filserverns redundanskluster. Ersätt värdet för <DOMAIN> med domännamn som du vill använda.

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>Arbetsgrupp

Kör följande kommando vid en upphöjd kommandotolk på filservern.

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>Konfigurera åtkomstkontroll till resurser

Kör följande kommandon i en upphöjd kommandotolk på filservern eller på noden filserverns redundanskluster som är aktuella resursägaren för klustret. Ersätt värdena i kursiv stil med värden som är specifika för din miljö.

#### <a name="active-directory"></a>Active Directory
```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=<C:\WebSites>
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Arbetsgrupp
```DOS
set WEBSITES_FOLDER=<C:\WebSites>
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server"></a>Förbereda SQLServer

För Azure App Service på Azure-stacken värd och avläsning databaser, måste du förbereda en SQL Server för Azure App Service-databaser.

För användning med Azure-stacken Development Kit kan du använda SQL Express 2014 SP2 eller senare.

För produktion och hög tillgänglighet, du bör använda en fullständig version av SQL 2014 SP2 eller senare kan aktivera verifiering i blandat läge och distribuera i en [konfiguration med hög tillgänglighet](https://docs.microsoft.com/en-us/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

Azure App Service på Azure-stacken SQL Server måste vara tillgänglig från alla roller för App Service. SQL Server kan distribueras inom standard providern prenumeration i Azure-stacken. Eller så kan du använda den befintliga infrastrukturen i din organisation (så länge det finns en anslutning till Azure-stacken).

Du kan använda en standardinstans eller namngiven instans för SQL Server-roller. Men om du använder en namngiven instans, måste du manuellt starta tjänsten SQL Browser och öppna port 1434.

## <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory-program

Konfigurera en Azure AD-tjänstens huvudnamn för att stödja följande:
- Virtuella skaluppsättning för integrering på Worker nivåerna.
- Enkel inloggning för Azure Functions portalen och avancerad utvecklingsverktygen.

De här stegen gäller för Azure AD som skyddas av Azure Stack-miljöer.

Administratörer måste konfigurera enkel inloggning till:
- Aktivera Avancerad utvecklingsverktygen i App Service (Kudu).
- Aktivera användning av Azure Functions-portaler.

Följ de här stegen:

1. Öppna ett PowerShell-instans som azurestack\cloudadmin.
2. Gå till platsen för skript har hämtat och extraherat i den [nödvändiga steg](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Installera Azure-stacken PowerShell](azure-stack-powershell-install.md).
4. Kör den **skapa AADIdentityApp.ps1** skript. Ange Azure AD-klient-ID som du använder för din Azure Stack-distribution, till exempel myazurestack.onmicrosoft.com när du uppmanas att ange ditt Azure AD-klient-ID.
5. I den **autentiseringsuppgifter** fönster, ange ditt Azure AD-tjänsten-administratörskonto och lösenord. Klicka på **OK**.
6. Ange sökväg för certifikatet och lösenordet för den [certifikat som skapades tidigare](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Certifikatet skapas för det här steget som standard är sso.appservice.local.azurestack.external.pfx.
7. Skriptet skapar ett nytt program i Azure AD-klient. Anteckna det program-ID som returneras i PowerShell-utdata. Du behöver den här informationen under installationen.
8. Öppna ett nytt fönster i webbläsaren och logga in på Azure-portalen (portal.azure.com) som den **Azure Active Directory-tjänstadministratör**.
9. Öppna Azure AD-resursprovidern.
10. Klicka på **App registreringar**.
11. Sök efter den **program-ID** returneras som en del i steg 7. Ett program med App Service anges.
12. Klicka på **programmet** i listan
13. Klicka på **nödvändiga behörigheter** > **bevilja behörigheter** > **Ja**.

| Skapa AADIdentityApp.ps1 parameter | Nödvändiga/valfria | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| DirectoryTenantName | Krävs | Null | Azure AD-klient-ID. Ange GUID eller sträng, till exempel myazureaaddirectory.onmicrosoft.com |
| AdminArmEndpoint | Krävs | Null | Slutpunkten Admin Azure Resource Manager, till exempel adminmanagement.local.azurestack.external |
| TenantARMEndpoint | Krävs | Null | Klient Azure Resource Manager-slutpunkt, till exempel: management.local.azurestack.external |
| AzureStackAdminCredential | Krävs | Null | Azure AD Service Admin-autentiseringsuppgifter |
| CertificateFilePath | Krävs | Null | Sökvägen till filen identitet programmet certifikatet skapats tidigare. |
| CertificatePassword | Krävs | Null | Lösenordet som används för att skydda den privata nyckeln för certifikatet. |

## <a name="create-an-active-directory-federation-services-application"></a>Skapa ett Active Directory Federation Services-program

För miljöer med Azure-stacken skyddas av AD FS måste du konfigurera en AD FS-tjänstens huvudnamn för att stödja följande:
- Virtuella skaluppsättning för integrering på Worker nivåerna.
- Enkel inloggning för Azure Functions portalen och avancerad utvecklingsverktygen.

Administratörer måste du konfigurera enkel inloggning till:
- Konfigurera ett huvudnamn för tjänsten för virtuell dator scale set integrering på Worker nivåer.
- Aktivera Avancerad utvecklingsverktygen i App Service (Kudu).
- Aktivera användning av Azure Functions-portaler.

Följ de här stegen:

1. Öppna ett PowerShell-instans som azurestack\azurestackadmin.
2. Gå till platsen för skript har hämtat och extraherat i den [nödvändiga steg](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Installera Azure-stacken PowerShell](azure-stack-powershell-install.md).
4.  Kör den **skapa ADFSIdentityApp.ps1** skript.
5.  I den **autentiseringsuppgifter** fönster, ange ditt AD FS molnet administratörskonto och lösenord. Klicka på **OK**.
6.  Ange sökväg för certifikatet och lösenordet för den [certifikat som skapades tidigare](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Certifikatet skapas för det här steget som standard är sso.appservice.local.azurestack.external.pfx.

| Skapa ADFSIdentityApp.ps1 parameter | Nödvändiga/valfria | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| AdminArmEndpoint | Krävs | Null | Azure Resource Manager admin-slutpunkten. Till exempel adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Krävs | Null | Privilegierade slutpunkt. Till exempel AzS ERCS01. |
| CloudAdminCredential | Krävs | Null | Azure Stack cloudadmin domän autentiseringsuppgifter för tjänstekontot. Till exempel Azurestack\CloudAdmin. |
| CertificateFilePath | Krävs | Null | Sökvägen till identitet programmet certifikatets PFX-fil. |
| CertificatePassword | Krävs | Null | Lösenordet som används för att skydda den privata nyckeln för certifikatet. |


## <a name="next-steps"></a>Nästa steg

[Installera App-tjänstresursprovider](azure-stack-app-service-deploy.md).
