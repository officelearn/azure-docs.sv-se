---
title: 'Självstudie: Konfigurera en Azure Time Series Insights Preview-miljö | Microsoft Docs'
description: Lär dig hur du konfigurerar miljön i förhandsversionen av Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 10/02/2019
ms.custom: seodec18
ms.openlocfilehash: 1af622f4b483abec757d1392bfa4fefe91c8bc49
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990009"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Självstudie: Konfigurera en Azure Time Series Insights för hands versions miljö

Den här självstudien vägleder dig genom processen för att skapa en Azure Time Series Insights-miljö (förhandsversion) med betala per användning (PAYG). 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en Azure Time Series Insights-miljö (förhandsversion).
> * Ansluta Azure Time Series Insights-miljön (förhandsversion) till en händelsehubb i Azure Event Hubs.
> * Kör ett lösningsacceleratorexempel för att sända data till förhandsversionen av Azure Time Series Insights-miljön.
> * Utföra grundläggande analys av data.
> * Definiera en typ av och hierarki för tidsseriemodell och associera dem med dina instanser.

>[!TIP]
> [IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators) tillhandahåller förkonfigurerade lösningar i företags klass som du kan använda för att påskynda utvecklingen av anpassade IoT-lösningar.

Registrera dig för en [kostnads fri Azure-prenumeration](https://azure.microsoft.com/free/) om du inte redan har en.

## <a name="prerequisites"></a>Krav

* Ditt Azure-inloggningsnamn måste också vara medlem i prenumerationens **ägar** roll. Mer information finns i [Hantera åtkomst med hjälp av rollbaserad åtkomst kontroll och Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Skapa en enhetssimulering

I det här avsnittet ska du skapa tre simulerade enheter som skickar data till en Azure IoT Hub-instans.

1. Gå till [Sidan för Azure IoT-lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators). Sidan visar flera fördefinierade exempel. Logga in med ditt Azure-konto. Välj sedan **Enhetssimulering**.

   [Sidan![Azure IoT Solution Accelerators](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Välj **Testa nu**.

1. På sidan **skapa lösning för enhets simulering** anger du följande parametrar:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Distributions namn** | Ange ett unikt värde för en ny resurs grupp. Angivna Azure-resurser skapas och tilldelas till resursgruppen. |
    | **Azure-prenumeration** | Välj den prenumeration där du vill skapa din Time Series Insightss miljö. |
    | **Azure-plats** | Välj den region som du använde för att skapa din Time Series Insightss miljö. |
    | **Distributions alternativ** | Välj **etablera ny IoT Hub**. |
 
    Välj **skapa lösning**. Det kan ta upp till 20 minuter för lösningen att slutföra distributionen.

    [Sidan![skapa lösning för enhets simulering](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Skapa en förhands granskning PAYG-miljö

I det här avsnittet beskrivs hur du skapar en Azure Time Series Insights för hands versions miljö och ansluter den till IoT-hubben som skapats av IoT Solution Accelerator med hjälp av [Azure Portal](https://portal.azure.com/).

1. Logga in på Azure-portalen med ditt prenumerationskonto.

1. Välj **skapa en resurs**  > **Sakernas Internet**  > **Time Series Insights**.

   [![Välj Sakernas Internet och välj sedan Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. I fönstret **skapa Time Series Insights miljö** går du till fliken **grundläggande** och anger följande parametrar:

    | Parameter | Åtgärd |
    | --- | ---|
    | **Miljönamn** | Ange ett unikt namn för förhands gransknings miljön för Azure Time Series Insights. |
    | **Prenumeration** | Ange den prenumeration där du vill skapa Azure Time Series Insights för hands versions miljön. Ett bra tips är att använda samma prenumeration som resten av IoT-resurserna som skapas av enhets simulatorn. |
    | **Resursgrupp** | Välj en befintlig resurs grupp eller skapa en ny resurs grupp för den Azure Time Series Insights för hands versions miljö resursen. En resursgrupp är en container för Azure-resurser. Ett bra tips är att använda samma resurs grupp som andra IoT-resurser som skapas av enhets simulatorn. |
    | **Plats** | Välj en data Center region för din Azure Time Series Insights Preview-miljö. För att undvika ytterligare latens, är det bäst att skapa Azure Time Series Insights för hands versions miljön i samma region som dina andra IoT-resurser. |
    | **Nivå** |  Välj **PAYG** (*betala per*användning). Det här är SKU för Azure Time Series Insights-produkten (förhandsversion). |
    | **Egenskaps-ID** | Ange ett värde som unikt identifierar tids serie instansen. Värdet du anger i rutan **egenskaps-ID** är oföränderligt och kan inte ändras senare. I den här självstudien anger du **iothub-Connection-Device-ID**. Mer information om Time Series ID finns i [metod tips för att välja ett Time Series-ID](./time-series-insights-update-how-to-id.md). |
    | **Lagringskontonamn** | Ange ett globalt unikt namn för ett nytt lagrings konto för kall lagring.|
    |**Replikering av lagrings konto**|Välj den replikeringsprincip som passar din verksamhet. Mer information om olika strategier för replikering finns i [Azure Storage-redundans](../storage/common/storage-redundancy.md). |
    |**Aktivera varmt Arkiv**|Skapa ett valfritt varmt arkiv. Den kan tas bort när som helst. Med den varmt butiken kan du snabbare, obegränsade frågor över dina senaste data (upp till de senaste 31 dagarna)  |
    |**Data lagring (i dagar)**|Välj data kvarhållning i varmt arkiv. Standardvärdet är 7 dagar och kan utökas till 31 dagar |
   
   Välj **Nästa: händelse källa**.

   [![s fönstret för att skapa en Time Series Insights-miljö](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Ange följande parametrar på fliken **händelse källa** :

   | Parameter | Åtgärd |
   | --- | --- |
   | **Skapa en händelsekälla?** | Välj **Ja**.|
   | **Namn** | Ange ett unikt värde för händelse källans namn. |
   | **Källtyp** | Välj **IoT Hub**. |
   | **Välj en hubb** | Välj **Välj befintlig**. |
   | **Prenumeration** | Välj den prenumeration som du använde för enhets simulatorn. |
   | **IoT-hubbnamn** | Välj det IoT Hub-namn som du skapade för enhets simulatorn. |
   | **Åtkomstprincip för IoT-hubb** | Välj **iothubowner**. |
   | **IoT Hub konsument grupp** | Välj **Ny**, ange ett unikt namn och välj sedan **Lägg till**. Konsument gruppen måste vara ett unikt värde i Azure Time Series Insights Preview. |
   | **Egenskap för tidsstämpel** | Det här värdet används för att identifiera **timestamp** -egenskapen i dina inkommande telemetridata. I den här självstudien lämnar du rutan tom. Den här simulatorn använder den inkommande tidsstämpeln från IoT Hub som är standardinställningen för Time Series Insights. |

   Välj **Granska + skapa**.

   [![konfigurera en händelse källa](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. På fliken **Granska + skapa** granskar du dina val och väljer sedan **skapa**.

    [![granska + skapa sida med knappen Skapa](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Du kan se statusen för din distribution:

    [![meddelande om att distributionen har slutförts](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Du har åtkomst till din Azure Time Series Insights Preview-miljö om du äger klienten. Så här ser du till att du har åtkomst:

   1. Sök efter din resurs grupp och välj sedan din Azure Time Series Insights förhands gransknings miljö:

      [![vald miljö](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. På sidan Azure Time Series Insights förhands granskning väljer du **data åtkomst principer**:

      [![principer för data åtkomst](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Kontrol lera att dina autentiseringsuppgifter visas:

      [![listade autentiseringsuppgifter](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Om dina autentiseringsuppgifter inte visas måste du ge dig behörighet att komma åt miljön. Läs [Bevilja åtkomst till data](./time-series-insights-data-access.md) om du vill veta mer om hur du anger behörigheter.

## <a name="stream-data"></a>Strömma data

Nu när du har distribuerat din Time Series Insights-miljö, strömma data till den för analys.

1. Gå tillbaka till [sidan med Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators). Leta upp din lösning på din instrument panel för Solution Accelerator. Välj sedan **Starta**:

    [![starta lösningen för enhets simulering](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Du omdirigeras till sidan **Enhetssimulering för Microsoft Azure IoT**. I det övre högra hörnet på sidan väljer du **ny simulering**.

    [Sidan![Azure IoT-simulering](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. I fönstret **simulerings konfiguration** anger du följande parametrar:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Namn** | Ange ett unikt namn för en simulator. |
    | **Beskrivning** | Ange en definition. |
    | **Simuleringens varaktighet** | Ange till **Kör på obestämd tid**. |
    | **Enhetsmodell** | **Namn**: ange **kylning**. <br />**Belopp**: ange **3**. |
    | **Mål-IoT Hub** | Ange till **Använd i förväg etablerad IoT Hub**. |

    [![parametrar som ska anges](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Välj **Starta simulering**.

    På instrument panelen för enhets simulering noterar du informationen som visas för **aktiva enheter** och **meddelanden per sekund**.

    [![instrument panel för Azure IoT-simulering](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>Analysera data

I det här avsnittet utför du grundläggande analys på dina tidsseriedata med hjälp av [Azure Time Series Insights-utforskaren (förhandsversion)](./time-series-insights-update-explorer.md).

1. Gå till din Azure Time Series Insights-utforskare (förhandsversion) genom att välja URL:en från resurssidan på [Azure Portal](https://portal.azure.com/).

    [![URL: en för Preview Explorer-Time Series Insights](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. I Utforskaren väljer du noden **Time Series instances** för att se alla Azure Time Series Insights för hands versioner i miljön.

    [![lista över överordnade instanser](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Välj den första Time Series-instansen. Välj sedan **Visa tryck**.

    [![valda Time Series-instansen med meny kommando för att Visa genomsnitts tryck](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Ett tids serie diagram visas. Ändra **intervallet** till **15s**.

    [diagram över![tids serie](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Upprepa steg 3 med de andra två tids serie instanserna. Du kan visa alla Time Series-instanser som visas i det här diagrammet:

    [![diagram för alla tids serier](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. I alternativ rutan **tidsram** ändrar du tidsintervallet för att se trender för tids serier under den senaste timmen:

    [![ange tidsintervallet till en timme](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definiera och tillämpa en modell

I det här avsnittet tillämpar du en modell för att strukturera data. För att slutföra modellen definierar du typer, hierarkier och instanser. Mer information om data modellering finns i [tids serie modell](./time-series-insights-update-tsm.md).

1. I utforskaren väljer du fliken **Modell**:

   [fliken![modell i Utforskaren](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Välj **Lägg** till för att lägga till en typ:

   [![knappen Lägg till för typer](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Därefter definierar du tre variabler för typen: *tryck*, *temperatur*och *fuktighet*. I fönstret **Lägg till en typ** anger du följande parametrar:

    | Parameter | Åtgärd |
    | --- | ---|
    | **Namn** | Ange **Kylaggregat**. |
    | **Beskrivning** | Ange **Det här är en typdefinitionen för kylaggregat**. |

   * Ange följande parametrar under **variabler**för att definiera *trycket*:

     | Parameter | Åtgärd |
     | --- | ---|
     | **Namn** | Ange **Genomsnittligt tryck**. |
     | **Värde** | Välj **tryck (Double)** . Det kan ta några minuter innan **värdet** fylls i automatiskt när Azure Time Series Insights för hands versionen börjar ta emot händelser. |
     | **Sammansättningsåtgärd** | Välj **AVG**. |

      [![val för att definiera tryck](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Om du vill lägga till nästa variabel väljer du **Lägg till variabel**.

   * Definiera *temperatur*:

     | Parameter | Åtgärd |
     | --- | ---|
     | **Namn** | Ange **Genomsnittlig temperatur**. |
     | **Värde** | Välj **temperatur (Double)** . Det kan ta några minuter innan **värdet** fylls i automatiskt när Azure Time Series Insights för hands versionen börjar ta emot händelser. |
     | **Sammansättningsåtgärd** | Välj **AVG**.|

      [![val för definition av temperatur](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      Om du vill lägga till nästa variabel väljer du **Lägg till variabel**.

   * Definiera *fuktighet*:

      | | |
      | --- | ---|
      | **Namn** | Ange **maximal fuktighet** |
      | **Värde** | Välj **fuktighet (Double)** . Det kan ta några minuter innan **värdet** fylls i automatiskt när Azure Time Series Insights för hands versionen börjar ta emot händelser. |
      | **Sammansättningsåtgärd** | Välj **MAX**.|

      [![val för definition av temperatur](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    Välj **Skapa**.

    Du kan se den typ som du har lagt till:

    [![information om den tillagda typen](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. Nästa steg är att lägga till en hierarki. Välj **Lägg till**under **hierarkier**:

    [fliken![hierarkier med knappen Lägg till](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. I fönstret **Redigera hierarki** anger du följande parametrar:

   | Parameter | Åtgärd |
   | --- | ---|
   | **Namn** | Ange **Platshierarkin**. |
   | **Nivå 1** | Ange **Land**. |
   | **Nivå 2** | Ange **Ort**. |
   | **Nivå 3** | Ange **Byggnad**. |

   Välj **Spara**.

    [![hierarki fält med knappen Skapa](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Du kan se den hierarki som du skapade:

    [![information om hierarkin](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Välj **instanser**. Välj den första instansen och välj sedan **Redigera**:

    [![att välja knappen Redigera för en instans](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. I fönstret **Redigera instanser** anger du följande parametrar:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Typ** | Välj **Kylaggregat**. |
    | **Beskrivning** | Ange **Instans för kylaggregat-01.1**. |
    | **Hierarkier** | Välj **platshierarki**. |
    | **Land/region** | Ange **USA**. |
    | **Ort** | Ange **Seattle**. |
    | **Byggnad** | Ange **Space Needle**. |

    [![instans fält med knappen Spara](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   Välj **Spara**.

1. Upprepa föregående steg för de andra sensorerna. Uppdatera följande värden:

   * För Kylaggregat 01.2:

     | Parameter | Åtgärd |
     | --- | --- |
     | **Typ** | Välj **Kylaggregat**. |
     | **Beskrivning** | Ange **Instans för kylaggregat-01.2**. |
     | **Hierarkier** | Välj **platshierarki**. |
     | **Land/region** | Ange **USA**. |
     | **Ort** | Ange **Seattle**. |
     | **Byggnad** | Ange **Pacific Science Center**. |

   * För Kylaggregat 01.3:

     | Parameter | Åtgärd |
     | --- | --- |
     | **Typ** | Välj **Kylaggregat**. |
     | **Beskrivning** | Ange **instans för kylning-01,3**. |
     | **Hierarkier** | Välj **platshierarki**. |
     | **Land/region** | Ange **USA**. |
     | **Ort** | Ange **New York**. |
     | **Byggnad** | Ange **Empire State Building**. |

1. Välj fliken **analysera** och uppdatera sidan. Under **platshierarki**, expandera alla hierarkinivåer för att Visa tids serie instanserna:

   [![fliken analysera](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Ändra **snabb tiderna** till **senaste timmen**för att utforska tids serie instanserna under den senaste timmen:

    [![rutan snabb tider med den senaste timmen valt](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. Under **Pacific Science Center**väljer du Time Series-instansen och väljer sedan **Visa Max fuktighet**.

    [![valda Time Series-instanser och meny valet Visa Max fuktighet](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. Tids serien för **maximal fuktighet** med en intervall storlek på **1 minut** öppnas. Om du vill filtrera ett intervall väljer du en region. Om du vill analysera händelser i tids ramen högerklickar du på diagrammet och väljer sedan **Zooma**:

   [![markerat intervall med kommandot Zooma på en snabb meny](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Om du vill visa händelse information väljer du en region och högerklickar sedan på diagrammet:

   [![detaljerad lista över händelser](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)


## <a name="clean-up-resources"></a>Rensa resurser

Nu när du har slutfört självstudien rensar du de resurser som du har skapat:

1. På den vänstra menyn i [Azure Portal](https://portal.azure.com)väljer du **alla resurser**, letar reda på Azure Time Series Insights resurs gruppen.
1. Ta antingen bort hela resurs gruppen (och alla resurser som ingår i den) genom att välja **ta bort** eller ta bort varje resurs individuellt.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:  

> [!div class="checklist"]
> * Skapa och kör acceleratorn för enhetssimulering.
> * Skapa en Azure Time Series Insights-miljö (förhandsversion) med betala per användning.
> * Anslut Azure Time Series Insights Preview-miljö till en IoT-hubb.
> * Kör ett lösningsacceleratorexempel för att sända data till förhandsversionen av Azure Time Series Insights-miljön.
> * Utföra en grundläggande analys av data.
> * Definiera en typ av och hierarki för tidsseriemodell och associera dem med dina instanser.

Nu när du vet hur du skapar din egen Azure Time Series Insights-miljö (förhandsversion) kan du lära dig mer om viktiga begrepp i Azure Time Series Insights.

Läs om lagringskonfiguration för Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Lagring och inkommande för Azure Time Series Insights (förhandsversion)](./time-series-insights-update-storage-ingress.md)

Läs mer om Time Series-modeller:

> [!div class="nextstepaction"]
> [Datamodellering för Azure Time Series Insights (förhandsversion)](./time-series-insights-update-tsm.md)
