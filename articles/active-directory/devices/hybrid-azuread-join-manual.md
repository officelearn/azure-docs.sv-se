---
title: Konfigurera anslutna Azure Active Directory-hybridenheter manuellt| Microsoft Docs
description: Lär dig att konfigurera Azure Active Directory-hybridanslutna enheter manuellt.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 6aae96320f8bb4c34fa172c54302bb69ea5799eb
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54447555"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Självstudier: Konfigurera Azure Active Directory-hybridanslutna enheter manuellt 

Med enhetshanteringen i Azure Active Directory (Azure AD) kan du se till att användarna har tillgång till dina resurser från enheter som uppfyller dina krav för säkerhet och kompatibilitet. Mer information finns i [Introduktion till enhetshantering i Azure Active Directory](overview.md).


> [!TIP]
> Om användning av Azure AD Connect är ett möjligt alternativ för dig kan du läsa relaterade självstudier för [hanterade](hybrid-azuread-join-managed-domains.md) eller [federerade](hybrid-azuread-join-federated-domains.md) domäner. Genom att använda Azure AD Connect kan du avsevärt förenkla konfigurationen av Hybrid Azure AD-anslutningen.

Om du har en lokal Active Directory-miljö och du vill ansluta dina domänanslutna enheter till Azure AD kan du göra det genom att konfigurera Hybrid Azure AD-anslutna enheter. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera Azure AD-hybridanslutning manuellt
> * Konfigurera en tjänstanslutningspunkt
> * Konfigurera utfärdande av anspråk
> * Aktivera äldre Windows-enheter
> * Verifiera anslutna enheter
> * Felsöka din implementering
 




## <a name="prerequisites"></a>Nödvändiga komponenter

I den här självstudien förutsätts att du känner till:
    
-  [Introduktion till enhetshantering i Azure Active Directory](../device-management-introduction.md)    
-  [Planera implementeringen av Azure Active Directory-hybridanslutning](hybrid-azuread-join-plan.md)
-  [Kontrollera Azure AD Join-hybriden för dina enheter](hybrid-azuread-join-control.md)


Innan du börjar aktivera Hybrid Azure AD-anslutna enheter i din organisation ser du till att:

- Du kör en uppdaterad version av Azure AD Connect.
- Azure AD Connect har synkroniserat datorobjekten för de enheter som du vill ska vara Hybrid Azure AD-anslutna till Azure AD. Om datorobjekten tillhör specifika organisationsenheter (OU) måste även organisationsenheterna konfigureras för synkronisering i Azure AD Connect.

  

Azure AD Connect:

- Behåller associationen mellan datorkontot i din lokala Active Directory-instans och enhetsobjektet i Azure AD. 
- Aktiverar andra enhetsrelaterade funktioner såsom Windows Hello för företag.

Kontrollerar att följande URL:er är åtkomliga från datorer i organisationens nätverk för registrering av datorer till Azure AD:

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- Organisationens STS (för federerade domäner), som ska ingå i användarens inställningar för lokalt intranät

Om organisationen planerar att använda sömlös enkel inloggning måste följande URL kunna nås från datorerna i organisationen. Den måste även läggas till i användarens lokala intranätzon.

- https://autologon.microsoftazuread-sso.com

Dessutom ska följande inställning vara aktiverad i användarens intranätzon: ”Allow status bar updates via script” (Tillåt uppdatering av statusfältet via skript).

Om din organisation använder hanterad (icke-federerad) konfiguration med lokal Active Directory och inte använder Active Directory Federation Services (AD FS) för att federera med Azure AD förlitar sig Azure AD-hybridanslutningen i Windows 10 på datorobjekten i Active Directory för att synkroniseras till Azure AD. Kontrollera att OU som innehåller de datorobjekt som måste vara Azure AD-hybridanslutna är aktiverade för synkronisering i Azure AD Connect-synkroniseringskonfigurationen.

För Windows 10-enheter med version 1703 eller tidigare måste du implementera Web Proxy Auto-Discovery (WPAD) för att göra det möjligt för Windows 10-datorer att registreras till Azure AD om din organisation kräver åtkomst till Internet via en utgående proxy. 

Från och med Windows 10 1803 försöker enheten att slutföra Azure AD-hybridanslutningen med hjälp av den synkroniserade datorn/enheten även om ett försök till en Azure AD-hybridanslutning från en enhet i en federerad domän via AD FS misslyckas, och om Azure AD Connect är konfigurerat för att synkronisera dator-/enhetsobjekten till Azure AD.

## <a name="verify-configuration-steps"></a>Verifiera konfigurationssteg

Du kan konfigurera Hybrid Azure AD-anslutna enheter för olika typer av plattformar för Windows-enheter. Det här avsnittet innehåller de steg som krävs för alla vanliga konfigurationsscenarier.  

Använd följande tabell för att få en översikt över stegen som krävs för ditt scenario:  



| Steg                                      | Aktuell Windows hash-synkronisering av lösenord | Aktuell Windows och federation | Äldre Windows-enheter |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Konfigurera en tjänstanslutningspunkt | ![Markera][1]                            | ![Markera][1]                    | ![Markera][1]        |
| Konfigurera utfärdande av anspråk           |                                        | ![Markera][1]                    | ![Markera][1]        |
| Aktivera enheter som inte använder Windows 10      |                                        |                                | ![Markera][1]        |
| Verifiera anslutna enheter          | ![Markera][1]                            | ![Markera][1]                    | ![Markera][1]        |



## <a name="configure-a-service-connection-point"></a>Konfigurera en tjänstanslutningspunkt

Dina enheter använder en tjänstanslutningspunkt (SCP) under registreringen för att upptäcka information om Azure AD-klientorganisation. I din lokala Active Directory-instans måste SCP-objektet för Azure AD-hybridanslutna enheter finnas i kontextpartitionen för konfigurationsnamngivningen för datorns skog. Det finns bara en kontext för konfigurationsnamngivning per skog. I en Active Directory-konfiguration med flera skogar måste tjänstanslutningspunkten finnas i alla skogar som innehåller domänanslutna datorer.

Du kan använda cmdleten [**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) för att hämta skogens kontext för konfigurationsnamngivning.  

För en skog med Active Directory-domännamnet *fabrikam.com*, är kontexten för konfigurationsnamngivning:

`CN=Configuration,DC=fabrikam,DC=com`

I din skog finns SCP-objektet för automatisk registrering av domänanslutna enheter här:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Beroende på hur du har distribuerat Azure AD Connect kan SCP-objektet redan ha konfigurerats.
Du kan kontrollera huruvida objektet finns och hämta identifieringsvärdena med hjälp av följande Windows PowerShell-skript: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

**$scp.Keywords**-utdata visar information om Azure AD-klientorganisation. Här är ett exempel:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Om tjänstanslutningspunkten inte finns kan du skapa den genom att köra cmdleten `Initialize-ADSyncDomainJoinedComputerSync` på din Azure AD Connect-server. Autentiseringsuppgifter för företagsadministratör krävs för att köra den här cmdleten.  

Cmdleten:

- Skapar tjänstanslutningspunkten i den Active Directory-skog som Azure AD Connect är anslutet till. 
- Kräver att du anger parametern `AdConnectorAccount`. Det här kontot är konfigurerat som Active Directory-anslutningskonto i Azure AD Connect. 


Följande skript visar ett exempel på användning av cmdleten. I det här skriptet kräver `$aadAdminCred = Get-Credential` att du skriver ett användarnamn. Du måste ange användarnamnet i UPN-format (user principal name) (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

Cmdleten `Initialize-ADSyncDomainJoinedComputerSync`:

- Använder Active Directory PowerShell-modulen och Azure Active Directory Domain Services-verktyg (Azure AD DS). De här verktygen är beroende av Active Directory-webbtjänster som körs på en domänkontrollant. Active Directory Web Services fungerar på domänkontrollanter som kör Windows Server 2008 R2 och senare.
- Det stöds endast av MSOnline PowerShell-modulversion 1.1.166.0. Ladda ned modulen via [den här länken](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).   
- Om Azure AD DS-verktygen inte är installerade misslyckas `Initialize-ADSyncDomainJoinedComputerSync`. Du kan installera AD DS-verktygen via Serverhanteraren under **Funktioner** > **Verktyg för fjärrserveradministration** > **Rolladministrationsverktyg**.

För domänkontrollanter som kör Windows Server 2008 eller tidigare versioner använder du följande skript för att skapa tjänstanslutningspunkten. I en konfiguration med flera skogar använder du följande skript för att skapa tjänstanslutningspunkten i varje skog där det finns datorer.
 
    $verifiedDomain = "contoso.com"    # Replace this with any of your verified domain names in Azure AD
    $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47"    # Replace this with you tenant ID
    $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com"    # Replace this with your Active Directory configuration naming context

    $de = New-Object System.DirectoryServices.DirectoryEntry
    $de.Path = "LDAP://CN=Services," + $configNC
    $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
    $deDRC.CommitChanges()

    $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
    $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
    $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

    $deSCP.CommitChanges()

I föregående skript är `$verifiedDomain = "contoso.com"` en platshållare. Ersätt den med något av dina verifierade domännamn i Azure AD. Du måste äga domänen för att kunna använda den.

Mer information om verifierade domännamn finns i [Lägga till ett anpassat domännamn i Azure Active Directory](../active-directory-domains-add-azure-portal.md). 

Om du vill hämta en lista över verifierade företagsdomäner kan du använda cmdleten [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0). 

![Lista över företagsdomäner](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>Konfigurera utfärdande av anspråk

I en federerad Azure AD-konfiguration förlitar sig enheter på AD FS eller en lokal federationstjänst från en Microsoft-partner för att autentisera till Azure AD. Enheter autentiseras för att få en åtkomsttoken för att registrera sig till Azure Active Directory Device Registration Service (Azure DRS).

Aktuella Windows-enheter autentiseras med hjälp av integrerad Windows-autentisering till en aktiv WS-Trust-slutpunkt (antingen version 1.3 eller 2005) som har den lokala federationstjänsten som värd.

> [!NOTE]
> När du använder AD FS måste antingen **adfs/services/trust/13/windowstransport** eller **adfs/services/trust/2005/windowstransport** aktiveras. Om du använder proxy för webbautentisering ska du även se till att slutpunkten publiceras via proxyn. Du kan se vilka slutpunkter som är aktiverade via AD FS-hanteringskonsolen under **Tjänst** > **Slutpunkter**.
>
>Om du inte har AD FS som en lokal federationstjänst följer du instruktionerna från din leverantör för att kontrollera att den stöder WS-Trust 1.3- eller 2005-slutpunkter och att de har publicerats via MEX-filen (Metadata Exchange).

För att enhetsregistrering ska kunna slutföras måste följande anspråk finnas i den token som tas emot av Azure DRS. Azure DRS skapar ett enhetsobjekt i Azure AD med några av de här uppgifterna. Azure AD Connect använder sedan den här informationen för att associera det nyligen skapade enhetsobjektet med det lokala datorkontot.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Om du har mer än ett verifierat domännamn måste du ange följande anspråk för datorer:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Om du redan utfärdar ett ImmutableID-anspråk (till exempel alternativt inloggnings-ID) måste du tillhandahålla ett motsvarande anspråk för datorer:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

I följande avsnitt hittar du information om:
 
- De värden som varje anspråk ska ha.
- Hur en definition skulle se ut i AD FS.

Definitionen hjälper dig att verifiera om värdena finns eller om du behöver skapa dem.

> [!NOTE]
> Om du inte använder AD FS för din lokala federationsserver ska du följa leverantörens instruktioner för att skapa en lämplig konfiguration för att utfärda anspråken.

### <a name="issue-account-type-claim"></a>Utfärda kontotypsanspråk

Anspråket `http://schemas.microsoft.com/ws/2012/01/accounttype` måste innehålla värdet **DJ**, som identifierar enheten som en domänansluten dator. I AD FS kan du lägga till en utfärdanderegel för transformering som ser ut så här:

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

Anspråket `http://schemas.microsoft.com/identity/claims/onpremobjectguid` måste innehålla värdet **objectGUID** på det lokala datorkontot. I AD FS kan du lägga till en utfärdanderegel för transformering som ser ut så här:

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

Anspråket `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` måste innehålla värdet **objectSid** på det lokala datorkontot. I AD FS kan du lägga till en utfärdanderegel för transformering som ser ut så här:

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

### <a name="issue-issuerid-for-the-computer-when-multiple-verified-domain-names-are-in-azure-ad"></a>Utfärda issuerID för datorn när flera verifierade domännamn finns i Azure AD

Anspråket `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` måste innehålla URI (Uniform Resource Identifier) för något av de verifierade domännamnen som ansluts med den lokala federationstjänst (AD FS eller partner) som utfärdar token. I AD FS kan du lägga till utfärdande av transformeringsregler som ser ut som de följande i den specifika ordningen efter de föregående. Observera att det krävs en regel för att uttryckligen utfärda regeln för användare. I följande regler läggs det till en första regel som identifierar användaren mot datorautentisering.

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


I föregående anspråk är `<verified-domain-name>` en platshållare. Ersätt den med något av dina verifierade domännamn i Azure AD. Använd till exempel `Value = "http://contoso.com/adfs/services/trust/"`.



Mer information om verifierade domännamn finns i [Lägga till ett anpassat domännamn i Azure Active Directory](../active-directory-domains-add-azure-portal.md).  

Om du vill hämta en lista över verifierade företagsdomäner kan du använda cmdleten [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0). 

![Lista över företagsdomäner](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-an-alternate-login-id-is-set"></a>Utfärda ImmutableID för datorn när det finns ett för användare (till när ett alternativt inloggnings-ID har angetts)

Anspråket `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID` måste innehålla ett giltigt värde för datorer. I AD FS kan du skapa en utfärdanderegel för transformering enligt följande:

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

Med följande skript kan du skapa utfärdande av transformeringsregler såsom beskrivits tidigare.

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

#### <a name="remarks"></a>Kommentarer 

- Det här skriptet lägger till reglerna till befintliga regler. Kör inte skriptet två gånger, eftersom regeluppsättningen då läggs till två gånger. Kontrollera att det inte finns några motsvarande regler för anspråken (under motsvarande villkor) innan du kör skriptet igen.

- Om du har flera verifierade domännamn (som visas i Azure AD-portalen eller via cmdleten **Get-MsolDomain**) anger du värdet **$multipleVerifiedDomainNames** i skriptet till **$true**. Se även till att ta bort eventuellt befintligt **issuerid**-anspråk som kan ha skapats av Azure AD Connect eller på andra sätt. Här är ett exempel för den här regeln:

      c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Om du redan har utfärdat ett **ImmutableID**-anspråk för användarkonton ställer du in värdet **$immutableIDAlreadyIssuedforUsers** i skriptet på **$true**.

## <a name="enable-windows-down-level-devices"></a>Aktivera äldre Windows-enheter

Om några av dina domänanslutna enheter är äldre Windows-enheter måste du:

- Ställ in en princip i Azure AD för att göra det möjligt för användare att registrera enheter. 
- Konfigurera din lokala federationstjänst för att utfärda anspråk för att stödja Integrated Windows Authentication (IWA) för enhetsregistrering. 
- Lägg till slutpunkten för Azure AD-enhetsautentisering till de lokala intranätzonerna för att undvika certifikatuppmaningar när enheten ska autentiseras.
- Kontrollera äldre Windows-enheter. 


### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Ange en princip i Azure AD för att göra det möjligt för användare att registrera enheter

Om du vill registrera äldre Windows-enheter ser du till att den inställning som tillåter användarna att registrera enheter i Azure AD är aktiverad. I Azure-portalen hittar du den här inställningen under **Azure Active Directory** > **Användare och grupper** > **Enhetsinställningar**.
    
Följande princip måste anges som **Alla**: **Användarna kan registrera sina enheter med Azure AD**.

![Knappen Alla som gör att användarna kan registrera enheter](./media/hybrid-azuread-join-manual/23.png)


### <a name="configure-the-on-premises-federation-service"></a>Konfigurera den lokala federationstjänsten 

Den lokala federationstjänsten måste ha stöd för utfärdande av anspråken **authenticationmethod** och **wiaormultiauthn** när den tar emot en autentiseringsbegäran till den Azure AD-förlitande part som en resource_params-parameter med följande kodat värde:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

När en sådan begäran inkommer måste den lokala federationstjänsten autentisera användaren med hjälp av integrerad Windows-autentisering. När autentiseringen lyckas måste federationstjänsten utfärda följande två anspråk:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

I AD FS måste du lägga till en utfärdanderegel för transformering som skickas genom autentiseringsmetoden. Så här lägger du till regeln:

1. I AD FS-hanteringskonsolen går du till **AD FS** > **Betrodda relationer** > **Förlitande partsförtroenden**.
2. Högerklicka på förtroendeobjektet som förlitar sig på Microsoft Office 365-identitetsplattformen och välj **Edit Claim Rules** (Redigera anspråksregler).
3. På fliken **Issuance Transform Rules** (Regler för utfärdandetransformering) väljer du **Lägg till regel**.
4. I mallistan **Claim rule** (Anspråksregel) väljer du **Skicka anspråk med hjälp av en anpassad regel**.
5. Välj **Nästa**.
6. I rutan **Claim rule name** (Anspråksregelnamn) anger du **Auth Method Claim Rule** (Anspråksregel för autentiseringsmetod).
7. I rutan **Claim rule** (Anspråksregel) anger du följande regel:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. På din federationsserver ange du följande PowerShell-kommando. Ersätt **\<RPObjectName\>** med objektnamnet på förlitande part för förtroendeobjektet för din Azure AD-förlitande part. Det här objektet heter vanligtvis **Microsoft Office 365-identitetsplattformen**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>Lägg till slutpunkten för Azure AD-enhetsautentisering till de lokala intranätzonerna

Om du vill undvika certifikatuppmaningar när användare av registrerade enheter autentiserar till Azure AD kan du skicka en princip till dina domänanslutna enheter. Det gör du för att lägga till följande URL till den lokala intranätzonen i Internet Explorer:

`https://device.login.microsoftonline.com`


### <a name="control-windows-down-level-devices"></a>Kontrollera äldre Windows-enheter 

För att registrera äldre Windows-enheter måste du ladda ned och installera ett Windows Installer-paket (.msi) från Download Center. Mer information finns i [Kontrollera Azure AD-anslutningshybriden för dina enheter](hybrid-azuread-join-control.md#control-windows-down-level-devices). 



## <a name="verify-joined-devices"></a>Verifiera anslutna enheter

Du kan leta efter anslutna enheter i din organisation med hjälp av cmdleten [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) i [Azure Active Directory PowerShell-modulen](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Cmdletens utdata visar enheter som är registrerade och anslutna till Azure AD. För att hämta alla enheter använder du parametern **-All** (-Alla) och filtrerar dem sedan med egenskapen **deviceTrustType**. Domänanslutna enheter har värdet **Domänansluten**.



## <a name="troubleshoot-your-implementation"></a>Felsöka din implementering

Om du har problem med att slutföra Azure AD-hybridanslutningen för domänanslutna Windows-enheter kan du läsa:

- [Felsöka Hybrid Azure AD-anslutningen för aktuella Windows-enheter](troubleshoot-hybrid-join-windows-current.md)
- [Felsöka Hybrid Azure AD-anslutningen för äldre Windows-enheter](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Nästa steg

* [Introduktion till enhetshantering i Azure Active Directory](overview.md)



<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png
