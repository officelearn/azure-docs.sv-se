---
title: Aktivera självbetjäningslösenordsåterställning av Azure Active Directory
description: I den här självstudien får du lära dig hur du aktiverar självbetjäningsåterställning av Azure Active Directory för en grupp användare och testar återställningsprocessen för lösenord.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71b9052f364dfbae205dd324ba69de9578ccc225
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77027680"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Självstudiekurs: Gör det möjligt för användare att låsa upp sitt konto eller återställa lösenord med hjälp av Azure Active Directory självbetjäningslösenordsåterställning

Azure Active Directory (Azure AD) självbetjäning lösenordsåterställning (SSPR) ger användarna möjlighet att ändra eller återställa sitt lösenord, utan administratör eller helpdesk inblandning. Om en användares konto är låst eller om de glömmer sitt lösenord kan de följa uppmaningar om att häva blockeringen och återgå till arbetet. Den här möjligheten minskar antalet supportsamtal och produktivitetsförluster när en användare inte kan logga in på sin enhet eller ett program.

> [!IMPORTANT]
> Den här snabbstarten visar en administratör hur du aktiverar återställning av lösenord för självbetjäning. Om du är en slutanvändare som redan är registrerad för återställning av lösenord https://aka.ms/ssprför självbetjäning och behöver komma tillbaka till ditt konto går du till .
>
> Om IT-teamet inte har aktiverat möjligheten att återställa ditt eget lösenord kan du kontakta din helpdesk för ytterligare hjälp.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Aktivera återställning av lösenord för självbetjäning för en grupp Azure AD-användare
> * Konfigurera autentiseringsmetoder och registreringsalternativ
> * Testa SSPR-processen som användare

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande resurser och privilegier:

* En aktiv Azure AD-klientorganisation med minst en aktiverad utvärderingslicens.
    * Om det behövs, [skapa en gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett konto med global *administratörsbehörighet.*
* En användare som inte är administratör med ett lösenord som du *känner till,* till exempel testanvändare . Du testar slutanvändarens SSPR-upplevelse med det här kontot i den här självstudien.
    * Om du behöver skapa en användare läser du [Snabbstart: Lägg till nya användare i Azure Active Directory](../add-users-azure-active-directory.md).
* En grupp som användaren som inte är administratör är medlem i, till exempel *SSPR-Test-Group*. Du aktiverar SSPR för den här gruppen i den här självstudien.
    * Om du behöver skapa en grupp kan du se hur du [skapar en grupp och lägger till medlemmar i Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

Med Azure AD kan du aktivera SSPR för *ingen,* *markerad*eller *alla* användare. Med den här detaljerade möjligheten kan du välja en delmängd användare för att testa SSPR-registreringsprocessen och arbetsflödet. När du är bekväm med processen och kan kommunicera kraven med en bredare uppsättning användare kan du välja ytterligare grupper av användare som ska aktiveras för SSPR. Du kan också aktivera SSPR för alla i Azure AD-klienten.

I den här självstudien konfigurerar du SSPR för en uppsättning användare i en testgrupp. I följande exempel används gruppen *SSPR-Test-Group.* Ange din egen Azure AD-grupp efter behov:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto med globala administratörsbehörigheter. *global administrator*
1. Sök efter och välj **Azure Active Directory**och välj sedan Återställning av **lösenord** på menyn till vänster.
1. Välj **grupp** som är aktiverad på sidan **Egenskaper** under alternativet *Självbetjäningslösenordsåterställning*
1. Bläddra efter och välj din Azure AD-grupp, till exempel *SSPR-Test-Group*, och välj sedan *Välj*.

    [![](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png "Select a group in the Azure portal to enable for self-service password reset")](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

    Som en del av en bredare distribution av SSPR stöds kapslade grupper. Kontrollera att användarna i gruppen/de grupper som du väljer har tilldelats rätt licenser. Det finns för närvarande ingen valideringsprocess för dessa licenskrav.

1. Om du vill aktivera SSPR för de utvalda användarna väljer du **Spara**.

## <a name="select-authentication-methods-and-registration-options"></a>Välj autentiseringsmetoder och registreringsalternativ

När användare behöver låsa upp sitt konto eller återställa sitt lösenord uppmanas de att ange en ytterligare bekräftelsemetod. Den här ytterligare autentiseringsfaktorn säkerställer att endast godkända SSPR-händelser slutförs. Du kan välja vilka autentiseringsmetoder som ska tillåtas, baserat på den registreringsinformation som användaren tillhandahåller.

1. På sidan **Autentiseringsmetoder** från menyn till vänster anger du **antalet metoder som krävs för att återställa** till *1*.

    För att förbättra säkerheten kan du öka antalet autentiseringsmetoder som krävs för SSPR.

1. Välj de metoder som **är tillgängliga för användare** som din organisation vill tillåta. För den här självstudien markerar du rutorna för att aktivera följande metoder:

    * *Meddelanden via mobilapp*
    * *Kod för mobilapp*
    * *Email*
    * *Mobiltelefon*
    * *Arbetstelefon*

1. Om du vill använda autentiseringsmetoderna väljer du **Spara**.

Innan användarna kan låsa upp sitt konto eller återställa ett lösenord måste de registrera sina kontaktuppgifter. Den här kontaktinformationen används för de olika autentiseringsmetoder som konfigurerats i föregående steg.

En administratör kan manuellt ange den här kontaktinformationen eller så kan användarna gå till en registreringsportal för att själva ange informationen. I den här självstudien konfigurerar du användarna så att de uppmanas att registrera sig när de loggar in nästa gång.

1. På sidan **Registrering** på menyn till vänster väljer du *Ja* för **Att kräva att användare registrerar sig när de loggar in**.
1. Det är viktigt att kontaktinformationen hålls uppdaterad. Om kontaktinformationen är inaktuell när en SSPR-händelse startas kanske användaren inte kan låsa upp sitt konto eller återställa sitt lösenord.

    Ställ in **Antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation** på *180*.
1. Om du vill använda registreringsinställningarna väljer du **Spara**.

## <a name="configure-notifications-and-customizations"></a>Konfigurera meddelanden och anpassningar

Om du vill hålla användarna informerade om kontoaktivitet kan du konfigurera e-postmeddelanden som ska skickas när en SSPR-händelse inträffar. Dessa meddelanden kan omfatta både vanliga användarkonton och administratörskonton. För administratörskonton ger det här meddelandet ytterligare ett lager av medvetenhet när ett lösenord för privilegierade administratörskonton återställs med SSPR.

1. På sidan **Meddelanden** från menyn till vänster konfigurerar du följande alternativ:

   * Ställ in alternativet **Meddela användare om lösenordsåterställning** på *Ja*.
   * Ställ in **Meddela alla administratörer när andra administratörer återställer sina lösenord** på *Ja*.

1. Om du vill använda meddelandeinställningarna väljer du **Spara**.

Om användarna behöver ytterligare hjälp med SSPR-processen kan du anpassa länken för "Kontakta administratören". Denna länk används i SSPR registreringsprocessen och när en användare låser upp sitt konto eller återställer sitt lösenord. För att se till att användarna får den support som behövs rekommenderar vi starkt att de tillhandahåller en anpassad e-post eller webbadress för helpdesk.

1. På **sidan Anpassning** från menyn till vänster anger du *länken Anpassa helpdesk* till **Ja**.
1. I **e-post- eller URL-fältet Anpassad helpdesk** anger du en e-postadress eller webbadress där användarna kan få ytterligare hjälp från din organisation, till exempel*https://support.contoso.com/*
1. Om du vill använda den anpassade länken väljer du **Spara**.

## <a name="test-self-service-password-reset"></a>Testa självåterställning av lösenord

När SSPR är aktiverat och konfigurerat testar du SSPR-processen med en användare som ingår i den grupp som du valde i föregående avsnitt, till exempel *Test-SSPR-Group*. I följande exempel används *testanvändarekontot.* Ange ett eget användarkonto som ingår i den grupp som du har aktiverat för SSPR i det första avsnittet i den här självstudien.

> [!NOTE]
> När du testar återställningen av lösenord för självbetjäning använder du ett icke-administratörskonto. Administratörer är alltid aktiverade för återställning av lösenord med självbetjäning och måste använda två autentiseringsmetoder för att återställa sitt lösenord.

1. Om du vill se den manuella registreringsprocessen öppnar du ett nytt webbläsarfönster [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)i InPrivate- eller inkognitoläge och bläddrar till . Användare bör dirigeras till den här registreringsportalen när de loggar in nästa gång.
1. Logga in med en testanvändare som inte är administratörer, till exempel *testanvändare,* och registrera kontaktinformationen för autentiseringsmetoder.
1. När du är klar väljer du knappen som är markerad **ser bra ut** och stänger webbläsarfönstret.
1. Öppna ett nytt webbläsarfönster i InPrivate- eller inkognitoläge och bläddra till [https://aka.ms/sspr](https://aka.ms/sspr).
1. Ange testanvändarnas kontoinformation som inte är administratörer, till exempel *testanvändare*, tecknen från CAPTCHA och välj sedan **Nästa**.

    ![Ange användarkontoinformation för att återställa lösenordet](media/tutorial-enable-sspr/password-reset-page.png)

1. Följ verifieringsstegen för att återställa lösenordet. När du är klar bör du få ett e-postmeddelande om att lösenordet har återställts.

## <a name="clean-up-resources"></a>Rensa resurser

I en följande självstudiekurs i den här serien konfigurerar du tillbakaskrivning av lösenord. Den här funktionen skriver lösenordsändringar från Azure AD SSPR tillbaka till en lokal AD-miljö. Om du vill fortsätta med den här självstudieserien för att konfigurera tillbakaskrivning av lösenord ska du inte inaktivera SSPR nu.

Om du inte längre vill använda SSPR-funktionen som du har konfigurerat som en del av den här självstudien anger du SSPR-statusen till **Ingen** med hjälp av följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter och välj **Azure Active Directory**och välj sedan Återställning av **lösenord** på menyn till vänster.
1. Välj **Ingen**på sidan **Egenskaper** under alternativet *Självbetjäningslösenordsåterställning*.
1. Om du vill använda SSPR-ändringen väljer du **Spara**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat azure ad-självbetjäningslösenordsåterställning för en vald grupp användare. Du har lärt dig att:

> [!div class="checklist"]
> * Aktivera återställning av lösenord för självbetjäning för en grupp Azure AD-användare
> * Konfigurera autentiseringsmetoder och registreringsalternativ
> * Testa SSPR-processen som användare

> [!div class="nextstepaction"]
> [Aktivera Azure Multi-Factor Authentication](tutorial-mfa-applications.md)
