---
title: Konfigurera anslutna Azure Active Directory-hybridenheter manuellt| Microsoft Docs
description: Lär dig att konfigurera anslutna Azure Active Directory-hybridenheter manuellt.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/01/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: a3c35057af883eb790c44b3547072031eaf4ad2f
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50962018"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Självstudie: Konfigurera anslutna Azure Active Directory-hybridenheter manuellt 

Med enhetshanteringen i Azure Active Directory (Azure AD) kan du se till att användarna har tillgång till dina resurser från enheter som uppfyller dina krav för säkerhet och kompatibilitet. Mer information finns i [Introduktion till hantering av enheter i Azure Active Directory](overview.md).


> [!TIP]
> Om Azure AD Connect är ett alternativ för dig läser du [Select your scenario](hybrid-azuread-join-plan.md#select-your-scenario) (Välja scenario). När du använder Azure AD Connect kan du förenkla konfigurationen av Hybrid Azure AD-anslutningen avsevärt.



Om du har en lokal Active Directory-miljö och du vill ansluta dina domänanslutna enheter till Azure AD kan du göra det genom att konfigurera Hybrid Azure AD-anslutna enheter. I den här självstudien lär du dig att manuellt konfigurera en Azure AD-hybridanslutning för enheter.

> [!div class="checklist"]
> * Nödvändiga komponenter
> * Konfigurationssteg
> * Konfigurera en tjänstanslutningspunkt
> * Konfigurera utfärdande av anspråk
> * Aktivera äldre Windows-enheter
> * Verifiera anslutna enheter
> * Felsöka din implementering
 




## <a name="prerequisites"></a>Nödvändiga komponenter

I den här självstudien förutsätts att du känner till:
    
-  [Introduktion till enhetshantering i Azure Active Directory](../device-management-introduction.md)
    
-  [Så här planerar du Azure Active Directory Join-hybridimplementeringen](hybrid-azuread-join-plan.md)

-  [Så här kontrollerar du Azure Active Directory Join-hybriden för dina enheter](hybrid-azuread-join-control.md)


Innan du börjar aktivera Hybrid Azure AD-anslutna enheter i din organisation måste du se till att:

- Du kör en uppdaterad version av Azure AD Connect.

- Azure AD Connect har synkroniserat datorobjekten för enheterna du vill ska vara Hybrid Azure AD-anslutna till Azure AD. Om datorobjekten tillhör specifika organisationsenheter (OU) måste även organisationsenheterna konfigureras för synkronisering i Azure AD.

  

Azure AD Connect:

- Behålla associationen mellan datorkontot i din lokala Active Directory (AD) och enhetsobjektet i Azure AD. 
- Aktivera andra enhetsrelaterade funktioner som Windows Hello för företag.

Kontrollera att följande webbadresser är åtkomliga från datorer i organisationens nätverk för registrering av datorer till Azure AD:

- https://enterpriseregistration.windows.net

- https://login.microsoftonline.com Tillåt
- https://device.login.microsoftonline.com

- Din organisations STS (federerade domäner)

Om de inte redan gör det ska organisationens STS (för federerade domäner) ingå i användarens inställningar för lokalt intranät.

Om din organisation planerar att använda sömlös SSO måste följande webbadresser kunna nås från datorer i organisationen, och de måste också läggas till i användarens lokala intranätzon:

- https://autologon.microsoftazuread-sso.com

- Följande inställning ska också vara aktiverad i användarens intranätzon: ”Allow status bar updates via script” (Tillåt uppdatering av statusfältet via skript).

Om din organisation använder hanterad (icke-federerad) konfiguration med lokal AD och inte använder ADFS för att federera med Azure AD förlitar sig Hybrid Azure AD Join på att Windows 10 på datorobjekten i AD är synkroniserat till Azure AD. Kontrollera att organisationsenheterna (OU) som innehåller datorobjekten som måste vara Hybrid Azure AD-anslutna är aktiverade för synkronisering i Azure AD Connect-synkroniseringskonfigurationen.

För Windows 10-enheter med version 1703 eller tidigare måste du implementera Web Proxy Auto-Discovery (WPAD) för att göra det möjligt för Windows 10-datorer att registreras för Azure AD om din organisation kräver åtkomst till Internet via en utgående proxy. 

Från och med Windows 10-1803, även om hybrid Azure AD Join-försök av en enhet i en federerad domän med hjälp av AD FS misslyckas, och om Azure AD Connect är konfigurerat för att synka dator-/enhetsobjekt till Azure AD försöker sedan enheten att slutföra Hybrid Azure AD-anslutningen med hjälp av den synkroniserade datorn/enheten.

## <a name="configuration-steps"></a>Konfigurationssteg

Du kan konfigurera Hybrid Azure AD-anslutna enheter för olika typer av plattformar för Windows-enheter. Det här avsnittet innehåller de steg som krävs för alla vanliga konfigurationsscenarier.  

Använd följande tabell för att få en översikt över stegen som krävs för ditt scenario:  



| Steg                                      | Aktuell Windows hash-synkronisering av lösenord | Aktuell Windows och federation | Äldre Windows-enheter |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Konfigurera en tjänstanslutningspunkt | ![Markera][1]                            | ![Markera][1]                    | ![Markera][1]        |
| Konfigurera utfärdande av anspråk           |                                        | ![Markera][1]                    | ![Markera][1]        |
| Aktivera enheter som inte använder Windows 10      |                                        |                                | ![Markera][1]        |
| Verifiera anslutna enheter          | ![Markera][1]                            | ![Markera][1]                    | ![Markera][1]        |



## <a name="configure-service-connection-point"></a>Konfigurera en tjänstanslutningspunkt

SCP-objektet (tjänstanslutningspunkt) används av dina enheter under registreringen för att upptäcka Azure AD-klientinformation. I din lokala Active Directory (AD) måste SCP-objektet för Hybrid Azure AD-anslutna enheter finnas i kontextpartitionen för konfigurationsnamngivningen för datorns skog. Det finns bara en kontext för konfigurationsnamngivning per skog. I en Active Directory-konfiguration med flera skogar måste tjänstanslutningspunkten finnas i alla skogar som innehåller domänanslutna datorer.

Du kan använda cmdleten [**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) för att hämta skogens kontext för konfigurationsnamngivning.  

För en skog med Active Directory-domännamnet *fabrikam.com*, är kontexten för konfigurationsnamngivning:

`CN=Configuration,DC=fabrikam,DC=com`

I din skog finns SCP-objektet för automatisk registrering av domänanslutna enheter här:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Beroende på hur du har distribuerat Azure AD Connect måste SCP-objektet redan ha konfigurerats.
Du kan kontrollera om objektet finns och hämta identifieringsvärdena med följande Windows PowerShell-skript: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

**$scp.Keywords**-utdata visar Azure AD-klientinformationen, till exempel:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Om tjänstanslutningspunkten inte finns kan du skapa den genom att köra cmdleten `Initialize-ADSyncDomainJoinedComputerSync` på din Azure AD Connect-server. Autentiseringsuppgifter för företagsadministratör krävs för att köra den här cmdleten.  
Cmdleten:

- Skapar tjänstanslutningspunkt i den Active Directory-skog som Azure AD Connect är anslutet till. 
- Kräver att du anger parametern `AdConnectorAccount`. Detta är det konto som har konfigurerats som Active Directory-anslutningskonto i Azure AD Connect. 


Följande skript visar ett exempel på användning av cmdleten. I det här skriptet kräver `$aadAdminCred = Get-Credential` att du skriver ett användarnamn. Du måste ange användarnamnet i UPN-format (user principal name) (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

Cmdleten `Initialize-ADSyncDomainJoinedComputerSync`:

- Använder Active Directory PowerShell-modulen och AD DS-verktyg som förlitar sig på att Active Directory Web Services körs på en domänkontrollant. Active Directory Web Services fungerar på domänkontrollanter som kör Windows Server 2008 R2 och senare.
- Det stöds endast av **MSOnline PowerShell-modulversion 1.1.166.0**. Ladda ned modulen med den här [länken](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).   
- Om inga AD DS-verktyg är installerade misslyckas `Initialize-ADSyncDomainJoinedComputerSync`.  Du kan installera AD DS-verktygen via serverhanteraren under Funktioner – Verktyg för fjärrserveradministration – Rolladministrationsverktyg.

För domänkontrollanter som kör Windows Server 2008 eller tidigare versioner använder du skriptet nedan för att skapa tjänstanslutningspunkten.

I en konfiguration med flera skogar ska du använda följande skript för att skapa tjänstanslutningspunkten i varje skog där det finns datorer:
 
    $verifiedDomain = "contoso.com"    # Replace this with any of your verified domain names in Azure AD
    $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47"    # Replace this with you tenant ID
    $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com"    # Replace this with your AD configuration naming context

    $de = New-Object System.DirectoryServices.DirectoryEntry
    $de.Path = "LDAP://CN=Services," + $configNC
    $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
    $deDRC.CommitChanges()

    $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
    $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
    $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

    $deSCP.CommitChanges()

I skriptet ovan

- är `$verifiedDomain = "contoso.com"` en platshållare som du måste ersätta med något av dina verifierade domännamn i Azure AD. Du måste äga domänen för att kunna använda den.

Mer information om verifierade domännamn finns i [Lägga till ett anpassat domännamn i Azure Active Directory](../active-directory-domains-add-azure-portal.md).  
Om du vill hämta en lista över verifierade företagsdomäner kan du använda cmdleten [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0). 

![Get-AzureADDomain](./media/hybrid-azuread-join-manual-steps/01.png)

## <a name="setup-issuance-of-claims"></a>Konfigurera utfärdande av anspråk

I en federerad Azure AD-konfiguration förlitar sig enheter på Active Directory Federation Services (AD FS) eller en lokal federationstjänst från tredje part för att autentisera till Azure AD. Enheter autentiseras för att få en åtkomsttoken för att registrera sig till Azure Active Directory Device Registration Service (Azure DRS).

Aktuella Windows-enheter autentiseras med integrerad Windows-autentisering till en aktiv WS-Trust-slutpunkt (antingen version 1.3 eller 2005) som har den lokala federationstjänsten som värd.

> [!NOTE]
> När du använder AD FS måste antingen **adfs/services/trust/13/windowstransport** eller **adfs/services/trust/2005/windowstransport** aktiveras. Om du använder proxy för webbautentisering ska du också se till att slutpunkten publiceras via proxyn. Du kan se vilka slutpunkter som är aktiverade via AD FS-hanteringskonsolen under **Tjänst > Slutpunkter**.
>
>Om du inte har AD FS som en lokal federationstjänst följer du instruktionerna för din leverantör för att kontrollera att den stöder WS-Trust 1.3- eller 2005-slutpunkter och att de har publicerats via MEX-filen (Metadata Exchange).

Följande anspråk måste finnas i den token som tas emot av Azure DRS för att enhetsregistreringen ska slutföras. Azure DRS skapar ett enhetsobjekt i Azure AD med en del av den här informationen som sedan används av Azure AD Connect för att associera det nyligen skapade enhetsobjektet med datorkontot lokalt.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Om du har mer än ett verifierat domännamn måste du ange följande anspråk för datorer:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Om du redan utfärdar ett ImmutableID-anspråk (t.ex. alternativt inloggnings-ID) måste du tillhandahålla ett motsvarande anspråk för datorer:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

I följande avsnitt hittar du information om:
 
- Värdena som varje anspråk ska ha
- Hur en definition skulle se ut i AD FS

Definitionen hjälper dig att verifiera om värdena finns eller om du behöver skapa dem.

> [!NOTE]
> Om du inte använder AD FS för din lokala federationsserver ska du följa leverantörens instruktioner för att skapa en lämplig konfiguration för att utfärda anspråken.

### <a name="issue-account-type-claim"></a>Utfärda kontotypsanspråk

**`http://schemas.microsoft.com/ws/2012/01/accounttype`** – Det här anspråket måste innehålla värdet **DJ**, som identifierar enheten som en domänansluten dator. I AD FS kan du lägga till en utfärdanderegel för transformering som ser ut så här:

    @RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Utfärda objectGUID för datorkontot lokalt

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`** – Det här anspråket måste innehålla värdet **objectGUID** på det lokala datorkontot. I AD FS kan du lägga till en utfärdanderegel för transformering som ser ut så här:

    @RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Utfärda objectSID för datorkontot lokalt

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`** – Det här anspråket måste innehålla värdet **objectSid** på det lokala datorkontot. I AD FS kan du lägga till en utfärdanderegel för transformering som ser ut så här:

    @RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>Utfärda issuerID för dator med flera verifierade domännamn i Azure AD

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`** – Det här anspråket måste innehålla URI (Uniform Resource Identifier) för något av de verifierade domännamnen som ansluts med den lokala federationstjänsten (AD FS eller tredje part) som utfärdar token. I AD FS kan du lägga till utfärdande av transformeringsregler som ser ut som dem nedan i den specifika ordningen efter de ovanstående. Observera att en regel uttryckligen måste utfärda regeln för användaren. I reglerna nedan läggs en första regel till som identifierar användar- och datorautentisering.

    @RuleName = "Issue account type with the value User when its not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://<verified-domain-name>/adfs/services/trust/"
    );


I anspråket ovan

- är `<verified-domain-name>` en platshållare som du måste ersätta med något av dina verifierade domännamn i Azure AD. Exempelvis, värde = ”http://contoso.com/adfs/services/trust/”



Mer information om verifierade domännamn finns i [Lägga till ett anpassat domännamn i Azure Active Directory](../active-directory-domains-add-azure-portal.md).  

Om du vill hämta en lista över verifierade företagsdomäner kan du använda cmdleten [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0). 

![Get-MsolDomain](./media/hybrid-azuread-join-manual-steps/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>Utfärda ImmutableID för datorn när en användare finns (t.ex. när alternativt inloggnings-ID har angetts)

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`** – Det här anspråket måste innehålla ett giltigt värde för datorer. I AD FS kan du skapa en utfärdanderegel för transformering enligt följande:

    @RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Hjälpskript för att skapa utfärdande av transformeringsregler för AD FS

Med följande skript kan du skapa utfärdande av transformeringsregler som beskrivs ovan.

    $multipleVerifiedDomainNames = $false
    $immutableIDAlreadyIssuedforUsers = $false
    $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains
    
    $rule1 = '@RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );'

    $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'

    $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);'

    $rule4 = ''
    if ($multipleVerifiedDomainNames -eq $true) {
    $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
    );'
    }

    $rule5 = ''
    if ($immutableIDAlreadyIssuedforUsers -eq $true) {
    $rule5 = '@RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'
    }

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

### <a name="remarks"></a>Kommentarer 

- Det här skriptet lägger till reglerna till befintliga regler. Kör inte skriptet två gånger, eftersom uppsättningen med regler då läggs till två gånger. Kontrollera att det inte finns några motsvarande regler för anspråken (under motsvarande villkor) innan du kör skriptet igen.

- Om du har flera verifierade domännamn (som visas i Azure AD-portalen via cmdleten Get-MsolDomains) ställer du in värdet **$multipleVerifiedDomainNames** i skriptet på **$true**. Se också till att ta bort eventuellt befintligt anspråk som kan ha skapats av Azure AD Connect eller på andra sätt. Här är ett exempel för den här regeln:


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Om du redan har utfärdat ett **ImmutableID**-anspråk för användarkonton ställer du in värdet **$immutableIDAlreadyIssuedforUsers** i skriptet på **$true**.

## <a name="enable-windows-down-level-devices"></a>Aktivera äldre Windows-enheter

Om några av dina domänanslutna enheter är äldre Windows-enheter måste du:

- Ställ in en princip i Azure AD för att göra det möjligt för användare att registrera enheter.
 
- Konfigurera din lokala federationstjänst för att utfärda anspråk för att stödja **Integrated Windows Authentication (IWA)** för enhetsregistrering.
 
- Lägg till slutpunkten för Azure AD-enhetsautentisering till de lokala intranätzonerna för att undvika certifikatuppmaningar när enheten ska autentiseras.

- Kontrollera äldre Windows-enheter 


### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Ställa in en princip i Azure AD för att göra det möjligt för användare att registrera enheter

Om du vill registrera äldre Windows-enheter måste du se till att inställningarna tillåter användarna att registrera enheter i Azure AD. Du hittar den här informationen i Azure-portalen under:

`Azure Active Directory > Users and groups > Device settings`
    
Följande princip måste vara inställd på **Alla**: **Användare kan registrera sina enheter med Azure AD**

![Registrera enheter](./media/hybrid-azuread-join-manual-steps/23.png)


### <a name="configure-on-premises-federation-service"></a>Konfigurera lokal federationstjänst 

Den lokala federationstjänsten måste ha stöd för utfärdande av anspråken **authenticationmethod** och **wiaormultiauthn** när du tar emot en autentiseringsbegäran till den Azure AD-förlitande part som har parametrarna resouce_params med ett kodat värde som visas nedan:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

När en sådan begäran kommer måste den lokala federationstjänsten autentisera användaren med integrerad Windows-autentisering. När det har gjorts ska följande två anspråk utfärdas:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

I AD FS måste du lägga till en utfärdanderegel för transformering som klarar autentiseringsmetoden.  

**Så här lägger du till regeln:**

1. I AD FS-hanteringskonsolen går du till `AD FS > Trust Relationships > Relying Party Trusts`.
2. Högerklicka på förtroendeobjektet som förlitar sig på Microsoft Office 365-identitetsplattformen och välj **Edit Claim Rules** (Redigera anspråksregler).
3. På fliken **Issuance Transform Rules** (Regler för utfärdandetransformering) väljer du **Lägg till regel**.
4. I mallistan **Claim rule** (Anspråksregel) väljer du **Skicka anspråk med hjälp av en anpassad regel**.
5. Välj **Nästa**.
6. I rutan **Claim rule name** (Anspråksregelnamn) skriver du **Auth Method Claim Rule** (Anspråksregel för autentiseringsmetod).
7. I rutan **Claim rule** (Anspråksregel) skriver du följande regel:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. På federationsservern skriver du PowerShell-kommandot nedan när du har ersatt **\<RPObjectName\>** med objektnamnet på den förlitande parten för din Azure AD-förlitande parts förtroendeobjekt. Det här objektet heter vanligtvis **Microsoft Office 365-identitetsplattformen**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>Lägg till slutpunkten för Azure AD-enhetsautentisering till de lokala intranätzonerna

Om du vill undvika certifikatuppmaningar när användare som registrerar enheter ska autentiseras till Azure AD kan du skicka en princip till dina domänanslutna enheter. Det gör du för att lägga till följande webbadresser till den lokala intranätzonen i Internet Explorer:

`https://device.login.microsoftonline.com`


### <a name="control-windows-down-level-devices"></a>Kontrollera äldre Windows-enheter 

För att registrera äldre Windows-enheter måste du ladda ned och installera ett Windows Installer-paket (.msi) från Download Center. Klicka [här](hybrid-azuread-join-control.md#control-windows-down-level-devices) för mer information. 



## <a name="verify-joined-devices"></a>Verifiera anslutna enheter

Du kan titta på anslutna enheter i din organisation med cmdleten [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) i [Azure Active Directory PowerShell-modulen](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Cmdletens utdata visar enheter som är registrerade och anslutna till Azure AD. För att hämta alla enheter använder du parametern **-All** (-Alla) och filtrerar dem sedan med egenskapen **deviceTrustType**. Domänanslutna enheter har värdet **Domänansluten**.



## <a name="troubleshoot-your-implementation"></a>Felsöka din implementering

Om du har problem med att slutföra Hybrid Azure AD-anslutningen för domänanslutna Windows-enheter kan du läsa:

- [Felsöka Hybrid Azure AD-anslutningen för aktuella Windows-enheter](troubleshoot-hybrid-join-windows-current.md)
- [Felsöka Hybrid Azure AD-anslutningen för äldre Windows-enheter](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Nästa steg

* [Introduktion till enhetshantering i Azure Active Directory](overview.md)



<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual-steps/12.png
