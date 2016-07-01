<properties
   pageTitle="Komma igång med Microsoft Power BI Embedded Preview"
   description="Power BI Embedded lägger till interaktiva Power BI-rapporter i dina Business Intelligence-appar"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="04/24/2016"
   ms.author="derrickv"/>

# Komma igång med Microsoft Power BI Embedded Preview

**Microsoft Power BI Embedded** är en Azure-tjänst som gör det möjligt för apputvecklare att lägga till interaktiva Power BI-rapporter till sina egna appar. **Power BI Embedded** fungerar med befintliga appar utan att man behöver ändra designen på apparna eller ändra hur användarna loggar in.

Mer information om Power BI Embedded finns i [Vad är Power BI Embedded?](power-bi-embedded-what-is-power-bi-embedded.md).

Enligt beskrivningen i [Vad är Power BI Embedded?](power-bi-embedded-what-is-power-bi-embedded.md) etableras resurser för **Microsoft Power BI Embedded** via [Azure ARM-API:erna](https://msdn.microsoft.com/library/mt712306.aspx). I det här fallet är den resurs som du etablerar en **Power BI-arbetsytesamling**. I nästa avsnitt beskrivs hur du skapar en arbetsytesamling.

![](media\power-bi-embedded-get-started\introduction.png)

## Skapa en arbetsytesamling
En **arbetsytesamling** är en Azure-resurs på högsta nivå och en behållare för det innehåll som kommer att vara inbäddat i din app. En **arbetsytesamling** kan skapas på två sätt:

   -    Använda Azure-portalen manuellt
   -    Genom programmering med Azure Resource Manager-API:er

Låt oss gå igenom stegen för att skapa en **arbetsytesamling** i Azure-portalen.

   1.   Öppna och logga in på **Azure-portalen**: [http://portal.azure.com](http://portal.azure.com).

   2.   Klicka på **+ Ny** på den övre panelen.

       ![](media\power-bi-embedded-get-started\create-workspace-1.png)

   3.   Under **Data + analys** klickar du på **Power BI Embedded**.
   4.   Ange den information som krävs på bladet **Skapa**. Mer information om **priser** finns i [Priser för Power BI Embedded](http://go.microsoft.com/fwlink/?LinkID=760527).

       ![](media\power-bi-embedded-get-started\create-workspace-2.png)

   5. Klicka på **Skapa**.

Det tar en liten stund att etablera **arbetsytesamlingen**. När den har slutförts förs du till  **arbetsytesamlingsbladet**.

   ![](media\power-bi-embedded-get-started\create-workspace-3.png)

Bladet **Skapa** innehåller den information du behöver för att anropa de API:er som skapar arbetsytor och distribuera innehåll till dem.

I nästa avsnitt beskrivs hur **åtkomstnycklar** används för att generera **apptoken** som används för att autentisera dina API-begäranden.

<a name="view-access-keys"/>
## Visa API-åtkomstnycklar för Power BI 

En av de viktigaste informationsdelarna som krävs för att anropa Power BI REST-API:er är  **åtkomstnycklarna**. Dessa används för att generera de **apptoken** som används för att autentisera dina API-begäranden. För att visa dina **åtkomstnycklar**, klickar du på **Åtkomstnycklar** på bladet **Inställningar**. Mer information om **apptoken** finns i [Så fungerar apptokenflödet](power-bi-embedded-app-token-flow.md).

   ![](media\power-bi-embedded-get-started\access-keys.png)

Du ser att du har två nycklar.

   ![](media\power-bi-embedded-get-started\access-keys-2.png)

Kopiera nycklarna och lagra dem på ett säkert sätt i din app. Det är mycket viktigt att du hanterar de här nycklarna lika försiktigt som ett lösenord, eftersom de ger åtkomst till allt innehåll i din **arbetsytesamling**.

Det finns två nycklar listade men bara en i taget behövs. Den andra nyckeln tillhandahålls så att du regelbundet kan återskapa nycklar utan att avbryta åtkomsten till tjänsten.

Nu när du har en Power BI-instans för din app och **åtkomstnycklar** kan du importera en rapport till din egen app. Innan du lär dig hur du importerar en rapport får du i nästa avsnitt lära dig hur du skapar Power BI-datauppsättningar och -rapporter som ska bäddas in i en app.

## Skapa Power BI-datauppsättningar och -rapporter som ska bäddas in i en app

Nu när du har skapat en Power BI-instans för din app och har **åtkomstnycklar** måste du skapa de Power BI-datauppsättningar och -rapporter som du vill bädda in. Datauppsättningar och rapporter kan skapas med hjälp av **Power BI Desktop**. Du kan hämta [Power BI Desktop kostnadsfritt](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/). Om du vill komma igång snabbt kan du också hämta [exempel på detaljhandelsanalys PBIX] (http://go.microsoft.com/fwlink/?LinkID=780547). Mer information om hur du använder **Power BI Desktop** finns i [Komma igång med Power BI Desktop](https://powerbi.microsoft.com/en-us/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Med **Power BI Desktop** ansluter du till din datakälla genom att importera en kopia av data till **Power BI Desktop** eller ansluta direkt till datakällan med **DirectQuery**.

Detta är skillnaderna mellan **Importera** och **DirectQuery**.

|Importera | DirectQuery
|---|---
|Tabeller, kolumner *och data* importeras eller kopieras till **Power BI Desktop**. När du arbetar med visualiseringar ställer **Power BI Desktop** frågor till en kopia av data. Om du vill se ändringar som gjorts i underliggande data måste du uppdatera eller importera en fullständig, aktuell datauppsättning igen.|Endast *tabeller och kolumner* importeras eller kopieras till **Power BI Desktop**. När du arbetar med visualiseringar ställer **Power BI Desktop** frågor till den underliggande datakällan, vilket innebär att du alltid visar aktuella data.

Mer information om hur du ansluter till en datakälla finns i [Ansluta till en datakälla](power-bi-embedded-connect-datasource.md).

När du har sparat ditt arbete i **Power BI Desktop** skapas en PBIX-fil. Den här filen innehåller rapporten. Om du importerar data innehåller PBIX dessutom den fullständiga datauppsättningen. Om du använder **DirectQuery** innehåller PBIX bara ett datauppsättningsschema. Du distribuerar PBIX genom programmering till din arbetsyta med hjälp av [Power BI Import API](https://msdn.microsoft.com/library/mt711504.aspx).

> [AZURE.NOTE] **Power BI Embedded** har ytterligare API:er för att ändra den server och den databas som din datauppsättning pekar på och ange autentiseringsuppgifter för tjänstekontot som datauppsättningen kommer att använda för att ansluta till databasen. Se [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) och [Datakälla för korrigeringsgateway](https://msdn.microsoft.com/library/mt711498.aspx).

## Nästa steg
I de föregående stegen skapade du en arbetsytesamling, din första rapport och dina första datauppsättningar. Nu är det dags att lära sig att skriva kod för **Power BI Embedded**. För att hjälpa dig att komma igång har vi tagit fram ett exempel på ett webbprogram: [Komma igång med exemplet](power-bi-embedded-get-started-sample.md). I det här exemplet visas hur du:

  - Etablerar innehåll
      - Skapar en arbetsyta
      - Importerar en PBIX-fil
      - Uppdaterar anslutningssträngarna och anger autentiseringsuppgifter för dina datauppsättningar

  - Bäddar in en rapport på ett säkert sätt

## Se även
- [Komma igång med exemplet](power-bi-embedded-get-started-sample.md)
- [Vad är Power BI Embedded?](power-bi-embedded-what-is-power-bi-embedded.md)
- [Komma igång med Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started)
- [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
- [Priser för Power BI Embedded](http://go.microsoft.com/fwlink/?LinkID=760527)



<!--HONumber=Jun16_HO2-->


