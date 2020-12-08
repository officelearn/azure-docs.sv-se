---
title: Slutför en åtkomst granskning av grupper & program – Azure AD
description: Lär dig hur du utför en åtkomst granskning av grupp medlemmar eller program åtkomst i Azure Active Directory åtkomst granskningar.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/07/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6491de18e65c5071ac0972e7ff49d1253cbd402
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779558"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Slutför en åtkomst granskning av grupper och program i åtkomst granskningar för Azure AD

Som administratör kan du [skapa en åtkomst granskning av grupper eller program](create-access-review.md) och granskare som [utför åtkomst granskningen](perform-access-review.md). Den här artikeln beskriver hur du visar resultatet av åtkomst granskningen och tillämpar resultatet.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Förutsättningar

- Azure AD Premium P2
- Global administratör, användar administratör, säkerhets administratör eller säkerhets läsare

Mer information finns i [licens krav](access-reviews-overview.md#license-requirements).

## <a name="view-an-access-review"></a>Visa en åtkomst granskning

Du kan följa förloppet när granskarna har slutfört granskningarna.

1. Logga in på Azure Portal och öppna [sidan identitets styrning](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Klicka på **åtkomst granskningar** på den vänstra menyn.

1. I listan klickar du på en åtkomst granskning.

    Om du vill visa framtida instanser av åtkomst granskningar går du till åtkomst granskningen och väljer schemalagda granskningar.

    På sidan **Översikt** kan du se förloppet. Ingen åtkomst behörighet har ändrats i katalogen förrän granskningen har slutförts.

    ![Status för åtkomst granskningar](./media/complete-access-review/overview-progress.png)
    
    Om du visar en åtkomst granskning som granskar gäst åtkomst över Microsoft 365 grupper (för hands version), visar översikts bladet varje grupp i granskningen.  

   ![granska gäst åtkomst över Microsoft 365 grupper](./media/complete-access-review/review-guest-access-across-365-groups.png)

    Klicka på en grupp för att se förloppet för granskningen i gruppen.

   ![granska gäst åtkomst över Microsoft 365 grupper i detalj](./media/complete-access-review/progress-group-review.png)

1. Om du vill stoppa en åtkomst granskning innan det har nått det schemalagda slutdatumet klickar du på knappen **stoppa** .

    När du stoppar en granskning kommer granskarna inte längre att kunna ge svar. Du kan inte starta om en granskning när den har stoppats.

1. Om du inte längre är intresse rad av åtkomst granskningen kan du ta bort den genom att klicka på knappen **ta bort** .

## <a name="apply-the-changes"></a>Tillämpa ändringarna

Om **automatiskt tillämpa resultat på resursen** har Aktiver ATS och baserat på dina val i när inställningarna för slut för **ande har slutförts**, utförs automatisk komplettering efter granskningens slutdatum eller när du stoppar granskningen manuellt.

Om **automatiskt tillämpa resultat till resursen** inte har Aktiver ATS för granskningen klickar du på **tillämpa** för att tillämpa ändringarna manuellt. Om en användares åtkomst nekades i granskningen och du klickar på **Verkställ**, tar Azure AD bort sina medlemskap eller program tilldelningar.

![Tillämpa ändringar av åtkomst granskning](./media/complete-access-review/apply-changes.png)

Status för granskningen ändras från **slutförd** till mellanliggande tillstånd, till exempel att **tillämpa** och slutligen för tillstånds **resultat som tillämpas**. Du bör förvänta dig att se till att nekade användare, om de finns, tas bort från grupp medlemskapet eller program tilldelningen om några minuter.

En konfigurerad automatisk granskning, eller om du väljer **tillämpa** , har ingen påverkan på en grupp som kommer från en lokal katalog eller en dynamisk grupp. Om du vill ändra en grupp som har sitt ursprung lokalt hämtar du resultaten och tillämpar ändringarna i åter givningen av gruppen i den katalogen.

## <a name="retrieve-the-results"></a>Hämta resultaten

Klicka på **resultat** sidan om du vill visa resultatet för en enstaka åtkomst granskning. Om du bara vill visa en användares åtkomst går du till rutan Sök och anger visnings namnet eller User Principal Name för en användare vars åtkomst granskades.

![Hämta resultat för en åtkomst granskning](./media/complete-access-review/retrieve-results.png) 

Om du vill visa förloppet för en aktiv åtkomst granskning som är återkommande klickar du på **resultat** sidan.

Om du vill visa resultatet från en slutförd instans av en åtkomst granskning som är återkommande klickar du på **gransknings historik** och väljer sedan den aktuella instansen i listan över slutförda åtkomst gransknings instanser, baserat på instansens start-och slutdatum. Resultatet från den här instansen kan hämtas från **resultat** sidan.

Om du vill hämta alla resultat från en åtkomst granskning klickar du på knappen **Ladda ned** . Den resulterande CSV-filen kan visas i Excel eller i andra program som öppnar CSV-filer med UTF-8.

## <a name="remove-users-from-an-access-review"></a>Ta bort användare från en åtkomst granskning

 Som standard är en användare som tas bort borttagen i Azure AD i 30 dagar. Under den här perioden kan användaren återställas av en administratör om det behövs.  Efter 30 dagar tas användaren bort permanent.  Med hjälp av Azure Active Directory Portal kan en global administratör dessutom uttryckligen [ta bort en nyligen borttagen användare](../fundamentals/active-directory-users-restore.md) innan tids perioden uppnås.  När en användare har tagits bort permanent tas efterföljande data om den användaren bort från aktiva åtkomstgranskningar.  Granskningsinformation om borttagna användare finns kvar i spårningsloggen.

## <a name="next-steps"></a>Nästa steg

- [Hantera användare med Azure AD-åtkomstgranskningar](manage-user-access-with-access-reviews.md)
- [Hantera gäståtkomst med Azure AD-åtkomstgranskningar](manage-guest-access-with-access-reviews.md)
- [Skapa en åtkomst granskning av grupper eller program](create-access-review.md)
- [Skapa en åtkomstgranskning av användarna i en administrativ roll i Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
