---
title: Slutför en åtkomstgranskning av grupper & program - Azure AD
description: Lär dig hur du slutför en åtkomstgranskning av gruppmedlemmar eller programåtkomst i Azure Active Directory-åtkomstgranskningar.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 266234f2872cfe99509d564c9460bfba4a0e2bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932549"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Slutför en åtkomstgranskning av grupper och program i Azure AD-åtkomstgranskningar

Som administratör skapar du [en åtkomstgranskning av grupper eller program](create-access-review.md) och granskare utför [åtkomstgranskningen](perform-access-review.md). I den här artikeln beskrivs hur du ser resultatet av åtkomstgranskningen och tillämpar resultaten.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Krav

- Azure AD Premium P2
- Global administratör, användaradministratör, säkerhetsadministratör eller säkerhetsläsare

Mer information finns i [Licenskrav](access-reviews-overview.md#license-requirements).

## <a name="view-an-access-review"></a>Visa en åtkomstgranskning

Du kan spåra förloppet när granskarna slutför sina recensioner.

1. Logga in på Azure-portalen och öppna [sidan Identitetsstyrning](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Klicka på **Access-recensioner**på den vänstra menyn .

1. Klicka på en åtkomstgranskning i listan.

    Om du vill visa en serie åtkomstgranskningar navigerar du till åtkomstgranskningen och du hittar kommande förekomster i schemalagda granskningar.

    På sidan **Översikt** kan du se förloppet. Inga åtkomsträttigheter ändras i katalogen förrän granskningen är klar.

    ![Framsteg i åtkomstgranskningar](./media/complete-access-review/overview-progress.png)

1. Om du vill stoppa en åtkomstgranskning innan den har nått det schemalagda slutdatumet klickar du på **stoppknappen.**

    När du stoppar en granskning kan granskarna inte längre ge svar. Du kan inte starta om en recension när den har stoppats.

1. Om du inte längre är intresserad av åtkomstgranskningen kan du ta bort den genom att klicka på knappen **Ta bort.**

## <a name="apply-the-changes"></a>Tillämpa ändringarna

Om **Auto tillämpa resultat på resursen** aktiverades och baserat på dina val i Efter **slutförande inställningar,** automatiskt tillämpa kommer att köras efter översynens slutdatum eller när du manuellt stoppa översynen.

Om **Auto tillämpa resultat på resurs** inte var aktiverat för granskningen klickar du på **Använd** för att manuellt tillämpa ändringarna. Om en användares åtkomst nekades i granskningen tas deras medlemskap eller programtilldelning bort när du klickar på **Verkställ.**

![Använda ändringar i åtkomstgranskning](./media/complete-access-review/apply-changes.png)

Statusen för granskningen ändras från **slutförda** till mellanliggande tillstånd som **Tillämpning** och slutligen till **tillståndsresultat tillämpas**. Du bör förvänta dig att se nekade användare, om några, tas bort från gruppmedlemskap eller programtilldelning i några minuter.

En konfigurerad automatisk granskning eller val **av Verkställ** påverkar inte en grupp som har sitt ursprung i en lokal katalog eller en dynamisk grupp. Om du vill ändra en grupp som har sitt ursprung lokalt hämtar du resultaten och tillämpar ändringarna på representationen av gruppen i den katalogen.

## <a name="retrieve-the-results"></a>Hämta resultaten

Klicka på sidan **Resultat** om du vill visa resultatet för en engångsföretening. Om du bara vill visa en användares åtkomst skriver du visningsnamnet eller användarnamnet för en användare vars åtkomst har granskats i rutan Sök.

![Hämta resultat för en åtkomstgranskning](./media/complete-access-review/retrieve-results.png)

Om du vill visa förloppet för en aktiv åtkomstgranskning som är återkommande klickar du på sidan **Resultat.**

Om du vill visa resultatet av en slutförd instans av en åtkomstgranskning som är återkommande klickar du på **Granskningshistorik**och väljer sedan den specifika instansen i listan över slutförda åtkomstgranskningsinstanser, baserat på instansens start- och slutdatum. Resultatet av den här instansen kan erhållas från sidan **Resultat.**

Om du vill hämta alla resultat av en åtkomstgranskning klickar du på knappen **Hämta.** Den resulterande CSV-filen kan visas i Excel eller i andra program som öppnar UTF-8-kodade CSV-filer.

## <a name="remove-users-from-an-access-review"></a>Ta bort användare från en åtkomstgranskning

 Som standard är en användare som tas bort borttagen i Azure AD i 30 dagar. Under den här perioden kan användaren återställas av en administratör om det behövs.  Efter 30 dagar tas användaren bort permanent.  Dessutom kan en global administratör, med hjälp av Azure Active Directory-portalen, uttryckligen [permanent ta bort en nyligen borttagen användare](../fundamentals/active-directory-users-restore.md) innan den tidsperioden har uppnåtts.  När en användare har tagits bort permanent tas efterföljande data om den användaren bort från aktiva åtkomstgranskningar.  Granskningsinformation om borttagna användare finns kvar i spårningsloggen.

## <a name="next-steps"></a>Nästa steg

- [Hantera användare med Azure AD-åtkomstgranskningar](manage-user-access-with-access-reviews.md)
- [Hantera gäståtkomst med Azure AD-åtkomstgranskningar](manage-guest-access-with-access-reviews.md)
- [Skapa en åtkomstgranskning av grupper eller program](create-access-review.md)
- [Skapa en åtkomstgranskning av användarna i en administrativ roll i Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
