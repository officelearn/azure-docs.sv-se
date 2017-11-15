---
title: Hur du konfigurerar hybrid Azure Active Directory-anslutna enheter | Microsoft Docs
description: "Lär dig hur du konfigurerar hybrid Azure Active Directory anslutna enheter."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 12469573eb58d53a4f6a8632c23d716ef6716263
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="how-to-configure-hybrid-azure-active-directory-joined-devices"></a>Så här konfigurerar du hybrid Azure Active Directory anslutna enheter

Med hantering av enheter i Azure Active Directory (Azure AD), kan du se till att dina användare har åtkomst till dina resurser från enheter som uppfyller dina krav för säkerhet och efterlevnad. Mer information finns i [introduktion till hantering av enheter i Azure Active Directory](device-management-introduction.md).

Om du har en lokal Active Directory-miljö och du vill ansluta till din domänanslutna enheter till Azure AD, kan du göra detta genom att konfigurera hybrid Azure AD anslutna enheter. Avsnittet ger relaterade steg. 


## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar konfigurera hybrid Azure AD anslutna enheter i din miljö bör du bekanta dig med scenarierna som stöds och begränsningar.  

För att förbättra läsbarhet beskrivningar, används det här avsnittet följande villkor: 

- **Aktuella Windows-enheter** -termen refererar till domänanslutna enheter som kör Windows 10 eller Windows Server 2016.
- **Windows-klientversioner enheter** -termen avser alla **stöds** domänanslutna Windows-enheter som inte är igång Windows 10 eller Windows Server 2016.  


### <a name="windows-current-devices"></a>Aktuella Windows-enheter

- För enheter som kör Windows-operativsystemet, bör du använda Windows 10 årsdagar Update (version 1607) eller senare. 
- Registreringen av aktuella Windows-enheter **är** i ofedererad miljöer, till exempel lösenord hash-synkronisering konfigurationer som stöds.  


### <a name="windows-down-level-devices"></a>Äldre Windows-enheter

- Följande Windows-klientversioner enheter stöds:
    - Windows 8.1
    - Windows 7
    - Windows Server 2012 R2
    - Windows Server 2012
    - Windows Server 2008 R2
- Registrering av Windows-klientversioner enheter **är** stöds i ofedererad miljöer genom sömlös enkel inloggning [Azure Active Directory sömlös enkel inloggning](https://aka.ms/hybrid/sso).
- Registrering av Windows-klientversioner enheter **är inte** stöds för enheter med hjälp av centrala profiler. Om du beroende av centrala profiler eller inställningar, använder du Windows 10.



## <a name="prerequisites"></a>Krav

Innan du börjar att aktivera hybrid Azure AD anslutna enheter i din organisation, måste du se till att du kör en uppdaterad version av Azure AD connect.

Azure AD Connect:

- Behåller associationen mellan datorkonto i din lokala Active Directory (AD) och enhetsobjekt i Azure AD. 
- Gör andra enheter relaterade funktioner som Windows Hello för företag.



## <a name="configuration-steps"></a>Konfigurationssteg

Du kan konfigurera hybrid Azure AD anslutna enheter för olika typer av plattformar för Windows-enheter. Det här avsnittet innehåller de nödvändiga stegen för alla scenarier för typisk konfiguration.  

Använd följande tabell för att få en översikt över de steg som krävs för ditt scenario:  



| Steg                                      | Windows aktuella och lösenord hash-synkronisering | Windows aktuella och federation | Windows-klientversioner |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Steg 1: Konfigurera tjänstanslutningspunkten | ![Markera][1]                            | ![Markera][1]                    | ![Markera][1]        |
| Steg 2: Konfigurera utfärdande av anspråk           |                                        | ![Markera][1]                    | ![Markera][1]        |
| Steg 3: Aktivera Windows 10-enheter      |                                        |                                | ![Markera][1]        |
| Steg 4: Distribution av kontrollen och distribution     | ![Markera][1]                            | ![Markera][1]                    | ![Markera][1]        |
| Steg 5: Kontrollera domänanslutna enheter          | ![Markera][1]                            | ![Markera][1]                    | ![Markera][1]        |



## <a name="step-1-configure-service-connection-point"></a>Steg 1: Konfigurera tjänstanslutningspunkten

Serviceobjektet anslutning punkter (SCP) används av dina enheter under registreringen identifiera information för Azure AD-klient. SCP-objektet för hybrid Azure AD anslutna enheter måste finnas i konfigurationen naming kontexten partition av datorns skog i din lokala Active Directory (AD). Det finns endast en konfigurationsnamngivningskontexten per skog. Tjänstanslutningspunkten måste finnas i alla skogar som innehåller domänanslutna datorer i en konfiguration för flera skogar Active Directory.

Du kan använda den [ **Get-ADRootDSE** ](https://technet.microsoft.com/library/ee617246.aspx) för att hämta konfigurationsnamngivningskontexten i skogen.  

För en skog med Active Directory-domännamn *fabrikam.com*, konfigurationsnamngivningskontexten är:

`CN=Configuration,DC=fabrikam,DC=com`

I din skog finns SCP-objektet för automatisk registrering för domänanslutna enheter här:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Beroende på hur du har distribuerat Azure AD Connect, har SCP-objektet redan konfigurerats.
Du kan verifiera att objektet och hämta identifiering värden med hjälp av följande Windows PowerShell-skript: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

Den **$scp. Nyckelord** utdata visar information om Azure AD-klient, till exempel:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Om tjänstanslutningspunkten inte finns, kan du skapa den genom att köra den `Initialize-ADSyncDomainJoinedComputerSync` cmdlet på Azure AD Connect-servern. Enterprise admin-autentiseringsuppgifter krävs för att köra denna cmdlet.  
Cmdlet:

- Skapar tjänstanslutningspunkten i Azure AD Connect är ansluten till Active Directory-skogen. 
- Du måste ange den `AdConnectorAccount` parameter. Detta är det konto som har konfigurerats som Active Directory connector-kontot i Azure AD connect. 


Följande skript visar ett exempel för att använda cmdlet. I det här skriptet `$aadAdminCred = Get-Credential` kräver att du anger ett användarnamn. Du måste ange ett användarnamn i formatet användarens huvudnamn (UPN) (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

Den `Initialize-ADSyncDomainJoinedComputerSync` cmdlet:

- Använder Active Directory PowerShell-modulen och AD DS-verktyg som förlitar sig på Active Directory-webbtjänster som körs på en domänkontrollant. Active Directory Web Services fungerar på domänkontrollanter som kör Windows Server 2008 R2 och senare.
- Stöds endast av den **MSOnline PowerShell Modulversion 1.1.166.0**. Använd det här alternativet om du vill hämta den här modulen [länk](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185).   
- Om AD DS-hanteringsverktygen inte är installerade på `Initialize-ADSyncDomainJoinedComputerSync` misslyckas.  Verktyg för AD DS kan installeras via Server Manager under funktioner - verktyg för fjärrserveradministration - Rolladministrationsverktyg.

Använda skriptet nedan för domänkontrollanter som kör Windows Server 2008 eller tidigare versioner, för att skapa tjänstanslutningspunkten.

Du bör använda följande skript för att skapa tjänstanslutningspunkten i varje skog där det finns datorer i en konfiguration för flera skogar:
 
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


## <a name="step-2-setup-issuance-of-claims"></a>Steg 2: Konfigurera utfärdande av anspråk

I en federerad Azure AD-konfiguration enheter förlitar sig på Active Directory Federation Services (AD FS) eller 3 part lokalt federationstjänsten att autentisera till Azure AD. Enheter autentiseras för att få en åtkomsttoken att registrera mot Azure Active Directory Device registrering Service (Azure DRS).

Windows aktuella enheter autentiseras med integrerad Windows-autentisering till en aktiv WS-Trust-slutpunkt (1.3 eller 2005 versioner) hos den lokala federationstjänsten.

> [!NOTE]
> När du använder AD FS, antingen **adfs/services/trust/13/windowstransport** eller **adfs/services/trust/2005/windowstransport** måste vara aktiverat. Om du använder en webbproxy för autentisering också se till att den här slutpunkten har publicerats via proxy. Du kan se vilka slutpunkter aktiveras via AD FS-hanteringskonsol under **Service > slutpunkter**.
>
>Om du inte har AD FS som federationstjänsten lokalt, följ instruktionerna för din leverantör för att kontrollera att de stöder WS-Trust 1.3 eller 2005 slutpunkter och dessa publiceras via Metadata Exchange-fil (MEX).

Följande anspråk måste finnas i den token som tas emot av Azure DRS för registrering av enheten för att slutföra. Azure DRS skapar ett enhetsobjekt i Azure AD med en del av den här informationen som sedan används av Azure AD Connect för att associera det nyligen skapade enhetsobjektet med datorn kontot Lokal.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Om du har mer än ett verifierat domännamn, måste du ange följande anspråk för datorer:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Om du redan utfärdar ett ImmutableID anspråk (t.ex. Alternativt inloggnings-ID) måste du ange ett motsvarande anspråk för datorer:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

I följande avsnitt hittar du information om:
 
- Värdet för varje anspråk ska ha
- Hur en definition skulle se ut i AD FS

Definitionen hjälper dig att kontrollera om värdena finns eller om du behöver skapa dem.

> [!NOTE]
> Om du inte använder AD FS för federationsservern lokalt, följer du leverantörens instruktioner för att skapa inställningar för att utfärda dessa anspråk.

### <a name="issue-account-type-claim"></a>Problemet konto typen anspråk

**`http://schemas.microsoft.com/ws/2012/01/accounttype`**-Detta anspråk måste innehålla ett värde av **DJ**, som identifierar enheten som en domänansluten dator. I AD FS kan du lägga till en regel för omvandling av utfärdande som ser ut så här:

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

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Utfärda objectGUID av datorn kontot Lokal

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`**-Detta anspråk måste innehålla den **objectGUID** värdet för det lokala datorkontot. I AD FS kan du lägga till en regel för omvandling av utfärdande som ser ut så här:

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
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Utfärda objectSID för den dator kontot Lokalt

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`**-Detta anspråk måste innehålla den den **objectSid** värdet för det lokala datorkontot. I AD FS kan du lägga till en regel för omvandling av utfärdande som ser ut så här:

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

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`**-Detta anspråk måste innehålla den identifierare URI (Uniform Resource) för någon av de verifierade domännamn som ansluter med federationstjänsten lokalt (AD FS eller 3 part) utfärdar token. Du kan lägga till regler för utfärdandetransformering som liknar de nedan i den specifika ordningen när de ovan i AD FS. Observera att en regel att uttryckligen utfärda regeln för användare är nödvändigt. En första regel som identifierar användare eller datorautentisering läggs till i reglerna nedan.

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

- `<verified-domain-name>`är en platshållare som du behöver ersätta med en av dina verifierat domännamn i Azure AD. Till exempel Value = ”http://contoso.com/adfs/services/trust/”



Mer information om verifierat domännamn finns [lägga till ett anpassat domännamn i Azure Active Directory](active-directory-domains-add-azure-portal.md).  
Om du vill hämta en lista över företagets verifierade domäner som du kan använda den [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0) cmdlet. 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>Utfärda ImmutableID för datorn när en användare finns (t.ex. Alternativt inloggnings-ID har angetts)

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`**-Detta anspråk måste innehålla ett giltigt värde för datorer. I AD FS kan du skapa en regel för omvandling av utfärdande som följer:

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

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Helper-skript för att skapa AD FS utfärdande reglerna för omvandling

Skriptet nedan hjälper dig att skapa utfärdande regler som beskrivs ovan.

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

- Skriptet lägger till regler på befintliga regler. Kör inte skriptet två gånger eftersom uppsättningen regler skulle läggas till två gånger. Se till att motsvarande inte finns några regler för dessa anspråk (villkor motsvarande) innan du kör skriptet igen.

- Om du har flera verifierat domännamn (som visas i Azure AD-portalen eller via cmdlet Get-MsolDomains), ange värdet för **$multipleVerifiedDomainNames** i skriptet till **$true**. Kontrollera också att du tar bort alla befintliga issuerid anspråk som kan ha skapats av Azure AD Connect eller via andra sätt. Här är ett exempel på den här regeln:


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Om du redan har skickat en **ImmutableID** anspråk för användarkonton, ange värdet för **$immutableIDAlreadyIssuedforUsers** i skriptet till **$true**.

## <a name="step-3-enable-windows-down-level-devices"></a>Steg 3: Aktivera äldre Windows-enheter

Om vissa enheter domänanslutna Windows äldre enheter, måste du:

- Ange en princip i Azure AD så att användarna kan registrera enheter.
 
- Konfigurera federationstjänsten lokalt för att utfärda anspråk för att stödja **Windows IWA (Integrated Authentication)** för registrering av enheten.
 
- Lägg till Azure AD-enheten autentiseringsslutpunkten zonerna lokala intranät för att undvika anvisningarna för certifikat vid autentisering av enheten.

### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Ange principen i Azure AD så att användarna kan registrera enheter

Om du vill registrera äldre Windows-enheter måste du se till att inställningen för att tillåta användare att registrera enheter i Azure AD har angetts. Du hittar den här inställningen under i Azure-portalen:

`Azure Active Directory > Users and groups > Device settings`
    
Följande princip måste anges till **alla**: **användarna kan registrera sina enheter med Azure AD**

![Registrera enheter](./media/active-directory-conditional-access-automatic-device-registration-setup/23.png)


### <a name="configure-on-premises-federation-service"></a>Konfigurera lokala federationstjänsten 

Federationstjänsten lokalt måste ha stöd för utfärdande av **authenticationmehod** och **wiaormultiauthn** anspråk när du tar emot en autentiseringsbegäran till den förlitande parten för Azure AD hålla en resouce_params parameter med ett kodat värde som visas nedan:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

När en sådan begäran kommer den lokala federationstjänsten måste autentisera användare som använder integrerad Windows-autentisering och vid lyckades, utfärda följande två anspråk:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

Du måste lägga till transformeringen regeluppsättningarna som överför via autentiseringsmetoden i AD FS.  

**Lägg till den här regeln:**

1. I hanteringskonsolen för AD FS går du till `AD FS > Trust Relationships > Relying Party Trusts`.
2. Högerklicka på Microsoft Office 365-Identitetsplattformen förlitande part förtroende-objekt och välj sedan **redigera Anspråksregler**.
3. På den **regler för Utfärdandetransformering** väljer **Lägg till regel**.
4. I den **anspråksregel** mall-listan, Välj **skicka anspråk med en anpassad regel**.
5. Välj **nästa**.
6. I den **Regelnamn för anspråk** skriver **Auth metoden Anspråksregel**.
7. I den **anspråksregel** skriver följande regel:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. Ange PowerShell-kommandot nedan på federationsservern när du ersätter  **\<RPObjectName\>**  med förlitande part objektnamnet för din Azure AD förlitande part förtroende-objekt. Det här objektet vanligen namnet **Identitetsplattformen för Microsoft Office 365**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>Lägger till Azure AD-enheten autentiseringsslutpunkten i zonerna Lokalt intranät

Uppmanar när användare registrera enheter autentiserar till Azure AD som du kan pressa en princip till dina domänanslutna enheter att lägga till följande URL i zonen Lokalt intranät i Internet Explorer för att undvika certifikat:

`https://device.login.microsoftonline.com`

## <a name="step-4-control-deployment-and-rollout"></a>Steg 4: Distribution av kontrollen och distribution

När du har slutfört nödvändiga steg är domänanslutna enheter redo att automatiskt ansluta till Azure AD:

- Alla domänanslutna enheter som kör Windows 10 årsdagar Update och Windows Server 2016 registrera med Azure AD på enheten startas om automatiskt eller användarinloggning. 

- Att registrera nya enheter med Azure AD när enheten startas om efter domän join-åtgärden har slutförts.

- Enheter som fanns tidigare Azure AD som registrerade (t.ex, för Intune) övergång till ”*domänanslutna, AAD registrerade*”, men det tar en stund att slutföra på alla enheter på grund av det normala flödet av domän och användaraktivitet.

### <a name="remarks"></a>Kommentarer

- Du kan använda ett grupprincipobjekt för att styra distributionen av automatisk registrering av Windows 10 och Windows Server 2016 domänanslutna datorer.

- Windows 10 November 2015 Update kopplas automatiskt med Azure AD **endast** om grupprincipobjektet för distribution har angetts.

- Distribution av äldre Windows-datorer, du kan distribuera en [Windows Installer-paketet](#windows-installer-packages-for-non-windows-10-computers) till datorer som du väljer.

- Om du push grupprincipobjektet till Windows 8.1-domänanslutna enheter görs en koppling; men det rekommenderas att du använder den [Windows Installer-paketet](#windows-installer-packages-for-non-windows-10-computers) att ansluta till alla dina Windows äldre enheter. 

### <a name="create-a-group-policy-object"></a>Skapa ett grupprincipobjekt 

Om du vill kontrollera installationen av aktuella Windows-datorer, bör du distribuera den **registrera domänanslutna datorer som enheter** grupprincipobjektet till de enheter som du vill registrera. Du kan till exempel distribuera principen till en organisationsenhet eller till en säkerhetsgrupp.

**Att ställa in principen:**

1. Öppna **Serverhanteraren**, gå sedan till `Tools > Group Policy Management`.
2. Gå till domännoden som motsvarar den domän där du vill aktivera automatisk registrering av aktuella Windows-datorer.
3. Högerklicka på **grupprincipobjekt**, och välj sedan **ny**.
4. Ange ett namn för grupprincipobjekt. Till exempel * Hybrid Azure AD-koppling. 
5. Klicka på **OK**.
6. Högerklicka på din nya grupprincipobjekt och välj sedan **redigera**.
7. Gå till **Datorkonfiguration** > **principer** > **Administrationsmallar** > **Windows-komponenter** > **Enhetsregistrering**. 
8. Högerklicka på **registrera domänanslutna datorer som enheter**, och välj sedan **redigera**.
   
   > [!NOTE]
   > Den här mallen har ändrats från tidigare versioner av konsolen Grupprinciphantering. Om du använder en tidigare version av konsolen, gå till `Computer Configuration > Policies > Administrative Templates > Windows Components > Workplace Join > Automatically workplace join client computers`. 

7. Välj **aktiverad**, och klicka sedan på **tillämpa**.
8. Klicka på **OK**.
9. Länka grupprincipobjektet till en plats. Du kan till exempel länka den till en viss organisationsenhet. Dessutom kan du länka det till en viss säkerhetsgrupp för datorer som automatiskt att ansluta med Azure AD. Länka grupprincipobjektet till domänen för att ställa in principen för alla domänanslutna Windows 10 och Windows Server 2016 datorer i din organisation.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>Windows Installer-paket för Windows 10-datorer

Om du vill ansluta till domänanslutna datorer med Windows-klientversioner i en federerad miljö kan du hämta och installera de här Windows Installer-paketet (.msi) från Download Center på den [till arbetsplatsen från Microsoft för Windows 10-datorer](https://www.microsoft.com/en-us/download/details.aspx?id=53554) sidan.

Du kan distribuera paketet med hjälp av ett program distributionssystem som System Center Configuration Manager. Paketet stöder alternativen standard tyst installation med den *tyst* parameter. System Center Configuration Manager Current Branch erbjuder ytterligare fördelar jämfört med tidigare versioner, t.ex. möjligheten att spåra slutförda registreringar. Mer information finns i [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager).

Installationsprogrammet skapar en schemalagd aktivitet på datorn som körs i användarens kontext. Aktiviteten utlöses när användaren loggar in på Windows. Aktiviteten ansluter tyst enheten med Azure AD med autentiseringsuppgifterna för användaren när autentisering med integrerad Windows-autentisering. Den schemalagda aktiviteten i den aktuella enheten, gå till **Microsoft** > **Arbetsplatsanslutning**, och gå sedan till bibliotek för Schemaläggaren.

## <a name="step-5-verify-joined-devices"></a>Steg 5: Kontrollera domänanslutna enheter

Du kan kontrollera lyckade domänanslutna enheter i organisationen med hjälp av den [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) cmdlet i den [Azure Active Directory PowerShell-modulen](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Utdata från den här cmdleten visar enheter som registreras och kopplas till Azure AD. Alla enheter får använda den **-alla** parameter, och filtrera dem med hjälp av den **deviceTrustType** egenskapen. Domänanslutna enheter har värdet **domänanslutna**.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till hantering av enheter i Azure Active Directory](device-management-introduction.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
