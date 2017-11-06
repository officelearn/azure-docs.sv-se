---
title: "Använd Power BI Embedded Migreringsverktyget | Microsoft Docs"
description: "Power BI Embedded Migreringsverktyget kan användas för att kopiera dina rapporter från Power BI arbetsytan samlingar till Power BI Embedded."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 0b7b5089045daf6dd88fcd84e316b2bd44f8c927
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-power-bi-embedded-migration-tool"></a>Använd Power BI Embedded Migreringsverktyget

Power BI Embedded Migreringsverktyget kan användas för att kopiera dina rapporter från Power BI arbetsytan samlingar till Power BI Embedded.

Migrera innehållet från arbetsytan samlingar till Power BI tjänsten kan göras parallella till lösningen och kräver inte någon avbrottstid.

## <a name="limitations"></a>Begränsningar

* Intryckt datauppsättningar kan inte hämtas och måste återskapas med hjälp av Power BI REST-API: er för Power BI-tjänsten.
* PBIX-filer som importeras innan 26 November 2016 inte kan hämtas.

## <a name="download"></a>Ladda ned

Du kan hämta verktyget-prov för migrering från [GitHub](https://github.com/Microsoft/powerbi-migration-sample). Du kan antingen ladda ned en zip av databasen eller kan klona lokalt. När du har hämtat, kan du öppna *powerbi-migrering-sample.sln* i Visual Studio för att skapa och köra Migreringsverktyget.

## <a name="migration-plans"></a>Planer

Din migreringsplan är bara metadata som katalogiserar innehållet i Power BI arbetsytan samlingar och hur du vill publicera dem i Power BI Embedded.

### <a name="start-with-a-new-migration-plan"></a>Börja med en ny migreringsplan

En migreringsplan är metadata för objekt som är tillgängliga i Power BI arbetsytan samlingar som du sedan vill flytta över till Power BI Embedded. Migreringsplan lagras som en XML-fil.

Du vill börja med att skapa en ny migreringsplan. Gör följande om du vill skapa en ny migreringsplan.

1. Välj **filen** > **nya migreringsplan**.

    ![Nya migreringsplan](media/migrate-tool/migrate-tool-plan.png)

2. I den **Välj Power BI arbetsytan samlingar resursgruppen** dialogrutan du vill välja den **miljö** listrutan och välj prod.

3. Du uppmanas att logga in. Du använder Azure-prenumeration inloggningen.

    > [!IMPORTANT]
    > Detta är **inte** din organisations Office 365-konto som du loggar in i Power BI med.

4. Välj den Azure-prenumeration som lagrar din Power BI arbetsytan samlingar resurs.

    ![Välj din Azure-prenumeration](media/migrate-tool/migrate-tool-select-resource-group.png)

5. Under listan prenumeration, Välj den **resursgruppen** som innehåller din arbetsyta samlingar och välj **Välj**.

    ![Välja en resursgrupp](media/migrate-tool/migrate-tool-select-resource-group2.png)

6. Välj **analysera**. Då hämtas en förteckning över artiklarna i din Azure-prenumeration att påbörja din plan.

    ![Knappen Välj analysera](media/migrate-tool/migrate-tool-analyze-group.png)

    > [!NOTE]
    > Analysera processen kan ta flera minuter beroende på antalet arbetsytan samlingar och hur mycket innehåll finns i samlingen arbetsytan.

7. När **analysera** är klar, blir du ombedd att spara din migreringsplan.

Nu har du anslutit din migreringsplan till din Azure-prenumeration. Läs nedan till flödet av hur du arbetar med din migreringsplan. Detta inkluderar att analysera och planera migrering, hämta, skapa grupper och ladda upp.

### <a name="save-your-migration-plan"></a>Spara din migreringsplan

Du kan spara din migreringsplan för senare användning. Detta skapar en XML-fil som innehåller all information i din migreringsplan.

Gör följande om du vill spara din migreringsplan.

1. Välj **filen** > **spara migreringsplan**.

    ![Spara menyalternativet planera för migrering](media/migrate-tool/migrate-tool-save-plan.png)

2. Namnge din fil eller använda det genererade filnamnet och välj **spara**.

### <a name="open-an-existing-migration-plan"></a>Öppna en befintlig migreringsplan

Du kan öppna en sparad migreringsplan att fortsätta arbeta med migreringen.

Gör följande om du vill öppna din befintliga migreringsplan.

1. Välj **filen** > **öppna befintliga migreringsplan**.

    ![Öppna en befintlig plan menyn migreringsalternativ](media/migrate-tool/migrate-tool-open-plan.png)

2. Välj din migreringsfilen och välj **öppna**.

## <a name="step-1-analyze-and-plan-migration"></a>Steg 1: Analysera och planera migrering

Den **analysera och planera migrering** fliken ger dig en översikt över vad som är för närvarande i resursgruppen för din Azure-prenumeration.

![Analysera och planera migrering fliken](media/migrate-tool/migrate-tool-step1.png)

Vi ska titta på den *SampleResourceGroup* som exempel.

### <a name="paas-topology"></a>PaaS-topologi

Detta är en lista över dina *resursgrupp > arbetsytan samlingar > arbetsytor*. Resurssamlingar arbetsytan och grupp visas ett eget namn. Arbetsytorna visas ett GUID.

Objekt i listan visas också en färg och ett nummer i formatet (#/ #). Detta anger antalet rapporter som kan hämtas.

En svart innebär att alla rapporter kan hämtas. En färg innebär att vissa rapporter inte kan hämtas. Vänster värde visar det totala antalet rapporter som kan hämtas. Numret på höger anger det totala antalet rapporter i grupperingen.

Du kan välja ett objekt i PaaS-topologin för att visa rapporterna i området rapporter.

### <a name="reports"></a>Rapporter

Rapportavsnittet visar en lista över ut de tillgängliga rapporterna och anger om du kan hämta eller inte.

![Lista över rapporter i Power BI arbetsytan samlingar](media/migrate-tool/migrate-tool-analyze-reports.png)

### <a name="target-structure"></a>Målstruktur

Den **mål strukturen** används för att instruera verktyget där saker hämtas till och hur du överför dem.

#### <a name="download-plan"></a>Hämta plan

En sökväg skapas automatiskt åt dig. Du kan ändra den här sökvägen om du vill. Om du ändrar sökvägen, behöver du väljer **uppdatera sökvägar**.

**Detta utför ingen hämtningen.** Detta är endast anger strukturen för där rapporterna hämtas till.

#### <a name="upload-plan"></a>Överför plan

Här kan du ange ett prefix som ska användas för app-arbetsytor som ska skapas i Power BI-tjänsten. Efter prefixet kommer att GUID för den arbetsyta som fanns i Azure.

![Ange grupp-namnprefix](media/migrate-tool/migrate-tool-upload-plan.png)

**Detta skapar faktiskt inte grupper i Power BI-tjänsten.** Detta definierar endast namngivning strukturen för grupperna.

Om du ändrar prefixet, behöver du väljer **generera överför planera**.

Du kan högerklicka på en grupp och väljer att byta namn på gruppen i planen Överför direkt, om så önskas.

![Byt namn på gruppen menyalternativet för kontexten](media/migrate-tool/migrate-tool-upload-report-rename-item.png)

> [!NOTE]
> Namnet på den *grupp* får inte innehålla blanksteg eller ogiltiga tecken.

## <a name="step-2-download"></a>Steg 2: ladda ned

På den **hämta** fliken visas en lista över rapporter och tillhörande metadata. Du kan se status export är tillsammans med föregående export status.

![Fliken hämta](media/migrate-tool/migrate-tool-download-tab.png)

Har du två alternativ.

* Välj specifika rapporter och **Hämta valt**
* Välj **hämta alla**.

![Hämta markerad knapp](media/migrate-tool/migrate-tool-download-options.png)

En lyckad hämtning, visas statusen *klar* och den visar att det finns PBIX-fil.

När hämtningen är klar väljer du den **skapa grupper** fliken.

## <a name="step-3-create-groups"></a>Steg 3: skapa grupper

När du har hämtat de rapporter som är tillgängliga går du till den **skapa grupper** fliken. Den här fliken skapar appen arbetsytor i Power BI-tjänsten baserat på migreringsplan som du skapade. Arbetsytan appar skapas med det namn du angett på den **överför** fliken inom **analysera och planera migrering**.

![Skapa grupper](media/migrate-tool/migrate-tool-create-groups.png)

Om du vill skapa app-arbetsytor kan du välja en **skapa valda grupper** eller **skapa grupper för alla saknas**.

När du väljer något av dessa alternativ kan uppmanas du att logga in. *Du ska du använda dina autentiseringsuppgifter för Power BI-tjänsten som du vill skapa app arbetsytor på.*

![Power BI-inloggning skärmen](media/migrate-tool/migrate-tool-create-group-sign-in.png)

Detta skapar app-arbetsytan i Power BI-tjänsten. Detta inte att överföra rapporterna till arbetsytan app.

Du kan kontrollera att app-arbetsytan har skapats genom att logga in på Power BI och verifiera att det finns på arbetsytan. Du ser att ingenting finns på arbetsytan.

![App-arbetsytan i Power BI-tjänsten](media/migrate-tool/migrate-tool-app-workspace.png)

Efter att arbetsytan har skapats kan du gå vidare till den **överför** fliken.

## <a name="step-4-upload"></a>Steg 4: Överför

På den **överför** fliken detta kommer att överföra rapporterna till Power BI-tjänsten. En lista över de rapporter som vi hämtat visas på fliken nedladdning tillsammans med namnet på den målgrupp baserat på din migreringsplan.

![Ladda upp fliken](media/migrate-tool/migrate-tool-upload-tab.png)

Du kan ladda upp markerade rapporter eller du kan ladda upp alla rapporter. Du kan också återställa Överföringsstatusen om du vill överföra objekt.

Du har också välja vad du gör om det finns en rapport med samma namn. Du kan välja mellan **Avbryt**, **Ignorera** och **Skriv över**.

![Alternativet listrutan för vad du gör om rapporten finns](media/migrate-tool/migrate-tool-upload-report-same-name.png)

![Överför valda resultat](media/migrate-tool/migrate-tool-upload-selected.png)

### <a name="duplicate-report-names"></a>Duplicera rapportnamn

Om du har en rapport som har samma namn, men du vet att det är en annan rapport, behöver du ändra den **TargetName** i rapporten. Du kan ändra namnet genom att manuellt redigera migreringsplan XML.

Du måste stänga Migreringsverktyget för att utföra ändringen och öppna verktyget och planera för migrering.

I exemplet ovan misslyckades en klonade rapporter som visar en rapport med samma namn fanns. Om vi tittar på migreringsplan XML, kommer vi att se följande.

```
<ReportMigrationData>
    <PaaSWorkspaceCollectionName>SampleWorkspaceCollection</PaaSWorkspaceCollectionName>
    <PaaSWorkspaceId>4c04147b-d8fc-478b-8dcb-bcf687149823</PaaSWorkspaceId>
    <PaaSReportId>525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe</PaaSReportId>
    <PaaSReportLastImportTime>1/3/2017 2:10:19 PM</PaaSReportLastImportTime>
    <PaaSReportName>cloned</PaaSReportName>
    <IsPushDataset>false</IsPushDataset>
    <IsBoundToOldDataset>false</IsBoundToOldDataset>
    <PbixPath>C:\MigrationData\SampleResourceGroup\SampleWorkspaceCollection\4c04147b-d8fc-478b-8dcb-bcf687149823\cloned-525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe.pbix</PbixPath>
    <ExportState>Done</ExportState>
    <LastExportStatus>OK</LastExportStatus>
    <SaaSTargetGroupName>SampleMigrate</SaaSTargetGroupName>
    <SaaSTargetGroupId>6da6f072-0135-4e6c-bc92-0886d8aeb79d</SaaSTargetGroupId>
    <SaaSTargetReportName>cloned</SaaSTargetReportName>
    <SaaSImportState>Failed</SaaSImportState>
    <SaaSImportError>Report with the same name already exists</SaaSImportError>
</ReportMigrationData>
```

Vi kan ändra namnet på SaaSTargetReportName för det misslyckade objektet.

```
<SaaSTargetReportName>cloned2</SaaSTargetReportName>
```

Vi kan sedan öppna igen plan i Migreringsverktyget och ladda upp rapporten misslyckades.

Gå tillbaka till Power BI, se vi att rapporter och datauppsättningar har överförts i arbetsytan app.

![Rapportlistan i Power BI-tjänsten](media/migrate-tool/migrate-tool-upload-app-workspace.png)

<a name="upload-local-file"></a>
### <a name="upload-a-local-pbix-file"></a>Ladda upp en lokal PBIX-fil

Du kan ladda upp en lokal version av en Power BI Desktop-fil. Du måste stänga verktyget, redigera XML och placera den fullständiga sökvägen till din lokala PBIX i den **PbixPath** egenskapen.

```
<PbixPath>[Full Path to PBIX file]</PbixPath>
```

När du har redigerat xml, öppnar den igen inom Migreringsverktyget planen och ladda upp rapporten.

<a name="directquery-reports"></a>
### <a name="directquery-reports"></a>DirectQuery-rapporter

Du behöver uppdatera för att uppdatera anslutningssträngen för DirectQuery-rapporter. Detta kan göras i *powerbi.com*, eller programmässigt fråga anslutningssträng från Power BI Embedded (Paas). Ett exempel finns [extrahera DirectQuery anslutningssträngen från PaaS rapport](migrate-code-snippets.md#extract-directquery-connection-string-from-power-bi-workspace-collections).

Du kan uppdatera anslutningssträngen för datamängden i Power BI-tjänsten och ange autentiseringsuppgifter för datakällan. Du kan se i följande exempel för att se hur du gör detta.

* [Uppdatera DirectQuery anslutningssträngen i Power BI Embedded](migrate-code-snippets.md#update-directquery-connection-string-in-power-bi-embedded)
* [Ange autentiseringsuppgifterna för DirectQuery i Power BI Embedded](migrate-code-snippets.md#set-directquery-credentials-in-power-bi-embedded)

## <a name="next-steps"></a>Nästa steg

Nu när dina rapporter har migrerats från Power BI arbetsytan samlingar till Power BI Embedded kan du nu uppdatera ditt program och börja bädda in rapporterna i den här appen arbetsytan.

Mer information finns i [hur du migrerar Power BI-Arbetsytesamling innehåll till Power BI Embedded](migrate-from-power-bi-workspace-collections.md).

Fler frågor? [Försök be Power BI-communityn](http://community.powerbi.com/)