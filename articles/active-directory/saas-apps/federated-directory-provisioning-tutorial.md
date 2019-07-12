---
title: 'Självstudier: Konfigurera federerad Directory för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till federerad Directory.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 19f5690a6852161abce2565a8c4a52ce86ff5187
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840594"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Självstudier: Konfigurera federerad Directory för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i federerad Directory och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till federerad Directory.

> [!NOTE]
>  Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningsappen är för närvarande i offentlig förhandsversion. Läs mer på allmänna Microsoft Azure-villkor för användning av förhandsversionsfunktioner [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [Ett federerat Directory](https://www.federated.directory/pricing).
* Ett användarkonto i federerad katalog med administratörsbehörighet.

## <a name="assign-users-to-federated-directory"></a>Tilldela användare till federerad katalog
Azure Active Directory använder ett begrepp som kallas tilldelningar för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till federerade katalog. När du valt, kan du tilldela dessa användare och/eller grupper till federerad katalog genom att följa instruktionerna här:

 * [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Viktiga tips för att tilldela användare till federerad katalog
 * Vi rekommenderar att en enda Azure AD-användare är tilldelad till federerad katalog att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till federerad katalog, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med rollen som standard åtkomst undantas från etablering.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Konfigurera federerad Directory för etablering

Innan du konfigurerar federerad katalog för automatisk användaretablering med Azure AD, måste du aktivera SCIM-etablering federerad katalogen.

1. Logga in på din [federerad Directory-administratörskonsolen](https://federated.directory/of)

    ![Federerad Directory självstudien](media/federated-directory-provisioning-tutorial/companyname.png)

2. Gå till **kataloger > användarkataloger** och välj din klient. 

    ![Federerad directory](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Om du vill generera en permanent ägartoken, navigera till **Directory nycklar > Skapa ny nyckel.** 

    ![Federerad directory](media/federated-directory-provisioning-tutorial/federated01.png)

4. Skapa en katalog-nyckel. 

    ![Federerad directory](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Kopiera den **åtkomsttoken** värde. Det här värdet ska anges i den **hemlighet Token** i fliken etablering programmets federerad Directory i Azure-portalen. 

    ![Federerad directory](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Lägg till federerad katalog från galleriet

Om du vill konfigurera federerad Directory för automatisk användarförsörjning med Azure AD, som du behöver lägga till federerad Directory från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Om du vill lägga till federerad katalog från Azure AD-programgalleriet, utför du följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **federerad Directory**väljer **federerad Directory** i resultatrutan.

    ![Federerad katalog i listan med resultat](common/search-new-app.png)

5. Navigera till den **URL** markerade nedan i en separat webbläsare. 

    ![Federerad directory](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Klicka på **LOGGA IN**.

    ![Federerad directory](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Eftersom federerad Directory är en OpenIDConnect app, väljer du att logga in till federerad katalog med ditt Microsoft-arbetskonto.
    
    ![Federerad directory](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. Acceptera medgivande för medgivande sidan efter en lyckad autentisering. Programmet sedan läggs automatiskt till din klient och du kommer att omdirigeras till din federerad Directory-konto.

    ![Federerad directory Lägg till SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Konfigurera automatisk användaretablering till federerad katalog 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i federerad Directory baserat på användare och/eller grupptilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Konfigurera automatisk användaretablering för federerad katalog i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företagsprogram**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **federerad Directory**.

    ![Federerad Directory länken i listan med program](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Etablering](common/provisioning.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Etablering](common/provisioning-automatic.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet Ange `https://api.federated.directory/v2/` i klient-URL. Ange värdet som du hämtade och sparat tidigare från federerad katalog i **hemlighet Token**. Klicka på **Testanslutningen** att se till att Azure AD kan ansluta till federerad katalogen. Om anslutningen misslyckas, kontrollera din federerad Directory-kontot har administratörsbehörighet och försök igen.

    ![Klient-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

8. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till federerad Directory**.

    ![Federerad Directory självstudien](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Granska användarattribut som synkroniseras från Azure AD till federerad katalog i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i federerad katalog för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Federerad Directory självstudien](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera den Azure AD-etableringstjänsten för federerad katalog, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Etableringsstatus aktivt](common/provisioning-toggle-on.png)

14. Ange användare och/eller grupper som du vill att etablera till federerad katalog genom att välja önskade värden i **omfång** i den **inställningar** avsnittet.

    ![Etablering omfång](common/provisioning-scope.png)

15. När du är redo att etablera, klickar du på **spara**.

    ![Sparar Etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten federerad katalogen.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarens kontoetablering](../manage-apps/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)