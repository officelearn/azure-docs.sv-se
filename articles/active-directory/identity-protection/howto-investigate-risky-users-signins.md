---
title: Hur du undersöker riskabla användare och inloggningar i Azure Active Directory identity protection (uppdateras) | Microsoft Docs
description: Lär dig hur du undersöker riskabla användare och inloggningar i Azure Active Directory identity protection (uppdateras).
services: active-directory
keywords: Azure active directory identity protection kan cloud app discovery, hantering av program, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprincip
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.author: markvi
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.date: 01/25/2019
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.reviewer: raluthra
ms.openlocfilehash: f72819bacd7c65ced7ccff3665789d3b96c5e30f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478380"
---
# <a name="how-to-investigate-risky-users-and-sign-ins"></a>Instruktioner: Undersöka riskfyllda användare och inloggningar 


Använda rapporter för riskfyllda inloggningar och riskfylld användare du undersöka och få insyn i risker i din miljö. Möjlighet att filtrera och sortera riskfyllda inloggningar och användare, kan du bättre förstår potentiella intrång i din organisation. 


## <a name="risky-users-report"></a>Rapport om riskanvändare

Med den information som tillhandahålls av rapporten över riskfyllda användare, kan du hitta svar på frågor som:

- Vilka användare som är hög risk?
- Vilka användare som har statusen riskerna åtgärdas?



Din startpunkt för den här rapporten är den **Undersök** avsnitt på sidan.

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/01.png)


Rapporten över riskfyllda användare har en standard-vy som visar:

- Namn

- Riskstatus

- Risknivå

- Riskinformation

- Risk senast uppdaterad

- Type

- Status
 

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/03.png)


Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/04.png)

Dialogrutan kolumner kan du visa ytterligare fält eller ta bort fält som redan visas.

När du klickar på ett objekt i listvyn visas all tillgänglig information om det i en horisontell vy.

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/05.png)


Visar Informationsvyn:

- Grundläggande information

- Senaste riskfyllda inloggningar

- Riskhändelser är inte länkade till någon inloggning

- Riskhistorik



Dessutom kan du:

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/08.png)

- Visa alla inloggningar genväg om du vill visa rapporten inloggningar för den användaren.

- Visa alla riskfyllda inloggningar för att visa alla inloggningar för den användaren som har flaggats som riskfyllt.

- Återställa en användares lösenord om du tror att användarens identitet har komprometterats.

- Ignorera användarrisk om du tror att de aktiva riskhändelser för en användare är falska positiva identifieringar. Mer information finns i [hur vi kan förbättra identifieringen](howto-improve-detection-accuracy.md).



### <a name="filter-risky-users"></a>Filtrera riskfylld användare

Om du vill begränsa data som rapporteras till en nivå som passar dig kan filtrera du riskfylld användardata med hjälp av följande standardfält:

- Namn

- Användarnamn

- Riskstatus

- Risknivå

- Type

- Status

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/06.png)



Den **namn** filter kan du ange namn eller användarens huvudnamn (UPN) för den användare som intresserar dig.


Den **riskerar tillstånd** filter kan du välja:

- I farozonen
- Åtgärdad
- Avvisad


Den **risknivå** filter kan du välja:

- Hög
- Medel
- Låg


Den **typ** filter kan du välja:

- Gäst
- Medlem

Den **Status** filter kan du välja:

- Borttagen
- Active


### <a name="download-risky-users-data"></a>Ladda ned riskabla användare

Du kan hämta data riskabla användare om du vill arbeta med den utanför Azure-portalen. Klicka på ladda ned skapar en CSV-fil för de senaste 5K posterna. 

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/07.png)


Du kan anpassa listvyn genom att klicka på kolumner i verktygsfältet.
 
På så sätt kan du visa ytterligare fält eller ta bort fält som redan visas.
 
Mer information om en riskfylld användare klicka på information om lådan så att det expanderas

 



## <a name="risky-sign-ins-report"></a>Rapport över riskfyllda inloggningar

Med den information som tillhandahålls av rapporten över riskfyllda inloggningar, kan du hitta svar på frågor som:

- Hur många genomförda inloggningar har det som hade riskhändelser för anonyma IP-adress under den senaste veckan?

- Vilka användare har bekräftats komprometteras i den senaste månaden?

- Vilka användare som hade riskfyllda inloggningar till Office 365-portalen?




Din startpunkt för den här rapporten är den **Undersök** avsnitt på sidan.

![Rapport över riskfyllda inloggningar](./media/howto-investigate-risky-users-signins/02.png)

Rapporten över riskfyllda inloggningar har en standard-vy som visar:

- Date

- Användare

- Program

- Inloggningsstatus

- Riskstatus

- Risknivå (aggregering)

- Risknivå (realtid)

- Villkorlig åtkomst

- MFA krävs  
 

![Rapport över riskfyllda inloggningar](./media/howto-investigate-risky-users-signins/09.png)


Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/11.png)

Dialogrutan kolumner kan du visa ytterligare fält eller ta bort fält som redan visas.

När du klickar på ett objekt i listvyn visas all tillgänglig information om det i en horisontell vy.

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/12.png)


Visar Informationsvyn:

- Grundläggande information

- Enhetsinformation

- Riskinformation

- MFA-information

- Villkorlig åtkomst





Dessutom kan du:

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/13.png)

- Bekräfta komprometterade 

- Bekräfta säker

Mer information finns i [hur vi kan förbättra identifieringen](howto-improve-detection-accuracy.md).




### <a name="filter-risky-sign-ins"></a>Filtrera riskfyllda inloggningar

Om du vill begränsa data som rapporteras till en nivå som passar dig kan filtrera du riskfylld användardata med hjälp av följande standardfält:

- Användare
- Program
- Inloggningsstatus
- Riskstatus
- Risknivå (aggregering)
- Risknivå (realtid)
- Villkorlig åtkomst
- Date
- På typen av risk

![Rapport över riskfyllda inloggningar](./media/howto-investigate-risky-users-signins/14.png)



Den **namn** filter kan du ange namn eller användarens huvudnamn (UPN) för den användare som intresserar dig.

Den **program** filter kan du ange användaren försökte komma åt molnappen.

Med filtret **Inloggningsstatus** kan du välja något av följande filter:

- Alla
- Lyckades
- Fel


Den **riskerar tillstånd** filter kan du välja:

- I farozonen
- Bekräftat komprometterad
- Bekräftat säker
- Avvisad
- Åtgärdad


Den **risknivå (aggregering)** filter kan du välja:

- Hög
- Medel
- Låg

Den **risknivå (realtid)** filter kan du välja:

- Hög
- Medel
- Låg


Den **villkorlig åtkomst** filter kan du välja:

- Alla
- Används inte
- Lyckades
- Fel


Med filtret **Datum** kan du definiera en tidsram för de data som returneras.
Möjliga värden:

- Senaste månaden
- Senaste 7 dagarna
- De senaste 24 timmarna
- Anpassat tidsintervall





### <a name="download-risky-sign-ins-data"></a>Ladda ned riskfyllda inloggningar

Om du vill arbeta med den utanför Azure-portalen kan du hämta data för riskfyllda inloggningar. Klicka på ladda ned skapar en CSV-fil för de senaste 5K posterna. 

![Rapport om riskanvändare](./media/howto-investigate-risky-users-signins/15.png)



## <a name="next-steps"></a>Nästa steg

För att få en översikt över Azure AD Identity Protection kan se den [översikt över Azure AD Identity Protection](overview-v2.md).
