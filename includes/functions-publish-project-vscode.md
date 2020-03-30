---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 256510f855256e648ae9203f46eb9f66c9ffaed6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77029275"
---
## <a name="publish-the-project-to-azure"></a>Publicera projektet på Azure

I det här avsnittet skapar du en funktionsapp och relaterade resurser i din Azure-prenumeration och distribuerar sedan din kod. 

1. Välj Azure-ikonen i aktivitetsfältet och välj sedan knappen **Distribuera för att fungera app...** i området **Azure: Functions.**

    ![Publicera ditt projekt i Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Ange följande information vid uppmaningarna:

    + **Välj prenumeration**: Välj den prenumeration som ska användas. Det ser du inte om du bara har en prenumeration.

    + **Välj Funktionsapp**i `+ Create new Function App` Azure `Advanced`: Välj (inte ). Den här artikeln stöder inte det [avancerade publiceringsflödet](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options). 
    
    >[!IMPORTANT]
    > Om du publicerar till en befintlig funktionsapp skrivs innehållet i den appen över i Azure. 
    
    + **Ange ett globalt unikt namn för funktionsappen**: Skriv ett namn som är giltigt i en URL-sökväg. Namnet du skriver valideras för att se till att det är unikt i Azure Functions. 
    
    ::: zone pivot="programming-language-python"
    + **Välj en körning:** Välj den version av Python som du har kört på lokalt. Du kan `python --version` använda kommandot för att kontrollera din version.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Välj en körning:** Välj den version av Node.js som du har kört på lokalt. Du kan `node --version` använda kommandot för att kontrollera din version.
    ::: zone-end

    + **Välj en plats för nya resurser**: Välj en [region](https://azure.microsoft.com/regions/) nära dig för bättre prestanda. 
    
1.  När du är klar skapas följande Azure-resurser i din prenumeration:

    + **[Resursgrupp](../articles/azure-resource-manager/management/overview.md)**: Innehåller alla skapade Azure-resurser. Namnet baseras på funktionsappens namn.
    + **[Lagringskonto](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)**: Ett standardlagringskonto skapas med ett unikt namn som baseras på funktionsappnamnet.
    + **[Värdplan](../articles/azure-functions/functions-scale.md)**: En förbrukningsplan skapas i regionen västra USA för att vara värd för din serverlösa funktionsapp.
    + **Funktionsapp:** Projektet distribueras till och körs i den här nya funktionsappen.
    + **Application Insights**: En instans som är ansluten till din funktionsapp skapas baserat på ditt funktionsnamn.

    Ett meddelande visas när funktionsappen har skapats och distributionspaketet har tillämpats. 
    
1. Välj **Visa utdata** i det här meddelandet om du vill visa skapande- och distributionsresultaten, inklusive De Azure-resurser som du skapade. Om du missar meddelandet väljer du klockikonen i det nedre högra hörnet för att se den igen.

    ![Skapa fullständig avisering](media/functions-publish-project-vscode/function-create-notifications.png)
