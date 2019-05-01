---
title: Kom igång med Microsoft Power BI-arbetsytesamlingar
description: Microsoft Power BI-arbetsytesamlingar är en Azure-tjänst som gör det möjligt för apputvecklare att lägga till interaktiva Power BI-rapporter till sina egna appar.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: conceptual
ms.workload: powerbi
ms.date: 09/25/2017
ms.openlocfilehash: d1011a8fd8f181233be8e1fa27c3bfaea3d86141
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685174"
---
# <a name="get-started-with-microsoft-power-bi-workspace-collections"></a>Kom igång med Microsoft Power BI-arbetsytesamlingar

**Microsoft Power BI-arbetsytesamlingar** är en Azure-tjänst som gör det möjligt för apputvecklare att lägga till interaktiva Power BI-rapporter till sina egna appar. **Microsoft Power BI-arbetsytesamlingar** fungerar med befintliga program utan att behöva designa om eller ändra hur användarna loggar in.

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Resurser för **Microsoft Power BI-arbetsytesamlingar** etableras via [Azure Resource Manager-API:erna](https://msdn.microsoft.com/library/mt712306.aspx). I det här fallet är den etablerade resursen en **Power BI-arbetsytesamling**.

![Allmänt flöde för Microsoft Power BI-arbetsytesamlingar](media/get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>Skapa en arbetsytesamling

En **arbetsytesamling** är en Azure-resurs på högsta nivå och en container för det innehåll som kommer att vara inbäddat i din app. En **arbetsytesamling** kan skapas på två sätt:

* Använda Azure Portal manuellt
* Genom programmering med Azure Resource Manager-API:er

Låt oss gå igenom stegen för att skapa en **arbetsytesamling** i Azure Portal.

1. Öppna och logga in på **Azure Portal**: [https://portal.azure.com](https://portal.azure.com).
2. Välj **+ Ny** på den övre panelen.
   
   ![+ Ny i Azure Portal](media/get-started/create-workspace-1.png)
3. Under **Data och analys** väljer du **Power BI-arbetsytesamling**.
4. I kom igång-meddelandet, om du redan har en befintlig prenumeration på Power BI-arbetsytesamling väljer du **Skapa en arbetsytesamling** längst ned.

5. Ange den information so krävs i **Arbetsytesamling**.
   
   ![Skapa arbetsytesamling](media/get-started/create-workspace-2.png)
1. Välj **Skapa**.

Det tar en liten stund att etablera **arbetsytesamlingen**. När det är klart kommer du till **Arbetsytesamling**.

   ![Arbetsytesamling i Azure Portal](media/get-started/create-workspace-3.png)

Resultatet av **Skapa** innehåller den information du behöver för att anropa de API:er som skapar arbetsytor och distribuera innehåll till dem.

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>Visa API-åtkomstnycklar för Power BI

En av de viktigaste informationsdelarna som krävs för att anropa Power BI REST-API:er är **åtkomstnycklarna**. Dessa används för att generera de **apptoken** som används för att autentisera dina API-begäranden. För att visa dina **åtkomstnycklar**, klickar du på **Åtkomstnycklar** i **Inställningar**. Mer information om **apptoken**, finns i [Autentisering och auktorisering med Power BI-arbetsytesamlingar](app-token-flow.md).

   ![Åtkomstnycklar i inställningarna för Arbetsytesamling i Azure Portal](media/get-started/access-keys.png)

Du ser att du har två nycklar.

   ![Två nycklar i Åtkomstnycklar](media/get-started/access-keys-2.png)

Kopiera nycklarna och lagra dem på ett säkert sätt i din app. Det är viktigt att du hanterar de här nycklarna lika försiktigt som ett lösenord, eftersom de ger åtkomst till allt innehåll i din **arbetsytesamling**.

Det finns två nycklar listade men bara en i taget behövs. Den andra nyckeln tillhandahålls så att du regelbundet kan återskapa nycklar utan att avbryta åtkomsten till tjänsten.

Nu när du har en Power BI-instans för din app och **åtkomstnycklar** kan du importera en rapport till din egen app. Innan du lär dig hur du importerar en rapport får du i nästa avsnitt lära dig hur du skapar Power BI-datauppsättningar och -rapporter som ska bäddas in i en app.

## <a name="working-with-workspaces"></a>Arbeta med arbetsytor

När du har skapat arbetsytesamlingen behöver du skapa en arbetsyta för dina rapporter och datauppsättningar. Du behöver använda [REST-API-anropet Post Workspace](https://msdn.microsoft.com/library/azure/mt711503.aspx) för att skapa en arbetsyta.

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app-using-power-bi-desktop"></a>Skapa Power BI-datauppsättningar och -rapporter som ska bäddas in i en app med hjälp av Power BI Desktop

Nu när du har skapat en Power BI-instans för din app och har **åtkomstnycklar** måste du skapa de Power BI-datauppsättningar och -rapporter som du vill bädda in. Datauppsättningar och rapporter kan skapas med hjälp av **Power BI Desktop**. Du kan hämta [Power BI Desktop kostnadsfritt](https://go.microsoft.com/fwlink/?LinkId=521662). Om du vill komma igång snabbt kan du också hämta [exempel på detaljhandelsanalys PBIX](https://go.microsoft.com/fwlink/?LinkID=780547).

> [!NOTE]
> Mer information om hur du använder **Power BI Desktop** finns i [Komma igång med Power BI Desktop](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Med **Power BI Desktop** ansluter du till din datakälla genom att importera en kopia av data till **Power BI Desktop** eller ansluta direkt till datakällan med **DirectQuery**.

Detta är skillnaderna mellan **Importera** och **DirectQuery**.

| Importera | DirectQuery |
| --- | --- |
| Tabeller, kolumner *och data* importeras eller kopieras till **Power BI Desktop**. När du arbetar med visualiseringar ställer **Power BI Desktop** frågor till en kopia av data. Om du vill se ändringar som gjorts i underliggande data måste du uppdatera eller importera en fullständig, aktuell datauppsättning igen. |Endast *tabeller och kolumner* importeras eller kopieras till **Power BI Desktop**. När du arbetar med visualiseringar ställer **Power BI Desktop** frågor till den underliggande datakällan, vilket innebär att du alltid visar aktuella data. |

Mer information om hur du ansluter till en datakälla finns i [Ansluta till en datakälla](connect-datasource.md).

När du har sparat ditt arbete i **Power BI Desktop** skapas en PBIX-fil. Den här filen innehåller rapporten. Om du importerar data innehåller PBIX dessutom den fullständiga datauppsättningen. Om du använder **DirectQuery** innehåller PBIX bara ett datauppsättningsschema. Du distribuerar PBIX genom programmering till din arbetsyta med hjälp av [Power BI Import API](https://msdn.microsoft.com/library/mt711504.aspx).

> [!NOTE]
> **Power BI-arbetsytesamlingar** har ytterligare API:er för att ändra den server och den databas som din datauppsättning pekar på och ange autentiseringsuppgifter för tjänstekontot som datauppsättningen kommer att använda för att ansluta till databasen. Se [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) och [Datakälla för korrigeringsgateway](https://msdn.microsoft.com/library/mt711498.aspx).

## <a name="create-power-bi-datasets-and-reports-using-apis"></a>Skapa Power BI-datauppsättningar och -rapporter med hjälp av API:er

### <a name="datasets"></a>Datauppsättningar

Du kan skapa datauppsättningar i Power BI-arbetsytesamlingar med hjälp av REST API. Du kan sedan skicka data till datauppsättningen. På så sätt kan du arbeta med data utan att du behöver Power BI Desktop. Mer information finns i [Post Datasets](https://msdn.microsoft.com/library/azure/mt778875.aspx) (Lägga upp datauppsättningar).

### <a name="reports"></a>Rapporter

Du kan skapa en rapport från en datamängd direkt i programmet med hjälp av JavaScript API. Mer information finns i [Skapa en ny rapport från en datauppsättning i Power BI-arbetsytesamlingar](create-report-from-dataset.md).

## <a name="see-also"></a>Se även

[Komma igång med exemplet](get-started-sample.md)  
[Autentisering och auktorisering i Power BI-arbetsytesamlingar](app-token-flow.md)  
[Bädda in en rapport](embed-report.md)  
[Skapa en ny rapport en datauppsättning i Power BI-arbetsytesamlingar](create-report-from-dataset.md)
[Spara rapporter](save-reports.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Inbäddat exempel med JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Fler frågor? [Försök med Power BI Community](https://community.powerbi.com/)

