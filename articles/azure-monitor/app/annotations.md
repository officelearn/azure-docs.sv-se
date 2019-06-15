---
title: Versionsanteckningar för Application Insights | Microsoft Docs
description: Lägg till distribution eller skapa markörer i metrics explorer diagrammen i Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mbullwin
ms.openlocfilehash: 6567d7f2ebaab5bd7b5bc8fb7b5a62970f169161
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476166"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anteckningar på måttdiagram i Application Insights

Anteckningar på [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) diagrammen visas där du har distribuerat en ny version eller betydande händelse. De gör det enkelt att se om ändringarna hade någon effekt på prestanda för ditt program. De kan skapas automatiskt av den [Azure DevOps-tjänsterna buildsystemet](https://docs.microsoft.com/azure/devops/pipelines/tasks/). Du kan också skapa anteckningar för att flagga en händelse om du vill genom att skapa dem från PowerShell.

> [!NOTE]
> Den här artikeln visar det inaktuella **klassiska mått uppleva**. Anteckningar är bara tillgängliga i den klassiska upplevelsen och i  **[arbetsböcker](../../azure-monitor/app/usage-workbooks.md)** . Mer information om aktuellt mått upplevelse, finns [i den här artikeln](../../azure-monitor/platform/metrics-charts.md).

![Exempel på anteckningar](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-devops-services-build"></a>Versionsanteckningar med Azure DevOps-Services-version

Versionsanteckningar är en funktion i Azure Pipelines molnbaserad tjänst för Azure DevOps-tjänsterna.

### <a name="install-the-annotations-extension-one-time"></a>Installera tillägget anteckningar (en gång)
Om du vill kunna skapa Versionsanteckningar måste du installera en av de många tillgängliga tillägg i Azure DevOps-tjänsterna i Visual Studio Marketplace.

1. Logga in på din [Azure DevOps-tjänsterna](https://azure.microsoft.com/services/devops/) projekt.
2. I Visual Studio Marketplace [hämta tillägg för Versionsanteckningar](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations), och lägga till den i din organisation med Azure DevOps-tjänsterna.

![Välj en Azure DevOps-organisation och sedan installera.](./media/annotations/1-install.png)

Du behöver bara göra detta en gång för din organisation med Azure DevOps-tjänsterna. Versionsanteckningar kan nu konfigureras för alla projekt i din organisation.

### <a name="configure-release-annotations"></a>Konfigurera Versionsanteckningar

Du behöver en separat API-nyckel för varje mall för versionen av Azure DevOps-tjänsterna.

1. Logga in på den [Microsoft Azure-portalen](https://portal.azure.com) och öppna den Application Insights-resurs som övervakar dina program. (Eller [skapa en nu](../../azure-monitor/app/app-insights-overview.md), om du inte gjort det ännu.)
2. Öppna den **API-åtkomst** fliken och kopiera den **Application Insights-ID**.
   
    ![Öppna din Application Insights-resurs på portal.azure.com och välj inställningar. Öppna API-åtkomst. Kopiera program-ID](./media/annotations/2-app-id.png)

4. Öppna (eller skapa) på versionsmall som hanterar dina distributioner från Azure DevOps-tjänsterna i ett separat webbläsarfönster.
   
    Lägg till en aktivitet och välj Application Insights på Versionsanteckning uppgiften på menyn.

   ![Klicka på plustecknet för att lägga till uppgiften och välj Application Insights på Versionsanteckning. Klistra in Application Insights-ID.](./media/annotations/3-add-task.png)

    Klistra in den **program-ID** som du kopierade från fliken API-åtkomst.
   
    ![Klistra in Application Insights-ID](./media/annotations/4-paste-app-id.png)

5. I fönstret Azure att skapa en ny API-nyckel och ta en kopia av den.
   
    ![På fliken API-åtkomst i Azure-fönstret klickar du på Skapa API-nyckel.](./media/annotations/5-create-api-key.png)

    ![Ange en kommentar på fliken Skapa API: et, kontrollera skriva kommentarer och på Generera nyckel. Kopiera den nya nyckeln.](./media/annotations/6-create-api-key.png)

6. Öppna fliken Konfiguration för versionsmall för.
   
    Skapa en variabel definition för `ApiKey`.
   
    Klistra in din API-nyckel till ApiKey variabeldefinitionen.
   
    ![Välj fliken variabeln och klicka på Lägg till i fönstret Azure DevOps-tjänsterna. Ange namnet till ApiKey och i värdet och klistra in den nyckel som du genererade och på låsikonen.](./media/annotations/7-paste-api-key.png)
1. Slutligen **spara** versionspipelinen.


## <a name="view-annotations"></a>Visa anteckningar
När du använder mallen versionen för att distribuera en ny version, nu skickas en anteckning till Application Insights. Anteckningarna visas i diagrammen i Metrics Explorer.

Klicka på någon anteckningsmarkör (ljust grå pilen) för att öppna information om versionen, inklusive begärande, kontroll källgren, viktig pipeline och miljö.

![Klicka på någon version anteckningsmarkör.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Skapa anpassade kommentarer från PowerShell
Du kan också skapa anteckningar från en process som du vill (utan att använda Azure DevOps-tjänster). 


1. Skapa en lokal kopia av den [Powershell-skriptet från GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Hämta program-ID och skapa en API-nyckel från fliken API-åtkomst.

3. Anropa skriptet så här:

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

Det är enkelt att ändra skriptet, till exempel för att skapa anteckningar för tidigare.

## <a name="next-steps"></a>Nästa steg

* [Skapa arbetsobjekt](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatisering med PowerShell](../../azure-monitor/app/powershell.md)
