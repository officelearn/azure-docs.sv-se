---
title: Så här använder du självbetjäning för program åtkomst | Microsoft Docs
description: Aktivera självbetjäning för program åtkomst så att användarna kan hitta sina egna program
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1c5a003eb113289bcb798423ff649b944dcf186
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2020
ms.locfileid: "84761076"
---
# <a name="how-to-use-self-service-application-access"></a>Använda självbetjäning för program åtkomst

Innan användarna kan identifiera program från sina åtkomst paneler måste du aktivera självbetjänings **program åtkomst** till alla program som du vill ge användare möjlighet att själv identifiera och begära åtkomst till.

Den här funktionen är ett bra sätt att spara tid och pengar som en IT-grupp och är starkt rekommenderat som en del av en modern program distribution med Azure Active Directory.

Med den här funktionen kan du:

-   Låt användare själv identifiera program från [program åtkomst panelen](https://myapps.microsoft.com/) utan att bry dig om IT-gruppen.

-   Lägg till dessa användare i en förkonfigurerad grupp så att du kan se vem som har begärt åtkomst, ta bort åtkomst och hantera de roller som har tilldelats dem.

-   Alternativt kan en företags god kännare godkänna begär Anden om program åtkomst så att IT-gruppen inte behöver.

-   Du kan också konfigurera upp till 10 personer som kan godkänna åtkomst till det här programmet.

-   Alternativt kan en företags god kännare ange de lösen ord som användarna kan använda för att logga in till programmet, direkt från affärs god kännaens [program åtkomst panel](https://myapps.microsoft.com/).

-   Du kan också automatiskt tilldela självbetjänings användare till en program roll direkt.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Aktivera självbetjäning för program åtkomst så att användarna kan hitta sina egna program

Åtkomst till självbetjänings program är ett bra sätt att ge användare möjlighet att identifiera program, vilket gör det möjligt för företags gruppen att godkänna åtkomst till dessa program. Du kan tillåta att affärs gruppen hanterar de autentiseringsuppgifter som tilldelats dessa användare för lösen ords program med enkel inloggning direkt från sina åtkomst paneler.

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

    * Grupper stöds inte.

13. **Valfritt:** **för program som visar roller**, om du vill tilldela självbetjänings godkända användare till en roll, klickar du på väljaren bredvid **rollen till vilken roll ska användare tilldelas i det här programmet?** Välj den roll som användarna ska tilldelas till.

14. Klicka på knappen **Spara** överst på bladet för att avsluta.

När du har slutfört självbetjänings program konfigurationen kan användarna navigera till sin [program åtkomst panel](https://myapps.microsoft.com/) och klicka på knappen **+ Lägg** till för att hitta appar som du har aktiverat självbetjänings åtkomst till. Affärs god kännare ser också ett meddelande i sin [program åtkomst panel](https://myapps.microsoft.com/). Du kan aktivera ett e-postmeddelande som meddelar dem när en användare har begärt åtkomst till ett program som kräver godkännande. 

Dessa godkännanden stöder endast enskilda godkännande arbets flöden, vilket innebär att om du anger flera god kännare kan en enskild god kännare godkänna åtkomst till programmet.

## <a name="next-steps"></a>Nästa steg
[Konfigurera Azure Active Directory för grupphantering via självbetjäning](../users-groups-roles/groups-self-service-management.md)
