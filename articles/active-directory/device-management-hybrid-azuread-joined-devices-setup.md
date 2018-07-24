---
title: Hur du konfigurerar hybrid Azure Active Directory-anslutna enheter | Microsoft Docs
description: Lär dig mer om att konfigurera hybrid Azure Active Directory-anslutna enheter.
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
ms.topic: article
ms.date: 03/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 3fbf47eca2c623e55ff5e70095c04f16a1a1b3e6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215170"
---
# <a name="how-to-configure-hybrid-azure-active-directory-joined-devices"></a>Konfigurera hybrid Azure Active Directory-anslutna enheter

Med enhetshantering i Azure Active Directory (AD Azure), kan du se till att dina användare kommer åt dina resurser från enheter som uppfyller dina krav för säkerhet och efterlevnad. Mer information finns i [introduktionen till enhetshantering i Azure Active Directory](device-management-introduction.md).

Om du har en lokal Active Directory-miljö och du vill ansluta till dina domänanslutna enheter till Azure AD, kan du göra detta genom att konfigurera hybrid Azure AD-anslutna enheter. Den här artikeln ger relaterade steg. 


## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar konfigurera hybrid Azure AD-anslutna enheter i din miljö, bör du bekanta dig med scenarierna som stöds och begränsningar.  

Om du lita på den [systemförberedelseverktyget (Sysprep)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc721940(v=ws.10)), kontrollera att du skapar bilder från en installation av Windows som har ännu inte registrerats med Azure AD.

Alla domänanslutna enheter som kör Windows 10 Anniversary Update och Windows Server 2016 registreras automatiskt med Azure AD vid omstart av enheten eller användaren logga in när de konfigurationssteg som anges nedan är uppfyllda. **Om problemet automatiskt registrera inte rekommenderas, eller om en kontrollerad distribution är det önskade**, genom att följa anvisningarna i avsnittet ”steg 4: Kontrollera distributionen och distribution” nedan först för att selektivt aktivera eller inaktivera automatisk distribution innan efter de andra konfigurationssteg.  

För att förbättra läsbarheten beskrivningar, använder den här artikeln följande villkor: 

- **Befintliga Windows-enheter** – den här termen avser domänanslutna enheter som kör Windows 10 eller Windows Server 2016.
- **Windows äldre enheter** -den här termen avser alla **stöds** domänanslutna Windows-enheter som är varken som kör Windows 10 eller Windows Server 2016.  

### <a name="windows-current-devices"></a>Befintliga Windows-enheter

- För enheter som kör Windows operativsystem, versionen som stöds är Windows 10 Anniversary Update (version 1607) eller senare. 
- Registreringen av befintliga Windows-enheter **är** i icke-federerade miljöer, till exempel lösenordshash-synkronisering konfigurationer som stöds.  


### <a name="windows-down-level-devices"></a>Windows äldre enheter

- Följande Windows äldre enheter stöds:
    - Windows 8.1
    - Windows 7
    - Windows Server 2012 R2
    - Windows Server 2012
    - Windows Server 2008 R2
- Registreringen av Windows äldre enheter **är** stöd i icke-federerade miljöer via sömlös enkel inloggning [Azure Active Directory sömlös enkel inloggning](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start). 
- Registreringen av Windows äldre enheter **är inte** stöds när du använder Azure AD-direktautentisering utan sömlös enkel inloggning.
- Registreringen av Windows äldre enheter **är inte** stöds för enheter med hjälp av centrala profiler. Om du lita på centrala profiler eller inställningar, använder du Windows 10.



## <a name="prerequisites"></a>Förutsättningar

Innan du börjar att aktivera hybrid Azure AD-anslutna enheter i din organisation, måste du se till att:

- Du kör en uppdaterad version av Azure AD connect.

- Azure AD connect har synkroniserats datorobjekt av de enheter som du vill ska vara hybrid Azure AD-anslutna till Azure AD. Anslut även om datorobjekt som tillhör specifika organisationsenheter (OU) dessa organisationsenheter måste konfigureras för synkronisering i Azure AD.

  

Azure AD Connect:

- Behåller associationen mellan datorkontot i din lokala Active Directory (AD) och enhetsobjekt i Azure AD. 
- Gör andra enheten relaterade funktioner som Windows Hello för företag.

Kontrollera att följande URL: er är tillgängliga från datorer i din organisationsnätverk för registrering av datorer till Azure AD:

- https://enterpriseregistration.windows.net

- https://login.microsoftonline.com Tillåt
- https://device.login.microsoftonline.com

- Din organisations STS (federerade domäner)

Om du inte redan har gjort, ska din organisations STS (för federerade domäner) ingå i användarinställningar lokalt intranät.

Om din organisation planerar att använda sömlös SSO, följande webbadresser som måste kunna nås från datorer i organisationen och de måste också läggas till användarens lokala intranätzonen:

- https://autologon.microsoftazuread-sso.com

- Dessutom följande inställning måste vara aktiverat i intranätzonen för användarens: ”Tillåt status uppdateras via skript”.

Om din organisation använder hanterade (icke-federerade) installation med lokala AD och inte använder AD FS för att federera med Azure AD, och sedan hybrid Azure AD-anslutning i Windows 10 är beroende av datorobjekt i AD för att vara sync'ed till Azure AD. Se till att alla organisationens enheter (OU) som innehåller datorobjekt som måste vara hybrid Azure AD-anslutna är aktiverad för synkronisering i Azure AD Connect sync-konfiguration.

Om din organisation kräver åtkomst till Internet via en utgående proxy för Windows 10-enheter på version 1703 eller tidigare måste du implementera Web Proxy Auto-Discovery (WPAD) för att aktivera Windows 10-datorer att registrera till Azure AD. 

## <a name="configuration-steps"></a>Konfigurationssteg

Du kan konfigurera hybrid Azure AD-anslutna enheter för olika typer av plattformar för Windows-enheter. Det här avsnittet innehåller steg som krävs för alla scenarier för typisk konfiguration.  

Använd tabellen nedan för att få en översikt över de steg som krävs för ditt scenario:  



| Steg                                      | Windows aktuella och lösenord hash-synkronisering | Windows aktuella och federation | Windows klientversioner |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Steg 1: Konfigurera tjänstanslutningspunkten | ![Markera][1]                            | ![Markera][1]                    | ![Markera][1]        |
| Steg 2: Konfigurera utfärdande av anspråk           |                                        | ![Markera][1]                    | ![Markera][1]        |
| Steg 3: Aktivera Windows 10-enheter      |                                        |                                | ![Markera][1]        |
| Steg 4: Kontrollera distributionen     | ![Markera][1]                            | ![Markera][1]                    | ![Markera][1]        |
| Steg 5: Kontrollera domänanslutna enheter          | ![Markera][1]                            | ![Markera][1]                    | ![Markera][1]        |



## <a name="step-1-configure-service-connection-point"></a>Steg 1: Konfigurera tjänstanslutningspunkten

Tjänsten anslutningsobjektet punkt (SCP) används av dina enheter under registreringen för att identifiera Azure AD-klient-information. SCP-objektet för hybrid Azure AD-anslutna enheter måste finnas i konfigurationen namnge kontext partition av datorns skog i din lokala Active Directory (AD). Det finns bara en konfigurationsnamngivningen per skog. Tjänstanslutningspunkten måste finnas i alla skogar som innehåller domänanslutna datorer i en konfiguration för flera skogar Active Directory.

Du kan använda den [ **Get-ADRootDSE** ](https://technet.microsoft.com/library/ee617246.aspx) cmdlet för att hämta konfigurationsnamngivningskontexten i skogen.  

För en skog med Active Directory-domännamn *fabrikam.com*, konfigurationsnamngivningen är:

`CN=Configuration,DC=fabrikam,DC=com`

I din skog finns SCP-objektet för automatisk registrering av domänanslutna enheter här:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Beroende på hur du har distribuerat Azure AD Connect, kan SCP-objektet redan har konfigurerats.
Du kan verifiera att objektet och hämta identifiering av värden med hjälp av följande Windows PowerShell-skript: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

Den **$scp. Nyckelord** utdata visar Azure AD-klient-information, till exempel:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Om tjänstanslutningspunkten inte finns, kan du skapa den genom att köra den `Initialize-ADSyncDomainJoinedComputerSync` cmdlet på din Azure AD Connect-servern. Enterprise-administratör autentiseringsuppgifter krävs för att köra denna cmdlet.  
Cmdlet: en:

- Skapar tjänstanslutningspunkten i Active Directory-skogen som Azure AD Connect är ansluten till. 
- Att ange den `AdConnectorAccount` parametern. Detta är det konto som har konfigurerats som Active Directory-anslutningskontot i Azure AD connect. 


Följande skript visar ett exempel för att använda cmdlet: en. I det här skriptet `$aadAdminCred = Get-Credential` måste ange ett användarnamn. Du måste ange användarnamnet i formatet användarens huvudnamn (UPN) (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

Den `Initialize-ADSyncDomainJoinedComputerSync` cmdlet:

- Använder Active Directory PowerShell-modulen och AD DS-verktyg som förlitar sig på Active Directory-webbtjänster som körs på en domänkontrollant. Active Directory Web Services fungerar på domänkontrollanter som kör Windows Server 2008 R2 och senare.
- Stöds endast av den **MSOnline PowerShell-Modulversion 1.1.166.0**. Använd det här alternativet om du vill hämta den här modulen [länk](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).   
- Om AD DS-verktyg inte installeras, den `Initialize-ADSyncDomainJoinedComputerSync` misslyckas.  AD DS-hanteringsverktyg kan installeras via Server Manager under funktioner - verktyg för fjärrserveradministration - Rolladministrationsverktyg.

Domänkontrollanter som kör Windows Server 2008 eller tidigare versioner, använda skriptet nedan för att skapa tjänstanslutningspunkten.

I en konfiguration med flera skogar, bör du använda följande skript för att skapa tjänstanslutningspunkten i varje skog där datorer finns:
 
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

I skriptet ovan,

- `$verifiedDomain = "contoso.com"` är en platshållare som du måste ersätta med någon av dina verifierat domännamn i Azure AD. Du måste äga domänen innan du kan använda den.

Mer information om verifierat domännamn finns i [lägga till ett anpassat domännamn i Azure Active Directory](active-directory-domains-add-azure-portal.md).  
Om du vill hämta en lista över verifierade företagets domäner, kan du använda den [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0) cmdlet. 

![Get-AzureADDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)

## <a name="step-2-setup-issuance-of-claims"></a>Steg 2: Konfigurera utfärdande av anspråk

I en federerad Azure AD-konfiguration enheter förlitar sig på Active Directory Federation Services (AD FS) eller en 3 parten den lokala federationstjänsten att autentisera till Azure AD. Enheter autentiseras för att få en åtkomsttoken för att registrera mot den Azure Active Directory Device Registration Service (Azure DRS).

Windows aktuella enheter ska autentiseras med integrerad Windows-autentisering till en aktiv slutpunkt för WS-Trust (1.3 eller 2005-versioner) värd för den lokala federationstjänsten.

> [!NOTE]
> När du använder AD FS är antingen **adfs/services/trust/13/windowstransport** eller **adfs/services/trust/2005/windowstransport** måste vara aktiverat. Om du använder en webbproxy för autentisering, se också till att den här slutpunkten har publicerats via proxy. Du kan se vilka slutpunkter är aktiverade via AD FS-hanteringskonsolen under **Service > slutpunkter**.
>
>Om du inte har AD FS som den lokala federationstjänsten, följer du anvisningarna för din leverantör för att se till att de stöder WS-Trust 1.3 eller 2005 slutpunkter och att dessa publiceras via Metadata Exchange-fil (MEX).

Följande anspråk måste finnas i den token som tas emot av Azure DRS för registrering av enheten för att slutföra. Azure DRS skapar ett enhetsobjekt i Azure AD med några av den här informationen som sedan används av Azure AD Connect för att associera det nyligen skapade enhetsobjektet med datorn konto lokala platser.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Om du har mer än ett verifierat domännamn, måste du ange följande anspråket för datorer:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Om du redan utfärdar en ImmutableID-anspråket (t.ex. alternativa inloggnings-ID) som du måste ange ett motsvarande anspråk för datorer:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

I följande avsnitt ser hitta du information om:
 
- Värden som ska ha varje anspråk
- Hur en definition skulle se ut i AD FS

Definitionen hjälper dig att kontrollera om värdena som är tillgänglig eller om du behöver skapa dem.

> [!NOTE]
> Om du inte använder AD FS för din lokala federationsserver, följer du leverantörens instruktioner för att skapa för att utfärda de här anspråken har konfigurerats korrekt.

### <a name="issue-account-type-claim"></a>Problemet konto typ av anspråk

**`http://schemas.microsoft.com/ws/2012/01/accounttype`** – Det här anspråket måste innehålla ett värde av **DJ**, som identifierar enheten som en domänansluten dator. I AD FS kan du lägga till en regel för omvandling av utfärdande som ser ut så här:

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

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Utfärda objectGUID av datorn konto lokala platser

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`** – Det här anspråket måste innehålla den **objectGUID** värdet för det lokala datorkontot. I AD FS kan du lägga till en regel för omvandling av utfärdande som ser ut så här:

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
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Utfärda objectSID av datorn konto lokala platser

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`** – Det här anspråket måste innehålla den **objectSid** värdet för det lokala datorkontot. I AD FS kan du lägga till en regel för omvandling av utfärdande som ser ut så här:

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

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>Utfärda issuerID för datorn när flera verifierat domännamn i Azure AD

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`** – Det här anspråket måste innehålla den identifierare URI (Uniform Resource) för någon av de verifierade domännamn som ansluter med den lokala federationstjänsten (AD FS eller 3 part) utfärda token. Du kan lägga till regler för utfärdandetransformering som ser ut som de nedan i den specifika ordningen efter de ovan i AD FS. Observera att en regel att uttryckligen utfärda regeln för användare är nödvändigt. En första regel som identifierar användare eller datorautentisering i reglerna nedan har lagts till.

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


I anspråket ovan,

- `<verified-domain-name>` är en platshållare som du måste ersätta med någon av dina verifierat domännamn i Azure AD. Till exempel värde = ”http://contoso.com/adfs/services/trust/”



Mer information om verifierat domännamn finns i [lägga till ett anpassat domännamn i Azure Active Directory](active-directory-domains-add-azure-portal.md).  

Om du vill hämta en lista över verifierade företagets domäner, kan du använda den [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0) cmdlet. 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>Utfärda ImmutableID för datorn när en användare finns (t.ex. alternativa inloggnings-ID har angetts)

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`** – Det här anspråket måste innehålla ett giltigt värde för datorer. I AD FS kan du skapa en regel för omvandling av utfärdande på följande sätt:

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

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Helper-skript för att skapa AD FS-utfärdande av transformeringsregler

Följande skript kan du skapa den utfärdande regler som beskrivs ovan.

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

- Det här skriptet lägger till regler till befintliga regler. Skriptet kör inte två gånger eftersom uppsättningen regler skulle läggas till två gånger. Kontrollera att inga motsvarande regler finns för de här anspråken (under motsvarande villkor) innan du kör skriptet igen.

- Om du har flera verifierat domännamn (som visas i Azure AD-portalen eller via cmdleten Get-MsolDomains), ange värdet för **$multipleVerifiedDomainNames** i skriptet till **$true**. Kontrollera också att du tar bort alla befintliga issuerid-anspråket som kan ha skapats av Azure AD Connect eller via andra sätt. Här är ett exempel för den här regeln:


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Om du redan har skickat en **ImmutableID** anspråk för användarkonton genom att ange värdet för **$immutableIDAlreadyIssuedforUsers** i skriptet till **$true**.

## <a name="step-3-enable-windows-down-level-devices"></a>Steg 3: Aktivera Windows äldre enheter

Om några av dina domänanslutna enheter är Windows äldre enheter, måste du:

- Ange en princip i Azure AD så att användarna kan registrera enheter.
 
- Konfigurera federationstjänsten på plats för att utfärda anspråk för **integrerad Windows autentisering (IWA)** för registrering av enheten.
 
- Lägg till autentiseringsslutpunkten för Azure AD-enhet i de lokala intranätszoner att undvika certifikat vid autentisering av enheten.

### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Ange princip i Azure AD så att användarna kan registrera enheter

Om du vill registrera Windows äldre enheter, måste du se till att inställningen för att tillåta användare att registrera enheter i Azure AD har angetts. Du kan hitta den här inställningen under Azure-portalen:

`Azure Active Directory > Users and groups > Device settings`
    
Följande princip måste anges till **alla**: **användare kan registrera sina enheter med Azure AD**

![Registrera enheter](./media/active-directory-conditional-access-automatic-device-registration-setup/23.png)


### <a name="configure-on-premises-federation-service"></a>Konfigurera den lokala federationstjänsten 

Den lokala federationstjänsten måste ha stöd för utfärdande av den **authenticationmethod** och **wiaormultiauthn** anspråk när du tar emot en verifieringsbegäran till den förlitande parten för Azure AD innehåller en resouce_params parameter med en kodad värde som visas nedan:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

När en sådan begäran kommer den lokala federationstjänsten måste autentisera användaren med integrerad Windows-autentisering och vid en lyckad distribution, utfärda följande två anspråk:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

Du måste lägga till en regel för utfärdande transformeringen som pass via autentiseringsmetod i AD FS.  

**Lägga till den här regeln:**

1. I AD FS-hanteringskonsolen, går du till `AD FS > Trust Relationships > Relying Party Trusts`.
2. Högerklicka på Microsoft Office 365-Identitetsplattform förlitande part förtroende-objekt och välj sedan **redigera Anspråksregler**.
3. På den **utfärdande av Transformeringsregler** fliken **Lägg till regel**.
4. I den **anspråksregel** mall-listan, Välj **skicka anspråk med en anpassad regel**.
5. Välj **Nästa**.
6. I den **Regelnamn för anspråk** skriver **Auth metoden Anspråksregel**.
7. I den **anspråksregel** skriver du följande regel:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. På din federationsserver, skriver du PowerShell-kommandot nedan när du har ersatt **\<RPObjectName\>** med förlitande part objektnamnet för din Azure AD förlitande part förtroende-objekt. Det här objektet vanligtvis har namnet **Microsoft Office 365-Identitetsplattform**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>Lägger till autentiseringsslutpunkten för Azure AD-enhet i zonerna Lokalt intranät

Uppmanar när användare i registrera enheter autentiserar till Azure AD som du kan skicka en princip till dina domänanslutna enheter att lägga till följande URL i zonen Lokalt intranät i Internet Explorer för att undvika certifikat:

`https://device.login.microsoftonline.com`

## <a name="step-4-control-deployment-and-rollout"></a>Steg 4: Kontrollera distributionen

När du har slutfört steg som krävs, är du redo att automatiskt ansluta till Azure AD-domänanslutna enheter:

- Alla domänanslutna enheter som kör Windows 10 Anniversary Update och Windows Server 2016 register med Azure AD på enheten startas om automatiskt eller användarinloggning. 

- Nya enheter registreras med Azure AD när enheten startas om efter domän join-åtgärd har slutförts.

- Enheter som var tidigare Azure AD registrerade (till exempel för Intune) övergång till ”*ansluten till domänen, AAD registrerad*”; men det tar lite tid för den här processen skulle slutföras på alla enheter på grund av det normala flödet av domän och användaraktivitet.

### <a name="remarks"></a>Kommentarer

- Du kan använda ett grupprincipobjekt eller System Center Configuration Manager-klienten inställning för att styra distributionen av automatisk registrering av Windows 10 och Windows Server 2016 domänanslutna datorer. **Om du inte vill att dessa enheter att automatiskt registrera med Azure AD eller om du vill styra registreringen**, och du måste distribuera en grupprincip som inaktiverar automatisk registrering i alla dessa enheter först eller om du använder konfigurationen Du måste konfigurera klientinställning under Cloud Services Manager -> automatiskt registrera nya Windows 10 domänanslutna enheter med Azure Active Directory som ”Nej” innan du börjar med några av konfigurationsstegen. När du är klar konfigurerar, och när du är redo att testa, måste du distribuera grupprinciper för att aktivera automatisk registrering till testenheter och sedan till alla andra enheter som du väljer.

- Distribution av Windows äldre datorer, du kan distribuera en [Windows Installer-paketet](#windows-installer-packages-for-non-windows-10-computers) till datorer som du väljer.

- Om du har överfört grupprincipobjektet till Windows 8.1-domänanslutna enheter som en koppling provas. men det rekommenderas att du använder den [Windows Installer-paketet](#windows-installer-packages-for-non-windows-10-computers) att ansluta till alla dina Windows äldre enheter. 

### <a name="create-a-group-policy-object"></a>Skapa ett grupprincipobjekt 

Om du vill styra distributionen av aktuella Windows-datorer, bör du distribuera den **registrera domänanslutna datorer som enheter** grupprincipobjekt till de enheter som du vill registrera. Du kan till exempel distribuera principen till en organisationsenhet eller i en säkerhetsgrupp.

**Att ställa in principen:**

1. Öppna **Serverhanteraren**, gå sedan till `Tools > Group Policy Management`.
2. Gå till domännoden som motsvarar den domän där du vill aktivera automatisk registrering av aktuella Windows-datorer.
3. Högerklicka på **grupprincipobjekt**, och välj sedan **New**.
4. Skriv ett namn för gruppolicy-objekt. Till exempel * Hybrid Azure AD-anslutning. 
5. Klicka på **OK**.
6. Högerklicka på din nya grupprincipobjekt och välj sedan **redigera**.
7. Gå till **Datorkonfiguration** > **principer** > **Administrationsmallar** > **Windows Komponenter** > **Enhetsregistrering**. 
8. Högerklicka på **registrera domänanslutna datorer som enheter**, och välj sedan **redigera**.
   
   > [!NOTE]
   > Den här mallen har ändrats från tidigare versioner av konsolen Grupprinciphantering. Om du använder en tidigare version av konsolen går du till `Computer Configuration > Policies > Administrative Templates > Windows Components > Workplace Join > Automatically workplace join client computers`. 

7. Välj **aktiverad**, och klicka sedan på **tillämpa**. Du måste välja **inaktiverad** om du vill att principen för att blockera enheter som styrs av den här grupprincipen från att automatiskt registrera med Azure AD.

8. Klicka på **OK**.
9. Länka grupprincipobjektet till en valfri plats. Du kan till exempel länka den till en viss organisationsenhet. Också kan du länka den till en specifik säkerhetsgrupp för datorer som automatiskt ansluter till med Azure AD. Länka grupprincipobjektet till domänen för att ställa in principen för alla domänanslutna Windows 10 och Windows Server 2016-datorer i din organisation.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>Windows Installer-paket för Windows 10-datorer

För att ansluta till domänanslutna Windows äldre datorer i en federerad miljö kan du hämta och installera de här Windows Installer-paketet (.msi) från Download Center på den [Microsoft Workplace Join för Windows 10-datorer](https://www.microsoft.com/en-us/download/details.aspx?id=53554) sidan.

Du kan distribuera paketet med hjälp av ett system för programvarudistribution som System Center Configuration Manager. Paketet stöder alternativ för standard tyst installation med den *tyst* parametern. System Center Configuration Manager Current Branch ger ytterligare fördelar jämfört med tidigare versioner, som möjligheten att spåra slutförda registreringar. Mer information finns i [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager).

Installationsprogrammet skapar en schemalagd aktivitet på system som körs i användarens kontext. Aktiviteten utlöses när användaren loggar in på Windows. Uppgiften kopplar tyst enhet med Azure AD med autentiseringsuppgifterna för användaren när de har autentiserat med integrerad Windows-autentisering. Om du vill se den schemalagda aktiviteten, i enheten, går du till **Microsoft** > **Arbetsplatsanslutning**, och gå sedan till Schemaläggaren-biblioteket.

## <a name="step-5-verify-joined-devices"></a>Steg 5: Kontrollera domänanslutna enheter

Du kan kontrollera lyckad domänanslutna enheter i organisationen med hjälp av den [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) cmdlet i den [Azure Active Directory PowerShell-modulen](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Utdata från denna cmdlet visar enheter som har registrerats och anslutits med Azure AD. Hämta alla enheter med den **-alla** parameter, och sedan filtrera dem med hjälp av den **deviceTrustType** egenskapen. Domänanslutna enheter har värdet **domänanslutna**.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till hantering av enheter i Azure Active Directory](device-management-introduction.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
