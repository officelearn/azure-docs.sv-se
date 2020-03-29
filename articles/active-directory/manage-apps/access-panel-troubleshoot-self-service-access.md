---
title: Problem med att använda självbetjäningsprogram | Microsoft-dokument
description: Felsöka problem relaterade till självbetjäningsprogramåtkomst
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
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a981dfb1d72c21eccf2ad7119ea219114ed15aed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784285"
---
# <a name="problem-using-self-service-application-access"></a>Problem med att använda självbetjäningstillgång

Självbetjäningsprogramåtkomst är ett bra sätt att tillåta användare att självupptäcka program, vilket gör det möjligt för företagsgruppen att godkänna åtkomst till dessa program. Du kan tillåta att företagsgruppen hanterar de autentiseringsuppgifter som tilldelats dessa användare för lösenordsbaserade enkel inloggningsprogram direkt från sina åtkomstpaneler.

Innan användarna själv kan upptäcka program från åtkomstpanelen måste du aktivera **självbetjäningsprogramåtkomst** till alla program som du vill tillåta användare att upptäcka och begära åtkomst till.

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrollera först

-   Kontrollera att självbetjäningstillträdet är korrekt konfigurerad. Se "Så här konfigurerar du självbetjäningstillträde för program".

-   Kontrollera att användaren eller gruppen har aktiverats för att begära självbetjäningstillträde för program.

-   Kontrollera att användaren besöker rätt plats för självbetjäningsprogramåtkomst. Användare kan navigera till sin [programåtkomstpanel](https://myapps.microsoft.com/) och klicka på knappen **+Lägg** till för att hitta de appar som du har aktiverat självbetjäningsåtkomst till.

-   Om självbetjäningsprogramåtkomsten nyligen har konfigurerats försöker du logga in och ut igen i användarens åtkomstpanel efter några minuter för att se om självbetjäningsåtkomständringarna har dykt upp.

## <a name="how-to-configure-self-service-application-access"></a>Konfigurera självbetjäningstillgång

Så här aktiverar du självbetjäningstillgång till ett program:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på navigeringsmenyn för vänster hand.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory-navigering till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du vill aktivera självbetjäningsåtkomst till i listan.

7. När programmet har läses in klickar du på **Självbetjäning** från programmets navigeringsmeny med vänster hand.

8. Om du vill aktivera självbetjäningstillgång för det här programmet vrider du **tillåt användare att begära åtkomst till det här programmet?** växla till **Ja.**

9. Om du vill välja den grupp som användare som begär åtkomst till det här programmet ska läggas till klickar du på väljaren bredvid etiketten **Till vilken grupp som ska tilldelas användare ska läggas till?**

10. **Valfritt:** Om du vill kräva ett företagsgodkännande innan användare tillåts åtkomst ställer du in **Kräv godkännande innan du beviljar åtkomst till det här programmet?** växla till **Ja**.

11. **Valfritt: För program som endast använder lösenord enkel inloggning,** om du vill tillåta dessa affärsgodkännare att ange de lösenord som skickas till det här programmet för godkända användare, ställer du in **Tillåt godkännare att ange användarens lösenord för det här programmet?** växla till **Ja**.

12. **Valfritt:** Om du vill ange de affärsgodkännare som får godkänna åtkomst till det här programmet klickar du på väljaren bredvid etiketten **Vem får godkänna åtkomst till det här programmet?**

    >[!NOTE]
    > Grupper stöds inte.
    >
    >

13. **Valfritt:** **För program som exponerar roller**, om du vill tilldela självbetjäningsgodkända användare till en roll, klickar du på väljaren bredvid rollen Till vilken roll som användare ska tilldelas i det här **programmet?**

14. Klicka på **knappen Spara** högst upp på bladet för att avsluta.

När du har slutfört självbetjäningsprogramkonfigurationen kan användarna navigera till sin [programåtkomstpanel](https://myapps.microsoft.com/) och klicka på knappen **+Lägg** till för att hitta de appar som du har aktiverat självbetjäningsåtkomst till. Företagsgodkännare ser också ett meddelande i sin [programåtkomstpanel](https://myapps.microsoft.com/). Du kan aktivera ett e-postmeddelande om dem när en användare har begärt åtkomst till ett program som kräver deras godkännande. 

Dessa godkännanden stöder endast arbetsflöden för enskilt godkännande, vilket innebär att om du anger flera godkännare kan en enskild godkännare godkänna åtkomst till programmet.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Om dessa felsökningssteg inte löser problemet 

öppna en supportbiljett med följande information om sådan finns:

-   Korrelationsfel-ID

-   UPN (användarens e-postadress)

-   TenantID (Klient-ID)

-   Typ av webbläsare

-   Tidszon och tid/tidsram under fel inträffar

-   Spelman spår

## <a name="next-steps"></a>Nästa steg
[Konfigurera Azure Active Directory för grupphantering via självbetjäning](../users-groups-roles/groups-self-service-management.md)
