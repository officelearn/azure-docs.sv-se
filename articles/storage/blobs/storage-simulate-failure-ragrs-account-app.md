---
title: Simulera ett fel vid läsåtkomst till redundant lagring i Azure | Microsoft Docs
description: Simulera ett fel vid läsåtkomst till geografiskt redundant lagring
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/23/2017
ms.author: tamram
ms.openlocfilehash: e242395df1ffb16897b73c0330f24de4857ede13
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398457"
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Simulera ett fel vid läsåtkomst till redundant lagring

Den här självstudien är del två i en serie.  I den här kursen kan du använda antingen [Fiddler](#simulate-a-failure-with-fiddler) eller [statisk routning](#simulate-a-failure-with-an-invalid-static-route) för att simulera fel för begäranden till den primära slutpunkten i ditt [Read-Access Geo Redundant](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage)-lagringskonto (RA-GRS) och få programmet att läsa från den sekundära slutpunkten.

![Scenarioprogram](media/storage-simulate-failure-ragrs-account-app/scenario.png)

För att kunna slutföra den här kursen måste du ha slutfört den tidigare lagringskursen: [Ge dina programdata hög tillgänglighet med Azure Storage][previous-tutorial].

I del två i serien lär du dig hur du:

> [!div class="checklist"]
> * Kör och pausar programmet
> * Simulerar ett fel med [Fiddler](#simulate-a-failure-with-fiddler) eller [en felaktig statisk väg](#simulate-a-failure-with-an-invalid-static-route) 
> * Simulerar en återställning av den primära slutpunkten


## <a name="prerequisites"></a>Nödvändiga komponenter

Så här simulerar du ett fel med Fiddler: 

* Ladda ned och [installera Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="simulate-a-failure-with-fiddler"></a>Simulera ett fel med Fiddler

När du simulerar ett fel med Fiddler matar du in ett misslyckat svar för begäranden till den primära slutpunkten för ditt RA-GRS-lagringskonto.

Följ stegen nedan för att simulera ett fel och återställning av den primära slutpunkten med Fiddler.

### <a name="launch-fiddler"></a>Starta Fiddler

Öppna Fiddler, välj **Rules** (Regler) och **Customize Rules** (Anpassa regler).

![Anpassa Fiddler-regler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Fiddler ScriptEditor startas och filen **SampleRules.js** visas. Den här filen används för att anpassa Fiddler. Klistra in följande kodexempel i funktionen `OnBeforeResponse`. Den nya koden är utkommenterad för att se till att logiken den skapar inte implementeras direkt. När det är klart väljer du **File** (Arkiv) och **Save** (Spara) för att spara ändringarna.

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

![Klistra in den anpassade regeln](media/storage-simulate-failure-ragrs-account-app/figure2.png)

### <a name="start-and-pause-the-application"></a>Starta och pausa programmet

Kör programmet i din IDE eller textredigerare. När programmet börjar läsa från den primära slutpunkten trycker du på **valfri tangent** i konsolfönstret för att pausa programmet.

### <a name="simulate-failure"></a>Simulera ett fel

Nu när programmet är pausat kan du utkommentera anpassningsregeln vi sparade i Fiddler i ett tidigare steg. Kodexemplet söker efter begäranden till RA-GRS-lagringskontot och om sökvägen innehåller namnet på bilden (`HelloWorld`) returnerar den svarskoden `503 - Service Unavailable`.

Navigera till Fiddler och välj **Rules** (Regler)  -> **Customize Rules...** (Anpassa regler...).  Ta bort utkommenteringen och ersätt `STORAGEACCOUNTNAME` med namnet på ditt lagringskonto. Välj **File** (Arkiv)  -> **Save** (Spara) för att spara ändringarna. 

> [!NOTE]
> Om du kör exempelprogrammet i Linux måste du starta om Fiddler varje gång du redigerar filen **CustomRule.js** för att Fiddler ska installera den anpassade logiken. 
> 
> 


```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

Tryck på **valfri tangent** för att börja köra programmet igen.

När programmet är igång igen börjar begäranden till den primära slutpunkten att misslyckas. Programmet gör fem försök att ansluta till den primära slutpunkten. När feltröskeln på fem försök har nåtts begär programmet bilden från den sekundära skrivskyddade slutpunkten. När programmet har hämtat bilden 20 gånger från den sekundära slutpunkten försöker programmet ansluta till den primära slutpunkten. Om den primära slutpunkten fortfarande inte kan nås återupptar programmet läsningen från den sekundära slutpunkten. Det här mönstret är [kretsbrytarmönstret](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) som beskrivs i den föregående kursen.

![Klistra in den anpassade regeln](media/storage-simulate-failure-ragrs-account-app/figure3.png)

### <a name="simulate-primary-endpoint-restoration"></a>Simulerar en återställning av den primära slutpunkten

Med den anpassade Fiddler-regeln som angavs i föregående steg misslyckas begäranden till den primära slutpunkten. För att kunna simulera att den primära slutpunkten fungerar igen tar du bort logiken som matar in `503`-felet.

Tryck på **valfri tangent** för att pausa programmet.

Navigera till Fiddler och välj **Rules** (Regler) och **Customize Rules...** (Anpassa regler...).  Kommentera ut eller ta bort den anpassade logiken i funktionen `OnBeforeResponse` så att bara standardfunktionen finns kvar. Välj **File** (Arkiv) och **Save** (Spara) för att spara ändringarna.

![Ta bort den anpassade regeln](media/storage-simulate-failure-ragrs-account-app/figure5.png)

När det är klart trycker du på **valfri tangent** för att återuppta körningen av programmet. Programmet fortsätter att läsa från den primära slutpunkten tills den når 999 läsningar.

![Återuppta körningen av programmet](media/storage-simulate-failure-ragrs-account-app/figure4.png)


## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simulera ett fel med en felaktig statisk väg 
Du kan skapa en felaktig statisk väg för alla begäranden till den primära slutpunkten för ditt [Read-Access Geo Redundant](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage)-lagringskonto (RA-GRS). I den här kursen används den lokala värden som gateway för dirigering av begäranden till lagringskontot. När den lokala värden används som gateway kommer alla begäranden till den primära slutpunkt för lagringskontot att utföra en loopback i värden, vilket resulterar i ett fel. Följ stegen nedan för att simulera ett fel och en återställning av den primära slutpunkten med en felaktig statisk väg. 

### <a name="start-and-pause-the-application"></a>Starta och pausa programmet

Kör programmet i din IDE eller textredigerare. När programmet börjar läsa från den primära slutpunkten trycker du på **valfri tangent** i konsolfönstret för att pausa programmet. 

### <a name="simulate-failure"></a>Simulera ett fel

Se till att programmet är pausat, starta kommandotolken i Windows som en administratör eller kör terminal som rot på Linux. Du kan få information om domänen för lagringskontots primära slutpunkt genom att ange följande kommando i en kommandotolk eller terminal.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 
 Ersätt `STORAGEACCOUNTNAME` med namnet på ditt lagringskonto. Kopiera IP-adressen för ditt lagringskonto till en textredigerare för senare användning. För att hämta IP-adressen för din lokala värd skriver du `ipconfig` i kommandotolken i Windows eller `ifconfig` i terminalen i Linux. 

Om du vill lägga till en statisk väg för en målvärd skriver du följande kommando i kommandotolken i Windows eller i terminalen i Linux. 


# <a name="linuxtablinux"></a>[Linux](#tab/linux)

  route add <destination_ip> gw <gateway_ip>

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

  route add <destination_ip> <gateway_ip>

---
 
Ersätt `<destination_ip>` med IP-adressen för ditt lagringskonto och `<gateway_ip>` med IP-adressen för din lokala värd. Tryck på **valfri tangent** för att börja köra programmet igen.

När programmet är igång igen börjar begäranden till den primära slutpunkten att misslyckas. Programmet gör fem försök att ansluta till den primära slutpunkten. När feltröskeln på fem försök har nåtts begär programmet bilden från den sekundära skrivskyddade slutpunkten. När programmet har hämtat bilden 20 gånger från den sekundära slutpunkten försöker programmet ansluta till den primära slutpunkten. Om den primära slutpunkten fortfarande inte kan nås återupptar programmet läsningen från den sekundära slutpunkten. Det här mönstret är [kretsbrytarmönstret](/azure/architecture/patterns/circuit-breaker.md) som beskrivs i den föregående kursen.

### <a name="simulate-primary-endpoint-restoration"></a>Simulerar en återställning av den primära slutpunkten

Om du vill simulera att den primära slutpunkten fungerar igen tar du bort den statiska vägen för den primära slutpunkten från routningstabellen. Detta innebär att alla begäranden till den primära slutpunkten dirigeras via standardgatewayen. 

Om du vill ta bort den statiska vägen för en målvärd skriver du följande kommando i kommandotolken i Windows eller i terminalen i Linux. 
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)

route del <destination_ip> gw <gateway_ip>

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

route delete <destination_ip>

---

Tryck på **valfri tangent** för att börja köra programmet igen. Programmet fortsätter att läsa från den primära slutpunkten tills den når 999 läsningar.

![Återuppta körningen av programmet](media/storage-simulate-failure-ragrs-account-app/figure4.png)


## <a name="next-steps"></a>Nästa steg

I den andra delen i serien lärde du dig hur du simulerade ett fel i RA-GRS, till exempel hur du:

> [!div class="checklist"]
> * Kör och pausar programmet
> * Simulerar ett fel med [Fiddler](#simulate-a-failure-with-fiddler) eller [en felaktig statisk väg](#simulate-a-failure-with-an-invalid-static-route) 
> * Simulerar en återställning av den primära slutpunkten

Läs följande artikel om du vill veta mer om hur RA-GRS-lagringen fungerar (och vilka risker det medför).

> [!div class="nextstepaction"]
> [Designa HA-appar med RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md