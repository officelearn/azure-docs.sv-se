---
title: 'Självstudier: Konfigurera Cisco för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Cisco Webex.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: v-wingf
ms.openlocfilehash: fdaf77e3d8a1858372298fb0d67ca05c2717adf6
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321152"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Cisco Webex för automatisk användaretablering


Syftet med den här kursen är att ange vilka åtgärder som ska utföras i Cisco Webex och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera Cisco Webex användare.


> [!NOTE]
> Den här självstudiekursen beskriver en koppling som bygger på Etableringstjänsten Azure AD-användare. Viktig information om vad tjänsten gör så här fungerar och vanliga frågor och svar finns [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande krav:

*   En Azure AD-klient
*   En Cisco-Webex-klient
*   Ett användarkonto i Cisco Webex med administratörsbehörigheter


> [!NOTE]
> Azure AD-etablering integration förlitar sig på den [Cisco Webex Webservice](https://developer.webex.com/getting-started.html), som är tillgängliga för Cisco Webex team.

## <a name="adding-cisco-webex-from-the-gallery"></a>Att lägga till Cisco Webex från galleriet
Du måste lägga till Cisco Webex från Azure AD application gallery i listan över hanterade SaaS-program innan du konfigurerar Cisco Webex för automatisk användaretablering med Azure AD.

**Utför följande steg för att lägga till Cisco Webex från Azure AD application gallery:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, i det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram** > **alla program**.

    ![Företagsprogram avsnitt][2]

3. Lägg till Cisco Webex, klicka på den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Cisco Webex**.

    ![Cisco Webex etablering](./media/cisco-webex-provisioning-tutorial/AppSearch.png)

5. Välj i resultatpanelen **Cisco Webex**, och klicka sedan på den **Lägg till** för att lägga till Cisco Webex i listan med SaaS-program.

    ![Cisco Webex etablering](./media/cisco-webex-provisioning-tutorial/AppSearchResults.png)

    ![Cisco Webex etablering](./media/cisco-webex-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-webex"></a>Tilldela användare till Cisco Webex

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras bara den användare och/eller grupper som har ”tilldelats” till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare i Azure AD behöver åtkomst till Cisco Webex. När du valt, kan du tilldela dessa användare till Cisco Webex genom att följa anvisningarna här:

*   [Tilldela en användare eller grupp till en enterprise-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Viktiga tips för att tilldela användare till Cisco Webex

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats Cisco Webex att testa automatisk konfiguration för användaretablering. Ytterligare användare kan tilldelas senare.

*   När du tilldelar en användare Cisco Webex, måste du välja någon giltig programspecifika roll (om tillgängligt) i dialogrutan tilldelning. Användare med den **standard åtkomst** roll är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Konfigurera automatisk användaretablering Cisco Webex

Det här avsnittet hjälper dig igenom stegen för att konfigurera Azure AD etableras för att skapa, uppdatera och inaktivera användare i Cisco Webex baserat på användare i Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Konfigurera automatisk användaretablering för Cisco Webex i Azure AD:


1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory > företagsprogram > alla program**.

2. Välj Cisco Webex från listan med SaaS-program.

    ![Cisco Webex etablering](./media/cisco-webex-provisioning-tutorial/Successcenter2.png)

3. Välj den **etablering** fliken.

    ![Cisco Webex etablering](./media/cisco-webex-provisioning-tutorial/ProvisioningTab.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Cisco Webex etablering](./media/cisco-webex-provisioning-tutorial/ProvisioningCredentials.png)

5. Under den **administratörsautentiseringsuppgifter** avsnitt, ange den **klient URL**, och **hemlighet Token** för din Cisco Webex konto.

    *   I den **klient URL** fältet, Fyll i Cisco Webex SCIM API-URL för din klient i form av `https://api.webex.com/v1/scim/[Tenant ID]/`, där `[Tenant ID]` är en alfanumerisk sträng som beskrivs i steg 6.

    *   I den **hemlighet Token** fältet, fylla hemlighet-Token som beskrivs i steg 6.

1. Den **klient-ID** och **hemlighet Token** för Cisco-Webex konto kan hittas genom att logga in på [Cisco Webex developer plats](https://developer.webex.com/) med ditt administratörskonto. Efter att du loggat –
    * Gå till den [sidan komma igång](https://developer.webex.com/getting-started.html)
    * Rulla ned till den [Autentiseringsavsnittet](https://developer.webex.com/getting-started.html#authentication)
    ![Cisco Webex-autentiseringstoken](./media/cisco-webex-provisioning-tutorial/SecretToken.png)
    * I rutan alfanumeriska strängen är din **hemlighet Token**. Kopiera den här variabeln till Urklipp
    * Gå till den [sidan få information om min egen](https://developer.webex.com/endpoint-people-me-get.html)
        * Se till att testa läge är Aktiverat
        * Skriv ordet ”ägar” följt av ett blanksteg och klistra in hemlighet-Token i fältet auktorisering ![Cisco Webex-autentiseringstoken](./media/cisco-webex-provisioning-tutorial/GetMyDetails.png)
        * Klicka på Kör
    * I svarstext till höger i **klient-ID** visas som ”orgId”:

    ```json
    {
        "id": "(...)",
        "emails": [
            "admin.user@contoso.com"
        ],
        "displayName": "John Smith",
        "nickName": "John",
        "orgId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
        (...)
    }
    ```

1. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** så Azure AD kan ansluta till Cisco Webex. Om anslutningen misslyckas, kontrollera kontots Cisco Webex har administratörsbehörigheter och försök igen.

    ![Cisco Webex etablering](./media/cisco-webex-provisioning-tutorial/TestConnection.png)

8. I den **e-postmeddelande** anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel uppstår**.

    ![Cisco Webex etablering](./media/cisco-webex-provisioning-tutorial/EmailNotification.png)

9. Klicka på **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare Cisco Webex**.

    ![Cisco Webex etablering](./media/cisco-webex-provisioning-tutorial/UserMapping.png)

11. Granska användarattribut som ska synkroniseras från Azure AD med Cisco Webex i den **attributmappning** avsnitt. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i Cisco Webex för uppdateringsåtgärder. Välj den **spara** för att genomföra ändringarna.

    ![Cisco Webex etablering](./media/cisco-webex-provisioning-tutorial/UserMappingAttributes.png)

12. Om du vill konfigurera målgrupp filter, referera till följande instruktionerna i den [Scoping filter kursen](../active-directory-saas-scoping-filters.md).

13. Om du vill aktivera Azure AD-tjänsten för Cisco Webex-etablering, ändra den **Status för etablering** till **på** i den **inställningar** avsnitt.

    ![Cisco Webex etablering](./media/cisco-webex-provisioning-tutorial/ProvisioningStatus.png)

14. Ange användare och/eller grupper som du vill att etablera till Cisco Webex genom att välja önskade värden i **omfång** i den **inställningar** avsnitt.

    ![Cisco Webex etablering](./media/cisco-webex-provisioning-tutorial/SyncScope.png)

15. När du är redo att etablera, klickar du på **spara**.

    ![Cisco Webex etablering](./media/cisco-webex-provisioning-tutorial/Save.png)


Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfång** i den **inställningar** avsnitt. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var 40 minuter som Azure AD Etablerar-tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapport som beskriver alla åtgärder som utförs av Azure AD-tjänsten på Cisco Webex-etablering.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granska loggarna och få rapporter om etablering aktivitet](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_03.png