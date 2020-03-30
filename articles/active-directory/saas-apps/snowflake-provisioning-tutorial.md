---
title: 'Självstudiekurs: Konfigurera Snowflake för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Snowflake.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2c5d91894ba35233f3fbebffdff9104edcfdd27b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063172"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Snowflake för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Snowflake och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare och/eller grupper till Snowflake.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klientorganisation.
* [En Snowflake hyresgäst](https://www.Snowflake.com/pricing/).
* Ett användarkonto i Snowflake med administratörsbehörighet.

## <a name="assigning-users-to-snowflake"></a>Tilldela användare till Snowflake

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Snowflake. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Snowflake genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>Viktiga tips för att tilldela användare till Snowflake

* Vi rekommenderar att en enda Azure AD-användare tilldelas Snowflake för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Snowflake måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="setup-snowflake-for-provisioning"></a>Konfigurera Snowflake för etablering

Innan du konfigurerar Snowflake för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering på Snowflake.

1. Logga in på din Snowflake Admin Console. Ange frågan som visas nedan i kalkylbladet markerat och klicka på **Kör**.

    ![Snowflake Admin Konsol](media/Snowflake-provisioning-tutorial/image00.png)

2.  En SCIM Access-token genereras för din Snowflake-klient. För att hämta den, klicka på länken markerad nedan.

    ![Snowflake Lägg TILL SCIM](media/Snowflake-provisioning-tutorial/image01.png)

3. Kopiera det genererade tokenvärdet och klicka på **Klar**. Det här värdet anges i fältet **Hemlig token** på fliken Etablering i ditt Snowflake-program i Azure-portalen.

    ![Snowflake Lägg TILL SCIM](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="add-snowflake-from-the-gallery"></a>Lägg till Snowflake från galleriet

Om du vill konfigurera Snowflake för automatisk användaretablering med Azure AD måste du lägga tillSnowflake från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Snowflake från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Snowflake,** väljer **Snowflake** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Snowflake i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>Konfigurera automatisk användaretablering till Snowflake 

Det här avsnittet guidar dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Snowflake baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för Snowflake , enligt instruktionerna i [Snowflake Single sign-on tutorial](Snowflake-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Snowflake i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Snowflake**i programlistan .

    ![Snowflake-länken i listan med program](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet Administratörsautentiseringsuppgifter anger du i `https://<Snowflake Account URL>/scim/v2` klient-URL:en. Ett exempel på klientadressen:`https://acme.snowflakecomputing.com/scim/v2`

6. Ange värdet **för SCIM-autentiseringstoken** som hämtats tidigare i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Snowflake. Om anslutningen misslyckas kontrollerar du att ditt Snowflake-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

7. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

8. Klicka på **Spara**.

9. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Snowflake**.

    ![Snowflake Användarmappningar](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. Granska användarattributen som synkroniseras från Azure AD till Snowflake i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Snowflake för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Användare av Snowflake-användare](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-grupper mot Snowflake**.

    ![Snowflake Grupp Kartläggningar](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. Granska gruppattributen som synkroniseras från Azure AD till Snowflake i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Snowflake för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Snowflake-gruppattribut](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Om du vill aktivera Azure AD-etableringstjänsten för Snowflake ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

15. Definiera de användare och/eller grupper som du vill etablera till Snowflake genom att välja önskade värden i **Scope** i avsnittet **Inställningar.** Om det här alternativet inte är tillgängligt konfigurerar du de obligatoriska fälten under Administratörsautentiseringsuppgifter, klickar på **Spara** och uppdaterar sidan. 

    ![Etableringsomfång](common/provisioning-scope.png)

16. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

    Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Snowflake.

    Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Begränsningar för anslutning

* Snowflake genererade SCIM-tokens löper ut om 6 månader. Tänk på att dessa måste uppdateras innan de upphör att gälla för att etableringssynkroniseringarna ska kunna fortsätta fungera. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg
* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md).
