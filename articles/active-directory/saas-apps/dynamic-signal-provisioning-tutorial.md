---
title: 'Självstudier: Konfigurera dynamisk Signal för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till dynamisk Signal.
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
ms.date: 05/07/2019
ms.author: zchia
ms.openlocfilehash: f79bc083105f997b08f7cfa6f8e08a8f4f455455
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65470560"
---
# <a name="tutorial-configure-dynamic-signal-for-automatic-user-provisioning"></a>Självstudier: Konfigurera dynamisk Signal för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i dynamiska Signal och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper för att dynamiskt Signal.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningsappen är för närvarande i offentlig förhandsversion. Läs mer på allmänna Microsoft Azure-villkor för användning av förhandsversionsfunktioner [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Nödvändiga komponenter

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En dynamisk Signal-klient](https://dynamicsignal.com/)
* Ett användarkonto i dynamiska Signal med administratörsbehörighet.

## <a name="add-dynamic-signal-from-the-gallery"></a>Lägg till dynamiska signalen från galleriet

Du måste lägga till dynamiska signalen från Azure AD-programgalleriet i listan över hanterade SaaS-program innan du konfigurerar dynamisk Signal för automatisk användarförsörjning med Azure AD.

**Utför följande steg för att lägga till dynamiska signalen från Azure AD-programgalleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **dynamisk Signal**väljer **dynamisk Signal** i resultatrutan och klicka sedan på den **Lägg till** för att lägga till programmet.

    ![Dynamic Signal i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-dynamic-signal"></a>Tilldela användare till dynamisk Signal

Azure Active Directory använder ett begrepp som kallas *tilldelningar* att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till dynamisk Signal. När valt, kan du tilldela dessa användare och/eller grupper till dynamisk signalen genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dynamic-signal"></a>Viktiga tips för att tilldela användare till dynamisk Signal

* Vi rekommenderar att en enda Azure AD-användare tilldelas dynamiskt signalen att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till dynamisk Signal, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-dynamic-signal"></a>Konfigurera automatisk användaretablering för dynamisk Signal 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i dynamiska Signal baserat på användare och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för dynamisk Signal, följa anvisningarna enligt den [dynamisk signalen enkel inloggning för självstudien](dynamicsignal-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-dynamic-signal-in-azure-ad"></a>Konfigurera automatisk användaretablering för dynamisk Signal i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företagsprogram**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Dynamic Signal**.

    ![Dynamic Signal-länken i programlistan](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Etablering](common/provisioning.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Etablering](common/provisioning-automatic.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet, ange den **klient-URL** och **hemlighet Token** för den dynamiska signalen kontot enligt beskrivningen i steg 6.

6. I administrationskonsolen dynamisk signalen går du till **Admin > Avancerat > API**.

    ![Dynamisk signalen etablering](./media/dynamic-signal-provisioning-tutorial/secret-token-1.png)

    Kopiera den **SCIM API-URL** till **klient-URL: en**. Klicka på **Generera ny Token** att generera en **ägar-Token** och kopiera värdet till **hemlighet Token**.

    ![Dynamisk signalen etablering](./media/dynamic-signal-provisioning-tutorial/secret-token-2.png)

7. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till dynamisk Signal. Om anslutningen misslyckas, kontrollera dynamisk Signal-kontot har administratörsbehörighet och försök igen.

    ![Klient-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

8. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-post för aviseringar](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till dynamisk Signal**.

    ![Dynamisk signalen-Användarmappningar](media/dynamic-signal-provisioning-tutorial/user-mappings.png)

11. Granska användarattribut som synkroniseras från Azure AD till dynamisk signalen i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i dynamiska Signal för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Dynamisk signalen användarattribut](media/dynamic-signal-provisioning-tutorial/user-mapping-attributes.png)

12. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera den Azure AD-etableringstjänsten för dynamisk Signal, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Etableringsstatus aktivt](common/provisioning-toggle-on.png)

14. Ange användare och/eller grupper som du vill kan etableras på dynamiska signalen genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![Etablering omfång](common/provisioning-scope.png)

15. När du är redo att etablera, klickar du på **spara**.

    ![Sparar Etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på dynamiska Signal.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutningen

* Dynamisk signalen stöder inte permanenta användaren tar bort från Azure AD. Om du vill ta bort en användare permanent i dynamiska signalen har åtgärden görs via dynamisk signalen administratörskonsolen Användargränssnittet. 
* Dynamisk signalen stöder för närvarande inte grupper.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)

