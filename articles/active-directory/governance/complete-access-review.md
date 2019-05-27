---
title: Slutför en åtkomstgranskning av grupper eller program – Azure Active Directory | Microsoft Docs
description: Lär dig hur du slutför en åtkomstgranskning för medlemmar i gruppen eller programmet åtkomst i Azure Active Directory-åtkomstgranskningar.
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
ms.date: 05/22/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: bae204ec1789f227150adc560d4a292404d23b7e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113311"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Slutför en åtkomstgranskning av grupper eller program i Azure AD-åtkomstgranskningar

Som administratör kan du [skapa en åtkomstgranskning av grupper eller program](create-access-review.md) och granskare [utföra åtkomstgranskningen](perform-access-review.md). Den här artikeln beskriver hur du se resultaten av åtkomstgranskningen och tillämpar resultaten.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

- Azure AD Premium P2
- Global administratör, Användaradministratör, säkerhetsadministratör eller säkerhetsläsare

Mer information finns i [vilka användare måste ha licenser?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="view-an-access-review"></a>Visa en åtkomstgranskning

Du kan följa förloppet när granskarna har slutfört sina granskningar.

1. Logga in på Azure-portalen och öppna den [sidan med åtkomstgranskningar](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. I den vänstra menyn klickar du på **Åtkomstgranskningar**.

1. Klicka på en åtkomstgranskning i listan.

    Visa en serie med granskningar, gå till åtkomstgranskningen och hittar du kommande förekomster i schemalagd granskning.

    På den **översikt** kan du kan följa förloppet. Ingen behörighet har ändrats i katalogen tills granskningen har slutförts.

    ![Åtkomstgranskningar pågår](./media/complete-access-review/overview-progress.png)

1. Om du vill stoppa en åtkomstgranskning före det schemalagda slutdatumet har uppnåtts, klickar på den **stoppa** knappen.

    Stoppa när en granskning, granskare kommer inte längre att kunna ge några svar. Du kan inte starta om en granskning när den är stoppad.

1. Om du inte längre är intresserad av åtkomstgranskningen kan du ta bort den genom att klicka på den **ta bort** knappen.

## <a name="apply-the-changes"></a>Tillämpa ändringarna

Om **tillämpa automatiskt resultaten på resursen** är aktiverat och baserat på dina val i **vid slutförande-inställningar**, automatisk-gäller kommer utförs efter slutet granskningsdatum eller när du manuellt stoppa granskningen.

Om **tillämpa automatiskt resultaten på resursen** har inte aktiverats för granskningen klickar du på **tillämpa** att tillämpa ändringarna manuellt. Om en användares åtkomst nekades i granskningen, när du klickar på **tillämpa**, Azure AD tar bort sitt medlemskap eller programmet tilldelning.

![Tillämpa åtkomst granska ändringar](./media/complete-access-review/apply-changes.png)

Status för granskningen kommer att ändras från **slutförd** via mellanliggande tillstånd som **tillämpar** och slutligen till tillstånd **resultatet tillämpades**. Du bör förvänta dig att se nekade användare, om sådant finns, tas bort från grupptilldelning för medlemskap eller ett program på några få minuter.

En konfigurerade automatiskt tillämpa granskning eller välja **tillämpa** inte påverka en grupp som har sitt ursprung på en lokal katalog eller en dynamisk grupp. Om du vill ändra en grupp som har sitt ursprung på plats, Hämta resultaten och tillämpa ändringarna på representation av gruppen i den katalogen.

## <a name="retrieve-the-results"></a>Hämta resultaten

Om du vill visa resultat för en enstaka åtkomstgranskning, klickar du på den **resultat** sidan. Om du vill visa bara en användares behörighet i sökrutan skriver du namn eller användarhuvudnamn för en användare vars åtkomst granskades.

![Hämta resultat för en åtkomstgranskning](./media/complete-access-review/retrieve-results.png)

Om du vill visa förloppet för en aktiv åtkomstgranskning som är återkommande, klickar du på den **resultat** sidan.

Om du vill visa resultatet av en slutförd instans av en åtkomstgranskning som är återkommande, klickar du på **granska historiken**och välj sedan den specifika instansen i listan för slutförda åtkomstgranskningsinstanser, baserat på den instansen start- och slutdatum. Resultatet av den här instansen kan hämtas från den **resultat** sidan.

Om du vill hämta alla resultat från en åtkomstgranskning, klickar du på den **hämta** knappen. Den resulterande CSV-filen kan ses i Excel eller i andra program som öppnar UTF-8-kodade CSV-filer.

## <a name="remove-users-from-an-access-review"></a>Ta bort användare från en åtkomstgranskning

 Som standard är en användare som tas bort borttagen i Azure AD i 30 dagar. Under den här perioden kan användaren återställas av en administratör om det behövs.  Efter 30 dagar tas användaren bort permanent.  En global administratör kan också använda Azure Active Directory-portalen för att uttryckligen och [permanent ta bort en användare som nyligen lagts till](../fundamentals/active-directory-users-restore.md) innan perioden är slut.  När en användare har tagits bort permanent tas efterföljande data om den användaren bort från aktiva åtkomstgranskningar.  Granskningsinformation om borttagna användare finns kvar i spårningsloggen.

## <a name="next-steps"></a>Nästa steg

- [Hantera användare med Azure AD-åtkomstgranskningar](manage-user-access-with-access-reviews.md)
- [Hantera gäståtkomst med Azure AD-åtkomstgranskningar](manage-guest-access-with-access-reviews.md)
- [Skapa en åtkomstgranskning av grupper eller program](create-access-review.md)
- [Skapa en åtkomstgranskning av användarna i en administrativ roll i Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
