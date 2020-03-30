---
title: 'Självstudiekurs: Konfigurera myPolicies för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till myPolicies.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f000896d-a78c-4d20-a79c-74c1f9b4961a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 353da826b6e339d40a5d85bbf63caac5bf7094f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061377"
---
# <a name="tutorial-configure-mypolicies-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera myPolicies för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i myPolicies och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till myPolicies.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klientorganisation.
* [En myPolicies hyresgäst](https://mypolicies.com/index.html#section10).
* Ett användarkonto i myPolicies med administratörsbehörighet.

## <a name="assigning-users-to-mypolicies"></a>Tilldela användare till myPolicies

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till myPolicies. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till myPolicies genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mypolicies"></a>Viktiga tips för att tilldela användare till myPolicies

* Vi rekommenderar att en enda Azure AD-användare tilldelas myPolicies för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till myPolicies måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="setup-mypolicies-for-provisioning"></a>Konfigurera myPolicies för etablering

Innan du konfigurerar myPolicies för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering på myPolicies.

1. Kontakta din myPolicies-representant **support@mypolicies.com** för att få den hemliga token som behövs för att konfigurera SCIM-etablering.

2.  Spara tokenvärdet från myPolicies representant. Det här värdet anges i fältet **Hemlig token** på fliken Etablering i myPolicies-programmet i Azure-portalen.

## <a name="add-mypolicies-from-the-gallery"></a>Lägg till myPolicies från galleriet

Om du vill konfigurera myPolicies för automatisk användaretablering med Azure AD måste du lägga till myPolicies från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till myPolicies från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **myPolicies,** väljer **myPolicies** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![myPolicies i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mypolicies"></a>Konfigurera automatisk användaretablering till myPolicies 

Det här avsnittet hjälper dig att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i myPolicies baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enda sign-on för myPolicies , efter instruktionerna i [myPolicies Single sign-on tutorial](mypolicies-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-mypolicies-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för myPolicies i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **myPolicies**.

    ![MyPolicies-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter,** indata `https://<myPoliciesCustomDomain>.mypolicies.com/scim` i **klient-URL** där `<myPoliciesCustomDomain>` är din myPolicies anpassade domän. Du kan hämta din myPolicies kunddomän, från din webbadress.
Exempel: `<demo0-qa>`.mypolicies.com.

6. I **hemlig token**anger du tokenvärdet som hämtades tidigare. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till myPolicies. Om anslutningen misslyckas kontrollerar du att ditt myPolicies-konto har administratörsbehörighet och försök igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

7. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

8. Klicka på **Spara**.

9. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till myPolicies**.

    ![myPolicies Användarmappningar](media/mypolicies-provisioning-tutorial/usermapping.png)

10. Granska användarattributen som synkroniseras från Azure AD till myPolicies i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i myPolicies för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![myPolicies Användarmappningar](media/mypolicies-provisioning-tutorial/userattribute.png)

11. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Om du vill aktivera Azure AD-etableringstjänsten för myPolicies ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

13. Definiera de användare och/eller grupper som du vill etablera till myPolicies genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

14. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på myPolicies.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

* myPolicies kräver alltid **userName,** **e-post** och **externalId**.
* myPolicies stöder inte hårda borttagningar för användarattribut.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
