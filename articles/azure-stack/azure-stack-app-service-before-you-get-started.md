---
title: Innan du distribuerar App Service i Azure Stack | Microsoft Docs
description: Steg för att slutföra innan du distribuerar App Service i Azure Stack
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: anwestg
ms.openlocfilehash: add4a7f1ce8133b5c3891f731fc98ee7fdb26ebd
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275677"
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Innan du sätter igång med App Service i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Innan du distribuerar Azure App Service i Azure Stack, måste du slutföra de nödvändiga stegen i den här artikeln.

> [!IMPORTANT]
> Uppdateringen är 1809 integrerade Azure Stack-system eller distribuera den senaste Azure Stack Development Kit (ASDK) innan du distribuerar Azure App Service 1.4.

## <a name="download-the-installer-and-helper-scripts"></a>Ladda ned installationsprogrammet och helper-skript

1. Ladda ned den [App Service i Azure Stack-distributionsskript helper](https://aka.ms/appsvconmashelpers).
2. Ladda ned den [App Service på Azure Stack-installationsprogrammet](https://aka.ms/appsvconmasinstaller).
3. Extrahera filerna från ZIP-filen helper-skript. Följande filer och mappar har extraherats:

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - Modulmappen
     - GraphAPI.psm1

## <a name="syndicate-the-custom-script-extension-from-the-marketplace"></a>Syndikera tillägget för anpassat skript från Marketplace

Azure App Service i Azure Stack kräver v1.9.0 för tillägget för anpassat skript.  Tillägget måste vara [syndikeras från Marketplace](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) före distribution eller uppgradering av Azure App Service på Azure Stack

## <a name="high-availability"></a>Hög tillgänglighet

Azure Stack 1802 uppdateringen lagt till stöd för feldomäner. Nya Azure App Service i Azure Stack-distributioner kommer att distribueras över feldomäner och ge feltolerans.

Befintliga distributioner av Azure App Service i Azure Stack, som distribuerats före uppdateringen 1802, finns det [balansera om en App Service-resursprovider över feldomäner](azure-stack-app-service-fault-domain-update.md) artikeln.

Dessutom distribuera nödvändiga fil- och SQL Server-instanser i en konfiguration med hög tillgänglighet.

## <a name="get-certificates"></a>Hämta certifikat

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Azure Resource Manager-rotcertifikatet för Azure Stack

Öppna en upphöjd PowerShell-session på en dator som kan nå Privilegierade slutpunkten på den integrerade Azure Stack-System eller Azure Stack Development Kit värden.

Kör den *Get-AzureStackRootCert.ps1* skriptet från den mapp där du extraherade helper-skript. Skriptet skapar ett rotcertifikat i samma mapp som det skript som App Service behöver för att skapa certifikat.

När du kör följande PowerShell-kommando, måste du ange privilegierad slutpunkt och autentiseringsuppgifterna för AzureStack\CloudAdmin.

```PowerShell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Get-AzureStackRootCert.ps1 Skriptparametrar

| Parameter | Obligatorisk eller valfri | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| PrivilegedEndpoint | Krävs | AzS-ERCS01 | Privilegierad slutpunkt |
| CloudAdminCredential | Krävs | AzureStack\CloudAdmin | Inloggningsuppgift för domänkontot för administratörer för Azure Stack-molnet |

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>Certifikat som krävs för ASDK distribution av Azure App Service

Den *skapa AppServiceCerts.ps1* skriptet fungerar med Azure Stack certifikatutfärdaren att skapa fyra certifikat som krävs för App Service.

| Filnamn | Användning |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | SSL-standardcertifikat för App Service |
| api.appservice.local.azurestack.external.pfx | App Service API SSL-certifikat |
| ftp.appservice.local.azurestack.external.pfx | SSL-certifikat för App Service-utgivare |
| sso.appservice.local.azurestack.external.pfx | App Service-identitetsprogramcertifikat |

Följ dessa steg för att skapa certifikat:

1. Logga in på Azure Stack Development Kit värden med hjälp av AzureStack\AzureStackAdmin-kontot.
2. Öppna en upphöjd PowerShell-session.
3. Kör den *skapa AppServiceCerts.ps1* skriptet från den mapp där du extraherade helper-skript. Det här skriptet skapar fyra certifikat i samma mapp som det skript som App Service behöver för att skapa certifikat.
4. Ange ett lösenord för att skydda PFX-filer och anteckna den. Du måste ange den i App Service på Azure Stack-installationsprogrammet.

#### <a name="create-appservicecertsps1-script-parameters"></a>Skapa AppServiceCerts.ps1 Skriptparametrar

| Parameter | Obligatorisk eller valfri | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| pfxPassword | Krävs | Null | Lösenordet som hjälper dig skydda den privata nyckeln för certifikatet |
| Domännamn | Krävs | local.azurestack.external | Azure Stack-region och domän suffix |

### <a name="certificates-required-for-azure-stack-production-deployment-of-azure-app-service"></a>Certifikat som krävs för Azure Stack Produktionsdistribution av Azure App Service

Om du vill köra resursprovidern i produktion, måste du ange följande certifikat:

- Standardcertifikat
- API-certifikat
- Publicera certifikat
- Identitetscertifikat

#### <a name="default-domain-certificate"></a>Standardcertifikat

Domän standardcertifikatet placeras på frontend-roll. Användarprogram för jokertecken eller standard domän-begäran till Azure App Service använder det här certifikatet. Certifikatet används också för källa kontrollåtgärder (Kudu).

Certifikatet måste vara i PFX-format och ska vara ett jokerteckencertifikat med tre ämne. Detta krav kan ett certifikat att täcka både standarddomänen och SCM-slutpunkten för åtgärder för kontroll av källa.

| Format | Exempel |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.scm.appservice.redmond.azurestack.external |
| \*.sso.appservice.<region>.<DomainName>.<extension> | \*.sso.appservice.redmond.azurestack.external |

#### <a name="api-certificate"></a>API-certifikat

API-certifikat är placerad på Management-roll. Resursprovidern använder den för att säkra API-anrop. Certifikatet för publicering måste innehålla ett ämne som matchar API DNS-posten.

| Format | Exempel |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Publicera certifikat

Certifikatet för rollen Publisher säkrar FTPS-trafik för programägare när de överför innehåll. Certifikatet för publicering måste innehålla ett ämne som matchar FTPS DNS-posten.

| Format | Exempel |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Identitetscertifikat

Certifikat för identitet-programmet kan:

- Integrering mellan Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS) directory, Azure Stack, och App Service för integrering med compute-resursprovidern.
- Enkel inloggning för avancerade developer tools i Azure App Service i Azure Stack.

Certifikatet för identitet måste innehålla ett ämne som matchar följande format.

| Format | Exempel |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |


### <a name="validate-certificates"></a>Verifiera certifikat
Innan du distribuerar app service-resursprovider, bör du [certifikatsverifiering som ska användas](azure-stack-validate-pki-certs.md#perform-platform-as-a-service-certificate-validation) med hjälp av verktyget Azure Stack-beredskap för installation som är tillgängliga från den [PowerShell-galleriet](https://aka.ms/AzsReadinessChecker). Azure Stack-beredskap för installation verktyget verifierar att de genererade PKI-certifikat är lämplig för distribution av app services. 

Som bästa praxis när du arbetar med någon av nödvändiga [Azure Stack PKI-certifikat](azure-stack-pki-certs.md), du bör planera att lämna tillräckligt med tid för att testa och återutfärda certifikat om det behövs. 

## <a name="virtual-network"></a>Virtuellt nätverk

> [!NOTE]
> Före skapandet av ett anpassat virtuellt nätverk är valfritt eftersom Azure App Service i Azure Stack kan skapa virtuella nätverket men måste sedan kommunicera med SQL och filservern via offentliga IP-adresser.

Azure App Service i Azure Stack kan du distribuera resursprovidern till ett befintligt virtuellt nätverk eller kan du skapa ett virtuellt nätverk som en del av distributionen. Med hjälp av ett befintligt virtuellt nätverk kan du använda interna IP-adresser att ansluta till fil- och SQLServer krävs av Azure App Service i Azure Stack. Det virtuella nätverket måste konfigureras med följande adressintervall och undernät innan du installerar Azure App Service på Azure Stack:

Virtuellt nätverk – /16

Undernät

- ControllersSubnet /24
- ManagementServersSubnet /24
- FrontEndsSubnet /24
- PublishersSubnet /24
- WorkersSubnet /21

## <a name="prepare-the-file-server"></a>Förbered servern

Azure App Service måste du använda för en filserver. Vid Produktionsdistribution måste konfigureras servern för att vara tillgängliga och kan hantera fel.

### <a name="quickstart-template-for-file-server-for-deployments-of-azure-app-service-on-asdk"></a>Snabbstartsmall för filserver för distributioner av Azure App Service i ASDK.

Du kan använda för Azure Stack Development Kit-distributioner, den [exempelmall för Azure Resource Manager-distribution](https://aka.ms/appsvconmasdkfstemplate) att distribuera en konfigurerade enkelnods-filserver. Enkelnods-filserver ska vara i en arbetsgrupp.

### <a name="quickstart-template-for-highly-available-file-server-and-sql-server"></a>Snabbstartsmall för Highly Available File Server och SQL Server

En [snabbstartsmall för referens arkitektur](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/appservice-fileserver-sqlserver-ha) finns nu tillgänglig, som distribuerar filservern, SQL Server, stöd för Active Directory-infrastruktur i ett virtuellt nätverk som konfigurerats för att stödja en högtillgänglig distribution av Azure App Service i Azure Stack.  

### <a name="steps-to-deploy-a-custom-file-server"></a>Steg för att distribuera en anpassad filserver

>[!IMPORTANT]
> Om du väljer att distribuera App Service i ett befintligt virtuellt nätverk måste ska servern distribueras i ett separat undernät från App Service.

#### <a name="provision-groups-and-accounts-in-active-directory"></a>Etablera grupper och konton i Active Directory

1. Skapa i följande globala Active Directory-säkerhetsgrupper:

   - FileShareOwners
   - FileShareUsers

2. Skapa följande Active Directory-konton som tjänstkonton:

   - FileShareOwner
   - FileShareUser

   Som säkerhetsregel bör bör användare för dessa konton (och för alla web-roller) vara unikt och har stark användarnamn och lösenord. Ange lösenord med följande villkor:

   - Aktivera **lösenordet upphör aldrig att gälla**.
   - Aktivera **användaren kan inte ändra lösenordet**.
   - Inaktivera **användaren måste byta lösenord vid nästa inloggning**.

3. Lägg till kontona i gruppmedlemskap på följande sätt:

   - Lägg till **FileShareOwner** till den **FileShareOwners** grupp.
   - Lägg till **FileShareUser** till den **FileShareUsers** grupp.

#### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Etablera grupper och konton i en arbetsgrupp

>[!NOTE]
> När du konfigurerar en server, kör du följande kommandon från ett **administratörskommandotolk**. <br>***Använd PowerShell inte.***

När du använder Azure Resource Manager-mall kan användare redan har skapats.

1. Kör följande kommandon för att skapa FileShareOwner och FileShareUser-konton. Ersätt `<password>` med dina egna värden.

   ``` DOS
   net user FileShareOwner <password> /add /expires:never /passwordchg:no
   net user FileShareUser <password> /add /expires:never /passwordchg:no
   ```

2. Ange lösenorden för kontona aldrig upphör att gälla genom att köra följande WMIC-kommandon:

   ``` DOS
   WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
   WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
   ```

3. Skapa de lokala grupperna FileShareUsers och FileShareOwners och lägga till konton i det första steget i dem:

   ``` DOS
   net localgroup FileShareUsers /add
   net localgroup FileShareUsers FileShareUser /add
   net localgroup FileShareOwners /add
   net localgroup FileShareOwners FileShareOwner /add
   ```

#### <a name="provision-the-content-share"></a>Etablera innehållsresursen

Innehållsresursen innehåller klient webbplatsens innehåll. Metoden för att etablera innehållsresursen på en enda server är samma för både Active Directory-och arbetsgruppsmiljöer. Men det är olika för ett redundanskluster i Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>Etablera innehållsresursen på en enda filserver (Active Directory eller arbetsgrupp)

Kör följande kommandon i en upphöjd kommandotolk på en enda filserver. Ersätt värdet för `C:\WebSites` med motsvarande sökvägar i din miljö.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="configure-access-control-to-the-shares"></a>Konfigurera åtkomstkontroll till resurser

Kör följande kommandon i en upphöjd kommandotolk på servern eller på redundansklusternoden som är aktuella resursägaren för klustret. Ersätt värdena i kursiv stil med värden som är specifika för din miljö.

#### <a name="active-directory"></a>Active Directory

```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Arbetsgrupp

```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server-instance"></a>Förbereda SQL Server-instansen

För Azure App Service på som är värd för Azure Stack och Avläsning av databaserna, måste du förbereda en SQL Server-instans för App Service-databaser.

För distributioner av Azure Stack Development Kit, kan du använda SQL Server Express 2014 SP2 eller senare.

För produktion och upprätthålla hög tillgänglighet, du bör använda en fullständig version av SQL Server 2014 SP2 eller senare kan aktivera blandat läge-autentisering, och distribuera i en [konfiguration med hög tillgänglighet](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

SQL Server-instans för Azure App Service i Azure Stack måste kunna nås från alla App Service-roller. Du kan distribuera SQL Server inom Providerprenumeration standard i Azure Stack. Eller så kan du använda den befintliga infrastrukturen i din organisation (så länge det finns en anslutning till Azure Stack). Om du använder en Azure Marketplace-avbildning, Kom ihåg att du konfigurerar brandväggen på lämpligt sätt.

>[!NOTE]
> Ett antal SQL IaaS avbildningar av virtuella datorer är tillgängliga via Marketplace-hanteringsfunktionen. Se till att du alltid hämta den senaste versionen av SQL IaaS-tillägget innan du distribuerar en virtuell dator med en Marketplace-objekt. SQL-avbildningar är samma som den virtuella SQL-datorer som är tillgängliga i Azure. För virtuella SQL-datorer skapas från dessa avbildningar, IaaS-tillägg och förbättringar av motsvarande portalen tillhandahåller funktioner som automatisk uppdatering och säkerhetskopiering.
>
Du kan använda en standardinstans eller namngiven instans för SQL Server-roller. Om du använder en namngiven instans, måste du manuellt starta SQL Server Browser-tjänsten och öppna porten 1434.

>[!IMPORTANT]
> Om du väljer att distribuera App Service i ett befintligt virtuellt nätverk som SQL Server ska distribueras i ett separat undernät från App Service och filservern.
>

## <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory-program

Konfigurera en Azure AD-tjänstens huvudnamn för att stödja följande åtgärder:

- VM-skalningsuppsättningen integrering på arbetarnivåer.
- Enkel inloggning för Azure Functions portalen och avancerade developer tools.

De här stegen gäller för Azure AD-säkrad Azure Stack-miljöer.

Administratörer måste konfigurera SSO till:

- Aktivera avancerade utvecklarverktyg i App Service (Kudu).
- Aktivera användning av Azure Functions-portalen.

Följ de här stegen:

1. Öppna en PowerShell-instans som azurestack\AzureStackAdmin.
2. Gå till platsen där de skript som du har hämtat och extraherat i den [nödvändiga steg](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md).
4. Kör den **skapa AADIdentityApp.ps1** skript. När du uppmanas, anger du Azure AD-klient-ID som du använder för din Azure Stack-distribution. Ange till exempel **myazurestack.onmicrosoft.com**.
5. I den **Credential** fönstret anger du Azure AD-administratör tjänstkonto och lösenord. Välj **OK**.
6. Ange filsökväg till certifikat och lösenord för certifikatet för den [certifikatet som skapades tidigare](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Certifikatet som skapades för det här steget som standard är **sso.appservice.local.azurestack.external.pfx**.
7. Skriptet skapar ett nytt program i Azure AD-instans klient. Anteckna program-ID som returneras i PowerShell-utdata. Du behöver den här informationen under installationen.
8. Öppna ett nytt webbläsarfönster och logga in på den [Azure-portalen](https://portal.azure.com) som Azure Active Directory-tjänsten.
9. Öppna Azure AD-resursprovidern.
10. Välj **Appregistreringar**.
11. Sök efter program-ID som returneras som en del av steg 7. En App Service-programmet visas.
12. Välj **programmet** i listan.
13. Välj **inställningar**.
14. Välj **nödvändiga behörigheter** > **bevilja** > **Ja**.

```PowerShell
    Create-AADIdentityApp.ps1
```

| Parameter | Obligatorisk eller valfri | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| DirectoryTenantName | Krävs | Null | Azure AD-klient-ID. Ange GUID eller sträng. Ett exempel är myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Krävs | Null | Administratören Azure Resource Manager-slutpunkten. Ett exempel är adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Krävs | Null | Azure Resource Manager-klientorganisationsslutpunkt. Ett exempel är management.local.azurestack.external. |
| AzureStackAdminCredential | Krävs | Null | Azure AD-tjänstens administrativa autentiseringsuppgifter. |
| CertificateFilePath | Krävs | Null | **Fullständig sökväg** till den certifikatfil för identitetsprogram genereras tidigare. |
| CertificatePassword | Krävs | Null | Lösenordet som hjälper dig skydda certifikatets privata nyckel. |
| Miljö | Valfri | AzureCloud | Namnet på den miljö som stöds av molnet där målet Azure Active Directory Graph-tjänsten är tillgänglig.  Tillåtna värden: 'AzureCloud', 'AzureChinaCloud', 'Azureusgovernment eller', 'AzureGermanCloud'.|

## <a name="create-an-active-directory-federation-services-application"></a>Skapa ett Active Directory Federation Services-program

För Azure Stack-miljöer som skyddas av AD FS, måste du konfigurera en AD FS-tjänstens huvudnamn för att stödja följande åtgärder:

- VM-skalningsuppsättningen integrering på arbetarnivåer.
- Enkel inloggning för Azure Functions portalen och avancerade developer tools.

Administratörer måste konfigurera SSO till:

- Konfigurera ett huvudnamn för tjänsten för VM scale set integrering på arbetarnivåer.
- Aktivera avancerade utvecklarverktyg i App Service (Kudu).
- Aktivera användning av Azure Functions-portalen.

Följ de här stegen:

1. Öppna en PowerShell-instans som azurestack\AzureStackAdmin.
2. Gå till platsen där de skript som du har hämtat och extraherat i den [nödvändiga steg](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md).
4. Kör den **skapa ADFSIdentityApp.ps1** skript.
5. I den **Credential** fönstret anger du AD FS molnet administratörskonto och lösenord. Välj **OK**.
6. Ange filsökväg till certifikat och lösenord för certifikatet för den [certifikatet som skapades tidigare](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Certifikatet som skapades för det här steget som standard är **sso.appservice.local.azurestack.external.pfx**.

```PowerShell
    Create-ADFSIdentityApp.ps1
```

| Parameter | Obligatorisk eller valfri | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| AdminArmEndpoint | Krävs | Null | Administratören Azure Resource Manager-slutpunkten. Ett exempel är adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Krävs | Null | Privilegierad slutpunkt. Ett exempel är AzS-ERCS01. |
| CloudAdminCredential | Krävs | Null | Inloggningsuppgift för domänkontot för administratörer för Azure Stack-molnet. Ett exempel är Azurestack\CloudAdmin. |
| CertificateFilePath | Krävs | Null | **Fullständig sökväg** till programmet identitet certifikatets PFX-fil. |
| CertificatePassword | Krävs | Null | Lösenordet som hjälper dig skydda certifikatets privata nyckel. |

## <a name="next-steps"></a>Nästa steg

[Installera App Service-resursprovider](azure-stack-app-service-deploy.md)
