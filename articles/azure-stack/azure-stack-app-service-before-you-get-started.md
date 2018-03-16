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
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 5323fe505adfd9b3495dd85ce41d6f141125184b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Innan du börjar med App Service på Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

> [!IMPORTANT]
> Uppdateringen är 1802 Azure Stack integrerade systemet eller distribuera den senaste Azure Stack development kit innan du distribuerar Azure App Service.
>
>

Innan du distribuerar Azure App Service på Azure-stacken måste du slutföra krav i den här artikeln.

## <a name="download-the-installer-and-helper-scripts"></a>Ladda ned installationsprogrammet och helper-skript

1. Hämta den [Apptjänst på Azure-stacken distribution helper skript](https://aka.ms/appsvconmashelpers).
2. Hämta den [Apptjänst i Azure-stacken installer](https://aka.ms/appsvconmasinstaller).
3. Extrahera filerna från ZIP-filen helper skript. Följande filer och mappstrukturer visas:
   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - Moduler
     - GraphAPI.psm1

## <a name="high-availability"></a>Hög tillgänglighet

På grund av 1802-versionen av Azure-stacken, vilket lägger till stöd för feldomäner nya distributioner av Azure App Service på Azure-stacken ska distribueras över feldomäner och ge feltolerans.  För befintliga distributioner av Azure App Service på Azure-stacken som har distribuerats innan lanseringen av 1802 uppdatera, se den [dokumentationen](azure-stack-app-service-fault-domain-update.md) att balansera om distributionen.

Dessutom distribuera Azure App Service på Azure-stacken för hög tillgänglighet krävs file server och SQL Server-instans i en konfiguration med hög tillgänglighet. 

## <a name="get-certificates"></a>Hämta certifikat

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Azure Resource Manager-rotcertifikatet för Azure-stacken

I en PowerShell-session som körs som azurestack\CloudAdmin på en dator som kan nå Privilegierade slutpunkten på Azure-stacken integrerat System eller Azure Stack Development Kit värddatorn kör skriptet Get-AzureStackRootCert.ps1 från mappen där du extraherade helper-skript. Skriptet skapar ett rotcertifikat i samma mapp som skriptet som Apptjänst behöver för att skapa certifikat.

| Get-AzureStackRootCert.ps1 parameter | Obligatorisk eller valfri | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| PrivilegedEndpoint | Krävs | AzS-ERCS01 | Privilegierade slutpunkt |
| CloudAdminCredential | Krävs | AzureStack\CloudAdmin | Domän-autentiseringsuppgift konto för Azure-stacken molnet administratörer |

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Certifikat som krävs för Azure-stacken Development Kit

Första skriptet fungerar med Azure Stack certifikatutfärdare att skapa fyra certifikat som behöver Apptjänst:

| Filnamn | Användning |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | Apptjänst standard SSL-certifikat |
| api.appservice.local.azurestack.external.pfx | App Service API SSL-certifikat |
| ftp.appservice.local.azurestack.external.pfx | Apptjänst publisher SSL-certifikat |
| sso.appservice.local.azurestack.external.pfx | Apptjänst identitetscertifikat program |

Kör skriptet på Azure-stacken Development Kit värden och kontrollera att du kör PowerShell som azurestack\CloudAdmin:

1. Kör skriptet Skapa AppServiceCerts.ps1 från mappen där du extraherade helper-skript i ett PowerShell-session som körs som azurestack\AzureStackAdmin. Skriptet skapar fyra certifikat i samma mapp som skriptet som Apptjänst behöver för att skapa certifikat.
2. Ange ett lösenord för att skydda PFX-filer och anteckna den. Du måste ange den i App Service i Azure-stacken installer.

#### <a name="create-appservicecertsps1-parameters"></a>Create-AppServiceCerts.ps1 parameters

| Parameter | Obligatorisk eller valfri | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| PfxPassword | Krävs | Null | Lösenordet som hjälper dig att skydda den privata nyckeln för certifikatet |
| DomainName | Krävs | local.azurestack.external | Azure Stack region och domänens suffix |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Certifikat som krävs för en Produktionsdistribution av Azure App Service på Azure-stacken

För att fungera resursprovidern i produktion, måste du ange följande fyra certifikat:

#### <a name="default-domain-certificate"></a>Standardcertifikat

Standard Domäncertifikat är placerad på frontend-rollen. Användarprogram för begäranden med jokertecken eller standard domän till Azure App Service använda det här certifikatet. Certifikatet används också för källa kontrollåtgärder (Kudu).

Certifikatet måste vara i PFX-format och ska vara ett jokerteckencertifikat med tre ämne. Detta gör att ett certifikat att täcka både standarddomänen och SCM-slutpunkten för kontroller som källa.

| Format | Exempel |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.scm.appservice.redmond.azurestack.external |
| \*.sso.appservice.<region>.<DomainName>.<extension> | \*.sso.appservice.redmond.azurestack.external |

#### <a name="api-certificate"></a>API-certifikat

API-certifikatet är placerad på Management-rollen. Resursprovidern använder den för att säkra API-anrop. Certifikatet för publicering måste innehålla ett ämne som matchar API DNS-posten.

| Format | Exempel |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Publicera certifikat

Certifikatet för rollen Publisher säkrar FTPS trafiken för programmet ägare när de överför innehåll. Certifikatet för publicering måste innehålla ett ämne som matchar FTPS DNS-posten.

| Format | Exempel |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Identitetscertifikat

Certifikatet för programmets identitet kan:

- Integrering mellan Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS) directory, Azure-stacken och Apptjänst till stöd för integrering med compute-resursprovidern.
- Enkel inloggning scenarier för avancerad utvecklingsverktygen i Azure App Service på Azure-stacken.

Certifikat för identitet måste innehålla ett ämne som matchar följande format:

| Format | Exempel |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |

## <a name="virtual-network"></a>Virtual Network

Azure Apptjänst Azure stacken kan du distribuera resursprovidern till antingen ett befintligt virtuellt nätverk eller Apptjänst skapas som en del av distributionen.  Med hjälp av ett befintligt virtuellt nätverk kan du använda interna IP-adresser ska ansluta till filservern och SQLServer krävs för Azure App Service på Azure-stacken.  Det virtuella nätverket konfigureras med följande adressintervallet och undernät innan du installerar Azure App Service på Azure Stack:

Virtual Network - /16

Undernät

* ControllersSubnet /24
* ManagementServersSubnet /24
* FrontEndsSubnet /24
* PublishersSubnet /24
* WorkersSubnet /21

## <a name="prepare-the-file-server"></a>Förbereda filservern

Azure Apptjänst kräver användning av en filserver. För Produktionsdistribution, måste servern konfigureras för hög tillgänglighet och kan hantera fel.

Azure-stacken Development Kit distributioner kan du använda den [exempel Azure Resource Manager Distributionsmall](https://aka.ms/appsvconmasdkfstemplate) att distribuera en konfigurerad enkelnods-filserver. Enkelnods-filserver ska finnas i en arbetsgrupp.

>[!IMPORTANT]
> Om du vill distribuera Apptjänst i ett befintligt virtuellt nätverk som servern ska distribueras till ett separat undernät från Apptjänst.
>

### <a name="provision-groups-and-accounts-in-active-directory"></a>Etablera grupper och konton i Active Directory

1. Skapa följande Active Directory globala säkerhetsgrupper:
   - FileShareOwners
   - FileShareUsers
2. Skapa följande Active Directory-konton som tjänstkonton:
   - FileShareOwner
   - FileShareUser

   Som bör bästa praxis bör användare för dessa konton (och för alla webbroller för) skiljer sig från varandra och ha starkt användarnamn och lösenord. Ange lösenord med följande villkor:
   - Aktivera **lösenordet upphör aldrig att gälla**.
   - Aktivera **användaren kan inte ändra lösenordet**.
   - Inaktivera **användaren måste byta lösenord vid nästa inloggning**.
3. Lägg till kontona gruppmedlemskap enligt följande:
   - Lägg till **FileShareOwner** till den **FileShareOwners** grupp.
   - Lägg till **FileShareUser** till den **FileShareUsers** grupp.

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Etablera grupper och konton i en arbetsgrupp

>[!NOTE]
> När du konfigurerar en server, kör du följande kommandon i en administrativ kommandotolk. *Använd inte PowerShell.*

När du använder Azure Resource Manager-mall kan användarna redan har skapats.

1. Kör följande kommandon för att skapa FileShareOwner och FileShareUser konton. Ersätt `<password>` med egna värden.
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

### <a name="provision-the-content-share"></a>Etablera innehållsdelen

Innehållsdelen innehåller klient webbplatsens innehåll. Proceduren för att etablera innehållsdelen på en enskild server är samma för både Active Directory-och arbetsgruppsmiljöer. Men det är olika för failover-kluster i Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>Etablera innehållsdelen på en enda filserver (Active Directory eller arbetsgrupp)

Kör följande kommandon i en upphöjd kommandotolk på en enda filserver. Ersätt värdet för `C:\WebSites` med motsvarande sökvägar i din miljö.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="add-the-fileshareowners-group-to-the-local-administrators-group"></a>Lägg till gruppen FileShareOwners till den lokala gruppen Administratörer

För Windows Remote Management ska fungera korrekt måste du lägga till gruppen FileShareOwners den lokala gruppen Administratörer.

#### <a name="active-directory"></a>Active Directory

Kör följande kommandon i en upphöjd kommandotolk på filservern eller på varje filserver som fungerar som en nod i redundansklustret. Ersätt värdet för `<DOMAIN>` med det domännamn som du vill använda.

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>Arbetsgrupp

Kör följande kommando vid en upphöjd kommandotolk på filservern:

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>Konfigurera åtkomstkontroll till resurser

Kör följande kommandon i en upphöjd kommandotolk på filservern eller redundansklusternoden som är aktuella resursägaren för klustret. Ersätt värdena i kursiv stil med värden som är specifika för din miljö.

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

För Azure App Service på Azure-stacken värd och avläsning databaser, måste du förbereda en SQL Server-instans för App Service-databaser.

Azure-stacken Development Kit distributioner kan du använda SQL Server Express 2014 SP2 eller senare.

För produktion och hög tillgänglighet, du bör använda en fullständig version av SQL Server 2014 SP2 eller senare kan aktivera blandad autentisering och distribuera i en [konfiguration med hög tillgänglighet](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

SQL Server-instansen för Azure App Service på Azure-stacken måste vara tillgänglig från alla roller för App Service. Du kan distribuera SQL Server inom standard providern prenumeration i Azure-stacken. Eller så kan du använda den befintliga infrastrukturen i din organisation (så länge det finns en anslutning till Azure-stacken). Om du använder en Azure Marketplace-avbildning, Tänk på att konfigurera brandväggen därefter.

Du kan använda en standardinstans eller namngiven instans för SQL Server-roller. Om du använder en namngiven instans, måste du manuellt starta tjänsten SQL Server Browser och öppna port 1434.

>[!IMPORTANT]
> Om du vill distribuera Apptjänst i ett befintligt virtuellt nätverk som SQL Server ska distribueras till ett separat undernät från App Service och filservern.
>

## <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory-program

Konfigurera en Azure AD-tjänstens huvudnamn för att stödja följande:

- Virtuella skaluppsättning för integrering på worker nivåerna.
- Enkel inloggning för Azure Functions portalen och avancerad utvecklingsverktygen.

De här stegen gäller för Azure AD-skyddad Azure Stack-miljöer.

Administratörer måste konfigurera enkel inloggning till:

- Aktivera Avancerad utvecklingsverktygen i App Service (Kudu).
- Aktivera användning av Azure Functions-portaler.

Följ de här stegen:

1. Öppna ett PowerShell-instans som azurestack\AzureStackAdmin.
2. Gå till platsen för de skript som du hämtade och installerade i den [nödvändiga steg](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Installera PowerShell för Azure-stacken](azure-stack-powershell-install.md).
4. Kör den **skapa AADIdentityApp.ps1** skript. När du uppmanas, anger du den Azure AD-klient-ID som du använder för din Azure Stack-distribution. Ange till exempel **myazurestack.onmicrosoft.com**.
5. I den **autentiseringsuppgifter** fönster, ange ditt Azure AD-tjänsten-administratörskonto och lösenord. Välj **OK**.
6. Ange sökväg för certifikatet och lösenordet för den [certifikat som skapades tidigare](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Certifikatet som skapats för det här steget som standard är **sso.appservice.local.azurestack.external.pfx**.
7. Skriptet skapar ett nytt program i Azure AD-instans för innehavare. Anteckna det program-ID som returneras i PowerShell-utdata. Du behöver den här informationen under installationen.
8. Öppna ett nytt fönster i webbläsaren och logga in på den [Azure-portalen](https://portal.azure.com) som tjänstadministratör för Azure Active Directory
9. Öppna Azure AD-resursprovidern.
10. Välj **App registreringar**.
11. Sök efter program-ID som returneras som en del i steg 7. Ett program med App Service anges.
12. Välj **programmet** i listan.
13. Klicka på **inställningar**.
14. Välj **nödvändiga behörigheter** > **bevilja behörigheter** > **Ja**.

| Create-AADIdentityApp.ps1  parameter | Obligatorisk eller valfri | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| DirectoryTenantName | Krävs | Null | Azure AD-klient-ID. Ange GUID eller sträng. Ett exempel är myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Krävs | Null | Admin Azure Resource Manager-slutpunkt. Ett exempel är adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Krävs | Null | Klient Azure Resource Manager-slutpunkt. An example is management.local.azurestack.external. |
| AzureStackAdminCredential | Krävs | Null | Azure AD-tjänsten admin autentiseringsuppgifter. |
| CertificateFilePath | Krävs | Null | Sökvägen till filen identitet programmet certifikatet skapats tidigare. |
| CertificatePassword | Krävs | Null | Lösenordet som hjälper dig att skydda den privata nyckeln för certifikatet. |

## <a name="create-an-active-directory-federation-services-application"></a>Skapa ett Active Directory Federation Services-program

För miljöer med Azure-stacken skyddas av AD FS måste du konfigurera en AD FS-tjänstens huvudnamn för att stödja följande:

- Virtuella skaluppsättning för integrering på worker nivåerna.
- Enkel inloggning för Azure Functions portalen och avancerad utvecklingsverktygen.

Administratörer måste konfigurera enkel inloggning till:

- Konfigurera ett huvudnamn för tjänsten för virtuell dator scale set integrering på worker nivåer.
- Aktivera Avancerad utvecklingsverktygen i App Service (Kudu).
- Aktivera användning av Azure Functions-portaler.

Följ de här stegen:

1. Öppna ett PowerShell-instans som azurestack\AzureStackAdmin.
2. Gå till platsen för de skript som du hämtade och installerade i den [nödvändiga steg](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Installera PowerShell för Azure-stacken](azure-stack-powershell-install.md).
4. Kör den **skapa ADFSIdentityApp.ps1** skript.
5. I den **autentiseringsuppgifter** fönster, ange ditt AD FS molnet administratörskonto och lösenord. Välj **OK**.
6. Ange sökväg för certifikatet och lösenordet för den [certifikat som skapades tidigare](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Certifikatet som skapats för det här steget som standard är **sso.appservice.local.azurestack.external.pfx**.

| Create-ADFSIdentityApp.ps1  parameter | Obligatorisk eller valfri | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| AdminArmEndpoint | Krävs | Null | Admin Azure Resource Manager-slutpunkt. Ett exempel är adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Krävs | Null | Privilegierade slutpunkt. Ett exempel är AzS ERCS01. |
| CloudAdminCredential | Krävs | Null | Konto-domänautentiseringsuppgifterna för Azure-stacken molnet administratörer. Ett exempel är Azurestack\CloudAdmin. |
| CertificateFilePath | Krävs | Null | Sökvägen till programmet identitet certifikatets PFX-fil. |
| CertificatePassword | Krävs | Null | Lösenordet som hjälper dig att skydda den privata nyckeln för certifikatet. |

## <a name="next-steps"></a>Nästa steg

[Installera App-tjänstresursprovider](azure-stack-app-service-deploy.md)
