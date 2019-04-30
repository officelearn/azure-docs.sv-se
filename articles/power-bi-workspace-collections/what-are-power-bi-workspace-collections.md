---
title: Vad är Power BI-arbetsytesamlingar?
description: Power BI Embedded kan du integrera Power BI-rapporter i dina webb- och mobilprogram så du inte behöver skapa anpassade lösningar.
services: power-bi-embedded
author: rkarlin
ROBOTS: NOINDEX
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: rkarlin
ms.openlocfilehash: 7f23856363b337a361f329ed54e2152842faf26e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118950"
---
# <a name="what-are-power-bi-workspace-collections"></a>Vad är Power BI-arbetsytesamlingar?

Med **Power BI-Arbetsytesamlingar**, kan du integrera Power BI-rapporter direkt i dina webb- och mobilprogram.

![Diagram för program](media/what-are-power-bi-workspace-collections/what-is.png)

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Power BI-Arbetsytesamlingar är en **Azure-tjänsten** som gör att ISV: er och apputvecklare till surface Power BI-dataupplevelser i sina program. Du har skapat program som utvecklare och program har sina egna användare och en specifik uppsättning funktioner. Dessa appar kan också inträffa att ha några inbyggda element som diagram och rapporter som kan nu tillhandahålls av Microsoft Power BI-Arbetsytesamlingar. Du behöver inte en Power BI-konto du använder din app. Du kan fortsätta att logga in på ditt program precis som tidigare, och visa och interagera med Power BI rapporteringsfunktioner utan några ytterligare licenser.

## <a name="licensing-for-microsoft-power-bi-workspace-collections"></a>Licensiering för Microsoft Power BI-Arbetsytesamlingar

I den **Microsoft Power BI-Arbetsytesamlingar** användningsmodell licensiering för Power BI inte är ansvar för användaren.  I stället **sessioner** köps av utvecklaren av appen som förbrukar de visuella objekten och debiteras till prenumerationen som äger de här resurserna. 

## <a name="microsoft-power-bi-workspace-collections-conceptual-model"></a>Microsoft Power BI-Arbetsytesamlingar konceptuell modell

![Programflödet med samlingar med arbetsytor](media/what-are-power-bi-workspace-collections/model.png)

Som någon annan tjänst i Azure-resurser för Power BI-Arbetsytesamlingar tillhandahålls via de [Azure Resource Manager API: er](https://msdn.microsoft.com/library/mt712306.aspx). I det här fallet är den resurs som är etablera en **Power BI-Arbetsytesamling**.

## <a name="workspace-collection"></a>Arbetsytesamling

En **Arbetsytesamling** är den högsta Azure behållare för resurser som innehåller 0 eller fler **arbetsytor**.  En **arbetsytan** **samling** har alla standardegenskaper för Azure, samt följande:

* **Åtkomstnycklar** – nycklar som används när anropas på ett säkert sätt i Power BI-API: er (beskrivs i ett senare avsnitt).
* **Användare** – Azure Active Directory (AAD)-användare som har administratörsbehörighet för att hantera Power BI-Arbetsytesamling via Azure portal eller Azure Resource Manager API.
* **Region** – som en del för att etablera en **Arbetsytesamling**, du kan välja en region som ska etableras i. Mer information finns i [Azure-regionerna](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Arbetsyta

En **arbetsytan** är en behållare för Power BI innehåll, vilket kan omfatta datauppsättningar och rapporter. En **arbetsytan** är tomt när först skapas. Du ska skapa innehåll med Power BI Desktop och du kommer att distribuera PBIX genom programmering till din arbetsyta med hjälp av den [Power BI Import API](https://msdn.microsoft.com/library/mt711504.aspx). Du kan också programmässigt skapa din datauppsättning och sedan skapa rapporter i ditt program istället för att använda Power BI Desktop.

## <a name="using-workspace-collections-and-workspaces"></a>Med hjälp av samlingar med arbetsytor och arbetsytor

**Samlingar med arbetsytor** och **arbetsytor** är behållare av innehåll som används och ordnas på sättet som bäst passar designen av programmet som du skapar. Det blir många olika sätt att du kan ordna innehållet i dem. Du kan välja att placera allt innehåll i en arbetsyta och sedan använda apptoken att ytterligare dela upp innehåll mellan dina kunder. Du kan också välja att placera alla dina kunder i olika arbetsytor så att det finns vissa separation sinsemellan. Eller du kan välja att dela upp användare per region i stället för kunden. Den här flexibel design kan du välja det bästa sättet att ordna innehåll.

## <a name="cached-datasets"></a>Med cachelagrade datauppsättningar

Du kan använda cachelagrade datauppsättningar.  Men du kan inte uppdatera cachelagrade data när de har lästs in i **Microsoft Power BI-Arbetsytesamlingar**. En cachelagrad datauppsättning innebär att du har importerat data till Power BI Desktop istället för att använda DirectQuery.

## <a name="authentication-and-authorization-with-app-tokens"></a>Autentisering och auktorisering med apptoken

**Microsoft Power BI-Arbetsytesamlingar** skjuter upp till ditt program för att utföra alla nödvändiga användarautentisering och auktorisering. Det finns inga explicit krav att dina slutanvändare att kunder i Azure Active Directory (AD Azure).  I stället programmets uttrycker till **Microsoft Power BI-Arbetsytesamlingar** auktorisering för att återge en Power BI-rapport med hjälp av **Autentiseringstoken för programmet (Apptoken)**.  Dessa **Apptoken** skapas efter behov när du vill att din app att återge en rapport.

![Diagram för App-användning](media/what-are-power-bi-workspace-collections/app-tokens.png)

**Autentiseringstoken för programmet (Apptoken)** används för att autentisera mot **Microsoft Power BI-Arbetsytesamlingar**.  Det finns tre typer av **Apptoken**:

1. Etablering token - används när du etablerar en ny **arbetsytan** i en **Arbetsytesamling**
2. Utveckling token - används för att göra anrop direkt till den **Power BI REST API: er**
3. Bädda in token - används för att göra anrop för att återge en rapport i inbäddad iframe

Dessa token används för olika faser i din kommunikation med **Microsoft Power BI-Arbetsytesamlingar**.  Token är utformade så att du kan delegera behörigheter från din app till Power BI. Mer information finns i [App Token Flow](app-token-flow.md).

## <a name="create-or-edit-reports-within-your-application"></a>Skapa eller redigera rapporter i ditt program

Du kan nu redigera befintliga rapporter eller skapa nya rapporter direkt i ditt program utan att använda Power BI Desktop. Detta kräver att det finns en datauppsättning i din arbetsyta.

## <a name="see-also"></a>Se också

[Vanliga scenarier för Microsoft Power BI-Arbetsytesamlingar](scenarios.md)  
[Kom igång med Microsoft Power BI-Arbetsytesamlingar](get-started.md)  
[Komma igång med exemplet](get-started-sample.md)  
[Bädda in en rapport](embed-report.md)  
[Autentisering och auktorisering i Power BI-arbetsytesamlingar](app-token-flow.md)  
[Inbäddat exempel med JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[PowerBI-CSharp Git Repo](https://github.com/Microsoft/PowerBI-CSharp)  
[Power BI-noden Git-lagringsplats](https://github.com/Microsoft/PowerBI-Node)  

Fler frågor? [Försök med Power BI Community](https://community.powerbi.com/)
