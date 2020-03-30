---
title: Lägga till Google som identitetsleverantör för B2B - Azure AD
description: Federera med Google så att gästanvändare kan logga in på dina Azure AD-appar med sitt eget Gmail-konto
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72c18e48c27942c7bea47931ec79a31af941064e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126647"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Lägg till Google som identitetsleverantör för B2B-gästanvändare

Genom att konfigurera federation med Google kan du tillåta inbjudna användare att logga in på dina delade appar och resurser med sina egna Gmail-konton, utan att behöva skapa Microsoft-konton (MSA). 

> [!NOTE]
> Google federation är utformad speciellt för Gmail-användare. Om du vill federera med G Suite-domäner använder du [funktionen för direktfederation](direct-federation.md).

## <a name="what-is-the-experience-for-the-google-user"></a>Vad är upplevelsen för Google-användaren?
När du skickar en inbjudan till en Google Gmail-användare bör gästanvändaren komma åt dina delade appar eller resurser med hjälp av en länk som innehåller klientkontexten. Deras erfarenhet varierar beroende på om de redan är inloggade på Google:
  - Om gästanvändaren inte är inloggad på Google uppmanas han att logga in på Google.
  - Om gästanvändaren redan är inloggad på Google uppmanas de att välja det konto de vill använda. De måste välja det konto som du använde för att bjuda in dem.

Om gästanvändaren ser ett "huvud för långt" fel kan de försöka rensa sina cookies, eller så kan de öppna ett privat eller inkognitofönster och försöka logga in igen.

![Skärmbild som visar inloggningssidan för Google](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Begränsningar

Teamen har fullt stöd för Googles gästanvändare på alla enheter. Google-användare kan logga in i Teams `https://teams.microsoft.com`från en gemensam slutpunkt som .

Andra program vanliga slutpunkter kanske inte stöder Google-användare. Googles gästanvändare måste logga in med en länk som innehåller din klientinformation. Följande är exempel:
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Om Googles gästanvändare försöker använda `https://myapps.microsoft.com` `https://portal.azure.com`en länk, till exempel eller får de ett felmeddelande.

Du kan också ge Google gästanvändare en direkt länk till ett program eller en `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`resurs, så länge den här länken innehåller din klientinformation, till exempel . 

## <a name="step-1-configure-a-google-developer-project"></a>Steg 1: Konfigurera ett utvecklarprojekt för Google
Skapa först ett nytt projekt i Google Developers Console för att få ett klient-ID och en klienthemlighet som du senare kan lägga till i Azure AD. 
1. Gå till GoogleS https://console.developers.google.comAPI:er och logga in med ditt Google-konto. Vi rekommenderar att du använder ett delat Team Google-konto.
2. Skapa ett nytt projekt: På instrumentpanelen väljer du **Skapa projekt**och väljer sedan **Skapa**. På sidan Nytt projekt anger du ett **projektnamn**och väljer sedan **Skapa**.
   
   ![Skärmbild som visar en ny projektsida för Google](media/google-federation/google-new-project.png)

3. Kontrollera att det nya projektet är markerat på projektmenyn. Välj sedan **OAuth-medgivandeskärm**under **API:er & Tjänster**.

4. Välj **Externt**och välj sedan **Skapa**. 
5. Ange ett **programnamn**på **skärmen OAuth consent**. (Lämna de andra inställningarna.)

   ![Skärmbild som visar alternativet för google OAuth-medgivande](media/google-federation/google-oauth-consent-screen.png)

6. Bläddra till avsnittet **Auktoriserade domäner** och ange microsoftonline.com.

   ![Skärmbild som visar avsnittet Auktoriserade domäner](media/google-federation/google-oauth-authorized-domains.png)

7. Välj **Spara**.

8. Välj **Autentiseringsuppgifter**. Välj **OAuth-klient-ID på**menyn **Skapa autentiseringsuppgifter** .

   ![Skärmbild som visar alternativet Skapa autentiseringsuppgifter för Google-API:er](media/google-federation/google-api-credentials.png)

9. Under **Programtyp**väljer du **Webbprogram**och anger sedan följande URI:er under **Auktoriserade omdirigering:**
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(var `<directory id>` är ditt katalog-ID)
   
     > [!NOTE]
     > Om du vill hitta ditt https://portal.azure.comkatalog-ID går du till och under **Azure Active Directory**väljer du **Egenskaper** och kopierar **katalog-ID.**

   ![Skärmbild som visar avsnittet Auktoriserade omdirigerings-URI:er](media/google-federation/google-create-oauth-client-id.png)

10. Välj **Skapa**. Kopiera klient-ID och klienthemlighet, som du använder när du lägger till identitetsprovidern i Azure AD-portalen.

   ![Skärmbild som visar OAuth-klient-ID och klienthemlighet](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Steg 2: Konfigurera Google-federationen i Azure AD 
Nu ska du ange Googles klient-ID och klienthemlighet, antingen genom att ange det i Azure AD-portalen eller med PowerShell. Var noga med att testa konfigurationen av Googles federation genom att bjuda in dig själv med en Gmail-adress och försöka lösa in inbjudan med ditt inbjudna Google-konto. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Så här konfigurerar du Google-federationen i Azure AD-portalen 
1. Gå till [Azure-portalen](https://portal.azure.com). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **Organisationsrelationer**.
3. Välj **Identitetsleverantörer**och klicka sedan på **Google-knappen.**
4. Ange ett namn. Ange sedan klient-ID och klienthemlighet som du fick tidigare. Välj **Spara**. 

   ![Skärmbild som visar sidan Lägg till Google-identitetsleverantör](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Så här konfigurerar du Google Federation med PowerShell
1. Installera den senaste versionen av Azure AD PowerShell for Graph-modulen[(AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Kör följande kommando: `Connect-AzureAD`.
3. Logga in med det hanterade globala administratörskontot vid inloggningsprompten.  
4. Kör följande kommando: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Använd klient-ID och klienthemlighet från appen du skapade i "Steg 1: Konfigurera ett utvecklarprojekt för Google". Mer information finns i artikeln [New-AzureADMSIdentityProvider.](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) 
 
## <a name="how-do-i-remove-google-federation"></a>Hur tar jag bort Googles federation?
Du kan ta bort inställningarna för Googles federation. Om du gör det kan google-gästanvändare som redan har löst in sin inbjudan inte logga in, men du kan ge dem åtkomst till dina resurser igen genom att ta bort dem från katalogen och bjuda in dem igen. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Så här tar du bort Google-federationen i Azure AD-portalen: 
1. Gå till [Azure-portalen](https://portal.azure.com). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **Organisationsrelationer**.
3. Välj **Identitetsleverantörer**.
4. På **Google-raden** väljer du snabbmenyn (**...**) och väljer sedan **Ta bort**. 
   
   ![Skärmbild som visar alternativet Ta bort för leverantören av social identitet](media/google-federation/google-social-identity-providers.png)

1. Välj **Ja** för att bekräfta borttagningen. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Så här tar du bort Google Federation med PowerShell: 
1. Installera den senaste versionen av Azure AD PowerShell for Graph-modulen[(AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Kör `Connect-AzureAD`.  
4. Logga in med det hanterade globala administratörskontot i inloggningsmeddelandet.  
5. Ange följande kommando:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Mer information finns i [Ta bort-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
