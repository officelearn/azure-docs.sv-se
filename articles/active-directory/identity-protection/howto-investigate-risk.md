---
title: Så här undersöker du risker i Azure Active Directory identitets skydd (uppdateras) | Microsoft Docs
description: Lär dig hur du undersöker riskfyllda användare, identifieringar och inloggningar i Azure Active Directory identitets skydd (uppdateras).
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
ms.openlocfilehash: a6e9715b63ccfbf1da3bdaedf947ea4cd65109b7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129723"
---
# <a name="how-to-investigate-risk"></a>Instruktioner: Undersök risker

Genom att använda riskfyllda inloggningar, riskfyllda användare och rapporter om risk identifiering kan du undersöka och få insikt i risker i din miljö. Med möjligheten att filtrera och sortera riskfyllda inloggningar, användare och identifieringar kan du bättre förstå potentiella intrång i din organisation. 

## <a name="risky-users-report"></a>Rapport om riskanvändare

Med den information som ges av rapporten riskfyllda användare kan du hitta svar på frågor som:

- Vilka användare är höga risker?
- Vilka användare har risk statusen åtgärdade?

Din första start punkt i den här rapporten är avsnittet **Undersök** på sidan säkerhet.

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/01.png)

Rapporten riskfyllda användare har en standardvy som visar:

- Name
- Riskstatus
- Risknivå
- Riskinformation
- Risk senast uppdaterad
- type
- State

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/03.png)

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/04.png)

I dialog rutan kolumner kan du Visa ytterligare fält eller ta bort fält som redan visas.

När du klickar på ett objekt i listvyn visas all tillgänglig information om det i en horisontell vy.

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/05.png)

Vyn information visar:

- Grundläggande information
- Senaste riskfyllda inloggningar
- Risk identifieringar som inte är länkade till en inloggning
- Riskhistorik

Dessutom kan du:

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/08.png)

- Visa alla inloggnings gen vägar för att Visa inloggnings rapporten för den användaren.
- Visa alla riskfyllda inloggningar för att visa alla inloggnings program för den användare som har flaggats som riskfylld.
- Återställ en användares lösen ord om du misstänker att användarens identitet har komprometterats.
- Ignorera användar risken om du tror att de aktiva risk identifieringarna för en användare är falska positiva identifieringar. Mer information finns i artikeln [ge feedback om risk identifieringar i Azure AD Identity Protection](howto-provide-risk-event-feedback.md).

### <a name="filter-risky-users"></a>Filtrera riskfyllda användare

Om du vill begränsa vilka data som rapporteras till en nivå som passar dig kan du filtrera riskfyllda användar data med hjälp av följande standard fält:

- Name
- Användarnamn
- Riskstatus
- Risknivå
- type
- State

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

- Hur många lyckade inloggningar fanns det där anonyma IP-adresser upptäcktes under den senaste veckan?
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

Mer information finns i artikeln [ge feedback om risk identifieringar i Azure AD Identity Protection](howto-provide-risk-event-feedback.md).

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


## <a name="risk-detections-report"></a>Rapport över risk identifieringar

Rapporten risk identifieringar ger dig insikt i varje identitets skydds risk i din klient organisation.


Rapporten om identifiering av risker har en standardvy som visar:

- Date

- Användare

- IP-adress

- Location

- Identifieringstyp

- Riskstatus

- Risknivå

- Fråge-ID
 

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet. I dialog rutan kolumner kan du Visa ytterligare fält eller ta bort fält som redan visas.

När du klickar på ett objekt i listvyn visas all tillgänglig information om det i en horisontell vy.


Vyn information visar ytterligare information om identifiering av risker.

Vyn information innehåller också länkar till

- Visa användarens risk rapport
- Visa användarens inloggnings program
- Visa användarens riskfyllda inloggningar
- Visa länkad riskfylld inloggning
- Visa användarens risk identifieringar


### <a name="filter-risk-detections"></a>Filtrera risk identifieringar

Om du vill begränsa vilka data som rapporteras till en nivå som passar dig kan du filtrera risk identifierings data med hjälp av följande standard fält:

- Identifieringstid
- Identifieringstyp
- Riskstatus
- Risknivå
- Användare
- Användarnamn
- IP-adress
- Location
- Identifierings tid
- Aktivitet 
- Source
- Typ av token utfärdare
- Fråge-ID
- Korrelations-ID


Med filtret för **identifierings tid** kan du definiera en tidsram för de data som returneras. Med det här filtret kan du välja:
- Senaste 90 dagarna
- Senaste 30 dagarna
- Senaste 7 dagarna
- De senaste 24 timmarna
- Anpassat tidsintervall

Med filtret **identifierings typ** kan du ange typen av risk identifiering (till exempel okända inloggnings egenskaper).

Filtret **risk tillstånd** gör att du kan välja:

- I farozonen
- Bekräftat komprometterad
- Bekräftat säker
- Avvisad
- Åtgärdad

Med filtret **risk nivå** kan du välja:

- Hög
- Medel
- Låg

Med **användar** filtret kan du ange namn eller objekt-ID för den användare som du bryr dig om.

Med filtret för **användar namn** kan du ange User Principal Name (UPN) för den användare som du bryr dig om.

Med filtret för **identifierings tids inställningar** kan du ange om identifieringen var i real tid eller offline. Obs! Du kan utmana inloggningar med identifieringar i real tid med hjälp av en princip för inloggnings risk. 

Med **aktivitets** filtret kan du ange om identifieringen var länkad till en inloggning (t. ex. anonym IP-adress) eller en användare (t. ex. läcka autentiseringsuppgifter).

Med **käll** filtret kan du ange källan för risk identifieringen (till exempel Azure AD eller Microsoft Cloud App Security). 

Filtret för token Issuer- **typ** gör att du kan ange var token utfärdades (till exempel Azure AD eller AD Federation Services)


### <a name="download-risk-detections-data"></a>Hämta data för risk identifiering

Du kan hämta risk identifierings data om du vill arbeta med dem utanför Azure Portal. Om du klickar på Hämta skapas en CSV-fil med de senaste 5 000 posterna. 

## <a name="next-steps"></a>Nästa steg

För att få en översikt över Azure AD Identity Protection, se [Översikt över Azure AD Identity Protection](overview-v2.md).
