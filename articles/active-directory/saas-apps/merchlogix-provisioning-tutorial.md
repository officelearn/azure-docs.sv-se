---
title: 'Självstudier: Konfigurera MerchLogix för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till MerchLogix.
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
ms.date: 03/01/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe36969661ae1b729601681c02f79e777b2f8cab
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345004"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Självstudier: Konfigurera MerchLogix för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i MerchLogix och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till MerchLogix. 

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

*   En Azure AD-klient
*   En MerchLogix-klient
*   En teknisk kontakt MerchLogix som kan ge SCIM slutpunkts-URL och Hemlig token krävs för etableringen av användare

## <a name="adding-merchlogix-from-the-gallery"></a>Att lägga till MerchLogix från galleriet
Du måste lägga till MerchLogix från Azure AD-programgalleriet i listan över hanterade SaaS-program innan du konfigurerar MerchLogix för automatisk användarförsörjning med Azure AD.

**Utför följande steg för att lägga till MerchLogix från Azure AD-programgalleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, på den vänstra navigeringspanelen klickar du på den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram** > **alla program**.

    ![Företagsprogram avsnittet][2]
    
3. Lägg till MerchLogix, klicka på den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **MerchLogix**.

5. I resultatpanelen väljer **MerchLogix**, och klicka sedan på den **Lägg till** vill lägga till MerchLogix i din lista över SaaS-program.

    ![MerchLogix etablering][4]

## <a name="assigning-users-to-merchlogix"></a>Tilldela användare till MerchLogix

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som är ”kopplade” till ett program i Azure AD. 

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till MerchLogix. När du valt, kan du tilldela dessa användare och/eller grupper till MerchLogix genom att följa instruktionerna här:

*   [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Viktiga tips för att tilldela användare till MerchLogix

*    Vi rekommenderar att en enda Azure AD-användare har tilldelats MerchLogix att testa din första automatisk användarförsörjning konfiguration. Ytterligare användare och/eller grupper kan tilldelas senare när testerna har lyckats.

*   När du tilldelar en användare till MerchLogix, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Konfigurera automatisk användaretablering för MerchLogix 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i MerchLogix baserat på användare och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för MerchLogix, följa anvisningarna enligt den [MerchLogix enkel inloggning för självstudien](merchlogix-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Konfigurera automatisk användaretablering för MerchLogix i Azure AD:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory > företagsprogram > alla program**.

2. Välj MerchLogix från din lista över SaaS-program.

3. Välj den **etablering** fliken.

4. Ange den **Etableringsläge** till **automatisk**.

    ![MerchLogix etablering](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet:

    *   I den **klient-URL** anger SCIM-slutpunkts-URL som tillhandahålls av din MerchLogix tekniska kontakt.

    *   I den **hemlighet Token** anger hemlig token som tillhandahålls av din MerchLogix tekniska kontakt.

6. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till MerchLogix. Om anslutningen misslyckas, kontrollera MerchLogix-kontot har administratörsbehörighet och försök igen.

    
7. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

8. Klicka på **Spara**.

9. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till MerchLogix**.

10. Granska användarattribut som synkroniseras från Azure AD till MerchLogix i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i MerchLogix för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

11. Under den **mappningar** väljer **synkronisera Azure Active Directory-grupper till MerchLogix**.

12. Granska Gruppattribut som synkroniseras från Azure AD till MerchLogix i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha grupper i MerchLogix för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

13. Om du vill aktivera den Azure AD-etableringstjänsten för MerchLogix, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

14. När du är redo att etablera, klickar du på **spara**.


Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på MerchLogix.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
