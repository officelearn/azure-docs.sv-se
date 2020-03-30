---
title: Så här konfigurerar du självbetjäningsprogramtilldelning | Microsoft-dokument
description: Aktivera självbetjäningsprogramåtkomst så att användarna kan hitta sina egna program
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcccc64e0c766164a06932e9b65a4459816f9deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409124"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Konfigurera självbetjäningsprogramtilldelning

Innan användarna själv kan upptäcka program från åtkomstpanelen Mina appar måste du aktivera **självbetjäningsprogramåtkomst** till alla program som du vill tillåta användare att själv upptäcka och begära åtkomst till. Den här funktionen är tillgänglig för program som har lagts till från [Azure AD Gallery,](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) [Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) eller har lagts till via [användarens eller administratörsmedgivandet](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience). 

Den här funktionen är ett bra sätt för dig att spara tid och pengar som en IT-grupp och rekommenderas starkt som en del av en modern programdistribution med Azure Active Directory.

Med den här funktionen kan du:

-   Låt användarna själv upptäcka program från [åtkomstpanelen Mina appar](https://myapps.microsoft.com/) utan att störa IT-gruppen.

-   Lägg till dessa användare i en förkonfigurerad grupp så att du kan se vem som har begärt åtkomst, ta bort åtkomst och hantera de roller som tilldelats dem.

-   Du kan också tillåta en affärsgodkännare att godkänna begäranden om programåtkomst så att IT-gruppen inte behöver det.

-   Du kan också konfigurera upp till 10 personer som kan godkänna åtkomst till det här programmet.

-   Du kan också tillåta en företagsgodkännare att ange de lösenord som användarna kan använda för att logga in på programmet, direkt från företagsgodkännarens [programåtkomstpanel](https://myapps.microsoft.com/).

-   Du kan automatiskt tilldela självbetjäningstilldelade användare till en programroll direkt.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Aktivera självbetjäningsprogramåtkomst så att användarna kan hitta sina egna program

Självbetjäningsprogramåtkomst är ett bra sätt att tillåta användare att självupptäcka program och eventuellt tillåta företagsgruppen att godkänna åtkomst till dessa program. För lösenordsbaserade program kan du också tillåta att företagsgruppen hanterar de autentiseringsuppgifter som tilldelats dessa användare från sina egna åtkomstpaneler för Mina appar.

Så här aktiverar du självbetjäningstillgång till ett program:

1. Logga in på [Azure-portalen](https://portal.azure.com) som global administratör.

2. Välj **Azure Active Directory**. Välj **Enterprise-program**på menyn till vänster .

3. Välj programmet i listan. Om du inte ser programmet börjar du skriva namnet i sökrutan. Eller använd filterkontrollerna för att välja programtyp, status eller synlighet och välj sedan **Använd**.

4. Välj **Självbetjäning**på menyn till vänster .

5. Om du vill aktivera självbetjäningstillgång för det här programmet vrider du **tillåt användare att begära åtkomst till det här programmet?** växla till **Ja.**

6. Klicka på **Markera grupp** **bredvid Till vilken grupp som ska tilldelas användare?** Välj en grupp och klicka sedan på **Markera**. När en användares begäran godkänns läggs de till i den här gruppen. När du visar den här gruppens medlemskap kan du se vem som har beviljats åtkomst till programmet via självbetjäningsåtkomst.
  
    > [!NOTE]
    > Den här inställningen stöder inte grupper som synkroniserats från lokala.

7. **Valfritt:** Om du vill kräva företagsgodkännande innan användare tillåts åtkomst anger du **Yes** **Godkännandet Kräv innan du beviljar åtkomst till det här programmet?**

8. **Valfritt: För program som endast använder lösenord enkel inloggning,** för att tillåta företagsgodkända att ange de lösenord som skickas till det här programmet för godkända användare, ställer du in **Tillåt godkännare att ange användarens lösenord för det här programmet?** växla till **Ja**.

9. **Valfritt:** Om du vill ange de affärsgodkännare som får godkänna åtkomst till det här programmet klickar du på **Välj godkännare**bredvid **Vem får godkänna åtkomsten till det här programmet?** Klicka sedan på **Välj**.

    >[!NOTE]
    >Grupper stöds inte. Du kan välja upp till 10 enskilda affärsgodkännare. Om du anger flera godkännare kan en enskild godkännare godkänna en åtkomstbegäran.

10. **Valfritt:** **För program som exponerar roller**, för att tilldela självbetjäningsgodkända användare till en roll, bredvid rollen Till vilken roll som användare ska tilldelas i det här **programmet?**, klicka på **Välj roll**och välj sedan den roll som dessa användare ska tilldelas. Klicka sedan på **Välj**.

11. Klicka på knappen **Spara** högst upp i fönstret för att avsluta.

När du har slutfört självbetjäningsprogramkonfigurationen kan användarna navigera till [åtkomstpanelen Mina appar](https://myapps.microsoft.com/) och klicka på knappen **Lägg till självbetjäningsappar** för att hitta de appar som är aktiverade med självbetjäningsåtkomst. Företagsgodkännare ser också ett meddelande i [åtkomstpanelen Mina appar](https://myapps.microsoft.com/). Du kan aktivera ett e-postmeddelande om dem när en användare har begärt åtkomst till ett program som kräver deras godkännande.

## <a name="next-steps"></a>Nästa steg
[Konfigurera Azure Active Directory för grupphantering via självbetjäning](../users-groups-roles/groups-self-service-management.md)
