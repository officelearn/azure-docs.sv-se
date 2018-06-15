---
title: Hämta resultat av Azure AD-åtkomstgranskning | Microsoft Docs
description: Så här hämtar du resultat av Azure Active Directory-åtkomstgranskningar.
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.component: compliance-reports
ms.date: 05/16/2018
ms.author: rolyon
ms.openlocfilehash: c30d166335e31cdbf03283371da42fa6b7cda162
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233122"
---
# <a name="retrieve-access-review-results"></a>Hämta resultat av åtkomstgranskningar

Administratörer kan använda Azure Active Directory (Azure AD) för att [skapa en åtkomstgranskning](active-directory-azure-ad-controls-create-access-review.md) för gruppmedlemmar eller användare som är tilldelade till ett program.  Användare med rollen **Global administratör**, **Säkerhetsadministratör** eller **Säkerhetsläsare** kan också läsa resultatet av en åtkomstgranskning.  För att tilldela användare en av de här rollerna kan en privilegierad rolladministratör använda Azure AD PIM för att göra så att en användare kan aktivera rollen, eller så kan en global administratör permanent [tilldela användaren rollen](active-directory-users-assign-role-azure-portal.md).

[!INCLUDE [Privacy](../../includes/gdpr-intro-sentence.md)]

## <a name="locating-an-access-review"></a>Hitta en åtkomstgranskning

Om du vet vilket program som innehåller åtkomstgranskningen kan du gå till sidan för åtkomstgranskningar, välja **Program** och sedan välja det program som innehåller kontrollen för åtkomstgranskning.  Välj sedan **Kontroller** och därefter kontrollen för åtkomstgranskning. Om det finns många kontroller i programmet kan du filtrera efter en viss typ av kontroller och sortera på status. Du kan även söka efter namnet på kontrollen för åtkomstgranskning eller efter visningsnamnet för ägaren som skapade den. 

Om du inte vet vilket program som innehåller åtkomstgranskningen kan du gå till sidan för åtkomstgranskningar och välja **Kontroller**.  Du kan filtrera efter kontroller av en viss typ och sortera på deras status. Det går även att söka efter namnet på kontrollen för åtkomstgranskningen eller visningsnamnet för ägaren som skapade den. 

## <a name="retrieving-the-results-for-a-one-time-access-review"></a>Hämta resultat av en engångsåtkomstgranskning

Om en återkommande åtkomstgranskning är av engångstyp kan förloppet av en aktiv åtkomstgranskning och resultatet av en slutförd åtkomstgranskning hämtas från avsnittet **Resultat**.  Du kan ange visningsnamnet eller User Principal Name (Användarens huvudnamn) för en användare vars åtkomst granskas om du vill visa enbart den användarens åtkomst.  Om du vill hämta alla resultat av en slutförd åtkomstgranskning klickar du på knappen **Ladda ned**.

## <a name="retrieving-the-results-for-multiple-instances-of-a-recurring-access-review"></a>Hämta resultat av flera instanser av en återkommande åtkomstgranskning

Om du vill visa förloppet för en återkommande aktiv åtkomstgranskning klickar du på avsnittet **Resultat**.  Du kan ange visningsnamn eller User Principal Name (Användarens huvudnamn) för en användare vars åtkomst granskas.

Om du vill visa resultatet av en slutförd instans av en återkommande åtkomstgranskning väljer du **Granska historik**. Välj sedan den specifika instansen i listan över slutförda åtkomstgranskningsinstanser baserat på instansens startdatum och slutdatum.   Resultatet av den här instansen kan hämtas från avsnittet **Resultat**.  Du kan ange visningsnamnet eller User Principal Name (Användarens huvudnamn) för en användare vars åtkomst granskas om du vill visa enbart den användarens åtkomst.  Om du vill hämta alla resultat av en slutförd instans av en återkommande åtkomstgranskning klickar du på knappen **Ladda ned**.


## <a name="removing-users-from-an-access-review"></a>Ta bort användare från en åtkomstgranskning

Som standard är en användare som tas bort borttagen i Azure AD i 30 dagar. Under den här perioden kan användaren återställas av en administratör om det behövs.  Efter 30 dagar tas användaren bort permanent.  En global administratör kan också använda Azure Active Directory-portalen för att uttryckligen och [permanent ta bort en användare som nyligen lagts till](active-directory-users-restore.md) innan perioden är slut.  När en användare har tagits bort permanent tas efterföljande data om den användaren bort från aktiva åtkomstgranskningar.  Granskningsinformation om borttagna användare finns kvar i spårningsloggen.

## <a name="next-steps"></a>Nästa steg

- [Hantera användare med Azure AD-åtkomstgranskningar](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Hantera gäståtkomst med Azure AD-åtkomstgranskningar](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [Hantera program och kontroller för Azure AD-åtkomstgranskningar](active-directory-azure-ad-controls-manage-programs-controls.md)
- [Skapa en åtkomstgranskning för medlemmar i en grupp eller åtkomst till ett program](active-directory-azure-ad-controls-create-access-review.md)
- [Skapa en åtkomstgranskning av användarna i en administrativ roll i Azure AD](active-directory-privileged-identity-management-how-to-start-security-review.md)


