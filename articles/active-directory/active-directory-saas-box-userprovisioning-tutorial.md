---
title: "Självstudier: Azure Active Directory-integrering med | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och rutan."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1c959595-6e57-4954-9c0d-67ba03ee212b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 9f061f3f5a0a4825854b893150ceccc8951487de
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-configuring-box-for-automatic-user-provisioning"></a>Självstudier: Konfigurera rutan för automatisk Användaretablering

Syftet med den här kursen är att visa steg som du behöver utföra i rutan och Azure AD att automatiskt etablera och avinstallation etablera användarkonton från Azure AD till Box.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient.
*   En ruta enkel inloggning för aktiverade prenumerationen.
*   Ett användarkonto i rutan Team administratörsbehörigheter.

## <a name="assigning-users-to-box"></a>Tilldela användare till rutan 

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk konto användaretablering, synkroniseras de användare och grupper som har ”tilldelats” till ett program i Azure AD.

Innan du konfigurerar och aktiverar tjänsten etablering, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till Box-app. När bestämt, kan du tilldela dessa användare till Box-app genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en enterprise-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Tilldela användare och grupper
Den **rutan > användare och grupper** fliken i Azure-portalen kan du ange vilka användare och grupper bör ha åtkomst till Box. Tilldelning av en användare eller grupp gör följande inträffar:

* Azure AD tillåter den tilldelade användaren (antingen direkt av tilldelning av eller gruppmedlemskap) för att autentisera till rutan. Om en användare inte har tilldelats, Azure AD tillåter inte dem att logga in på rutan och returnerar ett fel på sidan för Azure AD.
* En app-panelen för Box har lagts till i användarens [startprogrammet](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).
* Om automatisk etablering aktiveras sedan läggs tilldelade användare och/eller grupper till köns etablering etableras automatiskt.
  
  * Om endast användarobjekt har konfigurerats för att etableras, sedan alla direkt tilldelade användare placeras i kön för etablering och alla användare som är medlemmar i de tilldelade grupperna placeras i kön för etablering. 
  * Om gruppobjekt konfigurerades för att etableras, etableras alla tilldelade objekt till och alla användare som är medlemmar i dessa grupper. Grupp- och medlemskap bevaras vid skrivs till rutan.

Du kan använda den **attribut > enkel inloggning** att konfigurera vilka användarattribut (eller anspråk) visas texten i under SAML-baserad autentisering och **attribut > etablering** att konfigurera hur användar- och Gruppattribut flöda från Azure AD till rutan under etableringen åtgärder.

### <a name="important-tips-for-assigning-users-to-box"></a>Viktiga tips för att tilldela användare till rutan 

*   Vi rekommenderar att en enda Azure AD-användare som tilldelats rutan för att testa allokering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   Du måste välja en giltig användarroll när du tilldelar du en användare. Rollen ”standard åtkomst” fungerar inte för etablering.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatiserad etablering av användare

Det här avsnittet hjälper att ansluta din Azure AD till rutans användarkonto API-etablering och konfigurera tjänsten etablering för att skapa, uppdatera och inaktivera tilldelade användarkonton i rutan utifrån tilldelning av användare och grupper i Azure AD.

Om automatisk etablering aktiveras sedan läggs tilldelade användare och/eller grupper till köns etablering etableras automatiskt.
    
 * Om endast användarobjekt är konfigurerade för att etableras, och sedan direkt tilldelade användare placeras i kön för etablering och alla användare som är medlemmar i de tilldelade grupperna placeras i kön för etablering. 
    
 * Om gruppobjekt konfigurerades för att etableras, etableras alla tilldelade objekt till och alla användare som är medlemmar i dessa grupper. Grupp- och medlemskap bevaras vid skrivs till rutan.

> [!TIP] 
> Du kan också välja att aktivera SAML-baserade enkel inloggning för, följer du instruktionerna som anges i [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner komplettera varandra.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurera automatisk användarens konto-etablering:

Syftet med det här avsnittet är att beskriva hur du aktiverar etablering av Active Directory-användarkonton till Box.

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnitt.

2. Om du redan har konfigurerat rutan för enkel inloggning, söka efter din instans av rutan med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **rutan** i programgalleriet. Markera kryssrutan i sökresultatet och lägga till den i listan med program.

3. Välj din instans av kryssrutan och välj sedan den **etablering** fliken.

4. Ange den **Etableringsläge** till **automatisk**. 

    ![Etablering](./media/active-directory-saas-box-userprovisioning-tutorial/provisioning.png)

5. Under den **administratörsautentiseringsuppgifter** klickar du på **auktorisera** att öppna en dialogruta för inloggning i ett nytt webbläsarfönster.

6. På den **inloggning att bevilja åtkomst till rutan** anger autentiseringsuppgifterna som krävs, och klicka sedan på **auktorisera**. 
   
    ![Aktivera automatisk användaretablering](./media/active-directory-saas-box-userprovisioning-tutorial/IC769546.png "aktivera automatisk användaretablering")

7. Klicka på **bevilja åtkomst till rutan** att godkänna den här åtgärden och återgå till den Azure-portalen. 
   
    ![Aktivera automatisk användaretablering](./media/active-directory-saas-box-userprovisioning-tutorial/IC769549.png "aktivera automatisk användaretablering")

8. I Azure-portalen klickar du på **Testanslutningen** så Azure AD kan ansluta till Box-app. Om anslutningen misslyckas, kontrollera Box-konto har administratörsbehörigheter för teamet och försök den **”Godkänn”** steg igen.

9. Ange e-postadressen för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fält och markera kryssrutan.

10. Klicka på **spara.**

11. Välj under avsnittet mappningar **synkronisera Azure Active Directory-användare till Box.**

12. I den **attributmappning** avsnittet kan du granska användarattribut som synkroniseras från Azure AD till rutan. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i rutan för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

13. Aktivera Azure AD etableras för att ändra den **Status för etablering** till **på** i avsnittet Inställningar

14. Klicka på **spara.**

Som startar den första synkroniseringen av användare och/eller grupper som tilldelas till rutan i avsnittet användare och grupper. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var tjugonde minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapporter som beskriver alla åtgärder som utförs av tjänsten etablering på Box-app.

Du kan nu skapa ett testkonto. Vänta i upp till 20 minuter för att verifiera att kontot har synkroniserats till box.

I rutan-klient synkroniserade användare visas under **hanterade användare** i den **administratörskonsolen**.

![Integration status](./media/active-directory-saas-box-userprovisioning-tutorial/IC769556.png "integrering status")


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurera enkel inloggning](active-directory-saas-box-tutorial.md)