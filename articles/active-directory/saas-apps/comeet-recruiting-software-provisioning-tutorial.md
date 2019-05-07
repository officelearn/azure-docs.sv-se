---
title: 'Självstudier: Konfigurera Comeet rekrytering programvara för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Comeet rekrytering programvara.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: zchia
ms.openlocfilehash: 0f1b5f424a71aeccd4b1e57129c0f5b22ff158af
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159397"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Comeet rekrytering programvara för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i Comeet rekrytering programvara och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper Comeet rekrytering programvara.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningsappen är för närvarande i offentlig förhandsversion. Läs mer på allmänna Microsoft Azure-villkor för användning av förhandsversionsfunktioner [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Nödvändiga komponenter

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En Comeet rekrytering programvara-klient](https://www.comeet.co/)
* Ett användarkonto i Comeet rekrytering programvara med administratörsbehörighet.

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Lägg till Comeet rekrytering programvara från galleriet

Du måste lägga till Comeet rekrytering programvara från Azure AD-programgalleriet i listan över hanterade SaaS-program innan du konfigurerar Comeet rekrytering programvara för automatisk användaretablering med Azure AD.

**Utför följande steg för att lägga till Comeet rekrytering programvara från Azure AD-programgalleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Comeet rekrytering programvara**väljer **Comeet rekrytering programvara** i resultatrutan och klicka sedan på den **Lägg till** för att lägga till programmet.

    ![Comeet Recruiting Software i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Tilldela användare till Comeet rekrytering programvara

Azure Active Directory använder ett begrepp som kallas *tilldelningar* att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Comeet rekrytering programvara. När du valt, kan du tilldela dessa användare och/eller grupper Comeet rekrytering programvara genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Viktiga tips för att tilldela användare till Comeet rekrytering programvara

* Vi rekommenderar att en enda Azure AD-användare har tilldelats Comeet rekrytering programvara för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Comeet rekrytering programvara, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Konfigurera automatisk användaretablering för Comeet rekrytering programvara 

Det här avsnittet guider dig genom stegen för att konfigurera Azure AD etableringstjänsten att skapa, uppdatera och inaktivera användare och/eller grupper i Comeet rekrytering programvara baserat på användare och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Comeet rekrytering programvara, följa anvisningarna enligt den [Comeet rekrytering programvara enkel inloggning för självstudien](comeetrecruitingsoftware-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Konfigurera automatisk användaretablering för Comeet rekrytering programvara i Azure AD:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och välj **företagsprogram**väljer **alla program**och välj sedan **Comeet rekrytering programvara**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Comeet Recruiting Software**.

    ![Länken Comeet Recruiting Software i listan med program](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Etablering](common/provisioning.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Etablering](common/provisioning-automatic.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet, ange den **klient-URL** och **hemlighet Token** för din Comeet rekrytering programvarans kontot enligt beskrivningen i steg 6.

6. I den [Comeet rekrytering programvara administratörskonsolen](https://app.comeet.co/), gå till **Comeet > Inställningar > autentisering > Microsoft Azure**, och kopiera den **hemlighet Token för ditt företag**värde till den **hemlighet Token** i Azure AD.

    ![Comeet rekrytering tillhandahållande av programvara](./media/comeetrecruitingsoftware-provisioning-tutorial/secret-token-1.png)
    

7. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till Comeet rekrytering programvara. Om anslutningen misslyckas, kontrollera Comeet rekrytering programvara-kontot har administratörsbehörighet och försök igen.

    ![Token](common/provisioning-testconnection-token.png)

8. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-post för aviseringar](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Comeet**.

    ![Comeet rekrytering tillhandahållande av programvara](./media/comeetrecruitingsoftware-provisioning-tutorial/user-mappings.png)

11. Granska användarattribut som synkroniseras från Azure AD med Comeet rekrytering programvara i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i Comeet rekrytering programvara för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Comeet rekrytering tillhandahållande av programvara](./media/comeetrecruitingsoftware-provisioning-tutorial/user-mapping-attributes.png)

12. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera den Azure AD-etableringstjänsten för Comeet rekrytering programvara, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Etableringsstatus aktivt](common/provisioning-toggle-on.png)

14. Ange användare och/eller grupper som du vill att etablera Comeet rekrytering programvara genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![Etablering omfång](common/provisioning-scope.png)

15. När du är redo att etablera, klickar du på **spara**.

    ![Sparar Etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten Comeet rekrytering programvaran.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutningen

* Comeet rekrytering programvara stöder för närvarande inte grupper.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
