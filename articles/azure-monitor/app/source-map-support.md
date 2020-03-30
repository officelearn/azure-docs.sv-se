---
title: Stöd för källkartning för JavaScript-program – Azure Monitor Application Insights
description: Lär dig hur du laddar upp källkartor till din egen Blob-behållare för lagringskonto med Hjälp av Application Insights.
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 03/04/2020
ms.openlocfilehash: 4b452b31338760a8f53eed54420319101836bc00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474891"
---
# <a name="source-map-support-for-javascript-applications"></a>Stöd för källkarta för JavaScript-program

Application Insights stöder uppladdning av källkartor till din egen Storage Account Blob Container.
Källkartor kan användas för att avangöra samtalsstaplar som finns på sidan till transaktionsinformation. Alla undantag som skickas av [JavaScript SDK][ApplicationInsights-JS] eller [Node.js SDK][ApplicationInsights-Node.js] kan inte identifieras med källkartor.

![Avanpassa en samtalsstack genom att länka till ett lagringskonto](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>Skapa ett nytt lagringskonto och en blob-behållare

Om du redan har ett befintligt lagringskonto eller en blob-behållare kan du hoppa över det här steget.

1. [Skapa ett nytt lagringskonto][create storage account]
2. [Skapa en blob-behållare][create blob container] i ditt lagringskonto. Var noga med att ställa in `Private`"Public Access Level" till , för att säkerställa att dina källkartor inte är tillgängliga för allmänheten.

> [!div class="mx-imgBorder"]
>![Åtkomstnivån för behållaren måste vara inställd på Privat](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>Skicka källkartorna till din Blob-behållare

Du bör integrera din pipeline för kontinuerlig distribution med ditt lagringskonto genom att konfigurera den för att automatiskt överföra källmapparna till den konfigurerade Blob-behållaren. Du bör inte ladda upp källmapparna till en undermapp i Blob-behållaren. för närvarande hämtas källkartan endast från rotmappen.

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Ladda upp källkartor via Azure Pipelines (rekommenderas)

Om du använder Azure Pipelines för att kontinuerligt skapa och distribuera ditt program lägger du till en [Azure File Copy-uppgift][azure file copy] i pipelinen för att automatiskt ladda upp källmapparna.

> [!div class="mx-imgBorder"]
> ![Lägga till en Azure File Copy-uppgift i pipelinen för att ladda upp källmappningarna till Azure Blob Storage](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>Konfigurera din application insights-resurs med ett lagringskonto för källkartning

### <a name="from-the-end-to-end-transaction-details-page"></a>Från sidan information från till transaktion

Från fliken transaktionsinformation från till kan du klicka på *Unminify* och det kommer att visa en uppmaning att konfigurera om din resurs är okonfigurerad.

1. I portalen visar du information om ett undantag som är minifierat.
2. Klicka på *Unminify*
3. Om resursen inte har konfigurerats visas ett meddelande som uppmanar dig att konfigurera.

### <a name="from-the-properties-page"></a>Från egenskapssidan

Om du vill konfigurera eller ändra lagringskontot eller Blob-behållaren som är länkad till application insights-resursen kan du göra det genom att visa fliken Egenskaper för Application *Insights-resursen.*

1. Navigera till fliken *Egenskaper* för din application insights-resurs.
2. Klicka på *Ändra källkarta blob container*.
3. Välj en annan Blob-behållare som källa kartor behållare.
4. Klicka på `Apply`.

> [!div class="mx-imgBorder"]
> ![Konfigurera om din valda Azure Blob Container genom att navigera till egenskapsbladet](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>Felsökning

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>RBAC-inställningar (Required Role-based Access Control) på din Blob-behållare

Alla användare på portalen som använder den här funktionen måste åtminstone tilldelas som en [Storage Blob Data Reader][storage blob data reader] till din Blob-behållare. Du måste tilldela den här rollen till alla andra som ska använda källkartorna via den här funktionen.

> [!NOTE]
> Beroende på hur behållaren skapades kanske den inte har tilldelats automatiskt till dig eller ditt team.

### <a name="source-map-not-found"></a>Källkartan hittades inte

1. Kontrollera att motsvarande källkarta överförs till rätt blob-behållare
2. Kontrollera att källkartfilen har fått sitt namn efter den JavaScript-fil som den mappar till, suffixerad med `.map`.
    - Till exempel `/static/js/main.4e2ca5fa.chunk.js` kommer att söka efter bloben som heter`main.4e2ca5fa.chunk.js.map`
3. Kontrollera webbläsarens konsol för att se om några fel loggas. Inkludera detta i alla supporter.

## <a name="next-steps"></a>Efterföljande moment

* [Uppgift om filkopiering i Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal
[storage blob data reader]: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme