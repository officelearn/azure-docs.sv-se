---
title: 'Självstudiekurs: Konfigurera MerchLogix för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till MerchLogix.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 9df4c7c5-9a58-478e-93b7-2f77aae12807
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4aa60fb565552961a3c85346c39c318a90c8adc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061343"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera MerchLogix för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i MerchLogix och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till MerchLogix.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* En MerchLogix hyresgäst
* En teknisk kontakt på MerchLogix som kan tillhandahålla SCIM-slutpunktsadressen och hemlig token som krävs för att etablera användare

## <a name="adding-merchlogix-from-the-gallery"></a>Lägga till MerchLogix från galleriet

Innan du konfigurerar MerchLogix för automatisk användaretablering med Azure AD måste du lägga till MerchLogix från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till MerchLogix från Azure AD-programgalleriet:**

1. Klicka på Azure Active Directory i **[Azure-portalen](https://portal.azure.com)** på den vänstra navigeringspanelen. **Azure Active Directory** 

    ![Azure Active Directory-knappen][1]

2. Navigera till **Enterprise-program** > **Alla program**.

    ![Avsnittet Företagsprogram][2]

3. Om du vill lägga till MerchLogix klickar du på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. Skriv **MerchLogix**i sökrutan .

5. Välj **MerchLogix**på resultatpanelen och klicka sedan på knappen **Lägg** till för att lägga till MerchLogix i listan över SaaS-program.

    ![MerchLogix Etablering][4]

## <a name="assigning-users-to-merchlogix"></a>Tilldela användare till MerchLogix

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har "tilldelats" till ett program i Azure AD. 

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till MerchLogix. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till MerchLogix genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Viktiga tips för att tilldela användare till MerchLogix

* Vi rekommenderar att en enda Azure AD-användare tilldelas MerchLogix för att testa din första automatiska konfiguration för användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare när testerna har slutförts.

* När du tilldelar en användare till MerchLogix måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Konfigurera automatisk användaretablering till MerchLogix 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i MerchLogix baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för MerchLogix, enligt instruktionerna i [MerchLogix enda inloggningskurs](merchlogix-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för MerchLogix i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory > Enterprise-program > Alla program**.

2. Välj MerchLogix i listan över SaaS-program.

3. Välj fliken **Etablering.**

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![MerchLogix Etablering](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter:**

    * I fältet **Klient-URL** anger du URL:en för SCIM-slutpunkt som tillhandahålls av den tekniska kontakterna för MerchLogix.

    * Ange hemlig token som tillhandahålls av din Tekniska kontakt med MerchLogix i fältet **Hemlig token.**

6. När du fyller i fälten som visas i steg 5 klickar du på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till MerchLogix. Om anslutningen misslyckas kontrollerar du att ditt MerchLogix-konto har administratörsbehörighet och försöker igen.

7. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

8. Klicka på **Spara**.

9. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till MerchLogix**.

10. Granska användarattributen som synkroniseras från Azure AD till MerchLogix i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i MerchLogix för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

11. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-grupper med MerchLogix**.

12. Granska gruppattributen som synkroniseras från Azure AD till MerchLogix i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i MerchLogix för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

13. Om du vill aktivera Azure AD-etableringstjänsten för MerchLogix ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

14. När du är redo att etablera klickar du på **Spara**.

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på MerchLogix.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
