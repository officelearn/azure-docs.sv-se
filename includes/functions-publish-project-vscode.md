---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: d8665b4cec3357baee5d6c1b77b5719645575419
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81112848"
---
## <a name="publish-the-project-to-azure"></a>Publicera projektet på Azure

I det här avsnittet skapar du en funktionsapp och relaterade resurser i din Azure-prenumeration och distribuerar sedan din kod. 

> [!IMPORTANT]
> Om du publicerar till en befintlig funktionsapp skrivs innehållet i den appen över i Azure. 


1. Välj Azure-ikonen i aktivitetsfältet och välj sedan knappen **Distribuera för att fungera app...** i området **Azure: Functions.**

    ![Publicera ditt projekt i Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Ange följande information vid uppmaningarna:

    + **Välj prenumeration**: Välj den prenumeration som ska användas. Det ser du inte om du bara har en prenumeration.

    + **Välj funktionsapp i Azure:** Välj `+ Create new Function App`. (Välj inte alternativet, som inte ingår i den `Advanced` här artikeln.)
      
    + **Ange ett globalt unikt namn för funktionsappen**: Skriv ett namn som är giltigt i en URL-sökväg. Namnet du skriver valideras för att se till att det är unikt i Azure Functions. 
    
    ::: zone pivot="programming-language-python"
    + **Välj en körning:** Välj den version av Python som du har kört på lokalt. Du kan `python --version` använda kommandot för att kontrollera din version.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Välj en körning:** Välj den version av Node.js som du har kört på lokalt. Du kan `node --version` använda kommandot för att kontrollera din version.
    ::: zone-end

    + **Välj en plats för nya resurser**: Välj en [region](https://azure.microsoft.com/regions/) nära dig för bättre prestanda. 
    
1.  När du är klar skapas följande Azure-resurser i din prenumeration med namn baserat på funktionsappens namn:
    
    + En resursgrupp, som är en logisk behållare för relaterade resurser.
    + Ett standardkonto för Azure Storage, som upprätthåller tillstånd och annan information om dina projekt.
    + En förbrukningsplan som definierar den underliggande värden för din serverlösa funktionsapp. 
    + En funktionsapp som tillhandahåller miljön för att köra din funktionskod. Med en funktionsapp kan du gruppera funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser i samma värdplan.
    + En Application Insights-instans som är ansluten till funktionsappen och som spårar användningen av din serverlösa funktion.

    Ett meddelande visas när funktionsappen har skapats och distributionspaketet har tillämpats. 
    
1. Välj **Visa utdata** i det här meddelandet om du vill visa skapande- och distributionsresultaten, inklusive De Azure-resurser som du skapade. Om du missar meddelandet väljer du klockikonen i det nedre högra hörnet för att se den igen.

    ![Skapa fullständig avisering](media/functions-publish-project-vscode/function-create-notifications.png)
