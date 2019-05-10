---
title: 'Självstudier: Konfigurera sköter Lösenordshanteraren & digitala valv för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till sköter Lösenordshanteraren & digitala valvet.
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
ms.date: 05/07/2019
ms.author: zchia
ms.openlocfilehash: 445579eb780b49f536ef1a9e13e5ca43db6f98f6
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508559"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Självstudier: Konfigurera sköter Lösenordshanteraren & digitala valv för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i sköter Lösenordshanteraren & digitala valv och Azure Active Directory (AD Azure) att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till sköter lösenord Manager & digitala valvet.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningsappen är för närvarande i offentlig förhandsversion. Läs mer på allmänna Microsoft Azure-villkor för användning av förhandsversionsfunktioner [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Nödvändiga komponenter

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En klient som sköter Lösenordshanteraren & digitala valv](https://keepersecurity.com/pricing.html?t=e)
* Ett användarkonto i sköter Lösenordshanteraren & digitala valvet med administratörsbehörighet.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Lägg till sköter Lösenordshanteraren & digitala valvet från galleriet

Innan du konfigurerar sköter Lösenordshanteraren & digitala valv för automatisk användaretablering med Azure AD, som du behöver lägga till sköter Lösenordshanteraren & digitala valvet från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till sköter Lösenordshanteraren & digitala valvet från Azure AD-programgalleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **sköter Lösenordshanteraren & digitala Vault**väljer **sköter Lösenordshanteraren & digitala Vault** i resultatrutan och klicka sedan på den **Lägg till**för att lägga till programmet.

    ![Keeper Password Manager & Digital Vault i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Tilldela användare till sköter Lösenordshanteraren & digitala valv

Azure Active Directory använder ett begrepp som kallas *tilldelningar* att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till sköter Lösenordshanteraren & digitala valvet. När du valt, kan du tilldela dessa användare och/eller grupper till sköter Lösenordshanteraren & digitala valvet genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Viktiga tips för att tilldela användare till sköter Lösenordshanteraren & digitala valv

* Vi rekommenderar att en enda Azure AD-användare är tilldelad till sköter Lösenordshanteraren & digitala Vault att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till sköter Lösenordshanteraren & digitala valv, måste du välja en giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Konfigurera automatisk användaretablering till sköter Lösenordshanteraren & digitala valv 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i sköter Lösenordshanteraren & digitala Vault baserat på användare och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för sköter Lösenordshanteraren & digitala valv, följa anvisningarna enligt den [sköter Lösenordshanteraren & digitala Vault enkel inloggning för självstudien](keeperpasswordmanager-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Konfigurera automatisk användaretablering för sköter Lösenordshanteraren & digitala valv i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företagsprogram**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Keeper Password Manager & Digital Vault**.

    ![Keeper Password Manager & Digital Vault-länken i programlistan](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Etablering](common/provisioning.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Etablering](common/provisioning-automatic.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet, ange den **klient-URL** och **hemlighet Token** av din sköter Lösenordshanteraren & digitala Vault-kontot enligt beskrivningen i steg 6.

6. Logga in på din [sköter administratörskonsolen](https://keepersecurity.com/console/#login). Klicka på **Admin** och välj en befintlig nod eller skapa en ny. Navigera till den **etablering** fliken och markera **Lägg till metod**.

    ![Sköter-administratörskonsolen](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Välj **SCIM (System för Identitetshantering i domänerna**.

    ![Sköter Lägg till SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Klicka på **skapa etablering Token**.

    ![Sköter skapa slutpunkt](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Kopiera värdena för **URL** och **Token** och klistrar in dem i **klient-URL** och **hemlighet Token** i Azure AD. Klicka på **spara** att slutföra etablering installationen på sköter.

    ![Sköter skapa Token](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till sköter Lösenordshanteraren & digitala valvet. Om anslutningen misslyckas, kontrollera din sköter Lösenordshanteraren & digitala Vault-kontot har administratörsbehörighet och försök igen.

    ![Klient-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

8. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-post för aviseringar](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till sköter Lösenordshanteraren & digitala Vault**.

    ![Sköter Användarmappningar](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Granska användarattribut som synkroniseras från Azure AD sköter Lösenordshanteraren & digitala valv i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i sköter Lösenordshanteraren & digitala valv för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Sköter användarattribut](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Under den **mappningar** väljer **synkronisera Azure Active Directory-grupper som sköter Lösenordshanteraren & digitala Vault**.

    ![Sköter grupp mappningar](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Granska Gruppattribut som synkroniseras från Azure AD sköter Lösenordshanteraren & digitala valv i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha grupper i sköter Lösenordshanteraren & digitala valv för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Attributen för sköter](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aktivera Azure AD etablering sköter Lösenordshanteraren och Digital valvet för att ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Etableringsstatus aktivt](common/provisioning-toggle-on.png)

16. Ange användare och/eller grupper som du vill att etablera till sköter Lösenordshanteraren & digitala valvet genom att välja önskade värden i **omfång** i den **inställningar** avsnittet.

    ![Etablering omfång](common/provisioning-scope.png)

17. När du är redo att etablera, klickar du på **spara**.

    ![Sparar Etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på sköter Lösenordshanteraren & Digitala valvet.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutningen

* Sköter Lösenordshanteraren & digitala Vault kräver **e-postmeddelanden** och **användarnamn** att ha samma värde för källa, eventuella uppdateringar till antingen attribut ändrar andra värdet.
* Sköter Lösenordshanteraren & digitala valv inte stöd för användaren tar bort, bara inaktivera. Inaktiverade användare visas som låst i sköter Admin Console-Gränssnittet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)

