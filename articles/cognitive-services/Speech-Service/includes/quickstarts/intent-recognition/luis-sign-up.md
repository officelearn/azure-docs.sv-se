---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: a41c9dc888e85baf021712e3302da7ae6106db47
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422008"
---
För att slutföra snabbstarten för avsiktsigenkänning måste du skapa ett LUIS-konto och ett projekt med luis-förhandsgranskningsportalen. Den här snabbstarten kräver bara en LUIS-prenumeration. En prenumeration på taltjänsten krävs *inte.*

Det första du behöver göra är att skapa ett LUIS-konto och app med luis-förhandsgranskningsportalen. LUIS-appen som du skapar använder en fördefinierad domän för hemautomatisering, som innehåller avsikter, entiteter och exempelyttranden. När du är klar har du en LUIS-slutpunkt som körs i molnet som du kan anropa med tal-SDK. 

Så här skapar du LUIS-appen:

* <a href="https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Snabbstart: Skapa fördefinierad domänapp<span class="docon docon-navigate-external x-hidden-focus"></span></a>

När du är klar behöver du fyra saker:

* Återpublicera med **Talpriming** växlade på
* Din LUIS **primärnyckel**
* Din **LUIS-plats**
* Ditt **LUIS-app-ID**

Här hittar du den här informationen i [LUIS-förhandsgranskningsportalen:](https://preview.luis.ai/)

1. På LUIS-förhandsgranskningsportalen väljer du appen och väljer sedan knappen **Publicera.**

2. Välj **produktionsplats** om du använder `en-US` alternativet **Talpriming** till **påläge.** Välj sedan knappen **Publicera.**

    > [!IMPORTANT]
    > **Talpriming** rekommenderas starkt eftersom det kommer att förbättra taligenkänningsnoggrannheten.

    > [!div class="mx-imgBorder"]
    > ![Publicera LUIS till slutpunkt](../../../media/luis/publish-app-popup.png)

3. Välj **Hantera**på LUIS-förhandsgranskningsportalen och välj sedan **Azure Resources**. På den här sidan hittar du luis-tangenten och platsen (kallas ibland _region)._

   > [!div class="mx-imgBorder"]
   > ![LUIS-nyckel och plats](../../../media/luis/luis-key-region.png)

4. När du har fått din nyckel och plats behöver du app-ID: n. Välj **Programinställningar** – ditt app-ID är tillgängligt på den här sidan.

   > [!div class="mx-imgBorder"]
   > ![LUIS-app-ID](../../../media/luis/luis-app-id.png)
