---
title: 'Självstudier: Konfigurera GitHub för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till GitHub.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31f10ba0c04ccbd9f52b95c43fea7cc551fe64ee
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888023"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Självstudier: Konfigurera GitHub för automatisk användaretablering


Målet med den här självstudien är att visa dig de steg du måste utföra i GitHub och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till GitHub. 

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient
*   En GitHub-organisation som har skapats i [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) och kräver [GitHub Enterprise-faktureringsplanen](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)
*   Ett användarkonto i GitHub med administratörsbehörighet i organisationen

> [!NOTE]
> Azure AD etablering integration förlitar sig på den [GitHub SCIM API](https://developer.github.com/v3/scim/), som är tillgänglig för [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) kunder på de [GitHub Enterprise faktureringsplan](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) .

## <a name="assigning-users-to-github"></a>Tilldela användare till GitHub

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, synkroniseras de användare och grupper som är ”kopplade” till ett program i Azure AD. 

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till din GitHub-app. När du valt, kan du tilldela dessa användare till din GitHub-app genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Viktiga tips för att tilldela användare till GitHub

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats GitHub för att testa etablering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till GitHub, måste du välja antingen den **användaren** roll eller en annan giltig programspecifika roll (om tillgängligt) i dialogrutan för tilldelning. Den **standard åtkomst** rollen fungerar inte för etablering och dessa användare hoppas över.


## <a name="configuring-user-provisioning-to-github"></a>Konfigurera användaretablering till GitHub 

Det här avsnittet hjälper dig att ansluta din Azure AD till GitHub-användarkonto etablering API och konfigurera etableringstjänsten att skapa, uppdatera och inaktivera tilldelade användarkonton i GitHub baserat på användar- och grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktiveras SAML-baserad enkel inloggning för GitHub, följa anvisningarna enligt [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna komplettera varandra.


### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Konfigurera automatisk etablering av användarkonto till GitHub i Azure AD


1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnittet.

2. Om du redan har konfigurerat GitHub för enkel inloggning, söka efter din instans av GitHub med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **GitHub** i programgalleriet. Välj GitHub i sökresultatet och lägga till den i din lista över program.

3. Välj din instans av GitHub och välj sedan den **etablering** fliken.

4. Ange den **Etableringsläge** till **automatisk**.

    ![GitHub-etablering](./media/github-provisioning-tutorial/GitHub1.png)

5. Under den **administratörsautentiseringsuppgifter** klickar du på **auktorisera**. Den här åtgärden öppnar en dialogruta för GitHub-auktorisering i ett nytt webbläsarfönster. 

6. Logga in på GitHub med hjälp av ditt administratörskonto i nytt fönster. I den resulterande auktorisering väljer du de GitHub-team som du vill aktivera etablering för och välj sedan **auktorisera**. När klar gå tillbaka till Azure portal för att slutföra konfigurationen av etablering.

    ![Dialogrutan för auktorisering](./media/github-provisioning-tutorial/GitHub2.png)

7. Azure-portalen anger **klient-URL** och klicka på **Testanslutningen** att se till att Azure AD kan ansluta till din GitHub-app. Om anslutningen misslyckas se till att ditt GitHub-konto har administratörsbehörighet och **klient-URl** är inputted korrekt och försök igen ”auktorisera” steg (du kan utgöra **klient-URL** av regel: `https://api.github.com/scim/v2/organizations/<Organization_name>` , du kan hitta din organisationer under ditt GitHub-konto: **Settings** > **Organizations**).

    ![Dialogrutan för auktorisering](./media/github-provisioning-tutorial/GitHub3.png)

8. Ange e-postadress för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fältet och markera kryssrutan ”Skicka ett e-postmeddelande när ett fel uppstår”.

9. Klicka på **Spara**. 

10. Under avsnittet mappningar väljer **synkronisera Azure Active Directory-användare till GitHub**.

11. I den **attributmappningar** går du igenom användarattribut som synkroniseras från Azure AD till GitHub. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i GitHub för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

12. Om du vill aktivera den Azure AD-etableringstjänsten för GitHub, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet

13. Klicka på **Spara**. 

Den här åtgärden startar den första synkroniseringen av användare och/eller grupper som har tilldelats till GitHub i avsnittet användare och grupper. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av etableringstjänsten.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)
