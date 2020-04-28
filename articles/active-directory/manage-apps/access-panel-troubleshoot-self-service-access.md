---
title: Problem med att använda självbetjäning för program åtkomst | Microsoft Docs
description: Felsöka problem som rör åtkomst till självbetjänings program
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
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "65784285"
---
# <a name="problem-using-self-service-application-access"></a>Problem med självbetjäning för program åtkomst

Åtkomst till självbetjänings program är ett bra sätt att ge användare möjlighet att identifiera program, vilket gör det möjligt för företags gruppen att godkänna åtkomst till dessa program. Du kan tillåta att affärs gruppen hanterar de autentiseringsuppgifter som tilldelats dessa användare för lösen ords program med enkel inloggning direkt från sina åtkomst paneler.

Innan användarna kan identifiera program från sina åtkomst paneler måste du aktivera självbetjänings **program åtkomst** till alla program som du vill ge användare möjlighet att själv identifiera och begära åtkomst till.

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrol lera först

-   Kontrol lera att självbetjänings programmets åtkomst är korrekt konfigurerad. Se "konfigurera åtkomst till självbetjänings program".

-   Se till att användaren eller gruppen har Aktiver ATS för att begära åtkomst till självbetjänings program.

-   Se till att användaren besöker rätt plats för självbetjänings program åtkomst. användare kan navigera till sin [program åtkomst panel](https://myapps.microsoft.com/) och klicka på knappen **+ Lägg** till för att hitta appar som du har aktiverat självbetjänings åtkomst till.

-   Om självbetjänings programmet har kon figurer ATS nyligen kan du försöka logga in och ut igen i användarens åtkomst panel efter några minuter för att se om självbetjänings åtkomsten har ändrats.

## <a name="how-to-configure-self-service-application-access"></a>Så här konfigurerar du självbetjäning för program åtkomst

Följ stegen nedan om du vill aktivera självbetjänings program åtkomst till ett program:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** i Azure Active Directory vänster hand navigerings meny.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6. Välj det program som du vill aktivera självbetjänings åtkomst till i listan.

7. När programmet har lästs in klickar du på **självbetjäning** från programmets vänstra navigerings meny.

8. Aktivera självbetjäning för program åtkomst för det här programmet genom att aktivera alternativet **Tillåt användare att begära åtkomst till det här programmet?** växla till **Ja.**

9. Välj sedan den grupp som användare som begär åtkomst till det här programmet ska läggas till genom att klicka på väljaren bredvid etiketten som **gruppen ska tilldelas användare till?** och välja en grupp.

10. **Valfritt:** Om du vill kräva ett affärs godkännande innan användarna tillåts måste du ställa in **Kräv godkännande innan jag beviljar åtkomst till det här programmet?** växla till **Ja**.

11. **Valfritt: endast för program som använder enkel inloggning med lösen ord,** om du vill tillåta att affärs god kännare anger lösen ord som skickas till det här programmet för godkända användare ställer du in alternativet **Tillåt god kännare att ange användarens lösen ord för det här programmet?** växla till **Ja**.

12. **Valfritt:** Om du vill ange de affärs god kännare som tillåts att godkänna åtkomst till det här programmet klickar du på väljaren bredvid den etikett **som har behörighet att godkänna åtkomst till det här programmet?** om du vill välja upp till 10 enskilda affärs god kännare.

    >[!NOTE]
    > Grupper stöds inte.
    >
    >

13. **Valfritt:** **för program som visar roller**, om du vill tilldela självbetjänings godkända användare till en roll, klickar du på väljaren bredvid **rollen till vilken roll ska användare tilldelas i det här programmet?** Välj den roll som användarna ska tilldelas till.

14. Klicka på knappen **Spara** överst på bladet för att avsluta.

När du har slutfört självbetjänings program konfigurationen kan användarna navigera till sin [program åtkomst panel](https://myapps.microsoft.com/) och klicka på knappen **+ Lägg** till för att hitta appar som du har aktiverat självbetjänings åtkomst till. Affärs god kännare ser också ett meddelande i sin [program åtkomst panel](https://myapps.microsoft.com/). Du kan aktivera ett e-postmeddelande som meddelar dem när en användare har begärt åtkomst till ett program som kräver godkännande. 

Dessa godkännanden stöder endast enskilda godkännande arbets flöden, vilket innebär att om du anger flera god kännare kan en enskild god kännare godkänna åtkomst till programmet.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Om de här fel söknings stegen inte löser problemet 

öppna ett support ärende med följande information om den är tillgänglig:

-   Korrelations fel-ID

-   UPN (användarens e-postadress)

-   TenantID

-   Typ av webbläsare

-   Tidszon och tid/tidsram vid fel inträffar

-   Fiddler-spår

## <a name="next-steps"></a>Nästa steg
[Konfigurera Azure Active Directory för grupphantering via självbetjäning](../users-groups-roles/groups-self-service-management.md)
