---
title: 'Självstudier: Rutan Konfigurera för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Box.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1c959595-6e57-4954-9c0d-67ba03ee212b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: jeedes
ms.openlocfilehash: 58ab80b9cdf6fbcfa8aebeeac837687ded41ae39
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54821856"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Självstudier: Rutan Konfigurera för automatisk användaretablering

Målet med den här självstudien är att visa steg du måste utföra i rutan och Azure AD att automatiskt etablera och användares användarkonton från Azure AD till Box.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande saker för att konfigurera Azure AD-integrering med Box:

- En Azure AD-klient
- En ruta verksamhetsplan eller bättre

> [!NOTE]
> När du testar stegen i den här självstudien rekommenderar vi att du gör *inte* använder en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en testmiljö för Azure AD kan du [få en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-box"></a>Tilldela användare till Box 

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, synkroniseras de användare och grupper som är ”kopplade” till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till din Box-app. När du bestämt dig kan tilldela du dessa användare i din Box-app genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Tilldela användare och grupper
Den **Box > användare och grupper** fliken i Azure-portalen kan du ange vilka användare och grupper bör ha åtkomst till Box. Tilldelningen av en användare eller grupp gör de följande händelser:

* Azure AD tillåter den tilldelade användaren (antingen genom att direkt tilldelning eller gruppmedlemskap) för att autentisera till Box. Om en användare inte har tilldelats Azure AD tillåter inte dem att logga in till Box och returnerar ett fel på sidan för Azure AD.
* En appanel för Box har lagts till i användarens [startprogrammet](../manage-apps/end-user-experiences.md).
* Om automatisk etablering är aktiverat sedan läggs/tilldelade användare eller grupper till etablering kön som ska etableras automatiskt.
  
  * Om bara användarobjekt har konfigurerats för att etableras, sedan alla direkt tilldelade användare är placerade i etablering kön och alla användare som är medlemmar i några tilldelade grupper är placerade i etablering kön. 
  * Om gruppobjekt har konfigurerats för att etableras, etableras alla tilldelade gruppobjekt till och alla användare som är medlemmar i dessa grupper. Medlemskap för gruppen och användaren bevaras när de skrivs till Box.

Du kan använda den **attribut > enkel inloggning** fliken för att konfigurera vilka användarattribut (eller anspråk) visas rutan under SAML-baserad autentisering och **attribut > etablering** fliken konfigurera hur användar- och Gruppattribut flöda från Azure AD till Box under etableringen åtgärder.

### <a name="important-tips-for-assigning-users-to-box"></a>Viktiga tips för att tilldela användare till Box 

*   Vi rekommenderar att en enda Azure AD-användare som tilldelats testa etablering konfigurationen till Box. Ytterligare användare och/eller grupper kan tilldelas senare.

*   Du måste välja en giltig användarroll när du tilldelar en användare till box. Rollen ”standard åtkomst” fungerar inte för etablering.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk Användaretablering

Det här avsnittet hjälper till att ansluta din Azure AD till Box användarkonto etablering API och konfigurera etableringstjänsten att skapa, uppdatera och inaktivera tilldelade användarkonton i rutan utifrån användar- och grupptilldelningar i Azure AD.

Om automatisk etablering är aktiverat sedan läggs/tilldelade användare eller grupper till etablering kön som ska etableras automatiskt.
    
 * Om det bara användarobjekt är konfigurerade för att etableras, och sedan direkt tilldelade användare är placerade i etablering kön och alla användare som är medlemmar i några tilldelade grupper placeras i kön för etablering. 
    
 * Om gruppobjekt har konfigurerats för att etableras, etableras alla tilldelade gruppobjekt till och alla användare som är medlemmar i dessa grupper. Medlemskap för gruppen och användaren bevaras när de skrivs till Box.

> [!TIP] 
> Du kan också välja att aktiverat SAML-baserad enkel inloggning för Box, följa anvisningarna enligt [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurera automatisk användarkontoetablering:

Målet med det här avsnittet som beskriver hur du aktivera etablering av Active Directory-användarkonton till Box.

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnittet.

2. Om du redan har konfigurerat rutan för enkel inloggning, söka efter din instans av Box med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Box** i programgalleriet. Markera kryssrutan i sökresultatet och lägga till den i din lista över program.

3. Välj din instans av rutan och välj sedan den **etablering** fliken.

4. Ange den **Etableringsläge** till **automatisk**. 

    ![etablering](./media/box-userprovisioning-tutorial/provisioning.png)

5. Under den **administratörsautentiseringsuppgifter** klickar du på **auktorisera** att öppna en dialogruta för inloggning i ett nytt webbläsarfönster.

6. På den **inloggning att bevilja åtkomst till Box** anger autentiseringsuppgifterna som krävs, och klicka sedan på **auktorisera**. 
   
    ![Aktivera automatisk användaretablering](./media/box-userprovisioning-tutorial/IC769546.png "aktivera automatisk användaretablering")

7. Klicka på **bevilja åtkomst till Box** att auktorisera den här åtgärden och återgå till Azure-portalen. 
   
    ![Aktivera automatisk användaretablering](./media/box-userprovisioning-tutorial/IC769549.png "aktivera automatisk användaretablering")

8. I Azure-portalen klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till din Box-app. Om anslutningen misslyckas se till att ditt Box-konto har administratörsbehörighet för Team och försök på **”auktorisera”** steg igen.

9. Ange e-postadress för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fältet och markera kryssrutan.

10. Klicka på **spara.**

11. Under avsnittet mappningar väljer **synkronisera Azure Active Directory-användare till Box.**

12. I den **attributmappningar** går du igenom användarattribut som synkroniseras från Azure AD till Box. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i rutan för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

13. Om du vill aktivera den Azure AD-etableringstjänsten för Box, ändra den **Etableringsstatus** till **på** i avsnittet Inställningar

14. Klicka på **spara.**

Som startar den första synkroniseringen av användare och/eller grupper som tilldelats till rutan i avsnittet användare och grupper. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av etableringstjänsten på din Box-app.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

Synkroniserade användare i din Box-klient visas under **hanterade användare** i den **administratörskonsolen**.

![Status för integrering](./media/box-userprovisioning-tutorial/IC769556.png "integrering status")


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](box-tutorial.md)
