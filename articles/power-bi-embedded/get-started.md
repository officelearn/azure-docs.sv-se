---
title: Komma igång med Microsoft Power BI Embedded | Microsoft Docs
description: Power BI Embedded i Business Intelligence-appar
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/11/2018
ms.author: maghan
ms.openlocfilehash: 7a860742192227e92a14d9bb5622dc9b61f6cb99
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716031"
---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>Komma igång med Microsoft Power BI Embedded

**Power BI Embedded** ger oberoende programvaruleverantörer (ISV) och utvecklare ett sätt att snabbt lägga till fantastiska visuella objekt, rapporter och instrumentpaneler i sina program via en kapacitetsbaserad modell med timförbrukning.

![Diagram över flöde för inbäddning](media/get-started/introduction.png)

Power BI Embedded har fördelar för en ISV, dennes utvecklare och kunder. En ISV kan till exempel börja skapa visuella objekt utan kostnad med Power BI Desktop. Programvaruleverantörer kan få en kortare tid till marknad genom att minimera arbetet med visuell analytisk utveckling och skilja sig från konkurrenterna med differentierade dataupplevelser. ISV:erna kan även välja att debitera en avgift för det ytterligare värde som skapas med inbäddad analys.

Utvecklarna kan ägna tid åt att fokusera på att skapa kärnkompetens för programmet, snarare än att ägna tid åt att utveckla visuella objekt och analyser. Utvecklarna kan snabbt uppfylla kundens krav på rapporter och instrumentpanel och de kan enkelt bäddas in med fullständigt dokumenterade API:er och SDK:er. Genom att aktivera lättnavigerad datautforskning i apparna kan ISV:erna slutligen göra det möjligt för kunderna att fatta snabba datadrivna beslut i ett sammanhang och på ett tryggt sätt från valfri enhet.

## <a name="register-an-application-within-azure-active-directory"></a>Registrera ett program i Azure Active Directory

Ett registrerat program i Azure Active Directory (AAD) krävs för att bäddas in i ett anpassat program. Det registrerade programmet kräver att klienten är en Power BI-klient. En Power BI-klient innebär att minst en användare i organisationen har registrerat sig för Power BI. När en användare är registrerad för Power BI så kan Power BI API:erna visas i det registrerade programmet.

Mer information om att registrera ett program i AAD finns i [Register an Azure AD app to embed Power BI content](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/) (Registrera en Azure AD-app för att bädda in Power BI-innehåll).

## <a name="embed-content-in-your-application"></a>Bädda in innehåll i programmet

När du har registrerat programmet i AAD kan du bädda in Power BI-innehåll i programmet. Bädda in innehåll med hjälp av REST API tillsammans med JavaScript API:er.

Vi har några exempel som hjälper dig att komma igång. En genomgång av exemplet finns i [Integrate a dashboard, tile, or report into your application](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/) (Integrera en instrumentpanel, panel eller rapport i programmet).

## <a name="get-capacity-and-move-to-production"></a>Hämta kapacitet och gå till produktion

Skapa kapacitet för Power BI Embedded i Microsoft Azure för att flytta programmet till produktion. Information om hur du skapar kapacitet finns i [Create Power BI Embedded capacity in the Azure portal](create-capacity.md) (Skapa Power BI Embedded-kapacitet på Azure Portal).

> [!IMPORTANT]
> Eftersom inbäddade token enbart är avsedda för utvecklingstestning är antalet inbäddade token som ett Power BI-huvudkonto kan generera begränsat. En [kapacitet måste köpas](https://docs.microsoft.com/power-bi/developer/embedded-faq#technical) för produktionens inbäddningsscenarier. Det finns ingen gräns för att skapa en inbäddningstoken när en dedikerad kapacitet köps. Gå till [Hämta tillgängliga funktioner](https://msdn.microsoft.com/en-us/library/mt846473.aspx) och kontrollera den aktuella procentandelen inbäddad användning just nu.

Hantera kapaciteten i administrationsportalen för Power BI. Tilldela en administratör för arbetsytan för att hjälpa dig med appens arbetsytor. Mer information finns i [Manage capacities within Power BI Premium and Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/) (Hantera kapacitet i Power BI Premium och Power BI Embedded).

## <a name="next-steps"></a>Nästa steg

Om du är redo att skapa kapacitet för Power BI Embedded kan du läsa [Create Power BI Embedded capacity in the Azure portal](create-capacity.md) (Skapa Power BI Embedded-kapacitet på Azure Portal).

Om du vill ha en exempelgenomgång, kan du läsa [Integrera en instrumentpanel, panel eller rapport i ditt program](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/).

Fler frågor? [Försök med Power BI Community](http://community.powerbi.com/)
