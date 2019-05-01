---
title: Publicera erbjudande för Power BI-appen | Azure Marketplace
description: Publicera ett erbjudande med Power BI-appen på Microsoft AppSource-marknadsplatsen.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pabutler
ms.openlocfilehash: aae23feaf1cc5887de061414af985ef16070546b
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943182"
---
# <a name="publish-a-power-bi-app-offer"></a>Publicera ett erbjudande med Power BI-appen

Det sista steget är när du har definierat ett erbjudande i Cloud Partner Portal och skapade de associerade tekniska resurser att skicka erbjudande för publicering. Starta den här processen i den vänstra rutan i den **nytt erbjudande** väljer **publicera**. Mer information finns i [publicera Azure Marketplace och AppSource erbjudanden](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Publicera steg

Dessa är de viktigaste anvisningarna i publiceringsprocessen:

![Publicera processtegen för Power BI-appen erbjuder](./media/publishing-process-steps.png)

Den här tabellen beskriver varje steg och innehåller dess uppskattad tid:

|   Publiceringen            |   Tid     |   Beskrivning                                                                  |
| --------------------         |------------| ----------------                                                               |
| Verifiera förutsättningar       | 15 minuter     | Ger information och erbjuder inställningarna verifieras.                            |
| Certifiering                | 1-7 dagar   | Power BI-certifiering-teamet analyserar ditt erbjudande. Teamet körs Power BI-appen genom en manuell verifiering testning genom att installera appen via den angivna installation URL: en. Primär verifieringar utförs som en del av certifieringsprocessen app (beskrivs senare i det här dokumentet).         |
| Paketering                    | \< 1 timme  | Erbjudandets tekniska resurser paketeras för kundens användning.                        |
| Leda generation registrering | \< 1 timme  | Lead system konfigureras och distribueras.                                      |
| Publisher-signering            | \-         | Du har slutfört en slutgiltig granskning och en bekräftelse innan erbjudandet lanseras. Nu också har du en länk du förhandsgranskar ditt erbjudande. När du är nöjd med hur ser ut i förhandsgranskningen väljer **Go Live** på den **Status** fliken. Detta skickar en begäran till onboarding-teamet att publicera din app på AppSource.    |
| Live                         | \< tre timmar | Ditt erbjudande visas offentligt nu (”live”) på AppSource och kunder kan visa din app och distribuera den på sina Power BI-prenumerationer. Du får även ett e-postmeddelande med bekräftelse. I den högra kolumnen på den **alla erbjudanden** fliken visas status för alla dina erbjudanden. På den **Status** och du kan se detaljerad flow publiceringsstatus för ditt erbjudande. |
|   |   |

Tillåt upp till åtta dagar för den här processen skulle slutföras. När du går igenom stegen publishing erbjudandet Power BI-appen visas i den [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) appavsnittet i Power BI.


### <a name="app-certification-process"></a>Apparnas certifieringsprocess

Microsoft onboarding-teamet använder den här processen för att verifiera ditt Power BI-appen erbjudandet bidrag:

1. Granska juridiska dokument och hjälp-länkar.
2. Verifiera kontaktinformation för support.
3. Använd installationsprogrammet för URL: en för att kontrollera att.
4. Sök igenom appen för skadlig kod och annan skadlig kod.
5. Kontrollera att det visade innehållet matchar appens beskrivning.
6. Kontrollera att app-relaterade åtgärder fungerar som förväntat i Power BI. Teamet öppnas rapporter och instrumentpaneler med exempeldata, ansluter till anpassade datakällor, uppdaterar data och så vidare.

Certification-teamet ger feedback om de hitta eventuella problem.  Mer information om krav för Power BI-appar finns i den [dokumentationen för Power BI-app](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att du regelbundet övervaka din app i den [AppSource-marknadsplatsen](https://appsource.microsoft.com).  Bör du även använda den [försäljning Insights](../../cloud-partner-portal-orig/si-getting-started.md) funktion i den [Cloud Partner Portal](https://cloudpartner.azure.com/#insights) att få insikter om dina marketplace för kunder och appanvändning. Slutligen kan du [uppdatera ditt erbjudande](./cpp-update-existing-offer.md).
