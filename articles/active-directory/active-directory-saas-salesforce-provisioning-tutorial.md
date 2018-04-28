---
title: 'Självstudier: Konfigurera Salesforce för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: b5a85dd7d4ac6bdabcbab49f654ebfe4adae73cd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Salesforce för automatisk användaretablering

Syftet med den här kursen är att visa steg som krävs för att utföra i Salesforce och Azure AD för att automatiskt etablera och avinstallation etablera användarkonton från Azure AD för att Salesforce.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient
*   En Salesforce.com-klient

>[!IMPORTANT] 
>Om du använder ett utvärderingskonto för Salesforce.com sedan kan du inte konfigurera automatisk användaretablering. Utvärderingskonton har inte den behörighet som krävs av API aktiveras förrän de har köpts. Du kan komma runt denna begränsning med hjälp av ett gratis [utvecklarkonto](https://developer.salesforce.com/signup) till den här kursen.

Om du använder Salesforce begränsat läge, finns det [Salesforce Sandbox integration kursen](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Tilldela användare till Salesforce

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk konto användaretablering, synkroniseras de användare och grupper som har ”tilldelats” till ett program i Azure AD.

Innan du konfigurerar och aktiverar tjänsten etablering, måste du bestämma vilka användare eller grupper i Azure AD behöver åtkomst till ditt Salesforce-app. När du har gjort detta beslut du kan tilldela dessa användare Salesforce-app genom att följa instruktionerna i [tilldela en användare eller grupp till en enterprise-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Viktiga tips för att tilldela användare till Salesforce

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats till Salesforce testa allokering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*  Du måste välja en giltig användarroll när du tilldelar en användare till Salesforce. Rollen ”standard åtkomst” fungerar inte för etablering

    > [!NOTE]
    > Den här appen importerar anpassade roller från Salesforce som en del av etableringsprocessen som kunden kanske du väljer när du tilldelar användare

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk användaretablering

Det här avsnittet hjälper dig att ansluta din Azure AD till Salesforces användarkonto API-etablering och konfigurera tjänsten etablering för att skapa, uppdatera och inaktivera tilldelade användarkonton i Salesforce baserat på tilldelning av användare och grupper i Azure AD.

>[!Tip]
>Du kan också välja att aktivera SAML-baserade enkel inloggning för Salesforce, följer du instruktionerna som anges i [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner komplettera varandra.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurera automatisk användarens konto-etablering

Syftet med det här avsnittet är att beskriva hur du aktiverar användaretablering av Active Directory-användarkonton till Salesforce.

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnitt.

2. Om du redan har konfigurerat Salesforce för enkel inloggning, söka efter din instans av Salesforce med sökfältet. Annars väljer **Lägg till** och Sök efter **Salesforce** i programgalleriet. Välj Salesforce i sökresultatet och lägga till den i listan med program.

3. Välj ditt Salesforce-instans och välj sedan den **etablering** fliken.

4. Ange den **Etableringsläge** till **automatisk**.

    ![etablering](./media/active-directory-saas-salesforce-provisioning-tutorial/provisioning.png)

5. Under den **administratörsautentiseringsuppgifter** och ange följande inställningar:
   
    a. I den **Admin Username** textruta typen en Salesforce-kontonamn som har den **systemadministratören** profil i Salesforce.com som tilldelats.
   
    b. I den **adminlösenord** textruta skriver du lösenordet för det här kontot.

6. Om du vill hämta dina Salesforce säkerhetstoken, öppnar du en ny flik och logga i samma Salesforce-administratörskonto. Klicka på ditt namn i det övre högra hörnet på sidan och klicka sedan på **inställningar**.

     ![Aktivera automatisk användaretablering](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-my-settings.png "aktivera automatisk användaretablering")

7. I det vänstra navigeringsfönstret klickar du på **mina personuppgifter** Expandera avsnittet relaterade och klicka sedan på **Återställ mina säkerhetstoken**.
  
    ![Aktivera automatisk användaretablering](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-personal-reset.png "aktivera automatisk användaretablering")

8. På den **återställa säkerhetstoken** klickar du på **återställa säkerhetstoken** knappen.

    ![Aktivera automatisk användaretablering](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-reset-token.png "aktivera automatisk användaretablering")

9. Kontrollera den inkorg som är associerade med den här administratörskonto. Leta efter ett e-postmeddelande från Salesforce.com som innehåller ny säkerhetstoken.

10. Kopiera token, gå till Azure AD-fönstret och klistrar in det i den **hemlighet Token** fältet.

11. Den **klient URL** måste anges om Salesforce-instansen är i Salesforce offentliga moln. Annars är valfria. Ange klient-URL i formatet av https://your-instance.my.salesforce.com, ersätta din-instans med namnet på ditt Salesforce-instans.

12. I Azure-portalen klickar du på **Testanslutningen** så Azure AD kan ansluta till ditt Salesforce-app.

13. I den **e-postmeddelande** anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden om etablering fel och markera kryssrutan nedan.

14. Klicka på **spara.**  
    
15.  Välj under avsnittet mappningar **synkronisera Azure Active Directory-användare till Salesforce.**

16. I den **attributmappning** avsnittet kan du granska användarattribut som synkroniseras från Azure AD till Salesforce. Observera att attribut som är markerade som **matchande** egenskaper som används för att matcha användarkonton i Salesforce för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

17. Om du vill aktivera Azure AD-tjänsten för Salesforce-etablering, ändra den **Status för etablering** till **på** i avsnittet Inställningar

18. Klicka på **spara.**

Detta startar den första synkroniseringen av användare och/eller grupper som tilldelas till Salesforce i avsnittet användare och grupper. Observera att den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar som sker ungefär var 40 minuter så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av tjänsten etablering på ditt Salesforce-app.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurera enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
