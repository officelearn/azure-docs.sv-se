---
title: Integrering med Azure Stack datacenter - identitet
description: Lär dig hur du integrerar Azure Stack AD FS med ditt datacenter AD FS
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 01/23/19
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 01/23/19
ms.openlocfilehash: a7753280c0b84b21f44e52c303b96f11cd4cd8af
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55765798"
---
# <a name="azure-stack-datacenter-integration---identity"></a>Integrering med Azure Stack datacenter - identitet
Du kan distribuera Azure Stack med Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS) som identitetsleverantör man. Du måste göra valet innan du distribuerar Azure Stack. Distributionen med hjälp av AD FS är kallas även distribuera Azure Stack i frånkopplat läge.

I följande tabell visar skillnaderna mellan de två identity val:

||Ansluten till internet|Ansluten till internet|
|---------|---------|---------|
|Fakturering|Måste vara kapacitet<br> Endast Enterprise Agreement (EA)|Kapacitet eller betalning som du-användning<br>EA- eller leverantörer av Molnlösningar (CSP)|
|Identitet|Måste vara AD FS|Azure AD eller AD FS|
|Marketplace |Stöds<br>BYOL-licensiering|Stöds<br>BYOL-licensiering|
|Registrering|Krävs, kräver flyttbart medium<br> och en separat ansluten enhet.|Automatiserad|
|Korrigeringar och uppdateringar|Krävs, kräver flyttbart medium<br> och en separat ansluten enhet.|Uppdateringspaket kan hämtas direkt<br> från Internet till Azure Stack.|

> [!IMPORTANT]
> Du kan inte växla identitetsprovidern utan att omdistribuera hela Azure Stack-lösningen.

## <a name="active-directory-federation-services-and-graph"></a>Active Directory Federation Services och en graf

Distribuera med AD FS kan identiteter i en befintlig Active Directory-skog för att autentisera med resurser i Azure Stack. Den här befintliga Active Directory-skog kräver en distribution av AD FS för att tillåta skapandet av ett förtroende för AD FS-federation.

Autentisering är en del av identitet. Graph-komponent måste konfigureras för att hantera rollbaserad åtkomstkontroll (RBAC) i Azure Stack. När åtkomst till en resurs har delegerats kan söker komponenten Graph användarkonto i den befintliga Active Directory-skogen med LDAP-protokollet.

![Azure Stack AD FS-arkitektur](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

Den befintliga AD FS är det konto säkerhetstokentjänst (STS) som skickar anspråk till Azure Stack AD FS (resurs STS). I Azure Stack skapar automation den anspråk provider förtroenden med metadataslutpunkten för befintliga AD FS.

En förlitande part måste konfigureras i den befintliga AD FS. Det här steget görs inte av automation och måste konfigureras av operatorn. Azure Stack-metadataslutpunkt dokumenteras i filen AzureStackStampDeploymentInfo.JSON eller via privilegierad slutpunkt genom att köra kommandot `Get-AzureStackInfo`.

Förlitande part förtroende konfigurationen måste du konfigurera anspråksreglerna för omvandling som tillhandahålls av Microsoft.

Ett tjänstkonto för Graph-konfiguration, måste de vara förutsatt att har läsbehörighet i befintliga Active Directory. Det här kontot krävs som indata för automation för att aktivera RBAC-scenarier.

En ny ägare har konfigurerats för providern Standardprenumeration för det sista steget. Det här kontot har fullständig åtkomst till alla resurser när du loggat in på Azure Stack-administratörsportalen.

Krav:

|Komponent|Krav|
|---------|---------|
|Graph|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Konfigurera Graph-integrering

Diagrammet har endast stöd för integrering med en enda Active Directory-skog. Om flera skogar finns används i skogen som angetts i konfigurationen för att hämta användare och grupper.

Följande information krävs som indata för automation-parametrar:

|Parameter|Beskrivning|Exempel|
|---------|---------|---------|
|CustomADGlobalCatalog|FQDN för målet Active Directory-skog<br>som du vill integrera med|Contoso.com|
|CustomADAdminCredentials|En användare med läsbehörighet för LDAP|YOURDOMAIN\graphservice|

### <a name="configure-active-directory-sites"></a>Konfigurera Active Directory-platser

Konfigurera den närmaste Active Directory-plats till Azure Stack-distribution för Active Directory-distributioner med flera platser. Konfigurationen innebär att du slipper Azure Stack diagramtjänsten matcha frågor med hjälp av en Global katalogserver från en fjärrplats.

Lägg till Azure Stack [offentliga VIP-nätverk](azure-stack-network.md#public-vip-network) undernätet till Azure AD plats närmast Azure Stack. Till exempel om din Active Directory har två platser Seattle och Redmond med Azure Stack som distribueras på Seattle-plats, och Lägg till Azure Stack offentliga VIP-nätverksundernätet Azure AD-plats för Seattle.

Mer information om Active Directory-platser finns i [designa platstopologin](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology).

> [!Note]  
> Om din Active Directory består av en enda plats kan du hoppa över det här steget. Om du har en allomfattande undernät som konfigurerats att verifiera att Azure Stack offentliga VIP-nätverksundernätet inte är en del av den.

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Skapa användarkontot i befintliga Active Directory (valfritt)

Du kan också skapa ett konto för Graph-tjänsten i befintliga Active Directory. Utför det här steget om du inte redan har ett konto som du vill använda.

1. Skapa följande användarkontot (rekommendation) i befintliga Active Directory:
   - **Användarnamn**: graphservice
   - **Lösenord**: Använd ett starkt lösenord<br>Konfigurera lösenord för att aldrig upphöra att gälla.

   Det krävs inga särskilda behörigheter eller gruppmedlemskap.

#### <a name="trigger-automation-to-configure-graph"></a>Utlösa automatisering för att konfigurera graph

För den här proceduren ska du använda en dator i datacenternätverket som kan kommunicera med den privilegierade slutpunkten i Azure Stack.

1. Öppna en upphöjd Windows PowerShell-session (Kör som administratör) och ansluta till IP-adressen för privilegierade slutpunkten. Använd autentiseringsuppgifter för **CloudAdmin** att autentisera.

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Nu när du är ansluten till privilegierad slutpunkt, kör du följande kommando: 

   ```PowerShell  
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   När du uppmanas, anger du autentiseringsuppgifterna för det användarkonto som du vill använda för Graph-tjänsten (till exempel graphservice). Indata för cmdleten Register-DirectoryService måste vara skogens namn / root domän i skogen snarare än andra domäner i skogen.

   > [!IMPORTANT]
   > Vänta tills autentiseringsuppgifterna som popup (Get-Credential stöds inte i den privilegierade slutpunkten) och ange autentiseringsuppgifter för Graph-tjänstkontot.

#### <a name="graph-protocols-and-ports"></a>Graph-protokoll och portar

Graph-tjänsten i Azure Stack använder följande protokoll och portar för att kommunicera med en skrivbar Global katalog-Server (GC) och Key Distribution Center (KDC) som kan bearbeta inloggningsbegäranden i mål-Active Directory-skog.

Graph-tjänsten i Azure Stack använder följande protokoll och portar för att kommunicera med målet Active Directory:

|Type|Port|Protokoll|
|---------|---------|---------|
|LDAP|389|TCP OCH UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Om du konfigurerar AD FS-integrationen genom att ladda ned federationsmetadata

Följande information måste anges som indata för automation-parametrar:

|Parameter|Beskrivning|Exempel|
|---------|---------|---------|
|CustomAdfsName|Namnet på anspråksprovidern.<br>Den visas på så sätt på landningssidan för AD FS.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|Länk för Federation metadata|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Utlösa automatisering för att konfigurera anspråksleverantörsförtroendena i Azure Stack

I den här proceduren Använd en dator som kan kommunicera med privilegierad slutpunkt i Azure Stack. Det förväntas att certifikatet som används av kontot **STS AD FS** är betrodd av Azure Stack.

1. Öppna en upphöjd Windows PowerShell-session och Anslut till privilegierad slutpunkt.

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Nu när du är ansluten till privilegierad slutpunkt, kör du följande kommando med parametrar som är lämpliga för din miljö:

   ```PowerShell  
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Kör följande kommando för att uppdatera ägaren till providerprenumeration standard med hjälp av parametrarna som är lämpliga för din miljö:

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Om du konfigurerar AD FS-integrationen genom att tillhandahålla metadatafil för federation

Från och med version 1807 kan använda den här metoden om något av följande villkor är uppfyllda:

- Certifikatkedjan är olika för AD FS jämfört med andra slutpunkter i Azure Stack.
- Det finns ingen nätverksanslutning till den befintliga AD FS-servern från Azure Stack AD FS-instans.

Följande information måste anges som indata för automation-parametrar:


|Parameter|Beskrivning|Exempel|
|---------|---------|---------|
|CustomAdfsName|Namnet på anspråksprovidern. Den visas på så sätt på landningssidan för AD FS.|Contoso|
|CustomADFSFederationMetadataFileContent|Metadata för innehåll|$using:federationMetadataFileContent|

### <a name="create-federation-metadata-file"></a>Skapa federation metadata-fil

För att följande åtgärder måste du använda en dator som är ansluten till den befintliga AD FS-distribution, som blir kontot som STS. Nödvändiga certifikat måste vara installerad.

1. Öppna en upphöjd Windows PowerShell-session och kör följande kommando med parametrar som är lämpliga för din miljö:

   ```PowerShell  
    $url = "https://win-SQOOJN70SGL.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml"
    $webclient = New-Object System.Net.WebClient
    $webclient.Encoding = [System.Text.Encoding]::UTF8
    $metadataAsString = $webclient.DownloadString($url)
    Set-Content -Path c:\metadata.xml -Encoding UTF8 -Value $metadataAsString
   ```

2. Kopiera metadatafilen till en dator som kan kommunicera med privilegierad slutpunkt.

### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Utlösa automatisering för att konfigurera anspråksleverantörsförtroendena i Azure Stack

Använda en dator som kan kommunicera med privilegierad slutpunkt i Azure Stack och har åtkomst till metadatafilen som du skapade i föregående steg i den här proceduren.

1. Öppna en upphöjd Windows PowerShell-session och Anslut till privilegierad slutpunkt.

   ```PowerShell  
   $federationMetadataFileContent = get-content c:\metadata.xml
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Nu när du är ansluten till privilegierad slutpunkt, kör du följande kommando med parametrar som är lämpliga för din miljö:

    ```PowerShell
    Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataFileContent $using:federationMetadataFileContent
    ```

3. Kör följande kommando för att uppdatera ägaren till providerprenumeration standard med hjälp av parametrarna som är lämpliga för din miljö:

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

   > [!Note]  
   > När du roterar certifikat på den befintliga AD FS (STS-konto) måste du konfigurera AD FS-integrationen igen. Du måste konfigurera integrationen även om metadataslutpunkten kan nås eller konfigurerades genom att tillhandahålla metadatafilen.

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Konfigurera förlitande part på befintliga AD FS-distribution (STS-konto)

Microsoft tillhandahåller ett skript som konfigurerar den förlitande parten, inklusive omvandling anspråksregler. Med hjälp av skript är valfritt eftersom du kan köra kommandona manuellt.

Du kan ladda ned helper-skriptet från [Azure Stack Tools](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) på GitHub.

Följ dessa steg om du vill köra kommandon manuellt:

1. Kopiera följande innehåll till en txt-fil (till exempel sparas som c:\ClaimRules.txt) på ditt datacenter AD FS-instans eller -grupp medlem:

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
   => issue(claim = c);
   ```

2. Verifiera att Windows formulärbaserad autentisering för extranät och intranät är aktiverad. Kontrollera först om dess redan aktiverat genom att köra följande cmdlet:

   ```PowerShell  
   Get-AdfsAuthenticationProvider | where-object { $_.name -eq "FormsAuthentication" } | select Name, AllowedForPrimaryExtranet, AllowedForPrimaryIntranet
   ```

    > [!Note]  
    > Den Windows integrerad autentisering (WIA) stöds användaragent strängar kan är inaktuell för du AD FS-distribution kan kräva för att uppdateras för att stödja senaste klienter. Du kan läsa mer om att uppdatera WIA stöds användaren agent strängar i artikeln [konfigurera intranät formulärbaserad autentisering för enheter som inte stöder WIA](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-intranet-forms-based-authentication-for-devices-that-do-not-support-wia).<br>Stegen för att aktivera princip för formulärbaserad autentisering dokumenteras i artikeln [konfigurera autentiseringsprinciper](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-authentication-policies).

3. Om du vill lägga till den förlitande parten, kör du följande Windows PowerShell-kommando på din AD FS-instans eller en medlem i gruppen. Se till att uppdatera slutpunkten för AD FS och peka på filen som skapades i steg 1.

   **För AD FS 2016**

   ```PowerShell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone" -TokenLifeTime 1440
   ```

   **För AD FS 2012/2012 R2**

   ```PowerShell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -TokenLifeTime 1440
   ```

   > [!IMPORTANT]  
   > Du måste använda AD FS MMC-snapin-modulen för att konfigurera auktoriseringsregler för utfärdande när du använder Windows Server 2012 eller 2012 R2 AD FS.

4. När du använder Internet Explorer eller Microsoft Edge-webbläsaren för att komma åt Azure Stack, måste du ignorera token-bindningar. Annars misslyckas försök logga in. Kör följande kommando på din AD FS-instans eller en medlem i gruppen:

   > [!note]  
   > Det här steget gäller inte när du använder Windows Server 2012 eller 2012 R2 AD FS. Det är säkert att hoppa över det här kommandot och fortsätta med integreringen.

   ```PowerShell  
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

## <a name="spn-creation"></a>Skapa en SPN

Det finns många scenarier som kräver användning av ett tjänstens huvudnamn (SPN) för autentisering. Här följer några exempel:

- CLI-användning med AD FS-distribution av Azure Stack
- System Center Management Pack för Azure Stack när de distribueras med AD FS
- Resursprovidrar i Azure Stack när de distribueras med AD FS
- Olika program
- Du behöver en icke-interaktiv inloggning

> [!Important]  
> AD FS har endast stöd för interaktiv Inloggningssessioner. Om du behöver en icke-interaktiv inloggning i ett scenario med automatiserade måste du använda ett SPN.

Läs mer om hur du skapar ett SPN [skapa tjänstens huvudnamn för AD FS](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).


## <a name="troubleshooting"></a>Felsökning

### <a name="configuration-rollback"></a>Konfiguration av återställning

Om det inträffar ett fel som lämnar miljön i ett tillstånd där du kan inte längre autentisera, finns ett alternativ för återställning.

1. Öppna en upphöjd Windows PowerShell-session och kör följande kommandon:

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Kör följande cmdlet:

   ```PowerShell  
   Reset-DatacenterIntegrationConfiguration
   ```

   När du har kört återställningsåtgärden återställs alla konfigurationsändringar. Endast autentisering med inbyggt **CloudAdmin** användaren är möjligt.

   > [!IMPORTANT]
   > Du måste konfigurera den ursprungliga ägaren av standard-providerprenumeration

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>Samla in ytterligare loggar

Om någon av cmdletarna misslyckas kan du samla in ytterligare loggar med hjälp av den `Get-Azurestacklogs` cmdlet.

1. Öppna en upphöjd Windows PowerShell-session och kör följande kommandon:

   ```PowerShell  
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Kör följande cmdlet:

   ```PowerShell  
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>Nästa steg

[Integrera externa övervakningslösningar](azure-stack-integrate-monitor.md)
