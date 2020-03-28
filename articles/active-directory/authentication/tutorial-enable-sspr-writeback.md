---
title: Aktivera tillbakaskrivning av Azure Active Directory-lösenord
description: I den här självstudien får du lära dig hur du aktiverar tillbakaskrivning av självbetjäningsåterställning av Azure AD Connect med Azure AD Connect för att synkronisera ändringar tillbaka till en lokal Active Directory Domain Services-miljö.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3578cb1326ebd701c3f00618c19a501a1476372
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80332124"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>Självstudiekurs: Aktivera tillbakaskrivning av självbetjäningsanering av lösenordsåterbehörighet i Azure Active Directory till en lokal miljö

Med Azure Active Directory (Azure AD) självbetjäningslösenordsåterställning (SSPR) kan användare uppdatera sitt lösenord eller låsa upp sitt konto med hjälp av en webbläsare. I en hybridmiljö där Azure AD är anslutet till en lokal AD DS-miljö (Active Directory Domain Services) kan det här scenariot orsaka att lösenord skiljer sig mellan de två katalogerna.

Återställning av lösenord kan användas för att synkronisera lösenordsändringar i Azure AD tillbaka till din lokala AD DS-miljö. Azure AD Connect tillhandahåller en säker mekanism för att skicka tillbaka dessa lösenordsändringar till en befintlig lokal katalog från Azure AD.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera de behörigheter som krävs för återställning av lösenord
> * Aktivera alternativet för tillbakaskrivning av lösenord i Azure AD Connect
> * Aktivera tillbakaskrivning av lösenord i Azure AD SSPR

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande resurser och privilegier:

* En aktiv Azure AD-klientorganisation med minst en aktiverad utvärderingslicens.
    * Om det behövs, [skapa en gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
    * Mer information finns i [Licenskrav för Azure AD SSPR](concept-sspr-licensing.md).
* Ett konto med globala administratörsbehörighet. *global administrator*
* Azure AD konfigurerat för återställning av lösenord för självbetjäning.
    * Om det behövs [slutför du den föregående självstudien för att aktivera Azure AD SSPR](tutorial-enable-sspr.md).
* En befintlig lokal AD DS-miljö som konfigurerats med en aktuell version av Azure AD Connect.
    * Om det behövs konfigurerar du Azure AD Connect med hjälp av [express-](../hybrid/how-to-connect-install-express.md) eller [anpassade](../hybrid/how-to-connect-install-custom.md) inställningar.
    * Om du vill använda tillbakaskrivning av lösenord måste domänkontrollanterna vara Windows Server 2008 R2 eller senare.

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Konfigurera kontobehörigheter för Azure AD Connect

Med Azure AD Connect kan du synkronisera användare, grupper och autentiseringsuppgifter mellan en lokal AD DS-miljö och Azure AD. Du installerar vanligtvis Azure AD Connect på en Windows Server 2012 eller senare dator som är ansluten till den lokala AD DS-domänen.

För att fungera korrekt med SSPR-tillbakaskrivning måste kontot som anges i Azure AD Connect ha rätt behörighet och alternativ. Om du inte är säker på vilket konto som används för tillfället öppnar du Azure AD Connect och väljer alternativet **Visa aktuell konfiguration.** Kontot som du behöver lägga till behörigheter i visas under **Synkroniserade kataloger**. Följande behörigheter och alternativ måste anges på kontot:

* **Återställ lösenord**
* **Skriv behörigheter** på`lockoutTime`
* **Skriv behörigheter** på`pwdLastSet`
* **Utökade rättigheter** för "Unexpire Password" på antingen:
   * Rotobjektet för *varje domän* i skogen
   * De organisationsenheter för användare som du vill ska omfattas av SSPR

Om du inte tilldelar dessa behörigheter verkar tillbakaskrivningen vara korrekt konfigurerad, men användare stöter på fel när de hanterar sina lokala lösenord från molnet.

Så här konfigurerar du lämpliga behörigheter för återställning av lösenord:

1. Öppna **Active Directory -användare och datorer** med ett konto som har rätt *domänadministratörsbehörighet* i den lokala AD DS-miljön.
1. Kontrollera att **avancerade funktioner** är aktiverade på **Visa-menyn.**
1. På den vänstra panelen högerklickar du det objekt som representerar domänens rot och väljer **Egenskaper** > **säkerhet** > **avancerat**.
1. Välj **Lägg till**på fliken **Behörigheter** .
1. För **Huvudnamn**väljer du det konto som behörigheter ska tillämpas på (kontot som används av Azure AD Connect).
1. I **listrutan Gäller för** väljer du **Underordnade användarobjekt**.
1. Markera rutorna för följande alternativ under *Behörigheter:*
    * **Återställ lösenord**
1. Markera rutorna för följande alternativ under *Egenskaper.* Du måste bläddra igenom listan för att hitta dessa alternativ, som kanske redan är inställda som standard:
    * **Skriv lockoutTime**
    * **Skriv pwdLastSet**

    [![](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png "Set the appropriate permissions in Active Users and Computers for the account that is used by Azure AD Connect")](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. När du är klar väljer du **Använd/OK** för att tillämpa ändringarna och avsluta alla öppna dialogrutor.

När du uppdaterar behörigheter kan det ta upp till en timme eller mer för dessa behörigheter att replikera till alla objekt i katalogen.

Lösenordsprinciper i den lokala AD DS-miljön kan förhindra att återställning av lösenord bearbetas korrekt. För att tillbakaskrivning av lösenord ska fungera mest effektivt måste gruppprincipen för *lägsta ålder* för lösenord anges till 0. Den här inställningen finns under **principer för datorkonfiguration > > Windows-inställningar > säkerhetsinställningar > kontoprinciper** i `gpedit.msc`. 

Om du uppdaterar grupprincipen väntar du tills `gpupdate /force` den uppdaterade principen replikeras eller använder kommandot.

> [!Note]
> För att lösenorden ska kunna ändras omedelbart måste tillbakaskrivning av lösenord ställas in på 0. Men om användarna följer de lokala principerna och *lägsta ålder* för lösenord är inställt på ett värde som är större än noll, fungerar återställningen av lösenord fortfarande efter att de lokala principerna har utvärderats. 

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Aktivera tillbakaskrivning av lösenord i Azure AD Connect

Ett av konfigurationsalternativen i Azure AD Connect är för tillbakaskrivning av lösenord. När det här alternativet är aktiverat, lösenordsändring händelser orsaka Azure AD Connect att synkronisera de uppdaterade autentiseringsuppgifterna tillbaka till den lokala AD DS-miljön.

Om du vill aktivera självbetjäningsåterställning av lösenord aktiverar du först återskrivningsalternativet i Azure AD Connect. Gör så här på din Azure AD Connect-server:

1. Logga in på din Azure AD Connect-server och starta konfigurationsguiden för **Azure AD Connect.**
1. På sidan **Välkommen** klickar du på **Konfigurera**.
1. På sidan **Ytterligare uppgifter** väljer du **Anpassa synkroniseringsalternativ** och väljer sedan **Nästa**.
1. På sidan **Anslut till Azure AD** anger du en global administratörsautentiseringsuppgifter för din Azure-klientorganisation och väljer sedan **Nästa**.
1. På filtreringssidorna **Anslut kataloger** och **Domän/OU** väljer du **Nästa**.
1. På sidan **Valfria funktioner** markerar du rutan bredvid **Tillbakaskrivning av lösenord** och väljer **Nästa**.

    ![Konfigurera Azure AD Connect för tillbakaskrivning av lösenord](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. På sidan **Klart att konfigurera** väljer du **Konfigurera** och väntar tills processen slutförts.
1. När du ser att konfigurationen är klar väljer du **Avsluta**.

## <a name="enable-password-writeback-for-sspr"></a>Aktivera tillbakaskrivning av lösenord för SSPR

Med tillbakaskrivning av lösenord aktiverat i Azure AD Connect konfigurerar du nu Azure AD SSPR för tillbakaskrivning. När du aktiverar SSPR för att använda tillbakaskrivning av lösenord har användare som ändrar eller återställer sitt lösenord det uppdaterade lösenordet synkroniserat tillbaka till den lokala AD DS-miljön.

Så här aktiverar du tillbakaskrivning av lösenord i SSPR:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett globalt administratörskonto.
1. Sök efter och välj **Azure Active Directory**, välj **Lösenordsåterställning**och välj sedan **Lokal integrering**.
1. Ange alternativet för Skriv tillbaka lösenord till din *Yes* **lokala katalog?**
1. Ange alternativet för Tillåt användare att låsa upp konton utan *att*återställa **sitt lösenord?**

    ![Aktivera Azure AD-återställning av lösenordslösenord för återställning av lösenord](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. När du är klar väljer du **Spara**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre vill använda SSPR-återskrivningsfunktionen som du har konfigurerat som en del av den här självstudien gör du följande:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter och välj **Azure Active Directory**, välj **Lösenordsåterställning**och välj sedan **Lokal integrering**.
1. Ange alternativet för Skriv tillbaka lösenord till din *No* **lokala katalog?**
1. Ange alternativet för Tillåt användare att låsa upp konton utan *att*återställa **sitt lösenord?**

Om du inte längre vill använda några lösenordsfunktioner utför du följande steg från din Azure AD Connect-server:

1. Logga in på din Azure AD Connect-server och starta konfigurationsguiden för **Azure AD Connect.**
1. På sidan **Välkommen** klickar du på **Konfigurera**.
1. På sidan **Ytterligare uppgifter** väljer du **Anpassa synkroniseringsalternativ** och väljer sedan **Nästa**.
1. På sidan **Anslut till Azure AD** anger du en global administratörsautentiseringsuppgifter för din Azure-klientorganisation och väljer sedan **Nästa**.
1. På filtreringssidorna **Anslut kataloger** och **Domän/OU** väljer du **Nästa**.
1. På sidan **Valfria funktioner** avmarkerar du rutan bredvid **Tillbakaskrivning av lösenord** och väljer **Nästa**.
1. På sidan **Klart att konfigurera** väljer du **Konfigurera** och väntar tills processen slutförts.
1. När du ser att konfigurationen är klar väljer du **Avsluta**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat Azure AD SSPR-tillbakaskrivning till en lokal AD DS-miljö. Du har lärt dig att:

> [!div class="checklist"]
> * Konfigurera de behörigheter som krävs för återställning av lösenord
> * Aktivera alternativet för tillbakaskrivning av lösenord i Azure AD Connect
> * Aktivera tillbakaskrivning av lösenord i Azure AD SSPR

> [!div class="nextstepaction"]
> [Utvärdera risk vid inloggning](tutorial-risk-based-sspr-mfa.md)
