---
title: 'Självstudiekurs: Konfigurera ruta för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Box .
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1c959595-6e57-4954-9c0d-67ba03ee212b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1397b4189a9c2c15e3878687ea8c67c1da7567f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058577"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera ruta för automatisk etablering av användare

Syftet med den här självstudien är att visa de steg du behöver utföra i Box och Azure AD för att automatiskt etablera och avetableras användarkonton från Azure AD till Box.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Krav

Du behöver följande saker för att konfigurera Azure AD-integrering med Box:

- En Azure AD-klient
- En Box Affärsplan eller bättre

> [!NOTE]
> När du testar stegen i den här självstudien rekommenderar vi att du *inte* använder en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en testmiljö för Azure AD kan du [få en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-box"></a>Tilldela användare till ruta 

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk etablering av användarkonto synkroniseras endast användare och grupper som har "tilldelats" till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till din Box-app. När du har bestämt dig kan du tilldela dessa användare till box-appen genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Tilldela användare och grupper
På fliken **Ruta > användare och grupper** i Azure-portalen kan du ange vilka användare och grupper som ska beviljas åtkomst till Box. Tilldelning av en användare eller grupp gör att följande saker inträffar:

* Azure AD tillåter den tilldelade användaren (antingen genom direkt tilldelning eller gruppmedlemskap) att autentisera till Box. Om en användare inte har tilldelats tillåter azure AD dem inte att logga in på Box och returnerar ett fel på inloggningssidan för Azure AD.
* En apppanel för Box läggs till i användarens [programstartprogram](../manage-apps/end-user-experiences.md).
* Om automatisk etablering är aktiverad läggs de tilldelade användarna och/eller grupperna till i etableringskön som ska etableras automatiskt.
  
  * Om endast användarobjekt har konfigurerats för att etableras placeras alla direkt tilldelade användare i etableringskön och alla användare som är medlemmar i alla tilldelade grupper placeras i etableringskön. 
  * Om gruppobjekt har konfigurerats för att etableras etableras alla tilldelade gruppobjekt i Box och alla användare som är medlemmar i dessa grupper. Grupp- och användarmedlemskap bevaras när de skrivs till Box.

Du kan använda fliken **Attribut > Enkel inloggning** för att konfigurera vilka användarattribut (eller anspråk) som visas för rutan under SAML-baserad autentisering och fliken Attribut > **etablering** för att konfigurera hur användar- och gruppattribut flödar från Azure AD till Box under etableringsåtgärder.

### <a name="important-tips-for-assigning-users-to-box"></a>Viktiga tips för att tilldela användare till Box 

*   Vi rekommenderar att en enda Azure AD-användare som tilldelats Box för att testa etableringskonfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till rutan måste du välja en giltig användarroll. Rollen "Standardåtkomst" fungerar inte för etablering.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk etablering av användare

Det här avsnittet vägleder genom att ansluta ditt Azure AD till Box-API för etablering av användarkonton och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i Box baserat på användar- och grupptilldelning i Azure AD.

Om automatisk etablering är aktiverad läggs de tilldelade användarna och/eller grupperna till i etableringskön som ska etableras automatiskt.
    
 * Om endast användarobjekt är konfigurerade för att etableras placeras direkt tilldelade användare i etableringskön och alla användare som är medlemmar i alla tilldelade grupper placeras i etableringskön. 
    
 * Om gruppobjekt har konfigurerats för att etableras etableras alla tilldelade gruppobjekt i Box och alla användare som är medlemmar i dessa grupper. Grupp- och användarmedlemskap bevaras när de skrivs till Box.

> [!TIP] 
> Du kan också välja att aktivera SAML-baserade Single Sign-On for Box, enligt instruktionerna i [Azure portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-account-provisioning"></a>Så här konfigurerar du automatisk etablering av användarkonton:

Syftet med det här avsnittet är att beskriva hur du aktiverar etablering av Active Directory-användarkonton till Box.

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till **avsnittet Azure Active Directory > Enterprise Apps > Alla program.**

2. Om du redan har konfigurerat Box för enkel inloggning söker du efter din instans av Rutan med hjälp av sökfältet. Annars väljer du **Lägg till** och sök efter **ruta** i programgalleriet. Välj Ruta i sökresultaten och lägg till den i listan över program.

3. Markera din förekomst av ruta och välj sedan fliken **Etablering.**

4. Ställ in **etableringsläget** på **Automatiskt**. 

    ![Etableringen](./media/box-userprovisioning-tutorial/provisioning.png)

5. Under avsnittet **Administratörsautentiseringar** klickar du på **Auktorisera** för att öppna en dialogruta för ruta i ett nytt webbläsarfönster.

6. Ange de autentiseringsuppgifter som krävs på sidan **Logga in för att bevilja åtkomst till rutan** och klicka sedan på **Auktorisera**. 
   
    ![Aktivera automatisk etablering av användare](./media/box-userprovisioning-tutorial/IC769546.png "Aktivera automatisk etablering av användare")

7. Klicka på **Bevilja åtkomst till Box** för att auktorisera den här åtgärden och för att återgå till Azure-portalen. 
   
    ![Aktivera automatisk etablering av användare](./media/box-userprovisioning-tutorial/IC769549.png "Aktivera automatisk etablering av användare")

8. Klicka på Testa **anslutning** i Azure-portalen för att säkerställa att Azure AD kan ansluta till din Box-app. Om anslutningen misslyckas kontrollerar du att ditt Box-konto har behörigheten Teamadministratör och försöker **igen.**

9. Ange e-postadressen till en person eller grupp som ska få etableringsfelmeddelanden i fältet **E-postmeddelande** och markera kryssrutan.

10. Klicka på **Spara.**

11. Under avsnittet Mappningar väljer du **Synkronisera Azure Active Directory Users to Box.**

12. I avsnittet **Attributmappningar** granskar du de användarattribut som synkroniseras från Azure AD till Box. De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Box för uppdateringsåtgärder. Välj knappen Spara om du vill utföra eventuella ändringar.

13. Om du vill aktivera Azure AD-etableringstjänsten för Box ändrar **du etableringsstatusen** till **På** i avsnittet Inställningar

14. Klicka på **Spara.**

Det startar den första synkroniseringen av alla användare och/eller grupper som tilldelats Rutan i avsnittet Användare och grupper. Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge tjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitetsloggar, som beskriver alla åtgärder som utförs av etableringstjänsten i box-appen.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

I boxklienten visas synkroniserade användare under **Hanterade användare** i **administratörskonsolen**.

![Integrationsstatus](./media/box-userprovisioning-tutorial/IC769556.png "Integrationsstatus")


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](box-tutorial.md)
