---
title: 'Självstudier: Konfigurera 4me för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till 4me.
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
ms.author: zchia
ms.openlocfilehash: e2e7c27d8cfa79bc7a8f8462def4d46e598cb508
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595099"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Självstudier: Konfigurera 4me för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i 4me och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare och/eller grupper till 4me.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningsappen är för närvarande i offentlig förhandsversion. Läs mer på allmänna Microsoft Azure-villkor för användning av förhandsversionsfunktioner [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En 4me-klient](https://www.4me.com/trial/)
* Ett användarkonto i 4me med administratörsbehörighet.

## <a name="add-4me-from-the-gallery"></a>Lägg till 4me från galleriet

Du måste lägga till 4me från Azure AD-programgalleriet i listan över hanterade SaaS-program innan du konfigurerar 4me för automatisk användaretablering med Azure AD.

**Utför följande steg för att lägga till 4me från Azure AD-programgalleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **4me**väljer **4me** i resultatrutan och klicka sedan på den **Lägg till** för att lägga till programmet.

    ![4me i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Tilldela användare till 4me

Azure Active Directory använder ett begrepp som kallas *tilldelningar* att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till 4me. När du valt, kan du tilldela dessa användare och/eller grupper till 4me genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Viktiga tips för att tilldela användare till 4me

* Vi rekommenderar att en enda Azure AD-användare har tilldelats 4me att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till 4me, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Konfigurera automatisk användaretablering för 4me 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i 4me baserat på användare och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för 4me, följa instruktionerna i den [4me enkel inloggning för självstudien](4me-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Konfigurera automatisk användaretablering för 4me i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företagsprogram**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **4me** i listan med program.

    ![4me-länken i programlistan](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Etablering](common/provisioning.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Etablering](common/provisioning-automatic.png)

5. Att hämta den **klient-URL** och **hemlighet Token** kontots 4me följer den här genomgången enligt beskrivningen i steg 6.

6. Logga in på den 4me-administratörskonsolen. Gå till **inställningar**.

    ![4me inställningar](media/4me-provisioning-tutorial/4me01.png)

    Skriv i **appar** i sökfältet.

    ![4me appar](media/4me-provisioning-tutorial/4me02.png)

    Öppna den **SCIM** listrutan för att hämta hemligheten-Token och SCIM-slutpunkten.

    ![4me SCIM](media/4me-provisioning-tutorial/4me03.png)

7. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till 4me. Om anslutningen misslyckas, kontrollera 4me-kontot har administratörsbehörighet och försök igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till 4me**.

    ![4me Användarmappningar](media/4me-provisioning-tutorial/4me-user-mapping.png)
    
11. Granska användarattribut som synkroniseras från Azure AD till 4me i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i 4me för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![4me Användarmappningar](media/4me-provisioning-tutorial/4me-user-attributes.png)
    
12. Under den **mappningar** väljer **synkronisera Azure Active Directory-grupper till 4me**.

    ![4me Användarmappningar](media/4me-provisioning-tutorial/4me-group-mapping.png)
    
13. Granska Gruppattribut som synkroniseras från Azure AD till 4me i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha grupper i 4me för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![4me gruppmappningar](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera den Azure AD-etableringstjänsten för 4me, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Etableringsstatus aktivt](common/provisioning-toggle-on.png)

16. Ange användare och/eller grupper som du vill kan etableras på 4me genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![Etablering omfång](common/provisioning-scope.png)

17. När du är redo att etablera, klickar du på **spara**.

    ![Sparar Etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på 4me.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutningen

* 4me har olika SCIM endpoint URL: er för testning och produktionsmiljöer. Tidigare slutar med **.qa** medan det sistnämnda upphört att gälla med **.com**
* 4me genereras hemlighet token har ett utgångsdatum för en månad från generation.
* stöder inte 4me **ta bort** åtgärder

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)