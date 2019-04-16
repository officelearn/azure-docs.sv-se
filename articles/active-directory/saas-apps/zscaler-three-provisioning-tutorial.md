---
title: 'Självstudier: Konfigurera Zscaler tre för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: I de här självstudierna lär du dig att konfigurera Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Zscaler tre.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 385a1153-0f47-4e41-8f44-da1b49d7629e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: d96444984c503da68ccbda3aef9fea0ede5c7ff9
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579061"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Zscaler tre för automatisk användaretablering

I de här självstudierna lär du dig att konfigurera Azure Active Directory (Azure AD) för att automatiskt etablera och avetablera användare och/eller grupper som ska Zscaler tre.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-tjänst för användaretablering. Viktig information på vilka den här tjänsten gör och hur det fungerar och svar på vanliga frågor och svar, finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Den här anslutningsappen är för närvarande i offentlig förhandsversion. Mer information om allmänna Azure användningsvillkoren för förhandsfunktionerna finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra de steg som beskrivs i den här självstudien behöver du följande:

* En Azure AD-klientorganisation.
* En Zscaler tre klient.
* Ett användarkonto i tre Zscaler med administratörsbehörighet.

> [!NOTE]
> Etablering Azure AD-integrering är beroende av Zscaler ZSCloud SCIM-API, som är tillgängligt för företagskonton.

## <a name="adding-zscaler-three-from-the-gallery"></a>Att lägga till tre Zscaler från galleriet

Innan du konfigurerar Zscaler tre för automatisk användarförsörjning med Azure AD måste du lägga till Zscaler tre från Azure AD-programgalleriet i listan över hanterade SaaS-program.

I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan väljer **Azure Active Directory**:

![Välj Azure Active Directory](common/select-azuread.png)

Gå till **företagsprogram** och välj sedan **alla program**:

![Företagsprogram](common/enterprise-applications.png)

Om du vill lägga till ett program, Välj **nytt program** överst i fönstret:

![Välj nytt program](common/add-new-app.png)

I sökrutan anger **Zscaler tre**. Välj **Zscaler tre** i resultaten och välj sedan **Lägg till**.

![Resultatlistan](common/search-new-app.png)

## <a name="assign-users-to-zscaler-three"></a>Tilldela användare till tre Zscaler

Azure AD-användare måste tilldelas åtkomst till valda appar innan de kan använda dem. I samband med automatisk användaretablering, synkroniseras de användare eller grupper som tilldelas till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Zscaler tre. När du har bestämt som du kan tilldela dessa användare och grupper till Zscaler tre genom att följa instruktionerna i [tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>Viktiga tips för att tilldela användare till tre Zscaler

* Vi rekommenderar att du först tilldela en enda Azure AD-användare till Zscaler tre att testa konfigurationen för automatisk användaretablering. Du kan tilldela fler användare och grupper senare.

* Du måste välja en giltig programspecifika-roll (om tillgängligt) i dialogrutan tilldelning när du tilldelar en användare till Zscaler tre. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="set-up-automatic-user-provisioning"></a>Konfigurera automatisk användaretablering

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och grupper i Zscaler tre baserat på användar- och grupptilldelningar i Azure AD.

> [!TIP]
> Du kanske också vill aktivera SAML-baserad enkel inloggning för Zscaler tre. Om du, följer du anvisningarna i den [Zscaler tre självstudien för enkel inloggning](zscaler-three-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, men de två funktionerna kompletterar varandra.

1. Logga in på den [Azure-portalen](https://portal.azure.com) och välj **företagsprogram** > **alla program** > **Zscaler tre**:

    ![Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Zscaler tre**:

    ![Programlista](common/all-applications.png)

3. Välj den **etablering** fliken:

    ![Zscaler tre etablering](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. Ange den **Etableringsläge** till **automatisk**:

    ![Ange Etableringsläge](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. I den **administratörsautentiseringsuppgifter** anger den **klient-URL** och **hemlighet Token** för tre Zscaler-kontot, enligt beskrivningen i nästa steg.

6. Att hämta den **klient-URL** och **hemlighet Token**går du till **Administration** > **autentiseringsinställningar** i Zscaler Tre portalen och välj **SAML** under **autentiseringstyp**:

    ![Zscaler tre autentiseringsinställningar](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    Välj **konfigurera SAML** att öppna den **konfigurera SAML** fönster:

    ![Konfigurera SAML-fönstret](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    Välj **Enable SCIM-Based etablering** och kopiera den **bas-URL** och **ägar-Token**, och spara inställningarna. Klistra in i Azure-portalen i **bas-URL** till den **klient-URL** box och **ägar-Token** till den **hemlighet Token** box.

7. När du har angett värdena i den **klient-URL** och **hemlighet Token** klickar eller trycker **Testanslutningen** att kontrollera att Azure AD kan ansluta till Zscaler tre. Om anslutningen misslyckas se till att din Zscaler tre kontot har administratörsbehörighet och försök igen.

    ![Testa anslutningen](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. I den **e-postmeddelande** anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel. Välj **skicka ett e-postmeddelande när ett fel inträffar**:

    ![Konfigurera e-postmeddelande](./media/zscaler-three-provisioning-tutorial/notification.png)

9. Välj **Spara**.

10. I den **mappningar** väljer **synkronisera Azure Active Directory-användare till ZscalerThree**:

    ![Synkronisera Azure AD-användare](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. Granska användarattribut som synkroniseras från Azure AD till tre Zscaler i den **attributmappningar** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i Zscaler tre för uppdateringsåtgärder. Välj **spara** att genomföra ändringarna.

    ![Attributmappningar](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. I den **mappningar** väljer **synkronisera Azure Active Directory-grupper till ZscalerThree**:

    ![Synkronisera Azure AD-grupper](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. Granska Gruppattribut som synkroniseras från Azure AD till tre Zscaler i den **attributmappningar** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha grupper i Zscaler tre för uppdateringsåtgärder. Välj **spara** att genomföra ändringarna.

    ![Attributmappningar](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. Om du vill konfigurera Omfångsfilter läser du anvisningarna i den [Scoping filter självstudien](./../active-directory-saas-scoping-filters.md).

15. Om du vill aktivera den Azure AD-etableringstjänsten för Zscaler tre, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet:

    ![Etableringsstatus](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. Ange användare och/eller grupper som önskas kan etableras på tre Zscaler genom att välja de värden som du vill använda under **omfång** i den **inställningar** avsnittet:

    ![Områdesvärden](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. När du är redo att etablera väljer **spara**:

    ![Klicka på Spara](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats under **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker varje 40 minuter så länge som den Azure AD-etableringtjänsten körs. Du kan övervaka förloppet i den **synkroniseringsinformation** avsnittet. Du kan även följa länkar till en aktivitetsrapporten för etablering, som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på Zscaler tre.

Information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png
