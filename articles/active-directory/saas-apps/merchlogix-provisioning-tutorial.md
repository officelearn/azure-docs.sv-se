---
title: 'Självstudie: Konfigurera MerchLogix för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till MerchLogix.
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061343"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Självstudie: Konfigurera MerchLogix för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i MerchLogix och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till MerchLogix.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* En MerchLogix-klient
* En teknisk kontakt på MerchLogix som kan tillhandahålla SCIM slut punkts-URL och hemlig token som krävs för användar etablering

## <a name="adding-merchlogix-from-the-gallery"></a>Lägga till MerchLogix från galleriet

Innan du konfigurerar MerchLogix för automatisk användar etablering med Azure AD måste du lägga till MerchLogix från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till MerchLogix från Azure AD-programgalleriet:**

1. Klicka på ikonen **Azure Active Directory** i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** . 

    ![Azure Active Directory-knappen][1]

2. Gå till **företags program** > **alla program**.

    ![Avsnittet företags program][2]

3. Om du vill lägga till MerchLogix klickar du på knappen **nytt program** överst i dialog rutan.

    ![Knappen Nytt program][3]

4. I rutan Sök skriver du **MerchLogix**.

5. I resultat panelen väljer du **MerchLogix**och klickar sedan på knappen **Lägg** till för att lägga till MerchLogix i listan över SaaS-program.

    ![MerchLogix-etablering][4]

## <a name="assigning-users-to-merchlogix"></a>Tilldela användare till MerchLogix

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD. 

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till MerchLogix. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till MerchLogix genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Viktiga tips för att tilldela användare till MerchLogix

* Vi rekommenderar att en enda Azure AD-användare tilldelas till MerchLogix för att testa den första konfigurationen för automatisk användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare när testerna har slutförts.

* När du tilldelar en användare till MerchLogix måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Konfigurera automatisk användar etablering till MerchLogix 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i MerchLogix baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för MerchLogix genom att följa anvisningarna i [självstudien om enkel inloggning med MerchLogix](merchlogix-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Konfigurera automatisk användar etablering för MerchLogix i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till **Azure Active Directory > företags program > alla program**.

2. Välj MerchLogix i listan över SaaS-program.

3. Välj fliken **etablering** .

4. Ställ in **etablerings läget** på **automatiskt**.

    ![MerchLogix-etablering](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. Under avsnittet **admin-autentiseringsuppgifter** :

    * I fältet **klient-URL** anger du scim slut punkts adress som tillhandahålls av din MerchLogix tekniska kontakt.

    * I fältet **hemlig token** anger du en hemlig token från din MerchLogix tekniska kontakt.

6. När du fyller i fälten som visas i steg 5, klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till MerchLogix. Om anslutningen Miss lyckas kontrollerar du att MerchLogix-kontot har administratörs behörighet och försöker igen.

7. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

8. Klicka på **Save** (Spara).

9. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till MerchLogix**.

10. Granska de användarattribut som synkroniseras från Azure AD till MerchLogix i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i MerchLogix för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

11. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till MerchLogix**.

12. Granska gruppattributen som synkroniseras från Azure AD till MerchLogix i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i MerchLogix för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

13. Om du vill aktivera Azure AD Provisioning-tjänsten för MerchLogix ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

14. När du är redo att etablera klickar du på **Spara**.

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på MerchLogix.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
