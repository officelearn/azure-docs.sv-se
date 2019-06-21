---
title: 'Självstudier: Konfigurera zoomning för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och användares användarkonton till zoomning.
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
ms.date: 06/3/2019
ms.author: zchia
ms.openlocfilehash: ca79b3901e3933e25c5be92673e0c5bcc464782d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67167861"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Självstudier: Konfigurera zoomning för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i zoomning och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och användares användare och/eller grupper Zooma med skjutreglaget.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningsappen är för närvarande i offentlig förhandsversion. Läs mer på allmänna Microsoft Azure-villkor för användning av förhandsversionsfunktioner [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Nödvändiga komponenter

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En klient för zoomning](https://zoom.us/pricing)
* Ett användarkonto i zoomning med administratörsbehörighet

## <a name="add-zoom-from-the-gallery"></a>Lägg till zoomning från galleriet

Du måste lägga till zoomning från Azure AD-programgalleriet i listan över hanterade SaaS-program innan du konfigurerar zoomning för automatisk användaretablering med Azure AD.

**Utför följande steg för att lägga till zoomning från Azure AD-programgalleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Zooma**väljer **Zooma** i resultatrutan och klicka sedan på den **Lägg till** för att lägga till programmet.

    ![Zoom i listan med resultat](common/search-new-app.png)

## <a name="assign-users-to-zoom"></a>Tilldela användare till Zoom

Azure Active Directory använder ett begrepp som kallas *tilldelningar* att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till zoomning. När du valt, kan du tilldela dessa användare och/eller grupper Zooma med skjutreglaget genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zoom"></a>Viktiga tips för att tilldela användare till Zoom

* Vi rekommenderar att en enda Azure AD-användare har tilldelats zoomning att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till zoomning, måste du välja en giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configure-automatic-user-provisioning-to-zoom"></a>Konfigurera automatisk användaretablering för zoomning 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare eller grupper i zoomning baserat på användare och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för zoomning, följa anvisningarna enligt den [zoomning enkel inloggning för självstudien](zoom-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra.

### <a name="configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Konfigurera automatisk användaretablering för zoomning i Azure AD

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företagsprogram**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Zooma**.

    ![Zoom-länken i listan med program](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Etablering](common/provisioning.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Etablering](common/provisioning-automatic.png)

5. Under den **administratörsautentiseringsuppgifter** anger `https://api.zoom.us/scim` i **klient-URL**. Att hämta den **hemlighet Token** kontots zoomning följer den här genomgången enligt beskrivningen i steg 6.

6. Logga in på din [Zooma administratörskonsolen](https://zoom.us/signin). Gå till **Avancerat > Zooma för utvecklare** i det vänstra navigeringsfönstret.

    ![Zooma integreringar](media/zoom-provisioning-tutorial/zoom01.png)

    Gå till **hantera** i det övre högra hörnet på sidan. 

    ![Zooma installation](media/zoom-provisioning-tutorial/zoom02.png)

    Gå till din skapade Azure AD-app. 
    
    ![Zooma App](media/zoom-provisioning-tutorial/zoom03.png)

    Välj **App autentiseringsuppgifter** i det vänstra navigeringsfönstret.

    ![Zooma App](media/zoom-provisioning-tutorial/zoom04.png)

    Hämta värdet JWT-Token som visas nedan och ange det till den **hemlighet Token** i Azure AD. Om du behöver en ny token aldrig upphör att gälla, behöver du konfigurera om förfallodatum tid som automatiskt ska skapa en ny token. 

    ![Zooma installation](media/zoom-provisioning-tutorial/zoom05.png)

7. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till zoomning. Om anslutningen misslyckas se till att zooma-kontot har administratörsbehörighet och försök igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare att zooma**.

    ![Zooma Användarmappningar](media/zoom-provisioning-tutorial/zoom-user-mapping.png)

11. Granska användarattribut som synkroniseras från Azure AD för att zooma in det **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i zoomning för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.
    
     ![Zooma Användarmappningar](media/zoom-provisioning-tutorial/zoom-user-attributes.png)

12. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera den Azure AD-etableringstjänsten för zoomning, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.
    
    ![Etableringsstatus aktivt](common/provisioning-toggle-on.png)

14. Ange användare och/eller grupper som du vill att etablera Zooma med skjutreglaget genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![Etablering omfång](common/provisioning-scope.png)

15. När du är redo att etablera, klickar du på **spara**.

    ![Sparar Etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten vid zoomning.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutningen

* Zoom har inte stöd för etablering för grupper.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)
