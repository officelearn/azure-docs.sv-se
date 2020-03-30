---
title: 'Självstudiekurs: Konfigurera Promapp för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Promapp.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 41190ba0-9032-4725-9d2c-b3dd9c7786e6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: Zhchia
ms.openlocfilehash: 1a4a27846196e7eb134b834647b2a2d65672f385
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061025"
---
# <a name="tutorial-configure-promapp-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Promapp för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Promapp och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Promapp.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Promapp-klient](https://www.promapp.com/licensing/)
* Ett användarkonto i Promapp med administratörsbehörighet.

## <a name="assigning-users-to-promapp"></a>Tilldela användare till Promapp

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Promapp. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Promapp genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-promapp"></a>Viktiga tips för att tilldela användare till Promapp

* Vi rekommenderar att en enda Azure AD-användare tilldelas Promapp för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Promapp måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="setup-promapp-for-provisioning"></a>Konfigurera Promapp för etablering

1. Logga in på [administratörskonsolen i Promapp](https://freetrial.promapp.com/axelerate/Login.aspx). Under användarnamnet navigerar du till **Min profil**.

    ![Adminkonsol för Promapp](media/promapp-provisioning-tutorial/admin.png)

2.  Klicka på knappen Skapa token under **Åtkomsttoken.** **Access Tokens**

    ![Promapp Lägg till SCIM](media/promapp-provisioning-tutorial/addtoken.png)

3.  Ange alla namn i fältet **Beskrivning** och välj **Scim** på listrutan **Scope.** Klicka på ikonen för att spara.

    ![Lägg till promappsnamn](media/promapp-provisioning-tutorial/addname.png)

4.  Kopiera åtkomsttoken och spara den eftersom det blir den enda gången du kan visa den. Det här värdet anges i fältet Hemlig token på fliken Etablering i Promapp-programmet i Azure-portalen.

    ![Skapa token för Promapp](media/promapp-provisioning-tutorial/token.png)

## <a name="add-promapp-from-the-gallery"></a>Lägg till Promapp från galleriet

Innan du konfigurerar Promapp för automatisk användaretablering med Azure AD måste du lägga till Promapp från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Promapp från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Promapp,** väljer **Promapp** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Promapp i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-promapp"></a>Konfigurera automatisk användaretablering till Promapp 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Promapp baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Promapp genom att följa instruktionerna i [den proaktiva enkelinskriptingskursen](https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-promapp-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Promapp i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Promapp**i programlistan .

    ![Länken Promapp i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://api.promapp.com/api/scim` **klient-URL.** Ange värdet **för SCIM-autentiseringstoken** som hämtats tidigare i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Promapp. Om anslutningen misslyckas kontrollerar du att Promapp-kontot har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Promapp**.

    ![Mappning av Promapp-användare](media/promapp-provisioning-tutorial/usermappings.png)

9. Granska användarattributen som synkroniseras från Azure AD till Promapp i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Promapp för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Promapp-användarattribut](media/promapp-provisioning-tutorial/userattributes.png)

11. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Om du vill aktivera Azure AD-etableringstjänsten för Promapp ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

13. Definiera de användare och/eller grupper som du vill etablera till Promapp genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

14. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Promapp.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

