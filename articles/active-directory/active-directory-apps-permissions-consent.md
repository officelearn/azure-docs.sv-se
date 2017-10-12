---
title: "Appar, behörigheter och godkännande i Azure Active Directory.| Microsoft Docs"
description: "Azure AD Connect integrerar dina lokala kataloger med Azure Active Directory. På så sätt kan du tillhandahålla en gemensam identitet för Office 365-, Azure- och SaaS-program som är integrerade med Azure AD."
keywords: introduction to Azure AD, apps, what is Azure AD Connect, install active directory
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 25897cc4-7687-49b6-b0d5-71f51302b6b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/31/2017
ms.author: billmath
ms.reviewer: jesakowi
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 6f6baf5e1538fb280a899065c64ca5688473c04a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="apps-permissions-and-consent-in-azure-active-directory"></a>Appar, behörigheter och godkännande i Azure Active Directory
I Azure Active Directory kan du lägga till program i din katalog.  Programmen kan variera beroende på typen av program.  Om du vill visa program på den klassiska portalen markerar du en katalog och väljer program.

![](media/active-directory-apps-permissions-consent/apps1.png)

> [!IMPORTANT]
> Microsoft rekommenderar att du hanterar Azure AD via [Azure AD administratörscenter](https://aad.portal.azure.com) på Azure Portal istället för via den klassiska Azure-portalen som nämns i den här artikeln.

## <a name="types-of-apps"></a>Typer av appar

1. **Appar för en enda klient** </br>
    - **Appar för en enda klient** – Kallas ofta för verksamhetsspecifika appar (LOB, Line-Of-Business). Det här är fallet när någon i din organisation utvecklar sin egen app och vill att användare i organisationen ska kunna logga in i appen.
    ![](media/active-directory-apps-permissions-consent/apps2.png)
    - **App Proxy-appar** – När du exponerar ett lokalt program med Azure AD App Proxy registreras en app för en enda klient i din klientorganisation (förutom App Proxy-tjänsten). Den här appen är det som representerar ditt lokala program i alla molninteraktioner (till exempel autentisering). (App Proxy kräver Azure AD Basic eller högre.)


2. **Appar för flera klienter**
    - **Appar för flera klienter som andra kan godkänna** – Påminner om ”appar för en enda klient som din organisation utvecklar”. Den största skillnaden (förutom logiken i själva appen) är att användare från andra klientorganisationer också kan godkänna och logga in i appen.</br>
    ![](media/active-directory-apps-permissions-consent/apps4.png)
    - **Appar för flera klienter som andra utvecklar, som Contoso kan godkänna**. (Eller bara ”godkända appar”.) Det här är den andra sidan av ”appar för flera klienter som din organisation utvecklar”. När en annan organisation utvecklar en app för flera klienter kan användarna i din organisation godkänna appen och logga in i den.
    - **Första parts Microsoft-appar** – Appar som representerar Microsoft-tjänster. Du godkänner tjänsten genom att registrera dig för den. Ibland finns det särskilda användargränssnitt och logik för vissa förstapartsappar som ofta används när appåtkomstprinciper etableras.</br>
    ![](media/active-directory-apps-permissions-consent/apps3.png)
    - **Förintegrerade appar** – Appar som är tillgängliga i Azure AD App-galleriet, som du kan lägga till i din katalog för att tillhandahålla enkel inloggning (och i vissa fall etablering) i populära SaaS-appar.
    - **Enkel inloggning i Azure AD**: ”Verklig” enkel inloggning för appar som kan integreras med Azure AD via ett inloggningsprotokoll som stöds, t.ex. SAML 2.0 eller OpenID Connect. Guiden vägleder dig genom konfigurationen.
    - **Lösenord för enkel inloggning**: Azure AD lagrar säkert användarens autentiseringsuppgifter för appen, och autentiseringsuppgifterna matas in i inloggningsformuläret av webbläsartillägget Azure AD App Access. Kallas även för ”lösenordsvalv”.

## <a name="permissions"></a>Behörigheter

När en app registreras definierar användaren som utför registreringen (dvs. utvecklaren) vilka behörigheter appen behöver åtkomst till, och vilka resurser. (Själva resurserna definieras som andra appar.) Någon som till exempel bygger en app för e-postläsning anger att appen behöver behörigheten ”Access mailboxes as the signed-in user” (Komma åt postlådor som den inloggade användaren) i resursen ”Office 365 Exchange Online”:
    
![](media/active-directory-apps-permissions-consent/apps6.png)

För att en app (klienten) ska kunna begära en viss behörighet från en annan app (resurs) definierar resursappens utvecklaren de behörigheter som är tillgängliga. I vårt exempel har Microsoft, ägaren av resursappen ”Office 365 Exchange Online”, definierat en behörighet med namnet ”Access mailboxes as the signed-in user” (Komma åt postlådor som den inloggade användaren).

När behörigheterna definieras måste apputvecklaren ange om användaren kan godkänna behörigheten, eller om administratörens godkännande krävs. På så sätt kan utvecklaren tillåta att användarna själva godkänner appar som bara kräver låg behörighet, men kräva att administratörer godkänner känsligare behörigheter. Till exempel har resursappen ”Azure Active Directory” definierats så att användarna kan godkänna appar, men med begränsad läsbehörighet.  För fullständig läsbehörighet och skrivbehörighet krävs administratörens godkännande.

Eftersom interna klienter inte autentiseras kan en app som definierats som en intern klientapp endast begära delegerade behörigheter. Det innebär att det alltid måste finnas en verklig användare när en token begärs. Webbappar och webb-API:er (konfidentiella klienter) måste alltid autentisera med Azure AD när en åtkomsttoken begärs. Det innebär att de också har möjlighet att begära appspecifika behörigheter. Om en backend-tjänst till exempel måste autentisera till en annan backend-tjänst. Program som begär appspecifika behörigheter kräver alltid administratörens godkännande.

Sammanfattningsvis:



- En app (klient) anger de behörigheter som den behöver för andra appar (resurser).
- En app (resurs) anger vilka behörigheter som exponeras för andra appar (klienter).
- En behörighet kan vara en appspecifik behörighet, eller en delegerad behörighet.
- En delegerad behörighet kan märkas som ”tillåter användargodkännande” eller ”kräver administratörsgodkännande”.
- En app kan fungera som en klient (genom att deklarera att den behöver behörigheter till en resurs), som en resurs (genom att deklarera vilka behörigheter som den exponerar) eller som båda.

## <a name="controls"></a>Kontroller

Följande är en lista över de olika administratörskontroller som är tillgängliga för detta beteende. Administratörskontrollerna kan användas på den klassiska portalen från Konfigurera under katalogen.

![](media/active-directory-apps-permissions-consent/apps7.png)

På Azure Portal, under **Hantera**, **Användarinställningar**.

![](media/active-directory-apps-permissions-consent/apps11.png)



- Du kan styra om användare kan godkänna appar eller inte:

På den klassiska portalen väljer du **Users may give applications permissions to access their data (Användare kan ge program behörighet att komma åt deras data).**
![](media/active-directory-apps-permissions-consent/apps8.png)

På Azure Portal väljer du **Användare kan bevilja appar åtkomst till sina data**.
![](media/active-directory-apps-permissions-consent/apps12.png)



- Du kan styra om användare kan registrera sina egna LOB-appar för en enda klient: På den klassiska portalen väljer du **Users may add integrated applications (Användare kan lägga till integrerade program).**
![](media/active-directory-apps-permissions-consent/apps9.png)

På Azure Portal väljer du **Användare kan bevilja appar åtkomst till sina data**.
![](media/active-directory-apps-permissions-consent/apps13.png)

>[!NOTE]
>Även om du tillåter att användare registrerar LOB-appar för en enda klient finns det gränser för vad som kan registreras.  
>Ett exempel är utvecklare som inte är katalogadministratörer.
>
>- Användare kan inte göra en app för en enda klient till en app för flera klienter.
>- När användare registrerar LOB-appar för en enda klient kan de inte begära appspecifika behörigheter till andra appar.
>- När användare registrerar LOB-appar för en enda klient kan de inte begära delegerade behörigheter till andra appar om dessa behörigheter kräver administratörens godkännande.
>- Användare kan inte göra ändringar i appar som de inte äger.



- Du kan styra om användare kan lägga till förintegrerade appar som använder lösenord för enkel inloggning (SSO eller lösenordsvalv) ![](media/active-directory-apps-permissions-consent/apps10.png)



- Du kan styra när program kan nås, så kallad ”villkorlig åtkomst”. Tänk på att detta gäller både klientappen och resursappen. Anta till exempel att du skapar en princip för villkorlig åtkomst som anger att appen ”Office 365 Exchange Online” endast kan nås från datorer som följer standard.  Den här principen tillämpas också när en användare försöker använda en klientapp som begär behörighet till Exchange Online.



- Du kan se vilka appar som har godkänts och vilka som används.

1.  När en användare godkänner en app skapas ett ServicePrincipal-objekt i klientorganisationen. Genereringen av ServicePrincipal-objektet visas i granskningsrapporten.
2.  I rapporterna över användarnas inloggningsaktivitet kan du se vilken app som användaren är inloggad i. 

## <a name="example"></a>Exempel

Anta till exempel att du har lagt märke till att användare i din klientorganisation loggar in i appen ”FabrikamMail för Office 365”. ”FabrikamMail” är en e-postapp för Android, som publicerats av ”Fabrikam, Inc.”. Appen hör till kategorin ”appar för flera klienter som andra utvecklar, som Contoso kan godkänna”.

Om användare har tillåtelse att godkänna appen uppmanas de att godkänna den första gången de loggar in: ![](media/active-directory-apps-permissions-consent/apps14.png)

”Access your mailboxes” (Åtkomst till dina postlådor) är en sträng som ber om användarens godkännande för behörigheten ”Access mailboxes as the signed-in user” (Åtkomst till postlådor som den inloggade användaren) som exponeras av ”Office 365 Exchange Online” (dvs. Exchange).

Du kan se behörigheterna genom att leta upp ServicePrincipal-objektet för Exchange (resursen), som lades till när du registrerade dig för Office 365. Tänk dig ServicePrincipal-objektet som en ”instans” av appen i din klientorganisation, som används för att registrera olika alternativ och konfigurationer.  Du kan visa detta med hjälp av `Get-AzureADServicePrincipal` i PowerShell.

    PS C:\> Get-AzureADServicePrincipal -ObjectId 383f7b97-6754-4d3d-9474-3908ebcba1c6 | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : Office 365 Exchange Online
    AppId                     : 00000002-0000-0ff1-ce00-000000000000
    AppOwnerTenantId          : 
    AppRoleAssignmentRequired : False
    AppRoles                  : {...}
    DisplayName               : Microsoft.Exchange
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {...
                                , class OAuth2Permission {
                                  AdminConsentDescription : Allows the app to have the same access to mailboxes as the signed-in user via Exchange Web Services.
                                  AdminConsentDisplayName : Access mailboxes as the signed-in user via Exchange Web Services
                                  Id                      : 3b5f3d61-589b-4a3c-a359-5dd4b5ee5bd5
                                  IsEnabled               : True
                                  Type                    : User
                                  UserConsentDescription  : Allows the app full access to your mailboxes on your behalf.
                                  UserConsentDisplayName  : Access your mailboxes
                                  Value                   : full_access_as_user
                                },
                                ...}
    PasswordCredentials       : {}
    PublisherName             : 
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {00000002-0000-0ff1-ce00-000000000000/outlook.office365.com, 00000002-0000-0ff1-ce00-000000000000/mail.office365.com, 00000002-0000-0ff1-ce00-000000000000/outlook.com, 
                                00000002-0000-0ff1-ce00-000000000000/*.outlook.com...}
    Tags                      : {}

Godkännandet initieras när användaren klickar på ”Acceptera”. Först skapas ett ServicePrincipal-objekt för ”FabrikamMail for Office 365” i klientorganisationen. ServicePrincipal-objektet ser ut ungefär så här:

    PS C:\> Get-AzureADServicePrincipal -SearchString "FabrikamMail for Office 365" | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : a8b16333-851d-42e8-acd2-eac155849b37
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : FabrikamMail for Office 365
    AppId                     : aba7c072-2267-4031-8960-e7a2db6e0590
    AppOwnerTenantId          : 4a4076e0-a70f-41c6-b819-6f9c4a86df89
    AppRoleAssignmentRequired : False
    AppRoles                  : {}
    DisplayName               : FabrikamMail for Office 365
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {}
    PasswordCredentials       : {}
    PublisherName             : Fabrikam, Inc.
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {aba7c072-2267-4031-8960-e7a2db6e0590}
    Tags                      : {WindowsAzureActiveDirectoryIntegratedApp}

När en app godkänns skapas en Oauth2PermissionGrant-länk mellan följande:
  
- användarobjektet
- klientapparnas ServicePrincipalName (SPN)
- resursapparnas ServicePrincipalName (SPN)
- behörigheter i resursappen.  

I exemplet med FabrikamMail ser det ut ungefär så här:

    PS C:\> Get-AzureADUserOAuth2PermissionGrant -ObjectId ddiggle@aadpremiumlab.onmicrosoft.com | fl *
    
    ClientId    : a8b16333-851d-42e8-acd2-eac155849b37
    ConsentType : Principal
    ExpiryTime  : 05/15/2017 07:02:39 AM
    ObjectId    : M2OxqB2F6EKs0urBVYSbN5d7PzhUZz1NlH25COvLocbJjoxkUFfRQauryBKwBWet
    PrincipalId : 648c8ec9-5750-41d1-abab-c812b00567ad
    ResourceId  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    Scope       : full_access_as_user
    StartTime   : 01/01/0001 12:00:00 AM

(**ClientId** är ID:t för FabrikamMails ServicePrincipal-objekt (det som precis skapades), **PrincipalId** är ID:t för användarobjektet (för användaren som godkänt) och **ResourceId** är ID:t för Exchanges ServicePrincipal-objekt (Scope är behörigheten i Exchange som godkänts).

Om användarna inte har tillåtelse att ge sitt godkännande visas en skärm som anger att behörighet krävs.

