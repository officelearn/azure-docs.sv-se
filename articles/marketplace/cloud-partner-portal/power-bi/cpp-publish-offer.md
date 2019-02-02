---
title: Publicera Power BI-appen erbjudande – Azure Marketplace | Microsoft Docs
description: Publicera ett erbjudande med Power BI-appen på Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 2b3783060cf5502076ce3bc98cf07f005366a9e2
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666718"
---
# <a name="publish-power-bi-app-offer"></a>Publicera erbjudande för Power BI-appen

Det sista steget är när du har definierat erbjudandet i portalen och skapade de associerade tekniska resurser att skicka erbjudande för publicering.  Starta den här processen, klicka på den **publicera** knappen på menyn lodrät i den **nytt erbjudande** fönster.  Mer information finns i [publicera Azure Marketplace och AppSource erbjudanden](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Publicera steg

Följande diagram visar huvudstegen inom publiceringsprocessen ”gå live”.

![Publicera processtegen för Power BI-App](./media/publishing-process-steps.png)

I följande tabell beskrivs de här stegen och ger en uppskattning för maximal tid för slutförande:

|   Publiceringen            |   Tid     |   Beskrivning                                                                  |
| --------------------         |------------| ----------------                                                               |
| Verifiera förutsättningar       | 15 min     | Ger information och erbjuder inställningarna verifieras.                            |
| Certifiering                | 1-7 dagar   | Power BI-certifiering-teamet analyserar ditt erbjudande. Vi kör Power BI-appen genom en manuell verifiering testning genom att installera appen via angivna installationen URL: en. Viktigaste verifieringar utförs som en del av certifieringsprocessen app; Se nedan.         |
| Packas                    | \< 1 timme  | Erbjudandets tekniska resurser paketeras för kundens användning.                        |
| Leda Generation registrering | \< 1 timme  | Lead system konfigureras och distribueras.                                      |
| Publisher-signering            | \-         | Sista publisher granska och bekräfta innan erbjudandet lanseras. Du får nu även en länk du förhandsgranskar ditt erbjudande. När du är nöjd med hur ser ut i förhandsgranskningen klickar du på den **Go Live** knappen i den **Status** fliken. Den här åtgärden skickar en begäran till onboarding-teamet att publicera din app på AppSource.    |
| Live                         | \< tre timmar | Ditt erbjudande visas offentligt nu (”live”) på AppSource och kunder kommer att kunna visa och distribuera din app i deras Power BI-prenumerationer. Du får även ett e-postmeddelande med bekräftelse. När som helst som du kan klicka på den **alla erbjudanden** fliken och se status för alla dina erbjudanden som visas på den högra kolumnen. Du kan klicka på den **Status** fliken för att se publiceringsstatus för flow i detalj för ditt erbjudande. |
|   |   |

Tillåt i upp till åtta dagar för den här processen skulle slutföras. När du går igenom stegen publishing erbjudandet Power BI-appen visas i den [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) appavsnittet i Power BI.


### <a name="app-certification-process"></a>Apparnas certifieringsprocess

Microsoft onboarding-teamet använder följande process för att verifiera ditt bidrag för Power BI-erbjudande:

1. Juridiska dokument och hjälper dig att länkar granskas.
2. Kontaktinformation för support har verifierats.
3. Installationsprogrammet URL: en används för att verifiera korrekt installation. 
4. App genomsöks efter skadlig kod och annan skadlig kod. 
5. Verifieringen utförs innehållet visas matchar appens beskrivning.
6. App-relaterade åtgärderna fungerar som förväntat i Power BI: öppna rapporter och instrumentpaneler med exempeldata, ansluta till anpassade datakällor, uppdatering, osv.

Certification-teamet ger feedback om de hitta eventuella problem.  Mer information om kraven för Power BI-appen finns i den [Power BI-appen dokumentation](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att du regelbundet övervaka din app i den [AppSource-marknadsplatsen](https://appsource.microsoft.com).  Dessutom bör du använda den [försäljning Insights](../../cloud-partner-portal-orig/si-getting-started.md) funktion i den [Cloud Partner Portal](https://cloudpartner.azure.com/#insights) att ge insikter om din marketplace för kunder och användning.  Du kan också utföra vissa [uppdateringar av ditt erbjudande](./cpp-update-existing-offer.md).
