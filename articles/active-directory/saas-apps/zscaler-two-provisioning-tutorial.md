---
title: 'Självstudiekurs: Konfigurera Zscaler Two för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: I den här självstudien får du lära dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Zscaler Two.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0a250fcd-6ca1-47c2-a780-7a6278186a69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 364b106e7c1f01269ac02b0c2851f8824ea0f58c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77062703"
---
# <a name="tutorial-configure-zscaler-two-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Zscaler Two för automatisk etablering av användare

I den här självstudien får du lära dig hur du konfigurerar Azure Active Directory (Azure AD) för att automatiskt etablera och avetablera användare och/eller grupper till Zscaler Two.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som är byggd på Azure AD-användarens etableringstjänst. Viktig information om vad den här tjänsten gör och hur den fungerar och svar på vanliga frågor finns i [Automatisera användaretablering och avetablering till SaaS-program med Azure Active Directory](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Krav

För att kunna utföra stegen i den här självstudien behöver du följande:

* En Azure AD-klientorganisation.
* En Zscaler Två hyresgäst.
* Ett användarkonto i Zscaler Two med administratörsbehörighet.

> [!NOTE]
> Azure AD-etableringsintegreringen är beroende av Zscaler Two SCIM API, som är tillgängligt för Enterprise-konton.

## <a name="add-zscaler-two-from-the-gallery"></a>Lägg till Zscaler Two från galleriet

Innan du konfigurerar Zscaler Two för automatisk användaretablering med Azure AD måste du lägga till Zscaler Two från Azure AD-programgalleriet i listan över hanterade SaaS-program.

Välj **Azure Active Directory**i [Azure-portalen](https://portal.azure.com)i den vänstra rutan:

![Välj Azure Active Directory](common/select-azuread.png)

Gå till **Enterprise-program** och välj sedan **Alla program:**

![Företagsprogram](common/enterprise-applications.png)

Om du vill lägga till ett program väljer du **Nytt program** högst upp i fönstret:

![Välj Nytt program](common/add-new-app.png)

Skriv **Zscaler Two**i sökrutan . Välj **Zscaler Två** i resultatet och välj sedan **Lägg till**.

![Resultatlista](common/search-new-app.png)

## <a name="assign-users-to-zscaler-two"></a>Tilldela användare till Zscaler Two

Azure AD-användare måste tilldelas åtkomst till valda appar innan de kan använda dem. I samband med automatisk användaretablering synkroniseras endast användare eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Zscaler Two. När du har bestämt dig kan du tilldela dessa användare och grupper till Zscaler Two genom att följa instruktionerna i [Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-two"></a>Viktiga tips för att tilldela användare till Zscaler Two

* Vi rekommenderar att du först tilldelar en enda Azure AD-användare till Zscaler Two för att testa konfigurationen för automatisk användaretablering. Du kan tilldela fler användare och grupper senare.

* När du tilldelar en användare till Zscaler Two måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="set-up-automatic-user-provisioning"></a>Ställ in automatisk användaretablering

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och grupper i Zscaler Two baserat på användar- och grupptilldelningar i Azure AD.

> [!TIP]
> Du kanske också vill aktivera SAML-baserad enkel inloggning för Zscaler Two. Om du gör det följer du instruktionerna i [Zscaler Two single sign-on tutorial](zscaler-two-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, men de två funktionerna kompletterar varandra.

1. Logga in på [Azure-portalen](https://portal.azure.com) och välj **Enterprise-program** > **Alla program** > **Zscaler Two:**

    ![Företagsprogram](common/enterprise-applications.png)

2. Välj **Zscaler Two**i programlistan:

    ![Programlista](common/all-applications.png)

3. Välj fliken **Etablering:**

    ![Zscaler Två etablering](./media/zscaler-two-provisioning-tutorial/provisioning-tab.png)

4. Ställ in **etableringsläget** på **Automatisk:**

    ![Ange etableringsläge](./media/zscaler-two-provisioning-tutorial/provisioning-credentials.png)

5. I avsnittet **Administratörsautentiseringsuppgifter** anger du **klient-URL:en** och **hemlig token för** ditt Zscaler Two-konto, enligt beskrivningen i nästa steg.

6. Om du vill hämta **klient-URL:en** och **hemlig token**går du till**Administrationsautentiseringsinställningar** **Administration** > i Zscaler Two-portalen och väljer **SAML** under **Autentiseringstyp:**

    ![Zscaler två autentiseringsinställningar](./media/zscaler-two-provisioning-tutorial/secret-token-1.png)

    Välj **Konfigurera SAML** för att öppna **FÖNSTRET Konfigurera SAML:**

    ![Konfigurera SAML-fönster](./media/zscaler-two-provisioning-tutorial/secret-token-2.png)

    Välj **Aktivera SCIM-baserad etablering** och kopiera **bas-URL:en** och **innehavartoken**och spara sedan inställningarna. I Azure-portalen klistrar du in **bas-URL:en** i rutan **Klient-URL** och **innehavartoken** i rutan **Hemlig token.**

7. När du har angett värdena i **rutorna Klient-URL** och **Hemlig token** väljer du **Testa anslutning** för att se till att Azure AD kan ansluta till Zscaler Two. Om anslutningen misslyckas kontrollerar du att ditt Zscaler Two-konto har administratörsbehörighet och försöker igen.

    ![Testa anslutningen](./media/zscaler-two-provisioning-tutorial/test-connection.png)

8. I rutan **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel. Välj **Skicka ett e-postmeddelande när ett fel inträffar:**

    ![Konfigurera e-postmeddelande](./media/zscaler-two-provisioning-tutorial/notification.png)

9. Välj **Spara**.

10. I avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till ZscalerTwo:**

    ![Synkronisera Azure AD-användare](./media/zscaler-two-provisioning-tutorial/user-mappings.png)

11. Granska användarattributen som synkroniseras från Azure AD till Zscaler Two i avsnittet **Attributmappningar.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Zscaler Two för uppdateringsåtgärder. Välj **Spara** om du vill genomföra eventuella ändringar.

    ![Attributmappningar](./media/zscaler-two-provisioning-tutorial/user-attribute-mappings.png)

12. I avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-grupper mot ZscalerTwo:**

    ![Synkronisera Azure AD-grupper](./media/zscaler-two-provisioning-tutorial/group-mappings.png)

13. Granska gruppattributen som synkroniseras från Azure AD till Zscaler Two i avsnittet **Attributmappningar.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Zscaler Two för uppdateringsåtgärder. Välj **Spara** om du vill genomföra eventuella ändringar.

    ![Attributmappningar](./media/zscaler-two-provisioning-tutorial/group-attribute-mappings.png)

14. Information om hur du konfigurerar omfångsfilter finns i instruktionerna i [självstudiefilatkursen För att visa omfånget](./../active-directory-saas-scoping-filters.md).

15. Om du vill aktivera Azure AD-etableringstjänsten för Zscaler Two ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar:**

    ![Etableringsstatus](./media/zscaler-two-provisioning-tutorial/provisioning-status.png)

16. Definiera de användare och/eller grupper som du vill etablera till Zscaler Two genom att välja de värden du vill använda under **Scope** i avsnittet **Inställningar:**

    ![Scopevärden](./media/zscaler-two-provisioning-tutorial/scoping.png)

17. När du är redo att etablera väljer du **Spara:**

    ![Välja Spara](./media/zscaler-two-provisioning-tutorial/save-provisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som **definierats** under Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut, så länge Azure AD-etableringstjänsten körs. Du kan övervaka förloppet i avsnittet **Synkroniseringsinformation.** Du kan också följa länkar till en etableringsaktivitetsrapport som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Zscaler Two.

Information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för företagsappar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-03.png
