---
ms.openlocfilehash: 5fa8377b3cdddb2818c04c5a75e347ff73dc07d8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509120"
---
I den här självstudien krävs följande Azure-resurser:

* IoT Hub
* Lagringskonto
* Azure Media Services konto
* Virtuella Linux-datorer i Azure, med [IoT Edge runtime](../../../../../iot-edge/how-to-install-iot-edge.md) installerat

I den här snabb starten rekommenderar vi att du använder [installations skriptet Live Video Analytics-resurser](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) för att distribuera de nödvändiga resurserna i din Azure-prenumeration. Det gör du på följande sätt:

1. Öppna [Azure Cloud Shell](https://shell.azure.com).
1. Om du använder Cloud Shell för första gången uppmanas du att välja en prenumeration för att skapa ett lagrings konto och en Microsoft Azure fil resurs. Välj **skapa lagring** för att skapa ett lagrings konto för din Cloud Shell sessionsinformation. Det här lagrings kontot är skilt från det konto som skriptet kommer att skapa för att använda med ditt Azure Media Services-konto.
1. I den nedrullningsbara menyn på vänster sida av Cloud Shells fönstret väljer du **bash** som din miljö.

    ![Miljö väljare](../../../media/quickstarts/env-selector.png)
1. Kör följande kommando.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Om skriptet har slutförts bör du se alla nödvändiga resurser i din prenumeration.
1. När skriptet har körts väljer du klammerparenteserna för att exponera mappstrukturen. Du ser några filer i katalogen *~/clouddrive/lva-Sample* . Intressanthet i den här snabb starten är:

     * ***~/clouddrive/lva-Sample/Edge-Deployment/.env** _ – den här filen innehåller egenskaper som Visual Studio Code använder för att distribuera moduler till en Edge-enhet.
     _ ***~/clouddrive/lva-sample/appsetting.jspå*** -Visual Studio Code använder den här filen för att köra exempel koden.
     
    Du behöver dessa filer när du konfigurerar din utvecklings miljö i Visual Studio Code i nästa avsnitt. Du kanske vill kopiera dem till en lokal fil för tillfället.
    
    ![Appinställningar](../../../media/quickstarts/clouddrive.png)
