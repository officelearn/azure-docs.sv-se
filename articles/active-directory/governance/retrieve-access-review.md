---
title: Hämta åtkomstgranskningsresultaten efter grupper eller program i Azure AD-Åtkomstgranskningar | Microsoft Docs
description: Lär dig hur du hämtar åtkomstgranskningsresultat för medlemmar i gruppen eller programmet åtkomst i Azure AD-Åtkomstgranskningar.
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
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2516e61b6b86c3d2cee164cfcb80a1f938922908
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839652"
---
# <a name="retrieve-access-review-results-for-groups-or-applications-in-azure-ad-access-reviews"></a>Hämta åtkomstgranskningsresultaten efter grupper eller program i Azure AD-Åtkomstgranskningar

Administratörer kan använda Azure Active Directory (Azure AD) för att [skapa en åtkomstgranskning](create-access-review.md) för gruppmedlemmar eller användare som är tilldelade till ett program.  En användare som tillhör den **Global administratör**, **Användaradministratör**, **säkerhetsadministratör** eller **säkerhetsläsare** rollen kan även läsa resultaten av en åtkomstgranskning.  Om du vill tilldela användare till någon av dessa roller, en privilegierad rolladministratör kan använda Azure AD PIM för att berättiga en användare att aktivera rollen eller en Global administratör kan permanent [tilldela en användare till rollen](../fundamentals/active-directory-users-assign-role-azure-portal.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="locating-an-access-review"></a>Hitta en åtkomstgranskning

Om du vet vilket program som innehåller åtkomstgranskningen kan du gå till sidan för åtkomstgranskningar, välja **Program** och sedan välja det program som innehåller kontrollen för åtkomstgranskning.  Välj sedan **Kontroller** och därefter kontrollen för åtkomstgranskning. Om det finns många kontroller i programmet kan du filtrera efter en viss typ av kontroller och sortera på status. Du kan även söka efter namnet på kontrollen för åtkomstgranskning eller efter visningsnamnet för ägaren som skapade den. 

Om du inte vet vilket program som innehåller åtkomstgranskningen kan du gå till sidan för åtkomstgranskningar och välja **Kontroller**.  Du kan filtrera efter kontroller av en viss typ och sortera på deras status. Det går även att söka efter namnet på kontrollen för åtkomstgranskningen eller visningsnamnet för ägaren som skapade den. 

## <a name="retrieving-the-results-for-a-one-time-access-review"></a>Hämta resultat av en engångsåtkomstgranskning

Om en återkommande åtkomstgranskning är av engångstyp kan förloppet av en aktiv åtkomstgranskning och resultatet av en slutförd åtkomstgranskning hämtas från avsnittet **Resultat**.  Du kan ange visningsnamnet eller User Principal Name (Användarens huvudnamn) för en användare vars åtkomst granskas om du vill visa enbart den användarens åtkomst.  Om du vill hämta alla resultat av en slutförd åtkomstgranskning klickar du på knappen **Ladda ned**.

## <a name="retrieving-the-results-for-multiple-instances-of-a-recurring-access-review"></a>Hämta resultat av flera instanser av en återkommande åtkomstgranskning

Om du vill visa förloppet för en återkommande aktiv åtkomstgranskning klickar du på avsnittet **Resultat**.  Du kan ange visningsnamn eller User Principal Name (Användarens huvudnamn) för en användare vars åtkomst granskas.

Om du vill visa resultatet av en slutförd instans av en återkommande åtkomstgranskning väljer du **Granska historik**. Välj sedan den specifika instansen i listan över slutförda åtkomstgranskningsinstanser baserat på instansens startdatum och slutdatum.   Resultatet av den här instansen kan hämtas från avsnittet **Resultat**.  Du kan ange visningsnamnet eller User Principal Name (Användarens huvudnamn) för en användare vars åtkomst granskas om du vill visa enbart den användarens åtkomst.  Om du vill hämta alla resultat av en slutförd instans av en återkommande åtkomstgranskning klickar du på knappen **Ladda ned**.


## <a name="removing-users-from-an-access-review"></a>Ta bort användare från en åtkomstgranskning

Som standard är en användare som tas bort borttagen i Azure AD i 30 dagar. Under den här perioden kan användaren återställas av en administratör om det behövs.  Efter 30 dagar tas användaren bort permanent.  En global administratör kan också använda Azure Active Directory-portalen för att uttryckligen och [permanent ta bort en användare som nyligen lagts till](../fundamentals/active-directory-users-restore.md) innan perioden är slut.  När en användare har tagits bort permanent tas efterföljande data om den användaren bort från aktiva åtkomstgranskningar.  Granskningsinformation om borttagna användare finns kvar i spårningsloggen.

## <a name="next-steps"></a>Nästa steg

- [Hantera användare med Azure AD-åtkomstgranskningar](manage-user-access-with-access-reviews.md)
- [Hantera gäståtkomst med Azure AD-åtkomstgranskningar](manage-guest-access-with-access-reviews.md)
- [Hantera program och kontroller för Azure AD-åtkomstgranskningar](manage-programs-controls.md)
- [Skapa en åtkomstgranskning av grupper eller program](create-access-review.md)
- [Skapa en åtkomstgranskning av användarna i en administrativ roll i Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)


