---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: fb3935a58e1bc4e1ab3c582d9f3b84b87349aef7
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928039"
---
## <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.

> [!NOTE]
> Om du är registrerad för att använda delade Image Galleries i förhandsversionen kan du behöva registrera den `Microsoft.Compute` provider. Öppna [Cloud Shell](https://shell.azure.com/bash) och skriv: `az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>Skapa ett avbildningsgalleri

Ett galleri med avbildningar är den primära resursen som används för att aktivera delning av avbildningen. Tillåtna tecken för namn på galleriet är versaler, gemener, siffror, punkter och punkter. Namn på galleriet får inte innehålla bindestreck.  Galleriet namn måste vara unikt inom prenumerationen. 

I följande exempel skapas ett galleri med namnet *myGallery* i den *myGalleryRG* resursgrupp.

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.
1. Använda typen **delad bildgalleriet** i sökrutan och välj **delad bildgalleriet** i resultaten.
1. I den **delad bildgalleriet** klickar du på **skapa**.
1. Välj rätt prenumeration.
1. I **resursgrupp**väljer **Skapa nytt** och skriv *myGalleryRG* för namnet.
1. I **namn**, typ *myGallery* som namn på galleriet.
1. Låt standardvärdet för **Region**.
1. Du kan skriva en kort beskrivning av galleriet, t.ex *min bildgalleriet för testning.* och klicka sedan på **granska + skapa**.
1. När valideringen är godkänd väljer **skapa**.
1. När distributionen är klar väljer du **gå till resurs**.
   
## <a name="create-an-image-definition"></a>Skapa en definition för avbildning 

Bild definitioner skapa en logisk gruppering för avbildningar. De används för att hantera information om vilka avbildningsversioner som skapas i dem. Namn på definition bilder kan bestå av versaler, gemener, siffror, punkter, bindestreck och punkter. Läs mer om de värden som du kan ange för en avbildningsdefinitionen [bild definitioner](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Skapa definitionen för galleriet bilden i galleriet. I det här exemplet heter galleriet bilden *myImageDefinition*.

1. På sidan för din nya bildgalleriet väljer **lägger du till en ny bild** högst upp på sidan. 
1. För **definition avbildningsnamn**, typ *myImageDefinition*.
1. För **operativsystemet**, Välj rätt alternativ baserat på din Källavbildningen.
1. För **Publisher**, typ *myPublisher*. 
1. För **erbjuder**, typ *myOffer*.
1. För **SKU**, typ *mySKU*.
1. Se till att **Ja** oss valts för **aktivera** och välj sedan **granska + skapa**.
1. När avbildningsdefinitionen har klarat verifieringen väljer **skapa**.
1. När distributionen är klar väljer du **gå till resurs**.



## <a name="create-an-image-version"></a>Skapa en Bildversion

Skapa en Avbildningsversion från en hanterad avbildning. I det här exemplet är versionsnumret för avbildningen *1.0.0* och den replikeras till både *USA, västra centrala* och *södra centrala USA* datacenter. När du väljer målregioner för replikering, komma ihåg att du även inkludera den *källa* region som mål för replikering.

Tillåtna tecken för Avbildningsversion är siffror och punkter. Måste vara ett nummer i intervallet för ett 32-bitars heltal. Format: *MajorVersion*. *LägreVersion*. *Patch*.

1. På sidan för din avbildningsdefinitionen Välj **Lägg till version** högst upp på sidan.
1. I **Region**, väljer du den region där din hanterade avbildningen finns sparad. Bild-versioner måste skapas i samma region som hanterad avbildning de skapas från.
1. För **namn**, typ *1.0.0*. Version Avbildningsnamnet bör följa *större*. *mindre*. *patch* formatera med heltal. 
1. I **Källavbildningen**, Välj din hanterade Källavbildningen från listrutan.
1. I **exkludera från den senaste**, lämna standardvärdet *nr*.
1. För **slutet av liv datum**, Välj ett datum i kalendern som är några månader i framtiden.
1. I **replikering**, lämna den **standard replikantal** som 1. Du behöver att replikera till källregionen, se till att den första repliken som standard och välj en andra region som repliken ska vara *USA, östra*.
1. När du är klar väljer du **granska + skapa**. Azure verifierar konfigurationen.
1. När Avbildningsversion har klarat verifieringen väljer **skapa**.
1. När distributionen är klar väljer du **gå till resurs**.

Det kan ta en stund att replikera avbildningen till alla målregionerna som.
