---
title: Slutföra en åtkomst-granskning av medlemmar i en grupp eller användare åtkomst till ett program med Azure AD | Microsoft Docs
description: Lär dig hur du utför en åtkomst-granskning för medlemmar i en grupp eller användare med åtkomst till ett program i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: rolyon
ms.openlocfilehash: 7e3fbb0f355ff0ffab404af9b7de1a27de02f1fc
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34697922"
---
# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>Slutföra en åtkomst-granskning av medlemmar i en grupp eller användare åtkomst till ett program i Azure AD

Administratörer kan använda Azure Active Directory (Azure AD) för att [skapa en åtkomstgranskning](active-directory-azure-ad-controls-create-access-review.md) för gruppmedlemmar eller användare som är tilldelade till ett program. Azure AD skickar granskare automatiskt ett e-postmeddelande som uppmanar dem att granska åtkomst. Om en användare inte har fått ett e-postmeddelande, kan du skicka dem instruktionerna [granska din åtkomst](active-directory-azure-ad-controls-perform-access-review.md). (Observera att gäster som är tilldelad som granskare, men har inte accepterat inbjudan får ett e-postmeddelande från åtkomst granskningar som de måste du acceptera en inbjudan innan granska.) Följ stegen i den här artikeln finns och att tillämpa resultaten när omprövningsperioden åtkomst är över eller om en administratör slutar åtkomst granska.

## <a name="view-an-access-review-in-the-azure-portal"></a>Visa en åtkomst-granskning i Azure-portalen

1. Gå till den [åtkomst går igenom sidan](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)väljer **program**, och välj det program som innehåller granska åtkomstkontroll.

2. Välj **hantera**, och välj granska åtkomstkontroll. Om det finns många kontroller i programmet kan du filtrera efter en viss typ av kontroller och sortera på status. Du kan även söka efter namnet på kontrollen för åtkomstgranskning eller efter visningsnamnet för ägaren som skapade den. 

## <a name="stop-a-review-that-hasnt-finished"></a>Stoppa en granskning inte har slutförts

Om granskningen inte har nått det schemalagda slutdatumet kan en administratör kan välja **stoppa** du avsluta granskningen tidigt. När du stoppar granskningen kan användarna inte längre granskas. Du kan inte starta om en granskning när den har stoppats.

## <a name="apply-the-changes"></a>Tillämpa ändringarna 

När en åtkomst-granskning är klar, antingen eftersom den nått slutdatum eller administratören stoppade tjänsten manuellt och automatiskt tillämpa inte konfigurerats för granskning, kan du välja **tillämpa** att tillämpa ändringarna manuellt. Resultatet av granskningen implementeras genom att uppdatera den grupp eller ett program. Om en användares åtkomst nekades i granskningen, när en administratör väljer det här alternativet, Azure AD tar bort tilldelningen av deras medlemskap eller programmet. 

När en åtkomst-granskning har slutförts och tillämpa automatiskt konfigurerades status för granska kommer att ändras från slutförd via mellanliggande tillstånd och slutligen ändrar tillstånd tillämpats. Du bör se nekade användare om något som tas bort från resursen för att gruppera medlemskap eller app tilldelning i ett par minuter.

En konfigurerade automatiskt tillämpa granskning eller välja **tillämpa** inte har en effekt på en grupp som har sitt ursprung i en lokal katalog eller en dynamisk grupp. Om du vill ändra en grupp som har sitt ursprung lokalt Hämta resultaten och tillämpa ändringarna på representation av gruppen i den katalogen.

## <a name="download-the-results-of-the-review"></a>Hämta resultaten för granska

Om du vill hämta resultaten för granskningen väljer **godkännanden** och välj sedan **hämta**. Den resulterande CSV-fil som kan visas i Excel eller andra program som öppnar UTF-8-kodat CSV-filer.

## <a name="optional-delete-a-review"></a>Valfritt: Ta bort ett omdöme
Om du inte längre är intresserad av att granskningen kan du ta bort den. Välj **ta bort** att ta bort granskningen från Azure AD.

> [!IMPORTANT]
> Det finns ingen varning innan borttagningen sker, så Tänk på att du vill ta bort granskningen.
> 
> 

## <a name="next-steps"></a>Nästa steg

- [Hantera användare med Azure AD-åtkomstgranskningar](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Hantera gäståtkomst med Azure AD-åtkomstgranskningar](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [Hantera program och kontroller för Azure AD-åtkomstgranskningar](active-directory-azure-ad-controls-manage-programs-controls.md)
- [Skapa en åtkomstgranskning för medlemmar i en grupp eller åtkomst till ett program](active-directory-azure-ad-controls-create-access-review.md)
- [Skapa en åtkomstgranskning av användarna i en administrativ roll i Azure AD](active-directory-privileged-identity-management-how-to-start-security-review.md)
