---
title: Undersöka riskfyllda användare och inloggningar i Azure Active Directory identitets skydd (uppdateras) | Microsoft Docs
description: Lär dig hur du undersöker riskfyllda användare och inloggningar i Azure Active Directory identitets skydd (uppdateras).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c89658e962654f005eaee5ceff220d5fb343e86e
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370320"
---
# <a name="how-to-investigate-risky-users-and-sign-ins"></a>Instruktioner: Undersöka riskfyllda användare och inloggningar 

Med hjälp av rapporter om riskfyllda inloggningar och riskfyllda användare kan du undersöka och få insikt i risker i din miljö. Med möjligheten att filtrera och sortera riskfyllda inloggningar och användare kan du bättre förstå potentiella intrång i din organisation. 

## <a name="risky-users-report"></a>Rapport om riskanvändare

Med den information som ges av rapporten riskfyllda användare kan du hitta svar på frågor som:

- Vilka användare är höga risker?
- Vilka användare har risk statusen åtgärdade?

Din första start punkt i den här rapporten är avsnittet **Undersök** på sidan säkerhet.

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/01.png)

Rapporten riskfyllda användare har en standardvy som visar:

- Namn
- Riskstatus
- Risknivå
- Riskinformation
- Risk senast uppdaterad
- type
- Status

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/03.png)

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/04.png)

I dialog rutan kolumner kan du Visa ytterligare fält eller ta bort fält som redan visas.

När du klickar på ett objekt i listvyn visas all tillgänglig information om det i en horisontell vy.

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/05.png)

Vyn information visar:

- Grundläggande information
- Senaste riskfyllda inloggningar
- Riskhändelser är inte länkade till någon inloggning
- Riskhistorik

Dessutom kan du:

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/08.png)

- Visa alla inloggnings gen vägar för att Visa inloggnings rapporten för den användaren.
- Visa alla riskfyllda inloggningar för att visa alla inloggnings program för den användare som har flaggats som riskfylld.
- Återställ en användares lösen ord om du misstänker att användarens identitet har komprometterats.
- Ignorera användar risken om du tror att de aktiva risk händelserna för en användare är falska positiva identifieringar. Mer information finns i artikeln [ge feedback om risk händelser i Azure AD Identity Protection](howto-provide-risk-event-feedback.md).

### <a name="filter-risky-users"></a>Filtrera riskfyllda användare

Om du vill begränsa vilka data som rapporteras till en nivå som passar dig kan du filtrera riskfyllda användar data med hjälp av följande standard fält:

- Namn
- Användarnamn
- Riskstatus
- Risknivå
- type
- Status

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/06.png)

Med filtret **namn** kan du ange namnet eller User Principal Name (UPN) för den användare som du bryr dig om.

Filtret **risk tillstånd** gör att du kan välja:

- I farozonen
- Åtgärdad
- Avvisad

Med filtret **risk nivå** kan du välja:

- Hög
- Medel
- Låg

Med filtret **typ** kan du välja:

- Gäst
- Medlem

Med **status** filtret kan du välja:

- Borttagen
- Aktiv

### <a name="download-risky-users-data"></a>Hämta riskfyllda användares data

Du kan hämta risk användarens data om du vill arbeta med dem utanför Azure Portal. Om du klickar på Hämta skapas en CSV-fil med de senaste 2 500 posterna. 

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/07.png)

Du kan anpassa List visningen genom att klicka på kolumner i verktygsfältet.
 
På så sätt kan du visa ytterligare fält eller ta bort fält som redan visas.
 
Om du vill veta mer om en riskfylld användare klickar du på informations lådan för att expandera den

## <a name="risky-sign-ins-report"></a>Rapport över riskfyllda inloggningar

Med informationen i rapporten om riskfyllda inloggningar kan du hitta svar på frågor som:

- Hur många lyckade inloggningar fanns det där anonyma händelser för IP-risker hade varit under den senaste veckan?
- Vilka användare har bekräftat komprometterats under den senaste månaden?
- Vilka användare hade riskfyllda inloggningar till Office 365-portalen?

Din första start punkt i den här rapporten är avsnittet **Undersök** på sidan säkerhet.

![Rapport över riskfyllda inloggningar](./media/howto-investigate-risky-users-signins/02.png)

Rapporten över riskfyllda inloggningar har en standardvy som visar:

- Date
- Användare
- Program
- Inloggningsstatus
- Riskstatus
- Risk nivå (agg regering)
- Risk nivå (real tid)
- Villkorad åtkomst
- MFA krävs  

![Rapport över riskfyllda inloggningar](./media/howto-investigate-risky-users-signins/09.png)

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/11.png)

I dialog rutan kolumner kan du Visa ytterligare fält eller ta bort fält som redan visas.

När du klickar på ett objekt i listvyn visas all tillgänglig information om det i en horisontell vy.

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/12.png)

Vyn information visar:

- Grundläggande information
- Enhetsinformation
- Riskinformation
- MFA-information
- Villkorad åtkomst

Dessutom kan du:

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/13.png)

- Bekräfta kompromettering 
- Bekräfta säker

Mer information finns i artikeln [ge feedback om risk händelser i Azure AD Identity Protection](howto-provide-risk-event-feedback.md).

### <a name="filter-risky-sign-ins"></a>Filtrera riskfyllda inloggningar

Om du vill begränsa vilka data som rapporteras till en nivå som passar dig kan du filtrera riskfyllda användar data med hjälp av följande standard fält:

- Användare
- Program
- Inloggningsstatus
- Riskstatus
- Risk nivå (agg regering)
- Risk nivå (real tid)
- Villkorad åtkomst
- Date
- Typ av risk nivå

![Rapport över riskfyllda inloggningar](./media/howto-investigate-risky-users-signins/14.png)

Med filtret **namn** kan du ange namnet eller User Principal Name (UPN) för den användare som du bryr dig om.

Med **program** filtret kan du ange vilken molnbaserad app användaren försökte få åtkomst till.

Med filtret **Inloggningsstatus** kan du välja något av följande filter:

- Alla
- Lyckades
- Fel

Filtret **risk tillstånd** gör att du kan välja:

- I farozonen
- Bekräftat komprometterad
- Bekräftat säker
- Avvisad
- Åtgärdad

Med filtret **risk nivå (mängd)** kan du välja:

- Hög
- Medel
- Låg

Med filtret **risk nivå (i real tid)** kan du välja:

- Hög
- Medel
- Låg

Med filtret för **villkorlig åtkomst** kan du välja:

- Alla
- Används inte
- Klart
- Fel

Med filtret **Datum** kan du definiera en tidsram för de data som returneras.
Möjliga värden är:

- Senaste månaden
- Senaste 7 dagarna
- De senaste 24 timmarna
- Anpassat tidsintervall

### <a name="download-risky-sign-ins-data"></a>Hämta information om riskfyllda inloggningar

Du kan hämta de riskfyllda inloggnings data om du vill arbeta med dem utanför Azure Portal. Om du klickar på Hämta skapas en CSV-fil med de senaste 2 500 posterna. 

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/15.png)

## <a name="next-steps"></a>Nästa steg

För att få en översikt över Azure AD Identity Protection, se [Översikt över Azure AD Identity Protection](overview-v2.md).
