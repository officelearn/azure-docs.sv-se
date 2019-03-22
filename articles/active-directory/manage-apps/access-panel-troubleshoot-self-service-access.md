---
title: Problemet med hjälp av programåtkomst via självbetjäning | Microsoft Docs
description: Felsöka problem som rör självbetjäningsåtkomsten
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd3ae06c42452d425f041543715336827048ccb2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58076453"
---
# <a name="problem-using-self-service-application-access"></a>Problemet med hjälp av självbetjäningsåtkomsten

Självbetjäning för programåtkomst är ett bra sätt så att användarna kan identifiera lokal program, låta affärsgrupp att godkänna åtkomst till dessa program. Du kan tillåta affärsgrupp att hantera de autentiseringsuppgifter som har tilldelats till de användarna av lösenord för enkel inloggning på program direkt från sina åtkomstpaneler.

Innan användarna kan själva upptäcka program från sina åtkomstpaneler, måste du aktivera **självbetjäningsåtkomsten** för program som du vill tillåta användare att identifiera själv och begära åtkomst till.

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrollera först

-   Kontrollera att självbetjäningsåtkomsten är rätt konfigurerad. Se ”hur du konfigurerar självbetjäningsåtkomsten”.

-   Kontrollera att användaren eller gruppen har aktiverats för att begära åtkomst för självbetjäningsprogram.

-   Kontrollera att användaren är att gå till rätt plats för självbetjäning för programåtkomst. användare kan navigera till sina [Programåtkomstpanelen](https://myapps.microsoft.com/) och klicka på den **+ Lägg till** för att hitta de appar som du har aktiverat åtkomst med självbetjäning.

-   Om självbetjäningsåtkomsten konfigurerades nyligen, försök logga in och ut igen i åtkomstpanelen för användarens efter ett par minuter att se om självbetjäningsåtkomst ändringarna har visats.

## <a name="how-to-configure-self-service-application-access"></a>Så här konfigurerar du självbetjäningsåtkomsten

Följ stegen nedan om du vill aktivera självbetjäningsprogram åtkomst till ett program:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2. Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företagsprogram** från Azure Active Directory-vänstra navigeringsmenyn.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill ge åtkomst till i listan.

7. När programmet har lästs in klickar du på **självbetjäning** från programmets vänstra navigeringsmenyn.

8. Om du vill aktivera självbetjäningsåtkomsten för det här programmet, aktivera den **Tillåt användare att begära åtkomst till det här programmet?** växla till **Ja.**

9. Klicka sedan på väljaren bredvid etiketten för att välja gruppen till vilken användare som begär åtkomst till det här programmet ska läggas till, **till vilken grupp ska tilldelade användare läggas?** och välj en grupp.

10. **Valfritt:** Om du inte vill att kräva ett företag godkännande innan användarna får åtkomst genom att ange den **kräver godkännande innan du beviljar åtkomst till det här programmet?** växla till **Ja**.

11. **Valfritt: För program som använder lösenord för enkel inloggning på endast** om du vill att dessa företagsgodkännaren att ange de lösenord som skickas till det här programmet för godkända användare kan ställa in den **godkännare ha tillåtelse att konfigurera användarens lösenord för det här program?**  växla till **Ja**.

12. **Valfritt:** Om du vill ange den företagsgodkännaren som har behörighet att bevilja åtkomst till det här programmet, klickar du på väljaren bredvid etiketten **som har behörighet att bevilja åtkomst till det här programmet?** att välja upp till 10 enskilda företagsgodkännaren.

    >[!NOTE]
    > Grupper stöds inte.
    >
    >

13. **Valfritt:** **För program som exponera roller**, om du vill tilldela självbetjäning godkända användare till en roll, klickar du på väljaren bredvid den **till vilken roll ska användarna tilldelas i det här programmet?** att välja rollen som Dessa användare ska tilldelas.

14. Klicka på den **spara** längst upp på bladet för att slutföra.

När du har slutfört konfigurationen av programåtkomst via självbetjäning kan användare navigera till sina [Programåtkomstpanelen](https://myapps.microsoft.com/) och klicka på den **+ Lägg till** för att hitta de appar som du har aktiverat självbetjäning åtkomst. Företagsgodkännaren får även ett meddelande i sina [Programåtkomstpanelen](https://myapps.microsoft.com/). Du kan aktivera ett e-postmeddelande om när en användare har begärt åtkomst till ett program som kräver godkännande. 

Dessa godkännanden har stöd för enkel godkännandearbetsflöden, vilket innebär att en enda godkännare kan godkänna åtkomst till programmet om du anger flera godkännare.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Om de här felsökningsstegen inte löser problemet 

Öppna ett supportärende med följande information om de är tillgängliga:

-   Korrelations-ID för fel

-   UPN (användarens e-postadress)

-   TenantID

-   Typ av webbläsare

-   Tidszon och tid/tidsramen under fel inträffar

-   Fiddler-spårningar

## <a name="next-steps"></a>Nästa steg
[Konfigurera Azure Active Directory för grupphantering via självbetjäning](../users-groups-roles/groups-self-service-management.md)
