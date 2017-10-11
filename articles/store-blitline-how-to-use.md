---
title: "Hur du använder Blitline för avbildningen bearbetning - Azure funktion guide"
description: "Lär dig hur du använder tjänsten Blitline för att behandla bilder i ett Azure-program."
services: 
documentationcenter: .net
author: blitline-dev
manager: jason@blitline.com
editor: jason@blitline.com
ms.assetid: 6c711248-0e52-4895-ba9e-8395628de924
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2014
ms.author: support@blitline.com
ms.openlocfilehash: 1d90599e028b3407a513b04b878e3aefc39928a2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-blitline-with-azure-and-azure-storage"></a>Hur du använder Blitline med Azure och Azure Storage
Den här guiden förklarar hur du komma åt Blitline tjänster och skicka jobb till Blitline.

## <a name="what-is-blitline"></a>Vad är Blitline?
Blitline är en molnbaserad avbildning bearbetning av tjänst som tillhandahåller enterprise nivån avbildningen bearbetning på en bråkdel av det pris som den skulle kostnaden för att skapa den själv.

Faktum är att avbildningen bearbetningen har gjorts upprepade gånger, vanligtvis återskapas från grunden för varje webbplats. Vi förstår detta eftersom vi har byggt dem miljoner gånger för. En dag som vi valt kanske är det dags göra vi bara det för alla. Vi vet hur du gör den att göra det snabbt och effektivt och spara alla arbets under tiden.

Mer information finns i [http://www.blitline.com](http://www.blitline.com).

## <a name="what-blitline-is-not"></a>Vad Blitline är inte...
För att förtydliga vad Blitline är användbart för, är det ofta enklare att identifiera vad Blitline utför inte innan du går vidare.

* Blitline har inte HTML-widgetar att ladda upp bilder. Du måste ha tillgängliga avbildningarna offentligt eller med begränsade behörigheter som är tillgängliga för Blitline att nå.
* Blitline utför inte live avbildningen bearbetning, till exempel Aviary.com
* Blitline accepteras inte av bilder, kan inte vara utgivarinitierad bilderna till Blitline direkt. Du måste push-installera dem till Azure Storage eller andra platser har stöd för Blitline och berätta Blitline var hämta dem.
* Blitline är massivt parallell och utför inte någon synkron bearbetning. Vilket innebär att du måste ge oss en postback_url och vi kan meddela dig när vi är klar bearbetning.

## <a name="create-a-blitline-account"></a>Skapa ett Blitline-konto
[!INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a name="how-to-create-a-blitline-job"></a>Så här skapar du ett Blitline-jobb
Blitline använder JSON för att definiera de åtgärder som du vill ta med en bild. Den här JSON består av några enkla fält.

Det enklaste exemplet är följande:

        json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

Här är JSON som tar en avbildning ”src” ”... boys.jpeg” och sedan ändra storlek på avbildningen till 240 x 140.

Program-ID är något som du hittar i din **ANSLUTNINGSINFORMATION** eller **hantera** fliken på Azure. Det är din hemliga identifierare som gör det möjligt att köra på Blitline.

Parametern ”spara” identifierar information om var du vill placera avbildningen när vi har bearbetat den. Vi har inte definierats någon i det här fallet är trivial. Om ingen plats har definierats Blitline lagrar det lokalt (och tillfälligt) på en plats som unikt molnet. Du kommer att kunna hämta den platsen från JSON som returneras av Blitline när du gör Blitline. Identifieraren ”bild” krävs och returneras till dig när att identifiera den här viss spara bilden.

Du hittar mer information om den *funktioner* stöder vi här: <http://www.blitline.com/docs/functions>

Du kan också hitta dokumentation om jobbet alternativen: <http://www.blitline.com/docs/api>

När du har din JSON är allt du behöver göra **POST** att`http://api.blitline.com/job`

Får du JSON tillbaka som ser ut ungefär så här:

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


Du ser att Blitline har tagit emot din begäran, den har placerats i en kö för bearbetning och när den har slutförts avbildningen ska vara tillgängligt vid: **https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_APP\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a name="how-to-save-an-image-to-your-azure-storage-account"></a>Hur du sparar en avbildning i Azure Storage-konto
Om du har ett Azure Storage-konto kan har du enkelt Blitline skicka bearbetade bilder till din Azure-behållaren. Genom att lägga till en ”azure_destination” definiera plats och behörigheter för Blitline att skicka till.

Här är ett exempel:

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


Genom att fylla i CAPITALIZED värdena med dina egna, kan du skicka den här JSON till http://api.blitline.com/job och ”src” bilden kommer att bearbetas med ett filter för oskärpa och sedan pushas till Azure mål.

### <a name="please-note"></a>Obs!
SAS måste innehålla den hela SAS-url, inklusive filnamn för målfilen.

Exempel:

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


Du kan också läsa den senaste utgåvan av Blitline's Azure Storage docs [här](http://www.blitline.com/docs/azure_storage).

## <a name="next-steps"></a>Nästa steg
Besök blitline.com att läsa om våra andra funktioner:

* Blitline API-slutpunkt Docs <http://www.blitline.com/docs/api>
* Blitline API-funktioner <http://www.blitline.com/docs/functions>
* Blitline API exempel <http://www.blitline.com/docs/examples>
* Tredje Part Nuget biblioteket <http://nuget.org/packages/Blitline.Net>

