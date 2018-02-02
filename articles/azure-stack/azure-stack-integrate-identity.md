---
title: Integration av Azure Stack datacenter - identitet
description: "Lär dig att integrera Azure Stack AD FS med ditt datacenter AD FS"
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: 
ms.openlocfilehash: 2f15e130859272a729fb0ad6e0b718d4724f2103
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="azure-stack-datacenter-integration---identity"></a>Integration av Azure Stack datacenter - identitet
Du kan distribuera Azure-stacken använder Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS) som identitetsleverantörer. Innan du distribuerar Azure stacken måste du göra valet. Distribution med AD FS är kallas även distribuera Azure-stacken i frånkopplat läge.

Följande tabell visar skillnaderna mellan de två identity val:

||Fysiskt frånkopplad|Fysiskt ansluten|
|---------|---------|---------|
|Fakturering|Måste vara kapacitet<br> Enterprise-avtal (EA) endast|Kapacitet eller lön-som-du-användning<br>EA eller Cloud Solution Provider (CSP)|
|Identitet|Måste vara AD FS|Azure AD eller AD FS|
|Marketplace-syndikeringsfeed|Stöds<br>BYOL-licensiering|Stöds<br>BYOL-licensiering|
|Registrering|Rekommenderas, kräver flyttbart medium<br> och en separat ansluten enhet.|Automatisk|
|Korrigeringar och uppdateringar|Krävs, kräver flyttbart medium<br> och en separat ansluten enhet.|Uppdateringspaket kan hämtas direkt<br> från Internet till Azure-stacken.|

> [!IMPORTANT]
> Du kan inte växla identitetsleverantören utan att omdistribuera hela Azure Stack-lösningen.

## <a name="active-directory-federation-services-and-graph"></a>Active Directory Federation Services och Graph

Distribuera med AD FS kan identiteter i en befintlig Active Directory-skog för att autentisera med resurser i Azure-stacken. Den här befintliga Active Directory-skog kräver en distribution av AD FS för att skapa ett förtroende för AD FS-federation.

Autentisering är en del av identitet. Diagram-komponenten måste konfigureras för att hantera rollen åtkomstkontroll (RBAC) i Azure-stacken. När åtkomst till en resurs har delegerats söker komponenten diagrammet användarkonto i den befintliga Active Directory-skogen med LDAP-protokollet.

![Azure Stack AD FS-arkitektur](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

Den befintliga AD FS är det konto säkerhetstokentjänst (STS) som skickar anspråk till Azure-stacken AD FS (resurs-STS). I Azure-stacken skapar automation anspråksleverantörsförtroendet med metadataslutpunkten för befintliga AD FS.

En förlitande part måste konfigureras på den befintliga AD FS. Det här steget görs inte av automatisering och måste konfigureras av operatorn. Azure-stacken metadataslutpunkten dokumenteras i filen AzureStackStampDeploymentInfo.JSON eller via Privilegierade slutpunkten genom att köra kommandot `Get-AzureStackInfo`.

Förlitande part förtroende konfigurationen måste du konfigurera regler för anspråksomvandling anspråk som tillhandahålls av Microsoft.

Ett tjänstkonto måste vara under förutsättning att har läsbehörighet i befintliga Active Directory för Graph-konfigurationen. Det här kontot krävs som indata att aktivera scenarier med RBAC automatisering.

Det sista steget konfigureras en ny ägare för providern standardabonnemang. Det här kontot har fullständig åtkomst till alla resurser när loggar in på Azure Stack-administratörsportalen.

Krav:


|Komponent|Krav|
|---------|---------|
|Graph|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Konfigurering av Graph-integrering

Följande information krävs som indata för automation-parametrar:


|Parameter|Beskrivning|Exempel|
|---------|---------|---------|
|CustomADGlobalCatalog|FQDN för målet Active Directory-skog<br>du vill integrera med|Contoso.com|
|CustomADAdminCredentials|En användare med läsbehörighet för LDAP|YOURDOMAIN\graphservice|

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Skapa användarkonto i befintliga Active Directory (valfritt)

Du kan också kan du skapa ett konto för tjänsten diagram i befintliga Active Directory. Utför det här steget om du inte redan har ett konto som du vill använda.

1. Skapa följande användarkonto (rekommendation) i befintliga Active Directory:
   - **Användarnamnet**: graphservice
   - **Lösenordet**: Använd ett starkt lösenord<br>Konfigurera lösenordet upphör aldrig att gälla.

   Det krävs ingen särskilda behörigheter eller gruppmedlemskap.

#### <a name="trigger-automation-to-configure-graph"></a>Utlösaren automation för att konfigurera diagram

Använda en dator i ditt datacenternätverk som kan kommunicera med den privilegierade slutpunkten i Azure-stacken för den här proceduren.

2. Öppna en upphöjd Windows PowerShell-session (Kör som administratör) och ansluta till IP-adressen för privilegierade slutpunkten. Använd autentiseringsuppgifter för **CloudAdmin** att autentisera.

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

3. Nu när du är ansluten till Privilegierade slutpunkten kör du följande kommando: 

   ```powershell
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   När du uppmanas du ange autentiseringsuppgifter för det användarkonto som du vill använda för Graph-tjänsten (till exempel graphservice).

   > [!IMPORTANT]
   > Vänta tills autentiseringsuppgifterna som popup (Get-Credential stöds inte i den privilegierade slutpunkten) och ange autentiseringsuppgifterna för tjänstkontot för diagrammet.

#### <a name="graph-protocols-and-ports"></a>Diagram-protokoll och portar

Diagram tjänsten i Azure-stacken använder följande protokoll och portar för att kommunicera med målet Active Directory:

|Typ|Port|Protokoll|
|---------|---------|---------|
|LDAP|389|TCP OCH UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP-GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Ställa in integration med AD FS genom att hämta federationsmetadata

Följande information krävs som indata för automation-parametrar:

|Parameter|Beskrivning|Exempel|
|---------|---------|---------|
|CustomAdfsName|Namnet på anspråksprovidern. <cr>Verkar det sätt på den AD FS-landningssidan.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|Länk för Federation metadata|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Utlösaren automation för att konfigurera anspråksleverantörsförtroendena i Azure-stacken

Använda en dator som kan kommunicera med den privilegierade slutpunkten i Azure-stacken för den här proceduren. Det förväntas att certifikatet som används av kontot **STS AD FS** är betrodd av Azure-stacken.

1. Öppna en upphöjd Windows PowerShell-session och Anslut till Privilegierade slutpunkten.

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Nu när du är ansluten till Privilegierade slutpunkten, kör följande kommando med parametrar som är lämpliga för din miljö:

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Kör följande kommando för att uppdatera ägaren av providern standardabonnemang med parametrarna som är lämpliga för din miljö:

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Konfigurera AD FS-integrering med federation metadatafil

Använd den här metoden om något av följande villkor är uppfyllda:

- Certifikatkedjan är olika för AD FS jämfört med andra slutpunkterna i Azure-stacken.
- Det finns ingen nätverksanslutning till den befintliga AD FS-servern från Azure-stacken instans av AD FS.

Följande information krävs som indata för automation-parametrar:


|Parameter|Beskrivning|Exempel|
|---------|---------|---------|
|CustomAdfsName|Namnet på anspråksprovidern. På så sätt på den AD FS-landningssidan visas.|Contoso|
|CustomADFSFederationMetadataFile|Metadatafil för Federation|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|

### <a name="create-federation-metadata-file"></a>Skapa federation metadatafil

För följande procedur, måste du använda en dator som är ansluten till den befintliga AD FS-distributionen som blir kontot STS. Nödvändiga certifikat måste vara installerad.

1. Öppna en upphöjd Windows PowerShell-session och kör följande kommando med parametrar som är lämpliga för din miljö:

   ```powershell
   [XML]$Metadata = Invoke-WebRequest -URI https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml -UseBasicParsing

   $Metadata.outerxml|out-file c:\metadata.xml
   ```

2. Kopiera metadatafilen till en resurs som är tillgänglig från Privilegierade slutpunkten.


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Utlösaren automation för att konfigurera anspråksleverantörsförtroendena i Azure-stacken

Använda en dator som kan kommunicera med den privilegierade slutpunkten i Azure-stacken för den här proceduren.

1. Öppna en upphöjd Windows PowerShell-session och Anslut till Privilegierade slutpunkten.

   ```powershell
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Nu när du är ansluten till Privilegierade slutpunkten, kör följande kommando med parametrar som är lämpliga för din miljö:

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso – CustomADFSFederationMetadataFile \\share\metadataexample.xml
   ```

3. Kör följande kommando för att uppdatera ägaren av providern standardabonnemang med parametrarna som är lämpliga för din miljö:

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Konfigurera förlitande part på befintliga AD FS-distribution (konto STS)

Microsoft tillhandahåller ett skript som konfigurerar den förlitande parten, inklusive omvandling av anspråksregler. Med hjälp av skript är valfritt eftersom du kan köra kommandon manuellt.

Du kan ladda ned skriptet från helper [Azure Stack verktyg](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) på Github.

Följ dessa steg om du vill köra kommandon manuellt:

1. Kopiera följande innehåll till en txt-fil (till exempel sparas som c:\ClaimRules.txt) i ditt datacenter AD FS-instans eller servergruppen medlem:

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
   c:[Type == http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname]
   => issue(claim = c);
   ```

2. För att aktivera Windows Forms-baserad autentisering, öppna en Windows PowerShell-session som en användare med förhöjd behörighet och kör följande kommando:

   ```powershell
   Set-AdfsProperties -WIASupportedUserAgents @("MSAuthHost/1.0/In-Domain","MSIPC","Windows Rights Management Client","Kloud")
   ```

3. Om du vill lägga till den förlitande parten, kör du följande Windows PowerShell-kommandot på din AD FS-instans eller en medlem i gruppen. Se till att uppdatera AD FS-slutpunkten och peka på den fil som skapade i steg 1.

   **För AD FS 2016**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone"
   ```

   **För AD FS 2012/2012 R2**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true
   ```

   > [!IMPORTANT]
   > Du måste använda AD FS MMC-snapin-modulen för att konfigurera auktoriseringsregler för utfärdande när du använder Windows Server 2012 eller 2012 R2 AD FS.

4. När du använder Internet Explorer eller Edge-webbläsaren för att få åtkomst till Azure-stacken, måste du ignorera token bindningar. Annars misslyckas försöker logga in. Kör följande kommando på din AD FS-instans eller en medlem i gruppen:

   ```powershell
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

5. Om du vill aktivera uppdaterings-tokens, öppna en upphöjd Windows PowerShell-session och kör följande kommando:

   ```powershell
   Set-ADFSRelyingPartyTrust -TargetName AzureStack -TokenLifeTime 1440
   ```

## <a name="spn-creation"></a>Skapa en SPN

Det finns många scenarier som kräver användning av en tjänstens huvudnamn (SPN) för autentisering. Följande är några exempel:

- CLI användning med AD FS-distribution i Azure-stacken
- System Center Management Pack för Azure-Stack när de distribueras med AD FS
- Resursproviders i Azure-stacken när de distribueras med AD FS
- Olika program
- Du behöver en icke-interaktiv inloggning

Mer information om hur du skapar ett Tjänsthuvudnamn finns [skapa tjänstens huvudnamn för AD FS](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-ad-fs).


## <a name="troubleshooting"></a>Felsökning

### <a name="configuration-rollback"></a>Återställning av konfigurationen

Om det inträffar ett fel som lämnar miljön i ett tillstånd där du inte längre kan autentiseras, finns ett alternativ för återställning.

1. Öppna en upphöjd Windows PowerShell-session och kör följande kommandon:

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Kör följande cmdlet:

   ```powershell
   Reset-DatacenterIntegationConfiguration
   ```

   När du har kört Återföringsåtgärd återställs alla konfigurationsändringar som. Endast autentisering med inbyggt **CloudAdmin** användaren är möjliga.

   > [!IMPORTANT]
   > Du måste konfigurera den ursprungliga ägaren av prenumerationen för standard-provider

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>Samla in ytterligare loggfiler

Om någon av cmdletarna misslyckas du kan samla in ytterligare loggfiler med hjälp av den `Get-Azurestacklogs` cmdlet.

1. Öppna en upphöjd Windows PowerShell-session och kör följande kommandon:

   ```powershell
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Kör följande cmdlet:

   ```powershell
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>Nästa steg

[Registrera Azure Stack](azure-stack-registration.md)
