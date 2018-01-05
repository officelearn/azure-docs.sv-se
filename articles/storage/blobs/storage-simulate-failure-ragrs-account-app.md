---
title: "Simulera ett fel vid åtkomst till redundant lagring med läsbehörighet i Azure | Microsoft Docs"
description: "Simulera ett fel vid åtkomst till geo-redundant lagring med läsbehörighet"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/05/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 151e875bd72598b0b788d68eee7fb186fca86f46
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Simulera ett fel vid åtkomst till redundant lagring med läsbehörighet

Den här kursen ingår två i en serie. I kursen får du mata in ett felmeddelande med Fiddler för begäranden till en [geo-redundant läsbehörighet](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS) lagringskonto för att simulera ett fel och har program läsa från den sekundära slutpunkten.

![Scenario-app](media/storage-simulate-failure-ragrs-account-app/scenario.png)

I del två av serien får du lära dig hur du:

> [!div class="checklist"]
> * Kör och pausa programmet
> * Simulera ett fel
> * Simulera primära endpoint återställning

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* Hämta och installera [Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Den här kursen du måste ha slutfört föregående lagring kursen: [gör dina programdata högtillgänglig med Azure storage][previous-tutorial].

## <a name="launch-fiddler"></a>Starta fiddler

Öppna Fiddler, Välj **regler** och **anpassa regler**.

![Anpassa Fiddler regler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Fiddler ScriptEditor startar visar den **SampleRules.js** fil. Den här filen används för att anpassa Fiddler. Klistra in följande kodexempel i den `OnBeforeResponse` funktionen. Den nya koden är kommenterade för att säkerställa att den logik som den skapar inte tillämpas omedelbart. En gång slutföra väljer **filen** och **spara** att spara ändringarna.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![Klistra in anpassad regel](media/storage-simulate-failure-ragrs-account-app/figure2.png)

## <a name="start-and-pause-the-application"></a>Starta och pausa programmet

I Visual Studio trycker du på **F5** eller välj **starta** att starta felsökningen av programmet. När programmet börjar läsa från den primära slutpunkten, tryck på **valfri tangent** i konsolfönstret att pausa programmet.

## <a name="simulate-failure"></a>Simulera fel

Med programmet pausats kan du nu Avkommentera anpassat regeln har sparats i Fiddler föregående steg. Den här koden exempel söker efter begäranden till RA-GRS-lagringskonto och om sökvägen innehåller namnet på avbildningen, `HelloWorld`, returneras en svarskod `503 - Service Unavailable`.

Gå till Fiddler och välj **regler** -> **anpassa regler...** .  Ta bort kommentarerna ut följande rader genom att ersätta `STORAGEACCOUNTNAME` med namnet på ditt lagringskonto. Välj **filen** -> **spara** att spara ändringarna.

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

Om du vill återuppta programmet trycker du på **valfri tangent** .

När programmet börjar köras igen, börja begäranden till den primära slutpunkten misslyckas. Programmet försöker återansluta till den primära slutpunkten 5 gånger. När tröskelvärdet fel fem gånger begär avbildningen från sekundär skrivskyddad slutpunkten. När programmet har hämtar avbildningen 20 gånger från sekundär slutpunkten, försöker programmet att ansluta till den primära slutpunkten. Om den primära slutpunkten är fortfarande inte kan nås, fortsätter programmet läsning från den sekundära slutpunkten. Det här mönstret är den [strömbrytare](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) mönster som beskrivs i föregående kursen.

![Klistra in anpassad regel](media/storage-simulate-failure-ragrs-account-app/figure3.png)

## <a name="simulate-primary-endpoint-restoration"></a>Simulera primära endpoint återställning

Med Fiddler anpassad regel som i föregående steg, misslyckas begäranden till den primära slutpunkten. För att simulera primära slutpunkten fungerar igen kan du ta bort logik för att mata in det `503` fel.

Om du vill pausa programmet trycker du på **valfri tangent**.

### <a name="remove-the-custom-rule"></a>Ta bort anpassad regel

Gå till Fiddler och välj **regler** och **anpassa regler...** .  Kommentera eller ta bort den anpassa logiken i den `OnBeforeResponse` funktion, lämnar standardfunktionen. Välj **filen** och **spara** spara ändringarna.

![Ta bort anpassad regel](media/storage-simulate-failure-ragrs-account-app/figure5.png)

När du är klar trycker du på **valfri tangent** att återställa programmet. Programmet fortsätter läsning från den primära slutpunkten tills den når 999 läsningar.

![Återuppta program](media/storage-simulate-failure-ragrs-account-app/figure4.png)

## <a name="next-steps"></a>Nästa steg

Del två seriens du lärt dig om simulering av ett fel för att testa geo-redundant lagring med läsbehörighet till exempel att:

> [!div class="checklist"]
> * Kör och pausa programmet
> * Simulera ett fel
> * Simulera primära endpoint återställning

Den här länken om du vill se förskapad lagring prover.

> [!div class="nextstepaction"]
> [Azure storage skriptexempel](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md