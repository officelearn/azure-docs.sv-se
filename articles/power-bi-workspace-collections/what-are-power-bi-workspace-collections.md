---
title: Vad är Power BI arbetsytan samlingar?
description: Power BI Embedded kan du integrera Power BI-rapporter i webb- eller mobila program, så du inte behöver skapa anpassade lösningar.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 0bd6ba74d176ed1db1e8f1f1b38646182da2c379
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31412585"
---
# <a name="what-are-power-bi-workspace-collections"></a>Vad är Power BI arbetsytan samlingar?

Med **Power BI arbetsytan samlingar**, kan du integrera Power BI-rapporter direkt i ditt webb- eller mobila program.

![Diagram över](media/what-are-power-bi-workspace-collections/what-is.png)

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Power BI arbetsytan samlingar är en **Azure-tjänsten** som gör att ISV: er och apputvecklare för att visa Power BI data upplevelser i sina program. Du har skapat program som en utvecklare och programmen har sina egna användare och en specifik uppsättning funktioner. Dessa appar kan också inträffa har vissa inbyggda dataelement som diagram och rapporter som kan nu drivs av Microsoft Power BI arbetsytan samlingar. Du behöver ett Power BI-konto du använder din app. Du kan fortsätta att logga in i tillämpningsprogrammet precis som tidigare kan visa och interagera med Power BI reporting upplevelse utan ytterligare licenser.

## <a name="licensing-for-microsoft-power-bi-workspace-collections"></a>Licensiering för Microsoft Power BI arbetsytan samlingar

I den **Microsoft Power BI arbetsytan samlingar** användningsmodell licensiering för Power BI inte ansvarar för användaren.  I stället **sessioner** köps av utvecklaren av appen som förbrukar den visuella informationen och debiteras till prenumerationen som äger resurserna. 

## <a name="microsoft-power-bi-workspace-collections-conceptual-model"></a>Microsoft Power BI arbetsytan samlingar konceptuella modellen

![Programflöde med arbetsytan samlingar](media/what-are-power-bi-workspace-collections/model.png)

Precis som andra tjänster i Azure-resurser för Power BI arbetsytan samlingar tillhandahålls via den [Azure Resource Manager API: erna](https://msdn.microsoft.com/library/mt712306.aspx). I det här fallet är den resurs som etablera en **Power BI-Arbetsytesamling**.

## <a name="workspace-collection"></a>Arbetsytesamling

En **Arbetsytesamling** är den högsta nivån Azure behållare för resurser som innehåller 0 eller fler **arbetsytor**.  En **arbetsytan** **samlingen** har alla standardegenskaper för Azure som följande:

* **Åtkomstnycklar** – nycklar som används när anropas på ett säkert sätt i Power BI-API: er (beskrivs i ett senare avsnitt).
* **Användare** – Azure Active Directory (AAD)-användare som har administratörsrättigheter för att hantera Power BI-Arbetsytesamling via Azure-portalen eller Azure Resource Manager API.
* **Region** – som en del av etablerar en **Arbetsytesamling**, du kan välja en region som ska etableras i. Mer information finns i [Azure-regioner](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Arbetsyta

En **arbetsytan** är en behållare för Power BI innehåll, vilket kan omfatta datauppsättningar och -rapporter. En **arbetsytan** är tomt när först skapas. Du måste redigera innehåll med hjälp av Power BI Desktop och du kommer att distribuera PBIX genom programmering till din arbetsyta med hjälp av [Power BI Import API](https://msdn.microsoft.com/library/mt711504.aspx). Du kan också programmässigt skapa din datauppsättning och skapa rapporter i ditt program i stället för Power BI Desktop.

## <a name="using-workspace-collections-and-workspaces"></a>Använda arbetsytan samlingar och arbetsytor

**Arbetsytan samlingar** och **arbetsytor** är behållare för innehåll som används och ordnade i det bästa sättet passar design för det program som du skapar. Det blir många olika sätt att du kan ordna innehållet i dem. Du kan välja att placera allt innehåll i en arbetsyta och sedan använda apptoken att ytterligare dela upp innehållet bland dina kunder. Du kan också välja att placera alla dina kunder i separata arbetsytor så att det finns vissa åtskillnad mellan dem. Eller så kan du gruppera användare per region i stället för kunden. Den här flexibla designen kan du välja det bästa sättet att organisera innehåll.

## <a name="cached-datasets"></a>Cachelagrade datauppsättningar

Du kan använda cachelagrade datauppsättningar.  Men du kan inte uppdatera cachelagrade data när den har lästs in i **Microsoft Power BI arbetsytan samlingar**. En cachelagrad dataset innebär att du har importerat data till Power BI Desktop istället för att använda DirectQuery.

## <a name="authentication-and-authorization-with-app-tokens"></a>Autentisering och auktorisering med apptoken

**Microsoft Power BI arbetsytan samlingar** skjuts upp i programmet för att utföra alla nödvändiga användarautentisering och auktorisering. Krävs inte explicit att slutanvändarna att kunder i Azure Active Directory (AD Azure).  I stället tillämpningsprogrammet representerar till **Microsoft Power BI arbetsytan samlingar** tillstånd att göra en Power BI-rapport med hjälp av **programmet autentiseringstoken (Apptoken)**.  Dessa **Apptoken** skapas vid behov när din app vill göra en rapport.

![Diagram för App-token användning](media/what-are-power-bi-workspace-collections/app-tokens.png)

**Programmet autentiseringstoken (Apptoken)** används för att autentisera mot **Microsoft Power BI arbetsytan samlingar**.  Det finns tre typer av **Apptoken**:

1. Etablering token - som används vid etablering av en ny **arbetsytan** i en **Arbetsytesamling**
2. Utveckling token - används när du anropar direkt till den **Power BI REST API: er**
3. Bädda in token - används när du anropar återge en rapport i inbäddade iframe

Dessa token används för de olika faserna i interaktioner med **Microsoft Power BI arbetsytan samlingar**.  Token har utformats så att du kan delegera behörigheter från din app till Power BI. Mer information finns i [App Token flöda](app-token-flow.md).

## <a name="create-or-edit-reports-within-your-application"></a>Skapa eller redigera rapporter i ditt program

Du kan nu redigera befintliga rapporter eller skapa nya rapporter direkt i ditt program utan att använda Power BI Desktop. Detta kräver att det finns en datamängd i arbetsytan.

## <a name="see-also"></a>Se också

[Vanliga scenarier för Microsoft Power BI arbetsytan samlingar](scenarios.md)  
[Kom igång med Microsoft Power BI arbetsytan samlingar](get-started.md)  
[Komma igång med exemplet](get-started-sample.md)  
[Bädda in en rapport](embed-report.md)  
[Autentisering och auktorisering i Power BI-arbetsytesamlingar](app-token-flow.md)  
[Inbäddat exempel med JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[PowerBI CSharp Git Repo](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-nod Git Repo](https://github.com/Microsoft/PowerBI-Node)  

Fler frågor? [Försök med Power BI Community](http://community.powerbi.com/)
