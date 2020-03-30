---
title: 'Självstudiekurs: Konfigurera iProva för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till iProva.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 4edba747-242d-4795-9539-649f33af4c13
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: bb730bad2837616aee0ebfa2da04015542782d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057515"
---
# <a name="tutorial-configure-iprova-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera iProva för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i iProva och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare och/eller grupper till iProva.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En iProva-klient](https://www.iProva.com/)
* Ett användarkonto i iProva med administratörsbehörighet.

## <a name="assigning-users-to-iprova"></a>Tilldela användare till iProva

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till iProva. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till iProva genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-iprova"></a>Viktiga tips för att tilldela användare till iProva

* Vi rekommenderar att en enda Azure AD-användare tilldelas iProva för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till iProva måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="set-up-iprova-for-provisioning"></a>Ställ in iProva för etablering

1. Logga in på [administratörskonsolen iProva](https://www.iProva.com/). Navigera för **att gå till > Application Management**.

    ![iProva-administratörskonsol](media/iprova-provisioning-tutorial/admin.png)

2.  Klicka på **Extern användarhantering**.

    ![iProva Lägg till SCIM](media/iprova-provisioning-tutorial/external.png)

3. Om du vill lägga till **plus** en ny leverantör klickar du på plusikonen. Ange en **rubrik**i dialogrutan **Ny Lägg till leverantör** . Du kan välja att lägga till **IP-baserad åtkomstbegränsning**. Klicka **OK** på OK-knappen.

    ![iProva lägga till nya](media/iprova-provisioning-tutorial/add.png)

    ![iProva-tilläggsleverantör](media/iprova-provisioning-tutorial/addprovider.png)

4.  Klicka på **permanent token-knapp.** Kopiera den **permanenta token** och spara den eftersom detta kommer att vara den enda gången du kan visa den. Det här värdet anges i fältet Hemlig token på fliken Etablering i ditt iProva-program i Azure-portalen.

    ![iProva Skapa token](media/iprova-provisioning-tutorial/token.png)

## <a name="add-iprova-from-the-gallery"></a>Lägga till iProva från galleriet

Innan du konfigurerar iProva för automatisk användaretablering med Azure AD måste du lägga till iProva från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till iProva från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **iProva**, väljer **iProva** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![iProva i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-iprova"></a>Konfigurera automatisk användaretablering till iProva 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i iProva baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för iProva genom att följa instruktionerna i [iProva Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/iProva-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-iprova-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för iProva i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **iProva** i programlistan.

    ![iProva-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://identitymanagement.services.iProva.nl/scim` **klient-URL.** Mata in det **permanenta tokenvärdet** som hämtats tidigare i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till iProva. Om anslutningen misslyckas kontrollerar du att ditt iProva-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till iProva**.

    ![iProva-användarmappningar](media/iprova-provisioning-tutorial/usermappings.png)

9. Granska användarattributen som synkroniseras från Azure AD till iProva i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i iProva för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![iProva-användarattribut](media/iprova-provisioning-tutorial/userattributes.png)

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory Groups till iProva**.

    ![iProva-gruppmappningar](media/iprova-provisioning-tutorial/groupmappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till iProva i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i iProva för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![iProva-gruppattribut](media/iprova-provisioning-tutorial/groupattributes.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för iProva ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till iProva genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på iProva.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

