---
title: Lägg till Google som en identitets leverantör för B2B – Azure AD
description: Federera med Google så att gäst användare kan logga in på dina Azure AD-appar med sitt eget Gmail-konto
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
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126647"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Lägg till Google som en identitets leverantör för B2B-gäst användare

Genom att konfigurera Federation med Google kan du tillåta att inbjudna användare loggar in på dina delade appar och resurser med egna Gmail-konton, utan att behöva skapa Microsoft-konton (MSA: er). 

> [!NOTE]
> Google Federation är särskilt utformat för Gmail-användare. Använd [funktionen Direct Federation](direct-federation.md)för att federera med G Suite-domäner.

## <a name="what-is-the-experience-for-the-google-user"></a>Vad är upplevelsen av Google User?
När du skickar en inbjudan till en Google Gmail-användare bör gäst användaren komma åt dina delade appar eller resurser med en länk som innehåller klient kontexten. Deras upplevelse varierar beroende på om de redan är inloggade på Google:
  - Om gäst användaren inte är inloggad på Google uppmanas de att logga in på Google.
  - Om gäst användaren redan är inloggad på Google uppmanas de att välja det konto de vill använda. De måste välja det konto som du använde för att bjuda in dem.

Om gäst användaren ser ett fel meddelande om att huvudet är för långt, kan de försöka rensa sina cookies, eller så kan de öppna ett privat eller Incognito-fönster och försöka logga in igen.

![Skärm bild som visar Google-inloggnings Sidan](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Begränsningar

Team stöder fullständigt Google gäst användare på alla enheter. Google-användare kan logga in till Teams från en gemensam slut punkt som `https://teams.microsoft.com`.

Vanliga slut punkter för andra program kanske inte stöder Google-användare. Google gäst användare måste logga in med en länk som innehåller din klient information. Följande är exempel på detta:
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Om Google gäst användare försöker använda en länk som `https://myapps.microsoft.com` eller `https://portal.azure.com`får de ett fel meddelande.

Du kan också ge Google gäst användare en direkt länk till ett program eller en resurs, så länge den här länken innehåller din klient information, till exempel `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`. 

## <a name="step-1-configure-a-google-developer-project"></a>Steg 1: Konfigurera ett Google Developer-projekt
Börja med att skapa ett nytt projekt i Google Developer-konsolen för att hämta ett klient-ID och en klient hemlighet som du senare kan lägga till i Azure AD. 
1. Gå till Google-API: erna på https://console.developers.google.comoch logga in med ditt Google-konto. Vi rekommenderar att du använder ett delat team Google-konto.
2. Skapa ett nytt projekt: Välj **skapa projekt**på instrument panelen och välj sedan **skapa**. På sidan nytt projekt anger du ett **projekt namn**och väljer sedan **skapa**.
   
   ![Skärm bild som visar en ny projekt sida för Google](media/google-federation/google-new-project.png)

3. Se till att det nya projektet är markerat i menyn projekt. Under **API: er & tjänster**väljer du sedan **OAuth-medgivande skärmen**.

4. Välj **extern**och välj sedan **skapa**. 
5. Ange ett **program namn**på **skärmen OAuth-medgivande**. (Lämna övriga inställningar.)

   ![Skärm bild som visar skärm alternativet Google OAuth-medgivande](media/google-federation/google-oauth-consent-screen.png)

6. Bläddra till avsnittet **godkända domäner** och ange microsoftonline.com.

   ![Skärm bild som visar avsnittet godkända domäner](media/google-federation/google-oauth-authorized-domains.png)

7. Välj **Spara**.

8. Välj **autentiseringsuppgifter**. På menyn **skapa autentiseringsuppgifter** väljer du **OAuth-klient-ID**.

   ![Skärm bild som visar alternativet Google API: er skapa autentiseringsuppgifter](media/google-federation/google-api-credentials.png)

9. Under **program typ**väljer du **webb program**och under **auktoriserade omdirigerings-URI: er**anger du följande URI: er:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(där `<directory id>` är katalog-ID)
   
     > [!NOTE]
     > Om du vill hitta ditt katalog-ID går du till https://portal.azure.comoch väljer sedan **Egenskaper** under **Azure Active Directory**och kopierar **katalog-ID: t**.

   ![Skärm bild som visar avsnittet behöriga omdirigerings-URI: er](media/google-federation/google-create-oauth-client-id.png)

10. Välj **Skapa**. Kopiera klient-ID och klient hemlighet som du ska använda när du lägger till identitets leverantören i Azure AD-portalen.

   ![Skärm bild som visar OAuth-klient-ID och klient hemlighet](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Steg 2: konfigurera Google Federation i Azure AD 
Nu ska du ange Google-klient-ID och klient hemlighet, antingen genom att ange den i Azure AD-portalen eller med hjälp av PowerShell. Se till att testa din Google Federation-konfiguration genom att bjuda in dig själv med en Gmail-adress och försöka lösa in inbjudan med ditt inbjudna Google-konto. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Konfigurera Google Federation i Azure AD-portalen 
1. Gå till [Azure-portalen](https://portal.azure.com). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **organisations relationer**.
3. Välj **identitets leverantörer**och klicka sedan på **Google** -knappen.
4. Ange ett namn. Ange sedan det klient-ID och den klient hemlighet som du fick tidigare. Välj **Spara**. 

   ![Skärm bild som visar sidan Lägg till Google Identity Provider](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Konfigurera Google Federation med PowerShell
1. Installera den senaste versionen av Azure AD PowerShell för Graph module ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Kör följande kommando: `Connect-AzureAD`.
3. Logga in med det hanterade globala administratörs kontot vid inloggnings meddelandet.  
4. Kör följande kommando: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Använd klient-ID och klient hemlighet från den app som du skapade i "steg 1: Konfigurera ett Google Developer-projekt". Mer information finns i artikeln [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) . 
 
## <a name="how-do-i-remove-google-federation"></a>Hur gör jag för att ta bort Google Federation?
Du kan ta bort din Google Federation-installation. Om du gör det kan Google gäst användare som redan har löst deras inbjudan inte logga in, men du kan ge dem åtkomst till dina resurser igen genom att ta bort dem från katalogen och bjuda in dem igen. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Ta bort Google Federation i Azure AD-portalen: 
1. Gå till [Azure-portalen](https://portal.azure.com). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **organisations relationer**.
3. Välj **identitets leverantörer**.
4. Välj snabb menyn ( **...** ) på **Google** -raden och välj sedan **ta bort**. 
   
   ![Skärm bild som visar alternativet ta bort för den sociala identitets leverantören](media/google-federation/google-social-identity-providers.png)

1. Bekräfta borttagningen genom att välja **Ja** . 

### <a name="to-delete-google-federation-by-using-powershell"></a>Ta bort Google Federation med PowerShell: 
1. Installera den senaste versionen av Azure AD PowerShell för Graph module ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Kör `Connect-AzureAD`.  
4. Logga in med det hanterade globala administratörs kontot i inloggnings meddelandet.  
5. Ange följande kommando:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Mer information finns i [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
