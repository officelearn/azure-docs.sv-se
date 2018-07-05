---
title: Så här konfigurerar du tilldelning av programåtkomst via självbetjäning | Microsoft Docs
description: Aktivera självbetjäning programåtkomst så att användarna kan hitta sina egna program
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: barbkess
ms.openlocfilehash: 89eb80dbb749b2f50ca3f1fc097c205b62b6b4a4
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445304"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Så här konfigurerar du tilldelning av programåtkomst via självbetjäning

Innan användarna kan själva upptäcka program från sina åtkomstpaneler, måste du aktivera **självbetjäningsåtkomsten** för program som du vill tillåta användare att identifiera själv och begära åtkomst till.

Den här funktionen är ett bra sätt att spara tid och pengar som en IT-grupp och rekommenderas starkt som en del av en distribution för moderna program med Azure Active Directory.

Den här funktionen kan du:

-   Låta användare själva upptäcka program från den [Programåtkomstpanelen](https://myapps.microsoft.com/) utan stör IT-gruppen.

-   Lägga till dessa användare i en förkonfigurerad så att du kan se vem som har begärt åtkomst, ta bort åtkomst och hantera roller som tilldelats.

-   Du kan också kan ett företagsgodkännaren att godkänna åtkomstbegäranden för programmet så att IT-gruppen inte behöver.

-   Du kan också konfigurera upp till 10 personer som kan godkänna åtkomst till det här programmet.

-   Om du vill tillåta ett företag godkännaren att ange lösenorden dessa användare kan använda för att logga in på programmet, direkt från företagsgodkännaren [Programåtkomstpanelen](https://myapps.microsoft.com/).

-   Du kan också automatiskt tilldela självbetjäning tilldelade användare till en programroll direkt.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Aktivera självbetjäning programåtkomst så att användarna kan hitta sina egna program

Självbetjäning för programåtkomst är ett bra sätt så att användarna kan identifiera lokal program, låta affärsgrupp att godkänna åtkomst till dessa program. Du kan tillåta affärsgrupp att hantera de autentiseringsuppgifter som har tilldelats till de användarna av lösenord för enkel inloggning på program direkt från sina åtkomstpaneler.

Följ stegen nedan om du vill aktivera självbetjäningsprogram åtkomst till ett program:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory-vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill ge åtkomst till i listan.

7.  När programmet har lästs in klickar du på **självbetjäning** från programmets vänstra navigeringsmenyn.

8.  Om du vill aktivera självbetjäningsåtkomsten för det här programmet, aktivera den **Tillåt användare att begära åtkomst till det här programmet?** växla till **Ja.**

9.  Klicka sedan på väljaren bredvid etiketten för att välja gruppen till vilken användare som begär åtkomst till det här programmet ska läggas till, **till vilken grupp ska tilldelade användare läggas?** och välj en grupp.
  
  > [!NOTE]
  > Grupper som synkroniserats från lokalt stöds inte för att användas för gruppen som användare som begär åtkomst till det här programmet ska läggas till.
  
10. **Valfritt:** om du inte vill att kräva ett företag godkännande innan användarna får åtkomst genom att ange den **kräver godkännande innan du beviljar åtkomst till det här programmet?** växla till **Ja**.

11. **Valfritt: för program som använder lösenord för enkel inloggning på endast** om du vill att dessa företagsgodkännaren att ange de lösenord som skickas till det här programmet för godkända användare kan ställa in den **godkännare ha tillåtelse att ange användarens lösenord för det här programmet?**  växla till **Ja**.

12. **Valfritt:** för att ange den företagsgodkännaren som har behörighet att bevilja åtkomst till det här programmet, klickar du på väljaren bredvid etiketten **som har behörighet att bevilja åtkomst till det här programmet?** att välja upp till 10 person företagsgodkännaren.

   >[!NOTE]
   >Grupper stöds inte.
   >
   >

13. **Valfritt:** **för program som exponera roller**, om du vill tilldela självbetjäning godkända användare till en roll, klickar du på väljaren bredvid den **till vilken roll ska användarna tilldelas i det här programmet?** att välja rollen som dessa användare ska tilldelas.

14. Klicka på den **spara** längst upp på bladet för att slutföra.

När du har slutfört konfigurationen av programåtkomst via självbetjäning kan användare navigera till sina [Programåtkomstpanelen](https://myapps.microsoft.com/) och klicka på den **+ Lägg till** för att hitta de appar som du har aktiverat självbetjäning åtkomst. Företagsgodkännaren får även ett meddelande i sina [Programåtkomstpanelen](https://myapps.microsoft.com/). Du kan aktivera ett e-postmeddelande om när en användare har begärt åtkomst till ett program som kräver godkännande. 

Dessa godkännanden stöder enkel godkännandearbetsflöden, vilket innebär att om du anger flera godkännare kan en enda godkännare kan godkännaren åtkomst till programmet.

## <a name="next-steps"></a>Nästa steg
[Konfigurera Azure Active Directory för grupphantering via självbetjäning](users-groups-roles/groups-self-service-management.md)
