---
title: Publicera Power BI-apperbjudande | Azure Marketplace
description: Publicera ett Power BI-apperbjudande på Microsoft AppSource-marknadsplatsen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: a5eb5b652f7a419c6c2d1b3e6880c3c3003fb679
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985532"
---
# <a name="publish-a-power-bi-app-offer"></a>Publicera ett Power BI-apperbjudande

>[!Important]
>Från och med den 13 april 2020 börjar vi flytta hanteringen av dina Power BI-appar till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [översikten över hur du skapar Power BI-appar](https://aka.ms/AzureCreatePBIServiceApp) för att hantera dina migrerade erbjudanden.

Det sista steget, när du har definierat ett erbjudande i Cloud Partner Portal och skapat tillhörande tekniska resurser, är att skicka erbjudandet för publicering. Om du vill starta den här processen väljer du **Publicera**i den vänstra rutan i fönstret **Nytt erbjudande** . Mer information finns i [Publicera Azure Marketplace och AppSource-erbjudanden](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Publiceringssteg

Dessa är de viktigaste stegen i publiceringsprocessen:

![Publiceringsprocesssteg för Power BI App-erbjudande](./media/publishing-process-steps.png)

Den här tabellen beskriver varje steg och ger sin beräknade slutförandetid:

|   Publiceringssteg            |   Tid     |   Beskrivning                                                                  |
| --------------------         |------------| ----------------                                                               |
| Validera förutsättningar       | 15 minuter     | Erbjudandeinformation och erbjudandeinställningar valideras.                            |
| Certifiering                | 1-7 dagar   | Power BI-certifieringsteamet analyserar ditt erbjudande. Teamet kör din Power BI-app genom ett manuellt verifieringstest genom att installera appen via den angivna installationsadressen. Primära valideringar utförs som en del av appcertifieringsprocessen (beskrivs senare i det här dokumentet).         |
| Paketering                    | \<1 timme  | Erbjudandets tekniska tillgångar är förpackade för kundbruk.                        |
| Registrering av leadgenerering | \<1 timme  | Leadsystem konfigureras och distribueras.                                      |
| Signering av utgivare            | \-         | Du slutför en slutlig recension och bekräftelse innan erbjudandet går live. Nu har du också en länk för att förhandsgranska ditt erbjudande. När du är nöjd med hur förhandsgranskningen ser ut väljer du **Gå live** på fliken **Status.** Detta skickar en begäran till introduktionsteamet om att lista din app på AppSource.    |
| Live                         | \<3 timmar | Ditt erbjudande är nu offentligt listat (live) på AppSource och kunder kan visa din app och distribuera den i sina Power BI-prenumerationer. Du får också en bekräftelse via e-post. I den högra kolumnen på fliken **Alla erbjudanden** kan du se status för alla dina erbjudanden. På fliken **Status** kan du se den detaljerade publiceringsflödesstatusen för ditt erbjudande. |
|   |   |

Tillåt upp till åtta dagar för den här processen att slutföra. När du har gått igenom de här publiceringsstegen visas ditt Power BI-apperbjudande i avsnittet [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI-appar.


### <a name="app-certification-process"></a>Processen för appcertifiering

Microsofts introduktionsteam använder den här processen för att validera inlämning av ditt Power BI-apperbjudande:

1. Granska juridiska dokument och hjälplänkar.
2. Validera kontaktinformation för support.
3. Använd installations-URL:en för att verifiera korrekt installation.
4. Sök igenom appen efter skadlig kod och annat skadligt innehåll.
5. Kontrollera att det visade innehållet matchar appens beskrivning.
6. Kontrollera att apprelaterade åtgärder fungerar som förväntat i Power BI. Teamet öppnar rapporter och instrumentpaneler med exempeldata, ansluter till anpassade datakällor, uppdaterar data och så vidare.

Certifieringsteamet ger feedback om de hittar några problem.  Mer information om Power BI-appkrav finns i Dokumentationen till [Power BI-appen](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att du regelbundet övervakar din app på [AppSource marketplace](https://appsource.microsoft.com).  Du bör också använda [funktionen Säljarstatistik](../../cloud-partner-portal-orig/si-getting-started.md) i [Cloud Partner Portal](https://cloudpartner.azure.com/#insights) för att få insikter om dina marknadsplatskunder och appanvändning. Slutligen kan du [uppdatera ditt erbjudande](./cpp-update-existing-offer.md).
