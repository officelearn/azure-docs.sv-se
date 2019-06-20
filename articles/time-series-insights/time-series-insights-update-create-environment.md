---
title: 'Självstudier: Konfigurera en Azure Time Series Insights-miljö (förhandsversion) | Microsoft Docs'
description: Lär dig hur du konfigurerar miljön i förhandsversionen av Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 06/18/2019
ms.custom: seodec18
ms.openlocfilehash: 824d24b97f192583a42192b3bb90eb1818e1aa18
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273055"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Självstudie: Konfigurera en Azure Time Series Insights-miljö (förhandsversion)

Den här självstudien vägleder dig genom processen för att skapa en Azure Time Series Insights-miljö (förhandsversion) med betala per användning (PAYG). 

I den här guiden får du lära dig att:

* Skapa en Azure Time Series Insights-miljö (förhandsversion).
* Ansluta Azure Time Series Insights-miljön (förhandsversion) till en händelsehubb i Azure Event Hubs.
* Kör ett lösningsacceleratorexempel för att sända data till förhandsversionen av Azure Time Series Insights-miljön.
* Utföra grundläggande analys av data.
* Definiera en typ av och hierarki för tidsseriemodell och associera dem med dina instanser.

>[!TIP]
> [IoT-Lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators) tillhandahåller företagsklass förkonfigurerade lösningar som du kan använda för att påskynda utvecklingen av anpassade IoT-lösningar.

## <a name="create-a-device-simulation"></a>Skapa en enhetssimulering

I det här avsnittet skapar du tre simulerade enheter som skickar data till en Azure IoT Hub-instans.

1. Gå till [Sidan för Azure IoT-lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators). Sidan visar flera fördefinierade exempel. Logga in med ditt Azure-konto. Välj sedan **Enhetssimulering**.

   [![Acceleratorer sida för Azure IoT-lösning](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Välj **Testa nu**.

1. På den **skapa Enhetssimulering lösning** ställer du in följande parametrar:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Distributionsnamn** | Ange ett unikt värde för en ny resursgrupp. Angivna Azure-resurser skapas och tilldelas till resursgruppen. |
    | **Azure-prenumeration** | Välj den prenumeration som du använde för att skapa en Time Series Insights-miljö. |
    | **Azure-plats** | Välj den region som du använde för att skapa en Time Series Insights-miljö. |
    | **Distributionsalternativ** | Välj **etablera nya IoT-hubben**. |
 
    Välj **skapa lösning**. Det kan ta upp till 20 minuter för lösningen att slutföra distributionen.

    [![Skapa Enhetssimulering lösningssida](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Skapa en Time Series Insights-miljö (förhandsversion) med PAYG (betala per användning)

Det här avsnittet beskrivs hur du skapar en förhandsversion av Azure Time Series Insights-miljö och ansluter den till IoT-hubb som skapats av IoT Solution Accelerator med hjälp av den [Azure-portalen](https://portal.azure.com/).

1. Logga in på Azure-portalen med ditt prenumerationskonto.

1. Välj **skapa en resurs** > **Internet of Things** > **Time Series Insights**.

   [![Välj Sakernas Internet och välj sedan Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. I den **skapa Time Series Insights-miljö** fönstret på den **grunderna** fliken genom att ange följande parametrar:

    | Parameter | Åtgärd |
    | --- | ---|
    | **Miljönamn** | Ange ett unikt namn för förhandsversionen av Azure Time Series Insights-miljö. |
    | **Prenumeration** | Anger du prenumerationen där du vill skapa förhandsversion av Azure Time Series Insights-miljö. Ett bra tips är att använda samma prenumeration som resten av IoT-resurser som skapas av enhetssimulatorn. |
    | **Resursgrupp** | Välj en befintlig resursgrupp eller skapa en ny resursgrupp för resursen för förhandsversionen av Azure Time Series Insights-miljö. En resursgrupp är en container för Azure-resurser. Ett bra tips är att använda samma resursgrupp som de andra IoT-resurser som skapas av enhetssimulatorn. |
    | **Location** | Välj en datacenterregion för förhandsversionen av Azure Time Series Insights-miljön. Det är bäst att skapa en förhandsversion av Azure Time Series Insights-miljö i samma region som andra IoT-resurser för att undvika ytterligare fördröjning. |
    | **Nivå** |  Välj **PAYG** (*användningsbaserad*). Det här är SKU för Azure Time Series Insights-produkten (förhandsversion). |
    | **Egenskaps-ID** | Ange ett värde som unikt identifierar din time series-instans. Du har angett i den **egenskaps-ID** box kan inte ändras. Du kan inte ändra den senare. Den här självstudien anger **iothub-anslutning-enhet-id**. Läs mer om Time Series-ID i [bästa praxis för att välja en Time-ID](./time-series-insights-update-how-to-id.md). |
    | **Lagringskontonamn** | Ange ett globalt unikt namn för ett nytt lagringskonto att skapa. |
   
   Välj **Nästa: Händelsekälla**.

   [![Fönstret för att skapa en Time Series Insights-miljö](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. På den **händelsekälla** fliken genom att ange följande parametrar:

   | Parameter | Åtgärd |
   | --- | --- |
   | **Skapa en händelsekälla?** | Välj **Ja**.|
   | **Namn** | Ange ett unikt värde för händelsekällans namn. |
   | **Källtyp** | Välj **IoT-hubb**. |
   | **Välj en hubb** | Välj **Välj befintlig**. |
   | **Prenumeration** | Välj den prenumeration som du använde för enhetssimulatorn. |
   | **IoT-hubbnamn** | Välj namnet på det IoT-hubb som du skapade för enhetssimulatorn. |
   | **Åtkomstprincip för IoT-hubb** | Välj **iothubowner**. |
   | **IoT Hub-konsumentgrupp** | Välj **Ny**, ange ett unikt namn och välj sedan **Lägg till**. Konsumentgruppen måste vara ett unikt värde i förhandsversionen av Azure Time Series Insights. |
   | **Egenskap för tidsstämpel** | Det här värdet används för att identifiera den **tidsstämpel** -egenskapen i inkommande telemetridata. Den här självstudien lämnar du rutan tom. Den här simulatorn använder den inkommande tidsstämpeln från IoT Hub som är standardinställningen för Time Series Insights. |

   Välj **Granska + skapa**.

   [![Konfigurera en händelsekälla](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. På den **granska + skapa** fliken Granska dina val och väljer sedan **skapa**.

    [![Granska + skapa sidan med knappen Skapa](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Du kan se statusen för distributionen:

    [![Meddelandet att distributionen har slutförts](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Du har åtkomst till förhandsversionen av Azure Time Series Insights-miljön om du äger klienten. Så här ser du till att du har åtkomst:

   1. Sök efter din resursgrupp och välj sedan din förhandsversion av Azure Time Series Insights-miljö:

      [![Valda miljön](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. På sidan Azure Time Series Insights Preview väljer **Dataåtkomstprinciper**:

      [![Principerna för dataåtkomst](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Kontrollera att dina autentiseringsuppgifter visas:

      [![Angivna autentiseringsuppgifter](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Om dina autentiseringsuppgifter inte visas i listan måste du ge dig själv behörighet att komma åt miljön. Läs [Bevilja åtkomst till data](./time-series-insights-data-access.md) om du vill veta mer om hur du anger behörigheter.

## <a name="stream-data-into-your-environment"></a>Stream-data i din miljö

1. Gå tillbaka till den [Azure IoT-Lösningsacceleratorer sidan](https://www.azureiotsolutions.com/Accelerators). Leta upp din lösning i solution accelerator instrumentpanelen. Välj **starta**:

    [![Starta enheten simulering lösningen](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Du omdirigeras till sidan **Enhetssimulering för Microsoft Azure IoT**. I det övre högra hörnet på sidan Välj **nya simulering**.

    [![Sida för Azure IoT-simulering](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. I den **inställningar** fönstret, ange följande parametrar:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Namn** | Ange ett unikt namn för en simulator. |
    | **Beskrivning** | Ange en definition. |
    | **Simuleringens varaktighet** | Ange till **Kör på obestämd tid**. |
    | **Enhetsmodell** | **Namn på**: Ange **Kylaggregat**. <br />**Mängd**: Ange **3**. |
    | **Mål-IoT Hub** | Ange till **Använd i förväg etablerad IoT Hub**. |

    [![Parametrar för att konfigurera](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Välj **starta simulering**.

    Observera informationen som visas för instrumentpanelen för simulering enheten **aktiva enheter** och **meddelanden per sekund**.

    [![Instrumentpanelen för Azure IoT-simulering](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data-in-your-environment"></a>Analysera data i din miljö

I det här avsnittet utför du grundläggande analys på dina tidsseriedata med hjälp av [Azure Time Series Insights-utforskaren (förhandsversion)](./time-series-insights-update-explorer.md).

1. Gå till din Azure Time Series Insights-utforskare (förhandsversion) genom att välja URL:en från resurssidan på [Azure Portal](https://portal.azure.com/).

    [![URL: en för förhandsversionen av Time Series Insights explorer](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. I Utforskaren, Välj den **Time Series-instanser** noden för att visa alla förhandsversion av Azure Time Series Insights-instanser i miljön.

    [![Lista över utan överordnat objekt instanser](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Välj den första gång serien instansen. Välj **visa tryck**.

    [![Valt time series-instans med kommandot visar genomsnittlig belastning](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    En serie diagrammets visas. Ändra den **intervall** till **15 sek**.

    [![Time series-diagram](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Upprepa steg 3 med de andra två time series-instanser. Du kan visa alla time series-instanser, som visas i det här diagrammet:

    [![Diagram för alla tidsserier](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. I den **tidsram** kryssrutan måste du ändra tidsintervallet för att se trender för time series under den senaste timmen:

    [![Ange tidsintervall och en timme](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definiera och tillämpa en modell

I det här avsnittet tillämpar du en modell för att strukturera data. För att slutföra modellen definierar du typer, hierarkier och instanser. Läs mer om datamodellering i [Tidsseriemodell](./time-series-insights-update-tsm.md).

1. I utforskaren väljer du fliken **Modell**:

   [![Modellfliken i explorer](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Välj **Lägg till** att lägga till en typ:

   [![Knappen Lägg till för typer](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Nu ska du definiera tre variablerna för typ: *tryck*, *temperatur*, och *fuktighet*. I den **lägga till en typ** fönstret, ange följande parametrar:

    | Parameter | Åtgärd |
    | --- | ---|
    | **Namn** | Ange **Kylaggregat**. |
    | **Beskrivning** | Ange **Det här är en typdefinitionen för kylaggregat**. |

   * Definiera *tryck*under **variabler**, ange följande parametrar:

     | Parameter | Åtgärd |
     | --- | ---|
     | **Namn** | Ange **Genomsnittligt tryck**. |
     | **Värde** | Välj **tryck (Double)** . Det kan ta några minuter **värdet** fyllas i automatiskt när Azure Time Series Insights Preview börjar ta emot händelser. |
     | **Sammansättningsåtgärd** | Välj **AVG**. |

      [![Val för att definiera hög belastning](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Om du vill lägga till nästa variabeln, Välj **lägga till variabeln**.

   * Definiera *temperatur*:

     | Parameter | Åtgärd |
     | --- | ---|
     | **Namn** | Ange **Genomsnittlig temperatur**. |
     | **Värde** | Välj **temperatur (Double)** . Det kan ta några minuter **värdet** fyllas i automatiskt när Azure Time Series Insights Preview börjar ta emot händelser. |
     | **Sammansättningsåtgärd** | Välj **AVG**.|

      [![Val för att definiera temperatur](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      Om du vill lägga till nästa variabeln, Välj **lägga till variabeln**.

   * Definiera *fuktighet*:

      | | |
      | --- | ---|
      | **Namn** | Ange **Max fuktighet** |
      | **Värde** | Välj **fuktighet (Double)** . Det kan ta några minuter **värdet** fyllas i automatiskt när Azure Time Series Insights Preview börjar ta emot händelser. |
      | **Sammansättningsåtgärd** | Välj **MAX**.|

      [![Val för att definiera temperatur](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    Välj **Skapa**.

    Du kan se den typ som du har lagt till:

    [![Information om vilken typ som har lagts till](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. Nästa steg är att lägga till en hierarki. Under **hierarkier**väljer **Lägg till**:

    [![Fliken hierarkier med knappen Lägg till](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. I den **Redigera hierarki** fönstret, ange följande parametrar:

   | Parameter | Åtgärd |
   | --- | ---|
   | **Namn** | Ange **Platshierarkin**. |
   | **Nivå 1** | Ange **Land**. |
   | **Nivå 2** | Ange **Ort**. |
   | **Nivå 3** | Ange **Byggnad**. |

   Välj **Spara**.

    [![Hierarkifält med knappen Skapa](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Du kan se den hierarki som du skapade:

    [![Information om hierarkin](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Välj **instanser**. Välj den första instansen och välj sedan **redigera**:

    [![Att välja Redigera-knappen för en instans](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. I den **redigera instanser** fönstret, ange följande parametrar:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Typ** | Välj **Kylaggregat**. |
    | **Beskrivning** | Ange **Instans för kylaggregat-01.1**. |
    | **Hierarkier** | Välj **platshierarkin**. |
    | **Land/region** | Ange **USA**. |
    | **Ort** | Ange **Seattle**. |
    | **Byggnad** | Ange **Space Needle**. |

    [![Instansfält med knappen Spara](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   Välj **Spara**.

1. Upprepa föregående steg för de andra sensorerna. Uppdatera följande värden:

   * För Kylaggregat 01.2:

     | Parameter | Åtgärd |
     | --- | --- |
     | **Typ** | Välj **Kylaggregat**. |
     | **Beskrivning** | Ange **Instans för kylaggregat-01.2**. |
     | **Hierarkier** | Välj **platshierarkin**. |
     | **Land/region** | Ange **USA**. |
     | **Ort** | Ange **Seattle**. |
     | **Byggnad** | Ange **Pacific Science Center**. |

   * För Kylaggregat 01.3:

     | Parameter | Åtgärd |
     | --- | --- |
     | **Typ** | Välj **Kylaggregat**. |
     | **Beskrivning** | Ange **instans för kylaggregat 01.3**. |
     | **Hierarkier** | Välj **platshierarkin**. |
     | **Land/region** | Ange **USA**. |
     | **Ort** | Ange **New York**. |
     | **Byggnad** | Ange **Empire State Building**. |

1. Välj den **analysera** fliken och sedan uppdatera sidan. Under **platshierarkin**, expandera alla hierarkinivåer om du vill visa time series-instanser:

   [![Fliken analysera](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Om du vill utforska time series-instanser under den senaste timmen, ändra **Snabbgånger** till **senaste timmen**:

    [![Rutan Snabbgånger med senaste timmen som valts](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. Under **Pacific Science Center**, väljer du den time series-instansen och välj sedan **Visa högsta fuktighet**.

    [![Vald tid serien instans och menyalternativet Visa högsta fuktighet](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. Tidsserier för **högsta fuktighet** med en intervallstorlek för på **1 minut** öppnas. Välj en region för att filtrera ett intervall. Om du vill analysera händelser under tidsperioden, högerklickar du på diagrammet och välj sedan **Zooma**:

   [![Markerade intervallet med zoomning kommando på en snabbmeny](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Om du vill se händelseinformationen, Välj en region och sedan högerklickar du på diagrammet:

   [![Detaljerad lista över händelser](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:  

> [!div class="checklist"]
> * Skapa och kör acceleratorn för enhetssimulering.
> * Skapa en Azure Time Series Insights-miljö (förhandsversion) med betala per användning.
> * Ansluta Azure Time Series Insights-miljön (förhandsversion) till en händelsehubb.
> * Kör ett lösningsacceleratorexempel för att sända data till förhandsversionen av Azure Time Series Insights-miljön.
> * Utföra en grundläggande analys av data.
> * Definiera en typ av och hierarki för tidsseriemodell och associera dem med dina instanser.

Nu när du vet hur du skapar din egen Azure Time Series Insights-miljö (förhandsversion) kan du lära dig mer om viktiga begrepp i Azure Time Series Insights.

Läs om lagringskonfiguration för Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Preview storage och ingående](./time-series-insights-update-storage-ingress.md)

Läs mer om Time Series-modeller:

> [!div class="nextstepaction"]
> [Datamodellering för Azure Time Series Insights (förhandsversion)](./time-series-insights-update-tsm.md)
