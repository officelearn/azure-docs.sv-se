---
title: Rubrikbaserad autentisering med PingAccess för Azure AD Application Proxy | Microsoft Docs
description: Publicera program med PingAccess och App Proxy som stöd för rubrikbaserad autentisering.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0544ed0ff217b6e37cca22a1fc1e0048b30da462
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68694223"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Rubrikbaserad autentisering för enkel inloggning med Application Proxy och PingAccess

Azure Active Directory (Azure AD) Application Proxy har samarbetat med PingAccess så att dina Azure AD-kunder kan komma åt fler av dina program. PingAccess expanderar den [befintliga Application Proxy-erbjudanden](application-proxy.md) med enkel inloggning till program som använder rubriker för autentisering.

## <a name="whats-pingaccess-for-azure-ad"></a>Vad är PingAccess för Azure AD?

Med PingAccess för Azure AD kan du ge användare åtkomst och enkel inloggning (SSO) till program som använder rubriker för autentisering. Application Proxy behandlar programmen som någon annan, med Azure AD för att autentisera åtkomst och sedan skicka trafik via kopplingstjänsten. PingAccess placeras framför programmen och översätter åtkomsttoken från Azure AD till en rubrik. Programmet tar sedan emot autentiseringen i det format som det kan läsa.

Användarna ser inte något annorlunda när de loggar in att använda företagets program. De kan fortsätta att arbeta från var som helst på valfri enhet. Application Proxy-anslutningarna dirigerar fjärrtrafik till alla appar utan hänsyn till autentiseringstypen, så de kommer fortfarande att utjämna belastningen automatiskt.

## <a name="how-do-i-get-access"></a>Hur gör jag för att få åtkomst?

Eftersom det här scenariot kommer från ett partnerskap mellan Azure Active Directory och PingAccess behöver du licenser för båda tjänsterna. Azure Active Directory Premium-prenumerationer innehåller dock en grundläggande PingAccess-licens som omfattar upp till 20 program. Om du vill publicera fler än 20 rubrikbaserad program kan köpa du ytterligare en licens från PingAccess.

Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publicera dina program i Azure

Den här artikeln är för användare att publicera ett program med det här scenariot för första gången. Utöver informationen i publicerings stegen får du hjälp med att komma igång med både programproxy-och PingAccess. Om du redan har konfigurerat båda tjänsterna men vill ha en uppdatering av publicerings stegen går du vidare till avsnittet [Lägg till ditt program i Azure AD med Application Proxy](#add-your-application-to-azure-ad-with-application-proxy) .

> [!NOTE]
> Eftersom det här scenariot är ett partnerskap mellan Azure AD och PingAccess, några av instruktionerna finns på webbplatsen Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Installera en Application Proxy connector

Om du har aktiverat Application Proxy aktiverat och installerat en koppling redan, kan du hoppa över det här avsnittet och gå till [Lägg till ditt program i Azure AD med Application Proxy](#add-your-application-to-azure-ad-with-application-proxy).

Application Proxy Connector är en Windows Server-tjänst som dirigerar trafiken från dina fjärranslutna anställda till dina publicerade program. Mer detaljerad Installationsinstruktioner finns i [Självstudier: Lägg till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Logga in på [Azure Active Directory Portal](https://aad.portal.azure.com/) som program administratör. Sidan **Azure Active Directory administrations Center** visas.
1. Välj **Azure Active Directory** > **Application Proxy** > **Download Connector service**. Sidan **hämtning av Application Proxy Connector** visas.

   ![Hämtning av Application Proxy Connector](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Följ installationsanvisningarna.

Om du hämtar anslutningen ska du automatiskt aktivera programproxyn för din katalog, men om du inte gör det kan du välja **Aktivera programproxy**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Lägg till ditt program i Azure AD med Application Proxy

Det finns två åtgärder som du behöver göra i Azure-portalen. Först måste du publicera ditt program med programproxy. Sedan måste du samla in viss information om programmet som du kan använda under PingAccess-stegen.

#### <a name="publish-your-application"></a>Publicera programmet

Du måste först publicera ditt program. Den här åtgärden omfattar:

- Lägga till ditt lokala program i Azure AD
- Tilldela en användare för att testa programmet och välja rubrik-baserad SSO
- Konfigurera programmets omdirigerings-URL
- Bevilja behörigheter för användare och andra program att använda ditt lokala program

Så här publicerar du ett eget lokalt program:

1. Om du inte gjorde det sista avsnittet loggar du in på [Azure Active Directory Portal](https://aad.portal.azure.com/) som program administratör.
1. Välj **företags program** > **nytt program** > **lokalt**program. Sidan **Lägg till ett eget lokalt program** visas.

   ![Lägg till ditt egen lokala program](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Fyll i de obligatoriska fälten med information om det nya programmet. Använd rikt linjerna nedan för inställningarna.

   > [!NOTE]
   > En mer detaljerad genom gång av det här steget finns i [lägga till en lokal app i Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **Intern URL**: Normalt anger du webb adressen som tar dig till appens inloggnings sida när du är i företags nätverket. I det här scenariot måste anslutningen behandla PingAccess-proxyn som den första sidan i programmet. Använd det här formatet: `https://<host name of your PingAccess server>:<port>`. Porten är 3000 som standard, men du kan konfigurera det i PingAccess.

      > [!WARNING]
      > Den interna URL: en måste använda `https` och kan inte använda `http`för den här typen av enkel inloggning.

   1. **Metod för förautentisering**: Välj **Azure Active Directory**.
   1. **Översätt URL i rubriker**: Välj **Nej**.

   > [!NOTE]
   > Om det är ditt första program kan använda port 3000 att starta och gå tillbaka till att uppdatera den här inställningen om du ändrar din PingAccess-konfiguration. För efterföljande program måste porten matcha den lyssnare som du har konfigurerat i PingAccess. Läs mer om [lyssnare inom PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Välj **Lägg till**. Översikts sidan för det nya programmet visas.

Tilldela nu en användare för program testning och välj rubrik-baserad enkel inloggning:

1. I program sid panelen väljer **du användare och grupper** > **Lägg till användar** > **användare och grupper\<(antal > valt)** . En lista över användare och grupper visas där du kan välja bland.

   ![Visar en lista över användare och grupper](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Välj en användare för program testning och välj **Välj**. Kontrollera att det här testkontot har åtkomst till dina lokala program.
1. Välj **Tilldela**.
1. Från program sid panelen väljer du**rubrik baserad** >  **på enkel inloggning**.

   > [!TIP]
   > Om det här är första gången du använder rubrikbaserad enkel inloggning måste du installera PingAccess. Kontrollera din Azure-prenumeration associeras automatiskt med PingAccess-installationen genom att använda länken på den här sidan för enkel inloggning för att hämta PingAccess. Du kan öppna hämtningsplatsen nu eller kommer till den här sidan tillbaka senare.

   ![Visar rubrikbaserade inloggnings skärmen och PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Välj **Spara**.

Kontrol lera att din URL för omdirigering har angetts till din externa URL:

1. I **Azure Active Directory administrations centerns** sid panelen väljer du **Azure Active Directory** > **Appregistreringar**. En lista över program visas.
1. Välj ditt program.
1. Välj länken bredvid omdirigerings- **URI**: er som visar antalet omdirigerings-URI: er som kon figurer ATS för webb-och offentliga klienter. Sidan  **programnamn>-Authenticationvisas.\<**
1. Kontrol lera om den externa URL: en som du tilldelade ditt program tidigare finns i listan omdirigerings- **URI: er** . Om den inte är det lägger du till den externa URL: en nu, använder en omdirigerings **-URI-** typ och väljer **Spara**.

Slutligen konfigurerar du ditt lokala program så att användare har Läs-och Skriv behörighet till Läs-och Skriv behörighet:

1. Välj **API-behörigheter** > i **Appregistreringar** marginal list för ditt program,**Lägg till en behörighet** > **Microsoft API: er** > **Microsoft Graph**. Sidan **begär API-behörigheter** för **Microsoft Graph** visas, som innehåller API: er för Windows Azure Active Directory.

   ![Visar sidan begär API-behörigheter](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Välj **delegerade behörigheter** > **användaren** > **användare. Läs**.
1. Välj **program behörigheter** > **program** > **program. readwrite. all**.
1. Välj **Lägg till behörigheter**.
1. På sidan **API-behörigheter** väljer du **bevilja administratörs medgivande \<för ditt katalog namn >** .

#### <a name="collect-information-for-the-pingaccess-steps"></a>Samla in information för PingAccess-steg

Du måste samla in dessa tre informations delar (alla GUID) för att konfigurera ditt program med PingAccess:

| Namn på Azure AD-fält | Namn på fältet PingAccess | Dataformat |
| --- | --- | --- |
| **Program-ID (klient)** | **Klient-ID** | GUID |
| **Katalog (klient) ID** | **Utfärdare** | GUID |
| `PingAccess key` | **Klienthemlighet** | Slumpmässig sträng |

Samla in den här informationen:

1. I **Azure Active Directory administrations centerns** sid panelen väljer du **Azure Active Directory** > **Appregistreringar**. En lista över program visas.
1. Välj ditt program. **Appregistreringar** sidan för ditt program visas.

   ![Registrerings översikt för ett program](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Klicka på ikonen **Kopiera till Urklipp** bredvid **programmets (klient) ID-** värde och kopiera och spara den sedan. Du anger det här värdet senare som PingAccess-klient-ID.
1. Klicka på **Kopiera till Urklipp**bredvid **ID-värdet för katalogen (klient)** och kopiera och spara det. Du anger det här värdet senare som PingAccesss utfärdare.
1. Välj **certifikat och hemligheter** > **ny klient hemlighet**från List rutan för **Appregistreringar** för ditt program. Sidan **Lägg till en klient hemlighet** visas.

   ![Visar sidan Lägg till en klient hemlighet](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. I **Beskrivning**skriver `PingAccess key`du.
1. Under **upphör ande**väljer du hur du vill ange PingAccess-nyckeln: **Om 1 år**, **i två år**eller **aldrig**.
1. Välj **Lägg till**. PingAccess-nyckeln visas i tabellen med klient hemligheter, med en slumpmässig sträng som fyller i fältet **värde** .
1. Klicka på ikonen **Kopiera till Urklipp** bredvid **värde** fältet för PingAccess-nyckeln och kopiera och spara den. Du anger det här värdet senare som PingAccess-klient hemlighet.

### <a name="update-graphapi-to-send-custom-fields-optional"></a>Uppdatera GraphAPI för att skicka anpassade fält (valfritt)

Om du behöver ett anpassat anspråk som skickar andra tokens i access_token som används av PingAccess, anger du `acceptMappedClaims` fältet program till. `True` Du kan använda Graph Explorer eller Azure AD Portals applikations manifest för att göra den här ändringen.

**I det här exemplet används Graph Explorer:**

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

**I det här exemplet används [Azure Active Directory Portal](https://aad.portal.azure.com/) för att `acceptMappedClaims` uppdatera fältet:**

1. Logga in på [Azure Active Directory Portal](https://aad.portal.azure.com/) som program administratör.
1. Välj **Azure Active Directory** > **appregistreringar**. En lista över program visas.
1. Välj ditt program.
1. Välj **manifest**i list rutan på **Appregistreringar** sidan för ditt program. Manifest-JSON-koden för programmets registrering visas.
1. Sök efter `acceptMappedClaims` fältet och ändra värdet till `True`.
1. Välj **Spara**.

### <a name="use-of-optional-claims-optional"></a>Användning av valfria anspråk (valfritt)

Med valfria anspråk kan du lägga till standard-, men inte inkluderade, som standard-anspråk som varje användare och klient har. Du kan konfigurera valfria anspråk för programmet genom att ändra applikations manifestet. Mer information finns i [artikeln förstå Azure AD Application manifest](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

Exempel för att inkludera e-postadress i access_token som PingAccess kommer att använda:
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Användning av anspråks mappnings princip (valfritt)

[Princip för anspråks mappning (för hands version)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties) för attribut som inte finns i AzureAD. Med anspråks mappning kan du migrera gamla lokal appar till molnet genom att lägga till ytterligare anpassade anspråk som backas upp av ADFS-eller användar objekt

Om du vill att programmet ska använda ett anpassat anspråk och inkludera ytterligare fält, se till att du även har [skapat en anpassad princip för anspråks mappning och tilldelat den till programmet](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Om du vill använda ett anpassat anspråk måste du också ha en anpassad princip definieras och tilldelas programmet. Den här principen ska inkludera alla nödvändiga anpassade attribut.
>
> Du kan utföra princip definitioner och tilldelning via PowerShell, Azure AD Graph Explorer eller Microsoft Graph. Om du gör det i PowerShell kan du behöva först använda `New-AzureADPolicy` och sedan tilldela den till programmet med. `Add-AzureADServicePrincipalPolicy` Mer information finns i [princip tilldelning](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)för anspråks mappning.

Exempel:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Aktivera PingAccess för att använda anpassade anspråk

Att aktivera PingAccess för att använda anpassade anspråk är valfritt, men krävs om du förväntar dig att programmet ska använda ytterligare anspråk.

När du ska konfigurera PingAccess i följande steg måste webbsessionen du skapar (Inställningar-> åtkomst > webbsessioner) ha **begär ande profil** avmarkerad och **Uppdatera användarattribut** inställda på **Nej**

## <a name="download-pingaccess-and-configure-your-application"></a>Ladda ned PingAccess och konfigurera ditt program

Nu när du har slutfört alla steg för Azure Active Directory-installationen, kan du gå vidare till att konfigurera PingAccess.

De detaljerade stegen för PingAccess-delen av det här scenariot fortsätter i identitets dokumentationen för ping. Följ anvisningarna i [Konfigurera PingAccess för Azure AD för att skydda program som publicerats med Microsoft Azure AD Application Proxy](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) på ping Identity-webbplatsen.

Dessa steg hjälper dig att installera PingAccess och skapa ett PingAccess-konto (om du inte redan har ett). Om du sedan vill skapa en Azure AD OpenID Connect-anslutning (OIDC), ställer du in en token **-Provider med katalogen (klient) ID-** värdet som du kopierade från Azure AD-portalen. Sedan, för att skapa en webbsession på PingAccess, använder du **program-ID** och `PingAccess key` -värden. Efter det kan du konfigurera identitetsmappning och skapa en virtuell värd, plats och program.

### <a name="test-your-application"></a>Testa ditt program

När du har slutfört alla dessa steg bör programmet vara igång. Testa det genom att öppna en webbläsare och navigera till den externa URL: en som du skapade när du publicerade programmet i Azure. Logga in med det test konto som du har tilldelat till programmet.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera PingAccess för Azure AD för att skydda program som publiceras med Microsoft Azure AD Application Proxy](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md)
- [Felsöka problem med Application Proxy och felmeddelanden](application-proxy-troubleshoot.md)
