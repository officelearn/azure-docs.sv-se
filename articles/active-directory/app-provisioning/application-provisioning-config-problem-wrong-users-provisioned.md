---
title: Fel uppsättning användare etableras i en Azure AD Gallery-app
description: Läs om hur du tar reda på varför en annan uppsättning användare etableras i ett program än de du förväntade dig
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c94388011605da73666e82011bb8ef56d2af8d30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522789"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Fel uppsättning användare etableras i ett Azure AD Gallery-program

Vilka användare som etableras i appen drivs främst av vilka användare och grupper som har **tilldelats** programmet.

Använd följande resurser för att lära dig hur du kontrollerar vilka användare och grupper som har tilldelats ett program i Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Tilldela en användare direkt som administratör

Så här tilldelar du en eller flera användare ett program direkt:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du vill tilldela en användare till i listan.

7. När programmet har läses in klickar du på **Användare och grupper** från programmets navigeringsmeny till vänster.

8. Om du vill öppna fönstret **Lägg till tilldelning** klickar du på knappen Lägg **till** överst i listan Användare **och grupper.**

9. Klicka på väljaren **Användare och grupper** i fönstret Lägg till **tilldelning.**

10. Skriv in det **fullständiga namnet** eller **e-postadressen** till den användare som du är intresserad av att tilldela sökrutan Sök efter **namn eller e-postadress.**

11. Hovra över **användaren** i listan för att visa en **kryssruta**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp om du vill lägga till användaren i listan **Markerad.**

12. **Valfritt:** Om du vill lägga till **fler än en användare**skriver du in ett annat fullständigt namn eller en annan **fullständig** **adress** i sökrutan Sök efter namn **eller e-postadress** och klickar på kryssrutan om du vill lägga till den här användaren i listan **Markerad.**

13. När du är klar med att välja användare klickar du på knappen **Välj** om du vill lägga till dem i listan över användare och grupper som ska tilldelas programmet.

14. **Valfritt:** Klicka på **selectorn Välj roll** i fönstret Lägg till **tilldelning** om du vill välja en roll som du vill tilldela de användare som du har markerat.

15. Klicka på knappen **Tilldela** om du vill tilldela programmet till de markerade användarna.

Om etableringen är konfigurerad och redan körs för en app bör nya användare etableras i ett program på cirka 10 minuter. Mer information finns i **granskningsloggarna.**

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Tilldela en grupp direkt till ett program som administratör

Så här tilldelar du en eller flera grupper till ett program:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du vill tilldela en användare till i listan.

7. När programmet har läses in klickar du på **Användare och grupper** från programmets navigeringsmeny till vänster.

8. Om du vill öppna fönstret **Lägg till tilldelning** klickar du på knappen Lägg **till** överst i listan Användare **och grupper.**

9. Klicka på väljaren **Användare och grupper** i fönstret Lägg till **tilldelning.**

10. Skriv in det **fullständiga gruppnamnet** för den grupp som du är intresserad av att tilldela sökrutan Sök efter **namn eller e-postadress.**

11. Hovra över **gruppen** i listan för att visa en **kryssruta**. Klicka på kryssrutan bredvid gruppens profilfoto eller logotyp om du vill lägga till användaren i listan **Markerad.**

12. **Valfritt:** Om du vill lägga till **mer än en grupp**skriver du in ett annat fullständigt **gruppnamn** i sökrutan **Sök efter namn eller e-postadress** och klickar på kryssrutan om du vill lägga till den här gruppen i listan **Markerad.**

13. När du är klar med att välja grupper klickar du på knappen **Välj** om du vill lägga till dem i listan över användare och grupper som ska tilldelas programmet.

14. **Valfritt:** Klicka på **selectorn Välj roll** i fönstret Lägg till **tilldelning** om du vill välja en roll som ska tilldelas de grupper som du har markerat.

15. Klicka på knappen **Tilldela** om du vill tilldela programmet till de markerade grupperna.

Om etableringen är konfigurerad och redan körs för en app, bör nya användare som ingår i gruppen etableras i ett program på cirka 10 minuter. Mer information finns i **granskningsloggarna.**

>[!IMPORTANT]
>Etablering av gruppnamn och gruppinformation, förutom medlemmarna, om det stöds för vissa program. Du kan aktivera eller inaktivera den här funktionen genom att aktivera eller inaktivera **mappning** för gruppobjekt som visas på fliken **Etablering.** 
>
>

Om etableringsgrupper är aktiverade måste du granska attributmappningarna för att säkerställa att ett lämpligt fält används för "matchande ID". Det här matchande ID:t kan vara visningsnamnet eller e-postaliaset. Gruppen och dess medlemmar etableras inte om den matchande egenskapen är tom eller inte fylls i för en grupp i Azure AD.

## <a name="next-steps"></a>Nästa steg
[Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](user-provisioning.md)
