---
title: 'Självstudier: Simulera ett fel vid läsåtkomst till redundant lagring i Azure | Microsoft Docs'
description: Simulera ett fel vid läsåtkomst till geografiskt redundant lagring
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.openlocfilehash: 2d86c0c4838fb2ae9d839e64a067824019133d8b
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024441"
---
# <a name="tutorial-simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Självstudier: Simulera ett fel vid läsåtkomst till redundant lagring

Den här självstudien är del två i en serie. I den lär du dig om fördelarna med [Read-Access Geo Redundant](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) genom att simulera ett fel.

För att simulera ett fel kan du använda antingen [Fiddler](#simulate-a-failure-with-fiddler) eller [statisk routning](#simulate-a-failure-with-an-invalid-static-route). Endera metoden kan användas för simulering av fel för begäranden till den primära slutpunkten i ditt [Read-Access Geo Redundant](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage)-lagringskonto (RA-GRS), vilket gör att programmet läser från den sekundära slutpunkten i stället.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

I del två i serien lär du dig hur du:

> [!div class="checklist"]
> * Kör och pausar programmet
> * Simulerar ett fel med [Fiddler](#simulate-a-failure-with-fiddler) eller [en felaktig statisk väg](#simulate-a-failure-with-an-invalid-static-route) 
> * Simulerar en återställning av den primära slutpunkten

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar med den här självstudien slutför du föregående självstudie: [Ge programdata hög tillgänglighet med Azure Storage][previous-tutorial].

Så här simulerar du ett fel med Fiddler: 

* Ladda ned och [installera Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-fiddler"></a>Simulera ett fel med Fiddler

När du simulerar ett fel med Fiddler matar du in ett misslyckat svar för begäranden till den primära slutpunkten för ditt RA-GRS-lagringskonto.

Följande avsnitt visar hur du simulerar ett fel samt återställning av den primära slutpunkten med Fiddler.

### <a name="launch-fiddler"></a>Starta Fiddler

Öppna Fiddler, välj **Rules** (Regler) och **Customize Rules** (Anpassa regler).

![Anpassa Fiddler-regler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Fiddler ScriptEditor startas och visar filen **SampleRules.js**. Den här filen används för att anpassa Fiddler.

Klistra in följande kodexempel i funktionen `OnBeforeResponse`. Den nya koden är utkommenterad för att se till att logiken den skapar inte implementeras direkt.

När det är klart väljer du **File** (Arkiv) och **Save** (Spara) för att spara ändringarna.

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

### <a name="interrupting-the-application"></a>Avbryta programmet

# <a name="net-python-and-java-v7-tabdotnet-python-java-v7"></a>[.NET, Python och Java v7] (#tab/dotnet-python-java-v7)

Kör programmet i din IDE eller ditt gränssnitt.

När programmet börjar läsa från den primära slutpunkten trycker du på **valfri tangent** i konsolfönstret för att pausa programmet.

![Scenarioprogram](media/storage-simulate-failure-ragrs-account-app/scenario.png)

# <a name="java-v10-tabjava-v10"></a>[Java v10] (#tab/Java-v10)

Kör programmet i din IDE eller ditt gränssnitt.

Eftersom du styr exemplet behöver du inte avbryta det för att simulera ett fel. Kontrollera bara att filen har laddats upp till ditt lagringskonto genom att köra exemplet och ange **P**.

![Scenarioprogram](media/storage-simulate-failure-ragrs-account-app/Java-put-list-output.png)

---

### <a name="simulate-failure"></a>Simulera ett fel

När programmet är pausat avkommenterar du den anpassade regeln som vi sparade i Fiddler.

Kodexemplet söker efter begäranden till RA-GRS-lagringskontot, och om sökvägen innehåller namnet på filen (`HelloWorld`) returnerar det svarskoden `503 - Service Unavailable`.

Navigera till Fiddler och välj **Rules** (Regler)  -> **Customize Rules...** (Anpassa regler...).

Avkommentera följande rader och ersätt `STORAGEACCOUNTNAME` med namnet på ditt lagringskonto. Välj **File** (Arkiv)  -> **Save** (Spara) för att spara ändringarna. 

> [!NOTE]
> Om du kör exempelprogrammet i Linux måste du starta om Fiddler varje gång du redigerar filen **CustomRule.js** för att Fiddler ska installera den anpassade logiken.

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

# <a name="net-python-and-java-v7-tabdotnet-python-java-v7"></a>[.NET, Python och Java v7] (#tab/dotnet-python-java-v7)

Tryck på **valfri tangent** för att börja köra programmet igen.

När programmet är igång igen börjar begäranden till den primära slutpunkten att misslyckas. Programmet gör fem försök att ansluta till den primära slutpunkten. När feltröskeln på fem försök har nåtts begär programmet bilden från den sekundära skrivskyddade slutpunkten. När programmet har hämtat bilden 20 gånger från den sekundära slutpunkten försöker det ansluta till den primära slutpunkten. Om den primära slutpunkten fortfarande inte kan nås återupptar programmet läsningen från den sekundära slutpunkten.

Det här mönstret är [kretsbrytarmönstret](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) som beskrivs i den föregående kursen.

![Klistra in den anpassade regeln](media/storage-simulate-failure-ragrs-account-app/figure3.png)

# <a name="java-v10-tabjava-v10"></a>[Java v10] (#tab/Java-v10)

Nu när du har infört felet anger du **G** för att testa felet.

Det informerar dig om att det använder den sekundära pipelinen i stället för den primära pipelinen.

---

### <a name="simulate-primary-endpoint-restoration"></a>Simulerar en återställning av den primära slutpunkten

# <a name="net-python-and-java-v7-tabdotnet-python-java-v7"></a>[.NET, Python och Java v7] (#tab/dotnet-python-java-v7)

Med den anpassade Fiddler-regeln som angavs i föregående steg misslyckas begäranden till den primära slutpunkten.

För att kunna simulera att den primära slutpunkten fungerar igen tar du bort logiken som matar in `503`-felet.

Tryck på **valfri tangent** för att pausa programmet.

Navigera till Fiddler och välj **Rules** (Regler) och **Customize Rules...** (Anpassa regler...). 

Kommentera ut eller ta bort den anpassade logiken i funktionen `OnBeforeResponse` så att bara standardfunktionen finns kvar.

Välj **File** (Arkiv) och **Save** (Spara) för att spara ändringarna.

![Ta bort den anpassade regeln](media/storage-simulate-failure-ragrs-account-app/figure5.png)

När det är klart trycker du på **valfri tangent** för att återuppta körningen av programmet. Programmet fortsätter att läsa från den primära slutpunkten tills den når 999 läsningar.

![Återuppta körningen av programmet](media/storage-simulate-failure-ragrs-account-app/figure4.png)

# <a name="java-v10-tabjava-v10"></a>[Java v10] (#tab/Java-v10)

Med den anpassade Fiddler-regeln som angavs i föregående steg misslyckas begäranden till den primära slutpunkten.

För att kunna simulera att den primära slutpunkten fungerar igen tar du bort logiken som matar in `503`-felet.

Navigera till Fiddler och välj **Rules** (Regler) och **Customize Rules...** (Anpassa regler...).  Kommentera ut eller ta bort den anpassade logiken i funktionen `OnBeforeResponse` så att bara standardfunktionen finns kvar.

Välj **File** (Arkiv) och **Save** (Spara) för att spara ändringarna.

När det är klart anger du **G** för att testa nedladdningen. Programmet rapporterar att det nu använder den primära pipelinen igen.

---

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simulera ett fel med en felaktig statisk väg

Du kan skapa en felaktig statisk väg för alla begäranden till den primära slutpunkten för ditt [Read-Access Geo Redundant](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage)-lagringskonto (RA-GRS). I den här kursen används den lokala värden som gateway för dirigering av begäranden till lagringskontot. När den lokala värden används som gateway kommer alla begäranden till den primära slutpunkt för lagringskontot att utföra en loopback i värden, vilket resulterar i ett fel. Följ stegen nedan för att simulera ett fel och en återställning av den primära slutpunkten med en felaktig statisk väg. 

### <a name="start-and-pause-the-application"></a>Starta och pausa programmet

# <a name="net-python-and-java-v7-tabdotnet-python-java-v7"></a>[.NET, Python och Java v7] (#tab/dotnet-python-java-v7)

Kör programmet i din IDE eller ditt gränssnitt. När programmet börjar läsa från den primära slutpunkten trycker du på **valfri tangent** i konsolfönstret för att pausa programmet.

# <a name="java-v10-tabjava-v10"></a>[Java v10] (#tab/Java-v10)

Eftersom du styr exemplet behöver du inte avbryta det för att testa ett fel.

Kontrollera att filen har laddats upp till ditt lagringskonto genom att köra exemplet och ange **P**.

---

### <a name="simulate-failure"></a>Simulera ett fel

Se till att programmet är pausat, starta kommandotolken i Windows som en administratör eller kör terminal som rot på Linux.

Hämta information om domänen för lagringskontots primära slutpunkt genom att ange följande kommando i en kommandotolk eller terminal.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 
 Ersätt `STORAGEACCOUNTNAME` med namnet på ditt lagringskonto. Kopiera IP-adressen för ditt lagringskonto till en textredigerare för senare användning.

För att hämta IP-adressen för din lokala värd skriver du `ipconfig` i kommandotolken i Windows eller `ifconfig` i terminalen i Linux. 

Om du vill lägga till en statisk väg för en målvärd skriver du följande kommando i kommandotolken i Windows eller i terminalen i Linux. 

#### <a name="linux"></a>Linux

`route add <destination_ip> gw <gateway_ip>`

#### <a name="windows"></a>Windows

`route add <destination_ip> <gateway_ip>`

Ersätt `<destination_ip>` med IP-adressen för ditt lagringskonto och `<gateway_ip>` med IP-adressen för din lokala värd.

# <a name="net-python-and-java-v7-tabdotnet-python-java-v7"></a>[.NET, Python och Java v7] (#tab/dotnet-python-java-v7)

Tryck på **valfri tangent** för att börja köra programmet igen.

När programmet är igång igen börjar begäranden till den primära slutpunkten att misslyckas. Programmet gör fem försök att ansluta till den primära slutpunkten. När feltröskeln på fem försök har nåtts begär programmet bilden från den sekundära skrivskyddade slutpunkten. När programmet har hämtat bilden 20 gånger från den sekundära slutpunkten försöker programmet ansluta till den primära slutpunkten. Om den primära slutpunkten fortfarande inte kan nås återupptar programmet läsningen från den sekundära slutpunkten. Det här mönstret är [kretsbrytarmönstret](/azure/architecture/patterns/circuit-breaker) som beskrivs i den föregående kursen.

# <a name="java-v10-tabjava-v10"></a>[Java v10] (#tab/Java-v10)

Nu när du har infört felet anger du **G** för att testa felet. Det informerar dig om att det använder den sekundära pipelinen i stället för den primära pipelinen.

---

### <a name="simulate-primary-endpoint-restoration"></a>Simulerar en återställning av den primära slutpunkten

Om du vill simulera att den primära slutpunkten fungerar igen tar du bort den statiska vägen för den primära slutpunkten från routningstabellen. Detta innebär att alla begäranden till den primära slutpunkten dirigeras via standardgatewayen.

Om du vill ta bort den statiska vägen för en målvärd skriver du följande kommando i kommandotolken i Windows eller i terminalen i Linux.

#### <a name="linux"></a>Linux

`route del <destination_ip> gw <gateway_ip>`

#### <a name="windows"></a>Windows

`route delete <destination_ip>`

# <a name="net-python-and-java-v7-tabdotnet-python-java-v7"></a>[.NET, Python och Java v7] (#tab/dotnet-python-java-v7)

Tryck på **valfri tangent** för att börja köra programmet igen. Programmet fortsätter att läsa från den primära slutpunkten tills den når 999 läsningar.

![Återuppta körningen av programmet](media/storage-simulate-failure-ragrs-account-app/figure4.png)


# <a name="java-v10-tabjava-v10"></a>[Java v10] (#tab/Java-v10)

Ange **G** för att testa nedladdningen. Programmet rapporterar att det nu använder den primära pipelinen igen.

![Återuppta körningen av programmet](media/storage-simulate-failure-ragrs-account-app/java-get-pipeline-example-v10.png)

---

## <a name="next-steps"></a>Nästa steg

I den andra delen i serien lärde du dig hur du simulerar ett fel för att testa läsåtkomst för geo-redundant lagring.

Läs följande artikel om du vill veta mer om hur RA-GRS-lagringen fungerar samt vilka risker det medför:

> [!div class="nextstepaction"]
> [Designa HA-appar med RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md