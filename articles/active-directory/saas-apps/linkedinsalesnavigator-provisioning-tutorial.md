---
title: 'Självstudier: Konfigurera LinkedIn Sales Navigator för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton på LinkedIn Sales Navigator.
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
ms.date: 03/28/2019
ms.author: asmalser-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ce841a45893ebfbb0d9006e6b9eadc77f08a491
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057769"
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>Självstudier: Konfigurera LinkedIn Sales Navigator för automatisk användaretablering

Målet med den här självstudien är att visa dig de steg som du behöver utföra på LinkedIn Sales Navigator och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD på LinkedIn Sales Navigator.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klientorganisation
* En LinkedIn Sales Navigator-klient 
* Ett administratörskonto i LinkedIn Sales Navigator med åtkomst till Kontocenter LinkedIn

> [!NOTE]
> Azure Active Directory kan integreras med LinkedIn Sales Navigator med hjälp av den [SCIM](http://www.simplecloud.info/) protokoll.

## <a name="assigning-users-to-linkedin-sales-navigator"></a>Tilldela användare till LinkedIn Sales Navigator

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, kommer endast de användare och grupper som är ”kopplade” till ett program i Azure AD att synkroniseras.

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till LinkedIn Sales Navigator. När du valt, kan du tilldela dessa användare på LinkedIn Sales Navigator genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>Viktiga tips för att tilldela användare till LinkedIn Sales Navigator

* Vi rekommenderar att en enda Azure AD-användare tilldelas till LinkedIn Sales Navigator att testa etablering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare på LinkedIn Sales Navigator, måste du välja den **användaren** roll i dialogrutan för tilldelning. Rollen ”standard åtkomst” fungerar inte för etablering.

## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>Konfigurera användaretablering på LinkedIn Sales Navigator

Det här avsnittet vägleder dig genom anslutning av din Azure AD för att etablera API LinkedIn Sales Navigator-SCIM-användarkonto och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i LinkedIn Sales Navigator baserat på användaren och grupptilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktiveras SAML-baserad enkel inloggning för LinkedIn Sales Navigator, följa anvisningarna enligt [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna kompletterar varandra.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>Konfigurera automatisk etablering av användarkonto på LinkedIn Sales Navigator i Azure AD:

Det första steget är att hämta din LinkedIn-åtkomsttoken. Om du är en Enterprise-administratör kan själv du en åtkomst-token. I din kontocenter, går du till **inställningar &gt; globala inställningar** och öppna den **SCIM installationsprogrammet** panelen.

> [!NOTE]
> Om du kommer åt kontocentret direkt i stället för via en länk, kan du nå den med hjälp av följande steg.

1. Logga in på kontot Center.

2. Välj **Admin &gt; administratörsinställningar** .

3. Klicka på **avancerade integreringar** i vänster Sidopanel. Du dirigeras till kontocenter.

4. Klicka på **+ Lägg till ny SCIM konfiguration** , och följa genom att fylla i fälten.

    > [!NOTE]
    > När autoassign licenser inte är aktiverad, betyder det att endast användardata som är synkroniserad.

    ![LinkedIn Sales Navigator etablering](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

    > [!NOTE]
    > När autolicense tilldelning har aktiverats kan behöva du Observera programinstans och licenstyp. Licenser har tilldelats på en första kommer kan först fungerar basis tills kommer alla licenser.

    ![LinkedIn Sales Navigator etablering](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5. Klicka på **generera token**. Du bör se din token visas under den **åtkomsttoken** fält.

6. Spara ditt åtkomst-token till Urklipp eller datorn innan de lämnar sidan.

7. Logga sedan in på den [Azure-portalen](https://portal.azure.com), och bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnittet.

8. Om du redan har konfigurerat LinkedIn Sales Navigator för enkel inloggning, söka efter din instans av LinkedIn Sales Navigator med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **LinkedIn Sales Navigator** i programgalleriet. Välj LinkedIn Sales Navigator från sökresultaten och lägga till den i din lista över program.

9. Välj din instans av LinkedIn Sales Navigator och välj sedan den **etablering** fliken.

10. Ange den **Etableringsläge** till **automatisk**.

    ![LinkedIn Sales Navigator etablering](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11. Fyll i följande fält under **administratörsautentiseringsuppgifter** :

    * I den **klient-URL** anger https://api.linkedin.com.

    * I den **hemlighet Token** fältet, ange den åtkomsttoken som du genererade i steg 1 och klicka på **Testanslutningen** .

    * Du bör se ett meddelande om lyckad på upperright sida av portalen.

12. Ange e-postadress för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fältet och markera kryssrutan nedan.

13. Klicka på **Spara**.

14. I den **attributmappningar** går du igenom de användar- och attribut som synkroniseras från Azure AD på LinkedIn Sales Navigator. Observera att attribut som är markerade som **matchande** egenskaper som används för att matcha de användarkonton och grupper i LinkedIn Sales Navigator för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

    ![LinkedIn Sales Navigator etablering](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15. Om du vill aktivera den Azure AD-etableringstjänsten för LinkedIn Sales Navigator, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet

16. Klicka på **Spara**.

Detta startar den första synkroniseringen av användare och/eller grupper som har tilldelats på LinkedIn Sales Navigator i avsnittet användare och grupper. Observera att den inledande synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av etableringstjänsten på LinkedIn Sales Navigator-app.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
