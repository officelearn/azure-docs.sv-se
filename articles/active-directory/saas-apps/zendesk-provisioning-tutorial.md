---
title: 'Självstudier: Konfigurera Zendesk för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Zendesk.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e56cc5a893c5a88a5b64466d6feceb20ccd8cdc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167855"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Zendesk för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i Zendesk och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Zendesk. 

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

*   En Azure AD-klient
*   Ett Zendesk-klient med den [Enterprise](https://www.zendesk.com/product/pricing/) planera eller bättre aktiverat 
*   Ett användarkonto i Zendesk med administratörsbehörighet 

> [!NOTE]
> Azure AD etablering integration förlitar sig på den [Zendesk Rest API](https://developer.zendesk.com/rest_api/docs/core/introduction), som är tillgängliga för Zendesk-team på Enterprise-avtalet eller bättre.

## <a name="adding-zendesk-from-the-gallery"></a>Lägga till Zendesk från galleriet
Du måste lägga till Zendesk från Azure AD-programgalleriet i listan över hanterade SaaS-program innan du konfigurerar Zendesk för automatisk användarförsörjning med Azure AD.

**Utför följande steg för att lägga till Zendesk från Azure AD-programgalleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, på den vänstra navigeringspanelen klickar du på den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram** > **alla program**.

    ![Företagsprogram avsnittet][2]
    
3. Lägg till Zendesk, klicka på den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Zendesk**.

    ![Zendesk-etablering](./media/zendesk-provisioning-tutorial/ZenDesk6.png)

5. I resultatpanelen väljer **Zendesk**, och klicka sedan på den **Lägg till** vill lägga till Zendesk i din lista över SaaS-program.

    ![Zendesk-etablering](./media/zendesk-provisioning-tutorial/ZenDesk7.png)

    ![Zendesk-etablering](./media/zendesk-provisioning-tutorial/ZenDesk20.png)

## <a name="assigning-users-to-zendesk"></a>Tilldela användare till Zendesk

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som är ”kopplade” till ett program i Azure AD. 

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Zendesk. När du valt, kan du tilldela dessa användare och/eller grupper till Zendesk genom att följa instruktionerna här:

*   [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Viktiga tips för att tilldela användare till Zendesk

*    Zendesk roller fylls automatiskt och dynamiskt i Azure-portalens användargränssnitt idag. Se till att en initial synkronisering är slutfört mot Zendesk att hämta de senaste rollerna i din Zendesk-klient innan du tilldelar Zendesk-roller till användare.

*    Vi rekommenderar att en enda Azure AD-användare har tilldelats Zendesk för att testa din första automatisk användarförsörjning konfiguration. Ytterligare användare och/eller grupper kan tilldelas senare när testerna har lyckats.
  
*   Vi rekommenderar att en enda Azure AD-användare är tilldelad till Zendesk att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till Zendesk, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-zendesk"></a>Konfigurera automatisk användaretablering till Zendesk 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i Zendesk baserat på användare och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Zendesk, följa anvisningarna enligt den [Zendesk enkel inloggning för självstudien](zendesk-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Konfigurera automatisk användaretablering för Zendesk i Azure AD:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory > företagsprogram > alla program**.

2. Välj Zendesk från din lista över SaaS-program.
 
    ![Zendesk-etablering](./media/zendesk-provisioning-tutorial/ZenDesk3.png)

3. Välj den **etablering** fliken.
    
    ![Zendesk-etablering](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Zendesk-etablering](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet, ange den **Admin Username**, **hemlighet Token**, och **domän** av dina Zendesk-konto. Exempel på dessa värden är:

    *   I den **Admin Username** fältet, fylla i användarnamnet för administratörskontot på din Zendesk-klient. Exempel: admin@contoso.com.

    *   I den **hemlighet Token** fältet, Fyll hemlig token enligt beskrivningen i steg 6.

    *   I den **domän** fältet, fylla i underdomänen för din Zendesk-klient.
    Exempel: Ett konto med ett klient-URL för https://my-tenant.zendesk.com, din underdomän skulle vara **min klient**.

6. Den **hemlighet Token** för ditt Zendesk-konto är baserat i **Admin > API > Inställningar**. 

    ![Zendesk etablering](./media/zendesk-provisioning-tutorial/ZenDesk4.png) ![Zendesk etablering](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till Zendesk. Om anslutningen misslyckas, kontrollera din Zendesk-kontot har administratörsbehörighet och försök igen.

    ![Zendesk-etablering](./media/zendesk-provisioning-tutorial/ZenDesk19.png)
    
8. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

    ![Zendesk-etablering](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Klicka på **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Zendesk**.

    ![Zendesk-etablering](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Granska användarattribut som synkroniseras från Azure AD till Zendesk i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i Zendesk för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Zendesk-etablering](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Under den **mappningar** väljer **synkronisera Azure Active Directory-grupper till ZenDesk**.

    ![Zendesk-etablering](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Granska Gruppattribut som synkroniseras från Azure AD till Zendesk i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha grupper i Zendesk för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Zendesk-etablering](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera den Azure AD-etableringstjänsten för Zendesk, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Zendesk-etablering](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Ange användare och/eller grupper som du vill att etablera till Zendesk genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![Zendesk-etablering](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. När du är redo att etablera, klickar du på **spara**.

    ![Zendesk-etablering](./media/zendesk-provisioning-tutorial/ZenDesk18.png)


Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på Zendesk.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutningen
* Zendesk stöder användning av grupper för användare med Agent-roller. Mer information finns i [Zendesks dokumentation](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).
* När en anpassad roll har tilldelats en användare och/eller grupp kan den automatiska Azure AD-användare etableringstjänsten också tilldelar standardroll **agenten**. Endast **agenter** kan du tilldela en anpassad roll. Mer information finns i det här [Zendesk API-dokumentation](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests).  

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
