---
title: Rapportaviseringar i Azure Active Directory
description: "Hur du använder Azure Active Directory reporting meddelanden för misstänkt tecken moduler."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: ae6d4b0e-5931-4cb3-98bf-9be91b381c92
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: dhanyahk;markvi
ms.custom: oldportal
ms.reviewer: dhanyahk
ms.openlocfilehash: 99783eebb76363ca3fa96c6777906239f3de1131
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="azure-active-directory-reporting-notifications"></a>Rapportaviseringar i Azure Active Directory
## <a name="what-reports-generate-email-notifications"></a>Vilka rapporter Generera e-postaviseringar
E-postmeddelanden för tillfället endast oregelbundna inloggning aktivitet rapporten utlösare.

## <a name="what-is-an-irregular-sign-in"></a>Vad är ett ”oregelbundna inloggning”?
Oregelbundna inloggningar är de som har identifierats av våra maskininlärningsalgoritmer på grundval av ett ”omöjligt att resa” villkor som kombineras med ett avvikande inloggning plats och en enhet. Detta kan tyda på att en hackare har försökt logga in med det här kontot.

## <a name="who-receives-the-email-notifications"></a>Vem som får e-postmeddelanden?
E-postmeddelandet skickas till alla globala administratörer som har tilldelats en Active Directory Premium-licens. För att säkerställa att den levereras, skicka vi den till administratörer alternativ e-postadress samt. Administratörer bör innehålla aad-alerts-noreply@mail.windowsazure.com i deras betrodda avsändare så att de inte missar e-postmeddelandet.

## <a name="how-often-are-these-emails-sent"></a>Hur ofta uppdateras dessa e-postmeddelanden skickas?
E-postmeddelandet skickas om 10 nya oregelbundna inloggning aktiviteter som inträffar under de senaste 30 dagarna eller eftersom det sista meddelandet skickades, beroende på vilket som är mindre.

## <a name="how-do-i-access-the-report-mentioned-in-the-email"></a>Hur kommer jag åt rapporten som anges i e-postmeddelandet?
När du klickar på länken omdirigeras till sidan i Azure-portalen. Du måste vara både för att komma åt rapporten:

* En administratör eller medadministratör för Azure-prenumerationen
* En global administratör i katalogen, och tilldelas en Active Directory Premium-licens. Mer information finns i [Azure Active Directory-versioner](active-directory-editions.md).

## <a name="can-i-turn-off-these-emails"></a>Kan jag inaktivera dessa e-postmeddelanden?
Ja, om du vill inaktivera aviseringar som rör avvikande inloggningar i Azure-portalen klickar du på **konfigurera**, och välj sedan **inaktiverad** under den **meddelanden** avsnitt.

## <a name="whats-next"></a>Nästa steg
* Är du nyfiken på vilka säkerhets-, gransknings- och aktivitet rapporter är tillgängliga? Checka ut [Azure AD-säkerhetsgrupp, granskning och aktivitetsrapporter](active-directory-view-access-usage-reports.md)
* [Komma igång med Azure Active Directory Premium](active-directory-get-started-premium.md)
* [Anpassa inloggnings- och åtkomstpanelsidorna till ditt företag](customize-branding.md)

