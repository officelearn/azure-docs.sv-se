---
title: Övervaka ett test med Azure Stack-verifiering som en tjänst | Microsoft Docs
description: Övervaka ett test med Azure Stack-verifiering som en tjänst.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 2dc4d3f2855864ff80648b5b9635ff28c0dacbb7
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163337"
---
# <a name="monitor-a-test-with-azure-stack-validation-as-a-service"></a>Övervaka ett test med Azure Stack-verifiering som en tjänst

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Körningen av ett test kan övervakas genom att visa den **Operations** för testserier som pågår eller har slutförts. Den här sidan information om statusen för testet och dess drift.

## <a name="monitor-a-test"></a>Övervaka ett test

1. Välj en lösning.

2. Välj **hantera** på valfri panel för arbetsflödet.

3. Klicka på ett arbetsflöde för att öppna dess test sammanfattningssidan.

4. Expandera snabbmenyn **[...]**  för någon testinstans suite.

5. Välj **Visa åtgärder**

![Alternativ text](media\image4.png)

För tester som har körts, loggar kan laddas ned från sammanfattningssidan test genom att klicka på **Hämtningsloggar** i ett test snabbmeny **[...]** . Azure Stack-partner kan använda de här loggarna för att felsöka problem med misslyckade tester.

## <a name="open-the-test-pass-summary"></a>Öppna pass testsammanfattning

1. Öppna portalen. 
2. Välj namnet på en befintlig lösning som innehåller tidigare kör eller schemalagda test.

    ![Hantera tester](media/managetestpasses.png)

3. Välj **hantera** i den **tester** panelen.
4. Välj testet för att öppna pass testsammanfattning. Du kan granska testnamn, skapad, köra, hur lång tid tog testet och resultatet (lyckade eller misslyckade).
5. Välj [ **...  .** ].

### <a name="test-pass-summary"></a>Pass testsammanfattning

| Kolumn | Beskrivning |
| --- | --- |
| Testnamn | Namnet på testet. Refererar till tal för verifiering. |
| Skapad | Tid i testet har skapats. |
| Startade | Tidpunkt senaste testet kördes. |
| Varaktighet | Hur länge den tid det tog att testet ska köras. |
| Status | Resultatet (lyckades eller misslyckades) för rest-pass. |
| Agentnamn | Det fullständigt kvalificerade domännamnet av agenten. |
| Totala åtgärder | Det totala antalet åtgärder i test-pass. |
| Överförda åtgärder | Antal åtgärder som skickades i test-pass. |
|  Misslyckade åtgärder | Antal åtgärder som misslyckats. |

### <a name="group-columns-in-the-test-pass-summary"></a>Grupp-kolumner i testet skicka sammanfattning

Du kan markera och dra en kolumn i huvudet för att skapa en grupp på värdet i kolumnen.

## <a name="reschedule-a-test"></a>Schemalägga om ett test

1. [Öppna pass testsammanfattning](#open-the-test-pass-summary).
2. Välj **omboka** vill schemalägga i testet.
3. Ange administratörslösenordet som molnet för din Azure Stack-instans.
4. Ange diagnostik Storage anslutningssträngen du definierade när du konfigurerar ditt konto.
5. Schemalägga om testet.

## <a name="cancel-a-test"></a>Avbryt ett test

1. [Öppna pass testsammanfattning](#open-the-test-pass-summary).
2. Välj **Avbryt**.

## <a name="get-test-information"></a>Hämta testinformation om

1. [Öppna pass testsammanfattning](#open-the-test-pass-summary).
2. Välj **visa information** vill schemalägga i testet.

**Testinformation**

| Namn | Beskrivning |
| -- | -- |
| Testnamn | Namnet på test, till exempel OEM-uppdatering på Azure Stack 1806 RC-verifiering. |
| Testversion | Versionen för testet, till exempel 5.1.4.0. |
| Utgivare | Testa utgivare, till exempel Microsoft. |
| Kategori | Kategorin för testning, till exempel **funktionell** eller **tillförlitlighet**. |
| Mål-tjänster | De tjänster som testas, till exempel virtuella datorer |
| Beskrivning | Beskrivning av testet. |
| Uppskattad varaktighet (minuter) | Hur lång tid i minuter det tog för att köra testet. |
| Länkar | En länk till GitHub Felspårningsfunktionen. |

## <a name="get-test-parameters"></a>Hämta parametrar

1. [Öppna pass testsammanfattning](#open-the-test-pass-summary).
2. Välj **Visa parametrar** vill schemalägga i testet.

**Parametrar**

| Namn | Beskrivning |
| -- | -- |
| Testnamn | Namnet på test, till exempel oemupdate1806test. |
| Testversion | Versionen av rest, till exempel 5.1.4.0. |
| Testa instans-ID | GUID som identifierar den specifika instansen av test, till exempel 20b20645-b400-4f0d-bf6f-1264d866ada9. |
| cloudAdminUser | Namnet på det konto som används som molnadministratören, till exempel **cloudadmin**. |
| DiagnosticsContainerName | ID för diagnostisk behållaren, till exempel 04dd3815-5f35-4158-92ea-698027693080. |

## <a name="get-test-operations"></a>Hämta åtgärder för testning

1. [Öppna pass testsammanfattning](#open-the-test-pass-summary).
2. Välj **Visa åtgärder** vill schemalägga i testet. Fönstret som sammanfattning öppnas.

## <a name="get-test-logs"></a>Hämta loggar testet

1. [Öppna pass testsammanfattning](#open-the-test-pass-summary).
2. Välj **Hämtningsloggar** vill schemalägga i testet.  
    En zip-fil med namnet ReleaseYYYY-MM-DD.zip som innehåller loggarna nedladdningar.

## <a name="next-steps"></a>Nästa steg

- Mer information om [Azure Stack-verifiering som en tjänst](https://docs.microsoft.com/azure/azure-stack/partner).
