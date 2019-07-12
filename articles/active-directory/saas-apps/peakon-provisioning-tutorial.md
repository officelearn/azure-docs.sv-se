---
title: 'Självstudier: Konfigurera Peakon automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Peakon.
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 2547f34432ca1b4b52f34c343bb4aad2f2407f53
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673150"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Peakon för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i Peakon och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Peakon.

> [!NOTE]
>  Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningsappen är för närvarande i förhandsversion. Läs mer på allmänna Microsoft Azure-villkor för användning av förhandsversionsfunktioner [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav

* En Azure AD-klientorganisation.
* [En klient Peakon](https://peakon.com/us/pricing/).
* Ett användarkonto i Peakon med administratörsbehörighet.

## <a name="assigning-users-to-peakon"></a>Tilldela användare till Peakon

Azure Active Directory använder ett begrepp som kallas *tilldelningar* att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Peakon. När du valt, kan du tilldela dessa användare och/eller grupper till Peakon genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Viktiga tips för att tilldela användare till Peakon 

* Vi rekommenderar att en enda Azure AD-användare har tilldelats Peakon att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Peakon, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="set-up-peakon-for-provisioning"></a>Konfigurera Peakon för etablering

1.  Logga in på din [Peakon administratörskonsolen](https://app.Peakon.com/login). Klicka på **Configuration**. 

    ![Peakon-administratörskonsolen](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Välj **integreringar**.
    
    ![Peakon medarbetare etablera](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Aktivera **medarbetare etablering**.

    ![Peakon medarbetare etablera](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Kopiera värdena för **SCIM 2.0 URL** och **OAuth ägar-Token**. Dessa värden ska skrivas in i den **klient-URL** och **hemlighet Token** i fliken etablering Peakon program i Azure-portalen.

    ![Peakon skapa Token](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Lägg till Peakon från galleriet

Du måste lägga till Peakon från Azure AD-programgalleriet i listan över hanterade SaaS-program för att konfigurera Peakon för automatisk användarförsörjning med Azure AD.

1. I den  **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Peakon**väljer **Peakon** i resultatrutan och klicka sedan på den **Lägg till** för att lägga till programmet.

    ![Peakon i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Konfigurera automatisk användaretablering för Peakon 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i Peakon baserat på användare och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Peakon, följa anvisningarna enligt den [Peakon enkel inloggning för självstudien](peakon-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Konfigurera automatisk användaretablering för Peakon i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företagsprogram**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Peakon**.

    ![Länken Peakon i listan med program](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Etablering](common/provisioning.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Etablering](common/provisioning-automatic.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet, ange den **SCIM 2.0 URL** och **OAuth ägar-Token** värden som du hämtade tidigare i **klient-URL** och **hemlighet Token** respektive. Klicka på **Testanslutningen** att se till att Azure AD kan ansluta till Peakon. Om anslutningen misslyckas, kontrollera Peakon-kontot har administratörsbehörighet och försök igen.

    ![Klient-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

7. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

8. Klicka på **Spara**.

9. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Peakon**.

    ![Peakon Användarmappningar](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Granska användarattribut som synkroniseras från Azure AD till Peakon i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i Peakon för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Peakon användarattribut](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).
    
    ![Etablering omfång](common/provisioning-scope.png)

15. När du är redo att etablera, klickar du på **spara**.

    ![Sparar Etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på Peakon.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutningen

* Alla anpassade användarattribut i Peakon har utökas från Peakons anpassade SCIM användaren utökning av `urn:ietf:params:scim:schemas:extension:peakon:2.0:User`.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)