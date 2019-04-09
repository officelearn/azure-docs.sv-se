---
title: 'Självstudier: Konfigurera Zscaler för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Zscaler.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 31f67481-360d-4471-88c9-1cc9bdafee24
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 3ea502477cc5b380c99a183d9270c2b2e94375a8
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056443"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Zscaler för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i Zscaler och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Zscaler.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

> Den här anslutningsappen är för närvarande i offentlig förhandsversion. Läs mer på allmänna Microsoft Azure-villkor för användning av förhandsversionsfunktioner [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande:

* En Azure AD-klient
* En Zscaler-klient
* Ett användarkonto i Zscaler med administratörsbehörighet

> [!NOTE]
> Etablering Azure AD-integrering är beroende av Zscaler SCIM-API, som är tillgängliga för Zscaler utvecklare för konton med Enterprise-paketet.

## <a name="adding-zscaler-from-the-gallery"></a>Att lägga till Zscaler från galleriet

Du måste lägga till Zscaler från Azure AD-programgalleriet i listan över hanterade SaaS-program innan du konfigurerar Zscaler för automatisk användarförsörjning med Azure AD.

**Utför följande steg för att lägga till Zscaler från Azure AD-programgalleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Zscaler**väljer **Zscaler** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Zscaler i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Tilldela användare till Zscaler

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som är ”kopplade” till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Zscaler. När du valt, kan du tilldela dessa användare och/eller grupper till Zscaler genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Viktiga tips för att tilldela användare till Zscaler

* Vi rekommenderar att en enda Azure AD-användare har tilldelats Zscaler att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Zscaler, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Konfigurera automatisk användaretablering för Zscaler

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i Zscaler baserat på användare och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Zscaler, följa anvisningarna enligt den [Zscaler enkel inloggning för självstudien](zscaler-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Konfigurera automatisk användaretablering för Zscaler i Azure AD:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och välj **företagsprogram**väljer **alla program**och välj sedan **Zscaler**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Zscaler**.

    ![Zscaler-länk i listan med program](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Etablering av Zscaler](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Etablering av Zscaler](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet, ange den **klient-URL** och **hemlighet Token** för Zscaler-kontot enligt beskrivningen i steg 6.

6. Att hämta den **klient-URL** och **hemlighet Token**, gå till **Administration > autentiseringsinställningar** i användargränssnittet för Zscaler-portalen och klicka på **SAML** under **autentiseringstyp**.

    ![Etablering av Zscaler](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Klicka på **konfigurera SAML** att öppna **Configuration SAML** alternativ.

    ![Etablering av Zscaler](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Välj **Enable SCIM-Based etablering** att hämta **bas-URL** och **ägar-Token**, spara inställningarna. Kopiera den **bas-URL** till **klient-URL**, och **ägar-Token** till **hemlighet Token** i Azure-portalen.

7. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till Zscaler. Om anslutningen misslyckas, kontrollera Zscaler-kontot har administratörsbehörighet och försök igen.

    ![Etablering av Zscaler](./media/zscaler-provisioning-tutorial/test-connection.png)

8. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan **skicka ett e-postmeddelande när ett fel inträffar**.

    ![Etablering av Zscaler](./media/zscaler-provisioning-tutorial/notification.png)

9. Klicka på **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Zscaler**.

    ![Etablering av Zscaler](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Granska användarattribut som synkroniseras från Azure AD till Zscaler i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i Zscaler för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Etablering av Zscaler](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. Under den **mappningar** väljer **synkronisera Azure Active Directory-grupper till Zscaler**.

    ![Etablering av Zscaler](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Granska Gruppattribut som synkroniseras från Azure AD till Zscaler i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha grupper i Zscaler för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Etablering av Zscaler](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](./../active-directory-saas-scoping-filters.md).

15. Om du vill aktivera den Azure AD-etableringstjänsten för Zscaler, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Etablering av Zscaler](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Ange användare och/eller grupper som du vill kan etableras på Zscaler genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![Etablering av Zscaler](./media/zscaler-provisioning-tutorial/scoping.png)

17. När du är redo att etablera, klickar du på **spara**.

    ![Etablering av Zscaler](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på Zscaler.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
