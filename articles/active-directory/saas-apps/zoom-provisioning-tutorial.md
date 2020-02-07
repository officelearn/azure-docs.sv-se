---
title: 'Självstudie: Konfigurera zoom för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton för att zooma.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: cd832a9dfec4680222d2c985f49aba499a56aaac
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062794"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Självstudie: Konfigurera zoom för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i zooma och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper för att zooma.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En zoomnings klient](https://zoom.us/pricing)
* Ett användar konto i zoom med administratörs behörighet

## <a name="add-zoom-from-the-gallery"></a>Lägg till zoom från galleriet

Innan du konfigurerar zoom för automatisk användar etablering med Azure AD måste du lägga till en zoom från Azure AD-programgalleriet till listan över hanterade SaaS-program.

**Gör så här om du vill lägga till zoom från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **zoom**, väljer **Zooma** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Zoom i listan med resultat](common/search-new-app.png)

## <a name="assign-users-to-zoom"></a>Tilldela användare till zoomning

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver ha åtkomst till zoomning. När du har bestämt dig kan du tilldela dessa användare och/eller grupper för att zooma genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zoom"></a>Viktiga tips för att tilldela användare till zoomning

* Vi rekommenderar att en enda Azure AD-användare är tilldelad att zooma för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare att zooma in måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configure-automatic-user-provisioning-to-zoom"></a>Konfigurera automatisk användar etablering för zoomning 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare eller grupper i zoom baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för zoomning genom att följa anvisningarna i [självstudien zooma enkel inloggning](zoom-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Konfigurera automatisk användar etablering för zoomning i Azure AD

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Zooma**i listan program.

    ![Zoom-länken i listan med program](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** anger du `https://api.zoom.us/scim` i **klient-URL: en**. Om du vill hämta den **hemliga token** för ditt zoomnings konto följer du genom gången enligt beskrivningen i steg 6.

6. Logga in på [Administratörs konsolen för zoomning](https://zoom.us/signin). Gå till **avancerad > zoom för utvecklare** i det vänstra navigerings fönstret.

    ![Zooma in integreringar](media/zoom-provisioning-tutorial/zoom01.png)

    Navigera till **Hantera** i det övre högra hörnet på sidan. 

    ![Installera zoomning](media/zoom-provisioning-tutorial/zoom02.png)

    Navigera till din Azure AD-app som skapats. 
    
    ![Zooma app](media/zoom-provisioning-tutorial/zoom03.png)

    Välj **autentiseringsuppgifter för appen** i det vänstra navigerings fönstret.

    ![Zooma app](media/zoom-provisioning-tutorial/zoom04.png)

    Hämta värdet för JWT-token som visas nedan och mata in detta i fältet **hemligt token** i Azure AD. Om du behöver en ny token som inte upphör att gälla måste du konfigurera om den förfallo tid som automatiskt genererar en ny token. 

    ![Installera zoomning](media/zoom-provisioning-tutorial/zoom05.png)

7. När du fyller i fälten som visas i steg 5, klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till zoomning. Om anslutningen Miss lyckas ser du till att ditt zoomnings konto har administratörs behörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Save** (Spara).

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare att zooma**.

    ![Zooma användar mappningar](media/zoom-provisioning-tutorial/zoom-user-mapping.png)

11. Granska de användarattribut som synkroniseras från Azure AD för att zooma in i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i zoomfunktionen för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.
    
     ![Zooma användar mappningar](media/zoom-provisioning-tutorial/zoom-user-attributes.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för zoomning ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .
    
    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera för att zooma genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten för zoomning.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* Det går inte att konfigurera grupper med hjälp av zoomning.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
