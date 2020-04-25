---
title: Publicera erbjudande för Power BI app | Azure Marketplace
description: Publicera ett erbjudande för Power BI app på Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: eb31520c81a4064edbe54a0256b694c4ad88fb49
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141701"
---
# <a name="publish-a-power-bi-app-offer"></a>Publicera ett erbjudande för Power BI app

>[!Important]
>Från och med 13 april 2020 börjar vi flytta hanteringen av dina Power BI app-erbjudanden till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ anvisningarna i [Power BI översikt över skapande av appar](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) för att hantera dina migrerade erbjudanden.

Det sista steget, när du har definierat ett erbjudande i Cloud Partner Portal och skapat tillhör ande tekniska till gångar, är att skicka in erbjudandet om publicering. Starta den här processen genom att välja **publicera**i den vänstra rutan i fönstret **nytt erbjudande** . Mer information finns i [publicera Azure Marketplace-och AppSource-erbjudanden](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Publicerings steg

Dessa är de viktigaste stegen i publicerings processen:

![Publicerings process steg för Power BI app-erbjudande](./media/publishing-process-steps.png)

I den här tabellen beskrivs varje steg och den uppskattade slut tiden visas:

|   Publicerings steg            |   Tid     |   Beskrivning                                                                  |
| --------------------         |------------| ----------------                                                               |
| Verifiera krav       | 15 minuter     | Information om erbjudandet och erbjudande inställningarna verifieras.                            |
| Certifiering                | 1-7 dagar   | Den Power BI certifierings gruppen analyserar ditt erbjudande. Teamet kör din Power BI-app via ett manuellt verifierings test genom att installera appen via den angivna installations-URL: en. Primära verifieringar utförs som en del av appens certifierings process (beskrivs senare i det här dokumentet).         |
| Paketering                    | \<1 timme  | Erbjudandets tekniska till gångar är paketerade för kund användning.                        |
| Registrering av generering av lead | \<1 timme  | Lead-system konfigureras och distribueras.                                      |
| Publisher-signering            | \-         | Du har slutfört en slutgiltig granskning och bekräftelse innan erbjudandet går live. Nu har du också en länk för att förhandsgranska ditt erbjudande. När du är nöjd med hur din förhands granskning ser ut väljer du **Go Live** på fliken **status** . Detta skickar en begäran till onboarding-teamet för att lista din app på AppSource.    |
| Live                         | \<3 timmar | Ditt erbjudande visas nu offentligt ("Live") på AppSource, och kunderna kan visa din app och distribuera den i sina Power BI prenumerationer. Du får också ett e-postmeddelande om bekräftelse. I den högra kolumnen på fliken **alla erbjudanden** kan du se statusen för alla dina erbjudanden. På fliken **status** kan du se den detaljerade statusen för publicerings flödet för ditt erbjudande. |
|   |   |

Tillåt att den här processen slutförs på upp till åtta dagar. När du har gått igenom dessa publicerings steg visas ditt Power BI app-erbjudande i avsnittet [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI Apps.


### <a name="app-certification-process"></a>Certifierings process för appar

Microsoft onboarding-teamet använder den här processen för att verifiera att din Power BI app erbjudandet skickas:

1. Granska juridiska dokument och hjälp länkar.
2. Verifiera support kontakt information.
3. Använd installations-URL: en för att verifiera korrekt installation.
4. Genomsök appen efter skadlig kod och annat skadligt innehåll.
5. Kontrol lera att det innehåll som visas matchar appens beskrivning.
6. Kontrol lera att app-relaterade åtgärder fungerar som förväntat i Power BI. Teamet öppnar rapporter och instrument paneler med exempel data, ansluter till anpassade data källor, uppdaterar data och så vidare.

Certifierings teamet ger feedback om de hittar några problem.  Mer information om krav för Power BI appar finns i [dokumentationen för Power BI app](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att du regelbundet övervakar din app på [AppSource Marketplace](https://appsource.microsoft.com).  Du bör också använda funktionen för [säljar insikter](../../cloud-partner-portal-orig/si-getting-started.md) i [Cloud Partner Portal](https://cloudpartner.azure.com/#insights) för att få insikter om dina Marketplace-kunder och app-användning. Slutligen kan du [Uppdatera ditt erbjudande](./cpp-update-existing-offer.md).
