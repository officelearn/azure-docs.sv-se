---
title: 'Självstudier: Konfigurera Google Apps för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablera och avetablera användarkonton från Azure AD till Google Apps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: d5c68e709b72e4032eca76dd35103df50030ccca
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-google-apps-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Google Apps för automatisk användaretablering

Syftet med den här kursen är att visar hur du automatiskt etablera och avetablera användarkonton från Azure Active Directory (AD Azure) till Google Apps.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient.
*   En giltig klient för Google Apps för arbete eller Google Apps för utbildning. Du kan använda ett kostnadsfritt utvärderingskonto för antingen service.
*   Ett användarkonto i Google Apps-teamet administratörsbehörigheter.

## <a name="assign-users-to-google-apps"></a>Tilldela användare till Google Apps

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk konto användaretablering, synkroniseras de användare och grupper som har ”tilldelats” till ett program i Azure AD.

Innan du konfigurerar och aktiverar tjänsten etablering, måste du bestämma vilka användare eller grupper i Azure AD behöver åtkomst till din app på Google Apps. När du har gjort detta beslut du kan tilldela dessa användare till din app på Google Apps genom att följa instruktionerna i [tilldela en användare eller grupp till en enterprise-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

> [!IMPORTANT]
> Vi rekommenderar att en enda Azure AD-användare tilldelas till Google Apps testa allokering konfigurationen. Du kan tilldela ytterligare användare och grupper senare.

> När du tilldelar en användare till Google Apps, Välj den **användare** eller **grupp** roll i dialogrutan tilldelning. Den **standard åtkomst** roll fungerar inte för etablering.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk användaretablering

Det här avsnittet hjälper dig att ansluta din Azure AD till det användarkonto som etablerar API för Google Apps. Du kan också konfigurera tjänsten etablering för att skapa, uppdatera och inaktivera tilldelade användarkonton i Google Apps baserat på användare och grupptilldelning i Azure AD.

>[!TIP]
>Du kan också välja att aktivera SAML-baserade enkel inloggning för Google Apps genom att följa anvisningarna i den [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner komplettera varandra.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurera automatisk användarens konto-etablering

> [!NOTE]
> En annan genomförbart alternativ för att automatisera användaretablering till Google Apps är att använda [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en). GADS etablerar din lokala Active Directory identiteter till Google Apps. Lösningen i den här kursen etablerar däremot din Azure Active Directory (moln) användare och e-postaktiverade grupper till Google Apps. 

1. Logga in på den [Google Apps administratörskonsolen](http://admin.google.com/) med ditt administratörskonto och välj sedan **säkerhet**. Om du inte ser länken kan det vara dolt den **fler kontroller** menyn längst ned på skärmen.
   
    ![Välj säkerhet.][10]

2. På den **säkerhet** väljer **API-referens för**.
   
    ![Välj API-referens.][15]

3. Välj **aktivera API-åtkomst**.
   
    ![Välj API-referens.][16]

    > [!IMPORTANT]
    > För varje användare som du vill etablera till Google Apps sina användarnamn i Azure Active Directory *måste* vara bundna till en anpassad domän. Till exempel användare namn som bob@contoso.onmicrosoft.com accepteras inte av Google Apps. Å andra sidan bob@contoso.com accepteras. Du kan ändra en befintlig användares domän genom att redigera deras egenskaper i Azure AD. Innehåller instruktioner för hur du ställer in en anpassad domän för både Azure Active Directory och Google Apps i följande steg.
      
4. Om du inte har lagt till ett anpassat domännamn Azure Active Directory ännu, gör du följande:
  
    a. I den [Azure-portalen](https://portal.azure.com), på det vänstra navigeringsfönstret väljer **Active Directory**. Välj din katalog i kataloglistan. 

    b. Välj **domännamn** på vänstra navigeringsfönstret och välj sedan **Lägg till**.
     
     ![Domän](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_1.png)

     ![Lägg till domän](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_2.png)

    c. Skriv din domän till den **domännamn** fältet. Det här domännamnet måste vara samma domännamn som du tänker använda för Google Apps. Välj sedan den **Lägg till domän** knappen.
     
     ![Domännamn](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_3.png)

    d. Välj **nästa** att gå till sidan för verifiering. Redigera domänens DNS-poster enligt de värden som finns på den här sidan om du vill verifiera att du äger den här domänen. Du kan välja att kontrollera med hjälp av antingen **MX-poster** eller **TXT-poster**, beroende på vad du väljer för den **posttyp** alternativet. 
    
    Läs mer utförliga anvisningar om hur du verifierar domännamn med Azure AD [lägga till ditt eget domännamn i Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).
     
     ![Domän](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_4.png)

    e. Upprepa föregående steg för alla domäner som du vill lägga till i din katalog.

    > [!NOTE]
    För användaretablering, måste Google Apps anpassad domän matcha domännamnet för källan Azure AD. Om de inte matchar kanske du kan lösa problemet genom att implementera attributet mappning anpassning.


5. Nu när du har verifierat dina domäner med Azure AD, måste du kontrollera dem igen med Google Apps. Gör följande för varje domän som inte redan har registrerats med Google Apps:
   
    a. I den [Google Apps-administrationskonsolen](http://admin.google.com/)väljer **domäner**.
     
     ![Välj domäner][20]

    b. Välj **lägga till en domän eller en domän alias**.
     
     ![Lägg till en ny domän][21]

    c. Välj **lägga till en annan domän**, och skriv sedan namnet på den domän som du vill lägga till.
     
     ![Ange ditt domännamn][22]

    d. Välj **Fortsätt och verifiera domänen ägarskap**. Följ stegen för att verifiera att du äger domännamnet. Utförliga anvisningar om hur du verifierar din domän med Google Apps finns i [verifiera ditt platsägarskap med Google Apps](https://support.google.com/webmasters/answer/35179).

    e. Upprepa föregående steg för eventuella ytterligare domäner som du vill lägga till i Google Apps.
     
     > [!WARNING]
     > Om du ändrar den primära domänen för Google Apps klienten, och om du redan har konfigurerat enkel inloggning med Azure AD och du behöver Upprepa steg #3 under [steg 2: Aktivera enkel inloggning](#step-two-enable-single-sign-on).
       
6. I den [Google Apps administratörskonsolen](http://admin.google.com/)väljer **administratörsroller**.
   
     ![Välj Google Apps][26]

7. Bestämma vilka administratörskonto som du vill använda för att hantera användaretablering. För den **administratörsroll** på det kontot, redigera den **privilegier** för rollen. Se till att aktivera alla **API administratörsrättigheter** så att det här kontot kan användas för att etablera.
   
     ![Välj Google Apps][27]
   
    > [!NOTE]
    > Om du konfigurerar en produktionsmiljö är det bästa sättet att skapa ett administratörskonto i Google Apps specifikt för det här steget. Dessa konton måste ha en administratörsroll som är kopplade till dem som har behörighet som krävs API.
     
8. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory** > **Företagsappar** > **alla program** avsnitt.

9. Om du redan har konfigurerat Google Apps för enkel inloggning kan du söka efter din instans av Google Apps med hjälp av sökfältet. Annars väljer **Lägg till**, och sök sedan efter **Google Apps** i programgalleriet. Välj **Google Apps** i sökresultatet och Lägg sedan till den i listan över program.

10. Välj din instans av Google Apps och välj sedan den **etablering** fliken.

11. Ange den **Etableringsläge** till **automatisk**. 

     ![Etablering](./media/active-directory-saas-google-apps-provisioning-tutorial/provisioning.png)

12. Under den **administratörsautentiseringsuppgifter** väljer **auktorisera**. En dialogruta för auktorisering av Google Apps öppnas i ett nytt webbläsarfönster.

13. Bekräfta att du vill ge Azure Active Directory-behörighet att göra ändringar i din Google Apps-klient. Välj **acceptera**.
    
     ![Kontrollera behörigheter.][28]

14. Välj i Azure-portalen **Testanslutningen** så att Azure AD kan ansluta till din app på Google Apps. Om anslutningen misslyckas, kan du kontrollera att ditt Google Apps-konto har teamet administratörsbehörigheter. Försök i **auktorisera** steg igen.

15. Ange e-postadressen för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fältet. Markera sedan kryssrutan.

16. Välj **spara.**

17. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Google Apps**.

18. I den **attributmappning** avsnittet kan du granska användarattribut som synkroniseras från Azure AD till Google Apps. Attribut som är **matchande** egenskaper som används för att matcha användarkonton i Google Apps för uppdateringsåtgärder. Välj **spara** att genomföra ändringarna.

19. Om du vill aktivera Azure AD-tjänsten för Google Apps-etablering, ändra den **Status för etablering** till **på** i **inställningar**.

20. Välj **Spara**.

Den här processen startar den första synkroniseringen av alla användare eller grupper som tilldelas till Google Apps i avsnittet användare och grupper. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var 40 minuter medan tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsloggar. Dessa loggar beskrivs alla åtgärder som utförs av tjänsten etablering på Google Apps-app.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](active-directory-saas-google-apps-tutorial.md)



<!--Image references-->

[10]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-auth.png
