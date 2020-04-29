---
title: Käll kart stöd för JavaScript-program – Azure Monitor Application Insights
description: Lär dig hur du överför käll mappningar till din egen lagrings kontots BLOB-behållare med hjälp av Application Insights.
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 03/04/2020
ms.openlocfilehash: 4b452b31338760a8f53eed54420319101836bc00
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79474891"
---
# <a name="source-map-support-for-javascript-applications"></a>Käll kart stöd för JavaScript-program

Application Insights stöder överföring av käll mappningar till din egen lagrings kontots BLOB-behållare.
Käll mappningar kan användas för att unminify anrops stackar som finns på sidan för transaktions information från slut punkt till slut punkt. Eventuella undantag som skickas av [JavaScript SDK][ApplicationInsights-JS] eller [Node. js SDK][ApplicationInsights-Node.js] kan unminified med käll kartor.

![Unminify en anrops stack genom att länka med ett lagrings konto](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>Skapa ett nytt lagrings konto och en BLOB-behållare

Om du redan har ett befintligt lagrings konto eller en BLOB-behållare kan du hoppa över det här steget.

1. [Skapa ett nytt lagringskonto][create storage account]
2. [Skapa en BLOB-behållare][create blob container] i ditt lagrings konto. Se till att ange "offentlig åtkomst nivå" till `Private`för att säkerställa att dina käll kartor inte är offentligt tillgängliga.

> [!div class="mx-imgBorder"]
>![Behållar åtkomst nivån måste vara inställd på privat](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>Push-överför källan till din BLOB-behållare

Du bör integrera en pipeline för kontinuerlig distribution med ditt lagrings konto genom att konfigurera den så att den automatiskt laddar upp dina käll mappningar till den konfigurerade BLOB-behållaren. Du bör inte ladda upp dina käll mappningar till en undermapp i BLOB-behållaren. för närvarande kommer käll kartan bara att hämtas från rotmappen.

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Ladda upp käll kartor via Azure-pipelines (rekommenderas)

Om du använder Azure-pipelines för att kontinuerligt skapa och distribuera ditt program lägger du till en [Azure-filkopiera][azure file copy] -aktivitet i din pipeline för att automatiskt ladda upp dina käll mappningar.

> [!div class="mx-imgBorder"]
> ![Lägg till en Azure File Copy-aktivitet till din pipeline för att ladda upp dina käll kartor till Azure Blob Storage](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>Konfigurera din Application Insights-resurs med ett lagrings konto för käll mappning

### <a name="from-the-end-to-end-transaction-details-page"></a>Från sidan transaktions information från slut punkt till slut punkt

Från fliken transaktions information från slut punkt till slut punkt kan du klicka på *Unminify* så visas en uppstarts dialog ruta där du uppmanas att konfigurera om din resurs har kon figurer ATS.

1. I portalen kan du Visa information om ett undantag som är minified.
2. Klicka på *Unminify*
3. Om din resurs inte har kon figurer ATS visas ett meddelande som gör att du uppmanas att konfigurera.

### <a name="from-the-properties-page"></a>Från sidan Egenskaper

Om du vill konfigurera eller ändra lagrings kontot eller BLOB-behållaren som är länkad till Application Insights resurs kan du göra det genom att Visa Application Insights resursens *Egenskaper* -flik.

1. Gå till fliken *Egenskaper* för din Application Insights-resurs.
2. Klicka på *ändra käll mappnings-BLOB-behållare*.
3. Välj en annan BLOB-behållare som din käll mappnings behållare.
4. Klicka på `Apply`.

> [!div class="mx-imgBorder"]
> ![Konfigurera om den valda Azure Blob-behållaren genom att gå till egenskaps bladet](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>Felsökning

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>Nödvändiga inställningar för rollbaserad åtkomst kontroll (RBAC) i BLOB-behållaren

Alla användare på portalen som använder den här funktionen måste minst tilldelas som en [lagrings-BLOB-dataläsare][storage blob data reader] till BLOB-behållaren. Du måste tilldela rollen till någon annan som ska använda käll mappningarna genom den här funktionen.

> [!NOTE]
> Beroende på hur behållaren skapades kanske detta inte har tilldelats automatiskt till dig eller ditt team.

### <a name="source-map-not-found"></a>Käll kartan hittades inte

1. Verifiera att motsvarande käll mappning överförs till rätt BLOB-behållare
2. Kontrol lera att käll mappnings filen är namngiven efter JavaScript-filen som den mappar till, suffix `.map`med.
    - Söker till exempel `/static/js/main.4e2ca5fa.chunk.js` efter blobben med namnet`main.4e2ca5fa.chunk.js.map`
3. Kontrol lera webbläsarens konsol för att se om några fel loggas. Ta med detta i ett support ärende.

## <a name="next-steps"></a>Nästa steg

* [Azure-fil kopierings aktivitet](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal
[storage blob data reader]: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme