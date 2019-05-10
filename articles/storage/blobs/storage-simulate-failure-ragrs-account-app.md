---
title: 'Självstudier: Simulera ett fel vid läsåtkomst till redundant lagring i Azure | Microsoft Docs'
description: Simulera ett fel vid läsåtkomst till geografiskt redundant lagring
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.reviewer: artek
ms.openlocfilehash: 1f5c404e410ded2714be761e35060f3c07379bd3
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508101"
---
# <a name="tutorial-simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Självstudier: Simulera ett fel vid läsåtkomst till redundant lagring

Den här självstudien är del två i en serie. I den lär du dig om fördelarna med [Read-Access Geo Redundant](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) genom att simulera ett fel.

För att simulera ett fel, kan du använda antingen [statisk routning](#simulate-a-failure-with-an-invalid-static-route) eller [Fiddler](#simulate-a-failure-with-fiddler). Båda metoderna gör att du kan simulera fel för begäranden till den primära slutpunkten för din [read-access geo redundant](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) lagringskonto (RA-GRS), leder till att programmet läsa från den sekundära slutpunkten i stället.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

I del två i serien lär du dig hur du:

> [!div class="checklist"]
> * Kör och pausar programmet
> * Simulera ett fel med [en felaktig statisk väg](#simulate-a-failure-with-an-invalid-static-route) eller [Fiddler](#simulate-a-failure-with-fiddler)
> * Simulerar en återställning av den primära slutpunkten

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar med den här självstudien slutför du föregående självstudie: [Ge programdata hög tillgänglighet med Azure Storage][previous-tutorial].

Om du vill simulera ett fel med statisk routning, använder du en upphöjd kommandotolk.

För att simulera ett fel med Fiddler, hämta och [installera Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simulera ett fel med en felaktig statisk väg

Du kan skapa en felaktig statisk väg för alla begäranden till den primära slutpunkten för ditt [Read-Access Geo Redundant](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage)-lagringskonto (RA-GRS). I den här kursen används den lokala värden som gateway för dirigering av begäranden till lagringskontot. När den lokala värden används som gateway kommer alla begäranden till den primära slutpunkt för lagringskontot att utföra en loopback i värden, vilket resulterar i ett fel. Följ stegen nedan för att simulera ett fel och en återställning av den primära slutpunkten med en felaktig statisk väg.

### <a name="start-and-pause-the-application"></a>Starta och pausa programmet

Följ instruktionerna i den [föregående självstudie] [ previous-tutorial] starta exemplet och ladda ned Testfilen, bekräftar du att det kommer från primär lagring. Beroende på din målplattform kan du sedan manuellt pausa exemplet eller vänta i en kommandotolk.

### <a name="simulate-failure"></a>Simulera ett fel

När programmet är pausat, öppna en kommandotolk i Windows som en administratör eller kör terminal som rot på Linux.

Få information om den primära slutpunkten kontodomänen lagring genom att ange följande kommando på ett kommando fråga eller terminal ersätta `STORAGEACCOUNTNAME` med namnet på ditt lagringskonto.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Kopiera IP-adressen för ditt lagringskonto till en textredigerare för senare användning.

För att hämta IP-adressen för din lokala värd skriver du `ipconfig` i kommandotolken i Windows eller `ifconfig` i terminalen i Linux.

För att lägga till en statisk väg för en målvärd skriver du följande kommando i Kommandotolken för Windows eller i terminalen i Linux, ersätta `<destination_ip>` med storage-konto IP-adress och `<gateway_ip>` med din lokala värd-IP-adress.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

I fönstret med köra exemplet återuppta programmet eller tryck på den att hämta filen och bekräfta att det kommer från sekundär lagring. Du kan pausa exemplet igen eller vänta i Kommandotolken.

### <a name="simulate-primary-endpoint-restoration"></a>Simulerar en återställning av den primära slutpunkten

Ta bort felaktig statisk väg från routningstabellen för att simulera den primära slutpunkten blir fungerar igen. Detta innebär att alla begäranden till den primära slutpunkten dirigeras via standardgatewayen. Skriver du följande kommando i Kommandotolken för Windows eller Linux-terminal.

#### <a name="linux"></a>Linux

```
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route delete <destination_ip>
```

Du kan sedan fortsätta till programmet eller genom att trycka på rätt nyckel för att ladda ned exemplet filen igen, den här tiden som bekräftar att det igen kommer från primär lagring.

## <a name="simulate-a-failure-with-fiddler"></a>Simulera ett fel med Fiddler

När du simulerar ett fel med Fiddler matar du in ett misslyckat svar för begäranden till den primära slutpunkten för ditt RA-GRS-lagringskonto.

Följande avsnitt visar hur du simulerar ett fel samt återställning av den primära slutpunkten med Fiddler.

### <a name="launch-fiddler"></a>Starta Fiddler

Öppna Fiddler, välj **Rules** (Regler) och **Customize Rules** (Anpassa regler).

![Anpassa Fiddler-regler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Fiddler ScriptEditor startas och visar filen **SampleRules.js**. Den här filen används för att anpassa Fiddler.

Klistra in följande kodexempel i den `OnBeforeResponse` fungera måste ersätta `STORAGEACCOUNTNAME` med namnet på ditt lagringskonto. Beroende på det här exemplet du också behöva ersätta `HelloWorld` med namnet på Testfilen (eller ett prefix som `sampleFile`) laddas ned. Den nya koden är utkommenterad för att se till att den inte köras omedelbart.

När det är klart väljer du **File** (Arkiv) och **Save** (Spara) för att spara ändringarna. Lämna fönstret ScriptEditor öppen för användning i följande steg.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error.
        // When you're ready to stop sending back errors, comment these lines of script out again
        //     and save the changes.

        if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;
        }
    */
```

![Klistra in den anpassade regeln](media/storage-simulate-failure-ragrs-account-app/figure2.png)

### <a name="start-and-pause-the-application"></a>Starta och pausa programmet

Följ instruktionerna i den [föregående självstudie] [ previous-tutorial] starta exemplet och ladda ned Testfilen, bekräftar du att det kommer från primär lagring. Beroende på din målplattform kan du sedan manuellt pausa exemplet eller vänta i en kommandotolk.

### <a name="simulate-failure"></a>Simulera ett fel

När programmet är pausat växla tillbaka till Fiddler och ta bort kommentarerna för den anpassade regeln som du sparade i den `OnBeforeResponse` funktion. Se till att välja **filen** och **spara** att spara dina ändringar så att regeln ska gälla. Den här koden söker efter begäranden till RA-GRS-lagringskonto och, om sökvägen innehåller namnet på filen exemplet returnerar svarskoden `503 - Service Unavailable`.

I fönstret med köra exemplet återuppta programmet eller tryck på den att hämta filen och bekräfta att det kommer från sekundär lagring. Du kan pausa exemplet igen eller vänta i Kommandotolken.

### <a name="simulate-primary-endpoint-restoration"></a>Simulerar en återställning av den primära slutpunkten

Ta bort i Fiddler, eller kommentera ut den anpassade regeln igen. Välj **filen** och **spara** att se till att regeln inte längre kommer att tillämpas.

I fönstret med köra exemplet återuppta programmet eller tryck på den att hämta filen och bekräfta att det kommer från primär lagring igen. Du kan sedan avsluta exemplet.

## <a name="next-steps"></a>Nästa steg

I den andra delen i serien lärde du dig hur du simulerar ett fel för att testa läsåtkomst för geo-redundant lagring.

Läs följande artikel om du vill veta mer om hur RA-GRS-lagringen fungerar samt vilka risker det medför:

> [!div class="nextstepaction"]
> [Designa HA-appar med RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
