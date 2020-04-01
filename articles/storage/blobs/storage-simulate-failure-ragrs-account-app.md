---
title: Självstudiekurs - Simulera ett fel i att läsa data från den primära regionen
titleSuffix: Azure Storage
description: Simulera ett fel i att läsa data från den primära regionen när läsåtkomst geo-redundant lagring (RA-GRS) är aktiverat för lagringskontot.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: artek
ms.openlocfilehash: 3693ae8c9c425c35ab1a4ebf88b6cd3dfb58f92c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061297"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>Självstudiekurs: Simulera ett fel i att läsa data från den primära regionen

Den här självstudien är del två i en serie. I den lär du dig om fördelarna med geo redundant lagring med [läsåtkomst](../common/storage-redundancy.md) (RA-GRS) genom att simulera ett fel.

Om du vill simulera ett fel kan du använda antingen [statisk routning](#simulate-a-failure-with-an-invalid-static-route) eller [spelman](#simulate-a-failure-with-fiddler). Båda metoderna gör att du kan simulera fel för begäranden till den primära slutpunkten för ditt [läsåtkomst-geo-redundanta](../common/storage-redundancy.md) (RA-GRS) lagringskonto, vilket gör att programmet läs avstämd från den sekundära slutpunkten i stället.

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

I del två i serien lär du dig hur du:

> [!div class="checklist"]
> * Kör och pausar programmet
> * Simulera ett fel med [en ogiltig statisk väg](#simulate-a-failure-with-an-invalid-static-route) eller [Fiddler](#simulate-a-failure-with-fiddler)
> * Simulerar en återställning av den primära slutpunkten

## <a name="prerequisites"></a>Krav

Innan du påbörjar den här självstudien slutför du den föregående självstudien: [Gör programdata mycket tillgängliga med Azure-lagring][previous-tutorial].

Om du vill simulera ett fel med statisk routning använder du en upphöjd kommandotolk.

Om du vill simulera ett fel med Fiddler laddar du ned och [installerar Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simulera ett fel med en felaktig statisk väg

Du kan skapa en felaktig statisk väg för alla begäranden till den primära slutpunkten för ditt [Read-Access Geo Redundant](../common/storage-redundancy.md)-lagringskonto (RA-GRS). I den här kursen används den lokala värden som gateway för dirigering av begäranden till lagringskontot. När den lokala värden används som gateway kommer alla begäranden till den primära slutpunkt för lagringskontot att utföra en loopback i värden, vilket resulterar i ett fel. Följ stegen nedan för att simulera ett fel och en återställning av den primära slutpunkten med en felaktig statisk väg.

### <a name="start-and-pause-the-application"></a>Starta och pausa programmet

Använd instruktionerna i [föregående självstudie för][previous-tutorial] att starta exemplet och hämta testfilen och bekräfta att den kommer från primär lagring. Beroende på målplattformen kan du sedan pausa provet manuellt eller vänta med en uppmaning.

### <a name="simulate-failure"></a>Simulera ett fel

Medan programmet är pausat öppnar du en kommandotolk i Windows som administratör eller kör terminal som root på Linux.

Hämta information om den primära slutpunktsdomänen för lagringskontot `STORAGEACCOUNTNAME` genom att ange följande kommando i en kommandotolk eller terminal och ersätta med namnet på ditt lagringskonto.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Kopiera IP-adressen för ditt lagringskonto till en textredigerare för senare användning.

För att hämta IP-adressen för din lokala värd skriver du `ipconfig` i kommandotolken i Windows eller `ifconfig` i terminalen i Linux.

Om du vill lägga till en statisk väg för en målvärd skriver `<destination_ip>` du följande kommando `<gateway_ip>` på en Windows-kommandotolk eller Linux-terminal, ersätter med ditt lagringskonto IP-adress och med din lokala värd-IP-adress.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

I fönstret med det löpprovet återupptar du programmet eller trycker på lämplig tangent för att hämta exempelfilen och bekräfta att den kommer från sekundär lagring. Du kan sedan pausa exemplet igen eller vänta på uppmaningen.

### <a name="simulate-primary-endpoint-restoration"></a>Simulerar en återställning av den primära slutpunkten

Om du vill simulera att den primära slutpunkten blir funktionell igen tar du bort den ogiltiga statiska vägen från routningstabellen. Detta innebär att alla begäranden till den primära slutpunkten dirigeras via standardgatewayen. Skriv följande kommando på en Windows-kommandotolk eller Linux-terminal.

#### <a name="linux"></a>Linux

```
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route delete <destination_ip>
```

Du kan sedan återuppta programmet eller trycka på lämplig tangent för att hämta exempelfilen igen, den här gången bekräftar att det återigen kommer från primär lagring.

## <a name="simulate-a-failure-with-fiddler"></a>Simulera ett fel med Fiddler

När du simulerar ett fel med Fiddler matar du in ett misslyckat svar för begäranden till den primära slutpunkten för ditt RA-GRS-lagringskonto.

Följande avsnitt visar hur du simulerar ett fel samt återställning av den primära slutpunkten med Fiddler.

### <a name="launch-fiddler"></a>Starta Fiddler

Öppna Fiddler, välj **Rules** (Regler) och **Customize Rules** (Anpassa regler).

![Anpassa Fiddler-regler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Fiddler ScriptEditor startas och visar filen **SampleRules.js**. Den här filen används för att anpassa Fiddler.

Klistra in följande kodexempel i `OnBeforeResponse` funktionen och ersätt `STORAGEACCOUNTNAME` med namnet på ditt lagringskonto. Beroende på exemplet kan du också `HelloWorld` behöva ersätta med namnet på testfilen `sampleFile`(eller ett prefix som ) som hämtas. Den nya koden kommenteras ut för att säkerställa att den inte körs omedelbart.

När det är klart väljer du **File** (Arkiv) och **Save** (Spara) för att spara ändringarna. Lämna skripteditorfönstret öppet för användning i följande steg.

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

Använd instruktionerna i [föregående självstudie för][previous-tutorial] att starta exemplet och hämta testfilen och bekräfta att den kommer från primär lagring. Beroende på målplattformen kan du sedan pausa provet manuellt eller vänta med en uppmaning.

### <a name="simulate-failure"></a>Simulera ett fel

Medan programmet pausas växlar du tillbaka till Fiddler och avkommentar den anpassade regeln som du sparade i `OnBeforeResponse` funktionen. Var noga med att välja **Arkiv** och **Spara** för att spara ändringarna så att regeln börjar gälla. Den här koden söker efter begäranden till RA-GRS-lagringskontot och returnerar en `503 - Service Unavailable`svarskod för .

I fönstret med det löpprovet återupptar du programmet eller trycker på lämplig tangent för att hämta exempelfilen och bekräfta att den kommer från sekundär lagring. Du kan sedan pausa exemplet igen eller vänta på uppmaningen.

### <a name="simulate-primary-endpoint-restoration"></a>Simulerar en återställning av den primära slutpunkten

I Fiddler tar du bort eller kommenterar den anpassade regeln igen. Välj **Arkiv** och **Spara** om du vill vara säkra på att regeln inte längre gäller.

I fönstret med det löpprovet återupptar du programmet eller trycker på lämplig tangent för att hämta exempelfilen och bekräfta att den kommer från primärlagring igen. Du kan sedan avsluta provet.

## <a name="next-steps"></a>Nästa steg

I den andra delen i serien lärde du dig hur du simulerar ett fel för att testa läsåtkomst för geo-redundant lagring.

Läs följande artikel om du vill veta mer om hur RA-GRS-lagringen fungerar samt vilka risker det medför:

> [!div class="nextstepaction"]
> [Designa HA-appar med RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
