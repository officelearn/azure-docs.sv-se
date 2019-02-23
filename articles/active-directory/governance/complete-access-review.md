---
title: Slutför en åtkomstgranskning av grupper eller program i Azure AD-Åtkomstgranskningar | Microsoft Docs
description: Lär dig hur du slutför en åtkomstgranskning för medlemmar i gruppen eller programmet åtkomst i Azure AD-Åtkomstgranskningar.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/02/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc5dbee47bd6a621b14b39858132ce229663481b
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729618"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Slutför en åtkomstgranskning av grupper eller program i Azure AD-Åtkomstgranskningar

Administratörer kan använda Azure Active Directory (Azure AD) för att [skapa en åtkomstgranskning](create-access-review.md) för gruppmedlemmar eller användare som är tilldelade till ett program. Azure AD skickar granskare automatiskt ett e-postmeddelande som uppmanar dem att granska åtkomst. Om en användare inte får ett e-postmeddelande kan du skicka dem instruktionerna [granska åtkomst till grupper eller program](perform-access-review.md). (Observera att gäster som är tilldelad som granskare, men inte har accepterat inbjudan inte får ett e-postmeddelande från åtkomstgranskningar, eftersom de måste du acceptera en inbjudan innan du granskat.) När du åtkomstgranskningen är slut eller om en administratör slutar åtkomstgranskningen, följer du stegen i den här artikeln för att se och tillämpa resultaten.

## <a name="view-an-access-review-in-the-azure-portal"></a>Visa en åtkomstgranskning i Azure portal

1. Gå till den [sidan med åtkomstgranskningar](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)väljer **program**, och välj det program som innehåller åtkomstkontroll för granskning.

2. Välj **hantera**, och välj åtkomstkontroll för granskning. Om det finns många kontroller i programmet kan du filtrera efter en viss typ av kontroller och sortera på status. Du kan även söka efter namnet på kontrollen för åtkomstgranskning eller efter visningsnamnet för ägaren som skapade den. 

## <a name="stop-a-review-that-hasnt-finished"></a>Stoppa en granskning inte har slutförts

Om granskningen inte har nått det schemalagda slutdatumet, en administratör kan välja **stoppa** du avsluta granskningen tidigt. När du stoppa granskningen kan användare inte längre att granskas. Du kan inte starta om en granskning när den är stoppad.

## <a name="apply-the-changes"></a>Tillämpa ändringarna 

När en åtkomstgranskning är klar, antingen eftersom det har nått slutdatum eller en administratör stoppade den manuellt och Använd automatiskt har inte konfigurerats för granskning, kan du välja **tillämpa** att tillämpa ändringarna manuellt. Resultatet av granskningen implementeras genom att uppdatera gruppen eller programmet. Om en användares åtkomst nekades i granskningen, när en administratör väljer det här alternativet, Azure AD tar du bort deras medlemskap eller programmet tilldelning. 

När en åtkomstgranskning är klar och autotillämpad konfigurerades status för granskningen kommer att ändras från slutförd via mellanliggande tillstånd och slutligen kommer att ändras till tillstånd tillämpas. Du kan förvänta att se nekade användare, om sådant finns, tas bort från resursen gruppen medlemskap eller apptilldelningar på några få minuter.

En konfigurerade automatiskt tillämpa granskning eller välja **tillämpa** inte påverka en grupp som har sitt ursprung på en lokal katalog eller en dynamisk grupp. Om du vill ändra en grupp som har sitt ursprung på plats, Hämta resultaten och tillämpa ändringarna på representation av gruppen i den katalogen.

## <a name="download-the-results-of-the-review"></a>Hämta resultatet från granskningen

Om du vill hämta resultaten från granskningen, Välj **godkännanden** och välj sedan **hämta**. Den resulterande CSV-filen kan ses i Excel eller i andra program som öppnar UTF-8-kodade CSV-filer.

## <a name="optional-delete-a-review"></a>Valfritt: Ta bort en granskning
Om du inte längre är intresserad av att granskningen kan du ta bort den. Välj **ta bort** att ta bort granskningen från Azure AD.

> [!IMPORTANT]
> Det finns ingen varning innan borttagningen sker, så var noga med att du vill ta bort granskningen.
> 
> 

## <a name="next-steps"></a>Nästa steg

- [Hantera användare med Azure AD-åtkomstgranskningar](manage-user-access-with-access-reviews.md)
- [Hantera gäståtkomst med Azure AD-åtkomstgranskningar](manage-guest-access-with-access-reviews.md)
- [Hantera program och kontroller för Azure AD-åtkomstgranskningar](manage-programs-controls.md)
- [Skapa en åtkomstgranskning av grupper eller program](create-access-review.md)
- [Skapa en åtkomstgranskning av användarna i en administrativ roll i Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
