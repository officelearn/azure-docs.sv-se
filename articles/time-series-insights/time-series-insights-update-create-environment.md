---
title: 'Självstudier: Konfigurera en Azure Time Series Insights-miljö (förhandsversion) | Microsoft Docs'
description: Lär dig hur du konfigurerar miljön i förhandsversionen av Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.custom: seodec18
ms.openlocfilehash: f83063a88207f51f9d481447923fd8a8498692a2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713911"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Självstudie: Konfigurera en Azure Time Series Insights-miljö (förhandsversion)

Den här självstudien vägleder dig genom processen för att skapa en Azure Time Series Insights-miljö (förhandsversion) med betala per användning (PAYG). I den här guiden får du lära dig att:

* Skapa en Azure Time Series Insights-miljö (förhandsversion).
* Ansluta Azure Time Series Insights-miljön (förhandsversion) till en händelsehubb i Azure Event Hubs.
* Kör ett lösningsacceleratorexempel för att sända data till förhandsversionen av Azure Time Series Insights-miljön.
* Utföra grundläggande analys av data.
* Definiera en typ av och hierarki för tidsseriemodell och associera dem med dina instanser.

# <a name="create-a-device-simulation"></a>Skapa en enhetssimulering

I det här avsnittet skapar du tre simulerade enheter som ska skicka data till en IoT-hubb.

1. Gå till [Sidan för Azure IoT-lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators). Sidan visar flera fördefinierade exempel. Logga in med ditt Azure-konto. Välj sedan **Enhetssimulering**.

   [![Acceleratorer sida för Azure IoT-lösning](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Välj **Testa nu**.

1. Ange de obligatoriska parametrarna på sidan **Skapa enhetssimuleringslösning**:

   | Parameter | Beskrivning |
   | --- | --- |
   | **Namn på lösning** |    Ange ett unikt värde för att skapa en ny resursgrupp. Angivna Azure-resurser skapas och tilldelas till resursgruppen. |
   | **Prenumeration** | Ange samma prenumeration som användes för att skapa Time Series Insights-miljön. |
   | **Region** |   Ange samma region som användes för att skapa Time Series Insights-miljön. |
   | **Distribuera valfria Azure-resurser**    | Behåll den IoT Hub som valts, eftersom de simulerade enheterna kommer att använda den för att ansluta och strömma data. |

   Välj sedan **Skapa lösning**. Vänta 10–15 minuter för att din lösning ska distribueras.

   [![Skapa Enhetssimulering lösningssida](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. På lösningsacceleratorns instrumentpanel väljer du knappen **Starta**:

   [![Starta enheten simulering lösningen](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Du omdirigeras till sidan **Enhetssimulering för Microsoft Azure IoT**. Välj **+ Ny simulering** uppe till höger på sidan.

   [![Sida för Azure IoT-simulering](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. Fyll i de obligatoriska parametrarna på följande sätt:

    [![Parametrar för att fylla i](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    |||
    | --- | --- |
    | **Namn** | Ange ett unikt namn för en simulator. |
    | **Beskrivning** | Ange en definition. |
    | **Simuleringens varaktighet** | Ange till **Kör på obestämd tid**. |
    | **Enhetsmodell** | **Namn**: Ange `Chiller`. </br>**Mängd**: Ange `3`. |
    | **Mål-IoT Hub** | Ange till **Använd i förväg etablerad IoT Hub**. |

    Välj sedan **Starta simulering**.

1. På instrumentpanelen för enhetssimulering granskar du **Aktiva enheter** och **Meddelanden per sekund**.

    [![Instrumentpanelen för Azure IoT-simulering](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="list-device-simulation-properties"></a>Lista över egenskaper för enhetssimulering

Innan du skapar en Azure Time Series Insights-miljö behöver du namnen på din IoT-hubb, prenumerationen och resursgruppen.

1. Gå till instrumentpanelen för lösningsacceleratorn och logga in med samma Azure-prenumerationskonto. Hitta den enhetssimulering som du skapade i föregående steg.

1. Välj din enhetssimulator och välj **Starta**. Välj länken **Azure-hanteringsportalen** till höger.

    [![Simulatorn listor](media/v2-update-provision/device-six-listings.png)](media/v2-update-provision/device-six-listings.png#lightbox)

1. Notera namnen på IoT-hubb, prenumeration och resursgrupp.

    [![Azure-portalen](media/v2-update-provision/device-eight-portal.png)](media/v2-update-provision/device-eight-portal.png#lightbox)

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Skapa en Time Series Insights-miljö (förhandsversion) med PAYG (betala per användning)

I det här avsnittet beskrivs hur du skapar en Azure Time Series Insights-miljö (förhandsversion) med hjälp av [Azure-portalen](https://portal.azure.com/).

1. Logga in på Azure-portalen med ditt prenumerationskonto.

1. Välj **Skapa en resurs**.

1. Välj kategorin **Sakernas Internet** och välj sedan **Time Series Insights**.

   [![Välj Sakernas Internet och välj sedan Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. Fyll i fälten på sidan på följande sätt:

   | | |
   | --- | ---|
   | **Miljönamn** | Välj ett unikt namn för Azure Time Series Insights-miljön (förhandsversion). |
   | **Prenumeration** | Ange din prenumeration där du vill skapa Azure Time Series Insights-miljön (förhandsversion). Det är god praxis att använda samma prenumeration som resten av dina IoT-resurser som skapats av enhetssimulatorn. |
   | **Resursgrupp** | En resursgrupp är en container för Azure-resurser. Välj en befintlig resursgrupp eller skapa en ny för Azure Time Series Insights-miljöresursen (förhandsversion). Det är god praxis att använda samma resursgrupp som resten av dina IoT-resurser som skapats av enhetssimulatorn. |
   | **Plats** | Välj en datacenterregion för Azure Time Series Insights-miljön (förhandsversion). För att undvika kostnader för tillagd bandbredd och fördröjning är det bäst att hålla Azure Time Series Insights-miljön (förhandsversion) i samma region som andra IoT-resurser. |
   | **Nivå** |  Välj **PAYG**, som betyder betala per användning. Det här är SKU för Azure Time Series Insights-produkten (förhandsversion). |
   | **Egenskaps-ID** | Ange ett värde som unikt identifierar dina tidsserier. Observera att det här fältet är oföränderligt och inte kan ändras senare. Den här självstudien använder `iothub-connection-device-id`. Mer information om tidsserie-ID finns i avsnittet om [Hur du väljer ett tidserie-ID](./time-series-insights-update-how-to-id.md). |
   | **Lagringskontonamn** | Ange ett globalt unikt namn för ett nytt lagringskonto som ska skapas. |

   Välj sedan **Nästa: Händelsekälla**.

   [![Sidan för att skapa en Time Series Insights-miljö](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. På sidan för händelsekällan, fyller du i fälten på följande sätt:

   | | |
   | --- | --- |
   | **Skapa en händelsekälla?** | Ange `Yes`.|
   | **Namn** | Ange ett unikt värde som används för att namnge händelsekällan.|
   | **Källtyp** | Välj **IoT-hubb**. |
   | **Välja en hubb?** | Välj **Välj befintlig**. |
   | **Prenumeration** | Välj den prenumeration som du använde för enhetssimulatorn. |
   | **IoT-hubbnamn** | Välj namnet på det IoT-hubb som du skapade för enhetssimulatorn. |
   | **Åtkomstprincip för IoT-hubb** | Välj **iothubowner**. |
   | **Konsumentgrupp för IoT-hubb** | Du behöver en unik konsumentgrupp för en förhandsversion av Azure Time Series Insights. Välj **Ny**, ange ett unikt namn och välj sedan **Lägg till**. |
   | **Egenskap för tidsstämpel** | Det här fältet används för att identifiera tidsstämpelsegenskapen i inkommande telemetridata. Fyll inte i fältet för den här självstudien. Den här simulatorn använder den inkommande tidsstämpeln från IoT Hub som är standardinställningen för Time Series Insights.|

   Välj sedan **Granska + skapa**.

   [![Konfigurera en händelsekälla](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Granska alla fält på granskningssidan och välj **Skapa**.

   [![Granska + skapa sidan med knappen Skapa](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

1. Du kan se status för distributionen.

   [![Meddelandet att distributionen har slutförts](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Du bör få åtkomst till förhandsversionen av Azure Time Series Insights-miljön om du äger klienten. Så här ser du till att du har åtkomst:

   a. Sök efter din resursgrupp och välj din förhandsversion av Azure Time Series Insights-miljö:

      [![Valda miljön](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   b. På sidan för Azure Time Series Insights (förhandsversion) går du till **Dataåtkomstprinciper**.

     [![Principerna för dataåtkomst](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   c. Kontrollera att dina autentiseringsuppgifter visas.

     [![Angivna autentiseringsuppgifter](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Om dina autentiseringsuppgifter inte visas måste du ge dig själv behörighet att komma åt miljön. Läs [Bevilja åtkomst till data](./time-series-insights-data-access.md) om du vill veta mer om hur du anger behörigheter.

## <a name="analyze-data-in-your-environment"></a>Analysera data i din miljö

I det här avsnittet utför du grundläggande analys på dina tidsseriedata med hjälp av [Azure Time Series Insights-utforskaren (förhandsversion)](./time-series-insights-update-explorer.md).

1. Gå till din Azure Time Series Insights-utforskare (förhandsversion) genom att välja URL:en från resurssidan på [Azure Portal](https://portal.azure.com/).

   [![URL: en för förhandsversionen av Time Series Insights explorer](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. I Utforskaren, Välj den **Time Series-instanser** noden för att visa alla förhandsversion av Azure Time Series Insights-instanser i miljön.

   [![Lista över utan överordnat objekt instanser](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. I den tidsserie som visas väljer du den första instansen. Välj sedan **Visa genomsnittligt tryck**.

   [![Valda instansen med kommandot visar genomsnittlig belastning](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

   En serie diagrammets bör visas till höger. Justera den **intervall** till `15s`.

   [![Time series-diagram](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Upprepa **steg 3** med de andra två tidsserierna. Du kan visa alla tidsserier, såsom visas i det här diagrammet:

   [![Diagram för alla tidsserier](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Ändra tidsintervallet för att visa trender för tidsserier under den senaste timmen.

   a. Välj den **tidsram** alternativ:

      [![Ange tidsintervall och en timme](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definiera och tillämpa en modell

I det här avsnittet tillämpar du en modell för att strukturera data. För att slutföra modellen definierar du typer, hierarkier och instanser. Mer information om datamodellering finns i avsnittet om [Tidsseriemodeller](./time-series-insights-update-tsm.md).

1. I utforskaren väljer du fliken **Modell**:

   [![Modellfliken i explorer](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Välj **+ Lägg till** för att lägga till en typ. På höger sida öppnas en typredigerare.

   [![Knappen Lägg till för typer](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Definiera tre variabler för typen: tryck, temperatur och fuktighet. Ange följande information:

   | | |
   | --- | ---|
   | **Namn** | Ange `Chiller`. |
   | **Beskrivning** | Ange `This is a type definition of Chiller`. |

   * Definiera tryck med tre variabler:

      | | |
      | --- | ---|
      | **Namn** | Ange `Avg Pressure`. |
      | **Värde** | Välj **tryck (Double)**. Observera att det kan ta några minuter för det här fältet att fyllas i när förhandsversionen av Azure Time Series Insights börjar ta emot händelser. |
      | **Sammansättningsåtgärd** | Välj **AVG**. |

      [![Val för att definiera hög belastning](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Välj **+ Lägg till variabel** för att lägga till nästa variabel.

   * Definiera Temperatur:

      | | |
      | --- | ---|
      | **Namn** | Ange `Avg Temperature`. |
      | **Värde** | Välj **temperatur (Double)**. Observera att det kan ta några minuter för det här fältet att fyllas i när förhandsversionen av Azure Time Series Insights börjar ta emot händelser. |
      | **Sammansättningsåtgärd** | Välj **AVG**.|

      [![Val för att definiera temperatur](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

   * Definiera fuktighet:

      | | |
      | --- | ---|
      | **Namn** | Ange `Max Humidity` |
      | **Värde** | Välj **fuktighet (Double)**. Observera att det kan ta några minuter för det här fältet att fyllas i när förhandsversionen av Azure Time Series Insights börjar ta emot händelser. |
      | **Sammansättningsåtgärd** | Välj **MAX**.|

      [![Val för att definiera temperatur](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

   Välj sedan **Create** (Skapa).

1. Du kan se att din typ har lagts till:

   [![Information om vilken typ som har lagts till](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. Nästa steg är att lägga till en hierarki. I avsnittet **Hierarkier** väljer du **+ Lägg till**:

   [![Fliken hierarkier med knappen Lägg till](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. Definiera hierarkin. Fyll i fälten på följande sätt:

   | | |
   | --- | ---|
   | **Namn** | Ange `Location Hierarchy`. |
   | **Nivå 1** | Ange `Country`. |
   | **Nivå 2** | Ange `City`. |
   | **Nivå 3** | Ange `Building`. |

   Välj sedan **Create** (Skapa).

   [![Hierarkifält med knappen Skapa](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

1. Du kan se den hierarki som du skapade:

   [![Information om hierarkin](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Välj **Instanser** till vänster. När instanserna visas väljer du den första instansen och väljer sedan **Redigera**:

   [![Att välja Redigera-knappen för en instans](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. På höger sida visas en textredigerare. Lägg till följande information:

   | | |
   | --- | --- |
   | **Typ** | Välj **Kylaggregat**. |
   | **Beskrivning** | Ange `Instance for Chiller-01.1`. |
   | **Hierarkier** | Välj **platshierarkin**. |
   | **Land/region** | Ange `USA`. |
   | **Ort** | Ange `Seattle`. |
   | **Byggnad** | Ange `Space Needle`. |

    Välj sedan **Spara**.

   [![Instansfält med spara knappen](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

1. Upprepa föregående steg för de andra sensorerna. Använd följande fält:

   * För Kylaggregat 01.2:

     | | |
     | --- | --- |
     | **Typ** | Välj **Kylaggregat**. |
     | **Beskrivning** | Ange `Instance for Chiller-01.2`. |
     | **Hierarkier** | Välj **platshierarkin**. |
     | **Land/region** | Ange `USA`. |
     | **Ort** | Ange `Seattle`. |
     | **Byggnad** | Ange `Pacific Science Center`. |

   * För Kylaggregat 01.3:

     | | |
     | --- | --- |
     | **Typ** | Välj **Kylaggregat**. |
     | **Beskrivning** | Ange `Instance for Chiller-01.3`. |
     | **Hierarkier** | Välj **platshierarkin**. |
     | **Land/region** | Ange `USA`. |
     | **Ort** | Ange `New York`. |
     | **Byggnad** | Ange `Empire State Building`. |

1. Gå till fliken **Analysera** och uppdatera sidan. Expandera alla hierarkinivåer för att hitta tidsserien.

   [![Fliken analysera](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Utforska tidsserien under den senaste timmen genom att ändra **Quick Times** (Snabba tider) till den **Senaste timmen**:

    [![Rutan Snabbgånger med senaste timmen som valts](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. Välj tidsserierna under **Pacific Science Center** och välj **Visa högsta fuktighet**.

    [![Valda tidsserier med menyn Visa högsta fuktighet](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. Tidsserier för **högsta fuktighet** med en intervallstorlek för på **1 minut** öppnas. Välj en region för att filtrera ett intervall. Högerklicka sedan och välj **Zooma** för att analysera händelser under tidsramen:

   [![Markerade intervallet med zoomning kommando på en snabbmeny](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Du kan även välja en region och sedan högerklicka för att visa händelseinformation:

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
> [Lagring och inkommande för Azure Time Series Insights (förhandsversion)](./time-series-insights-update-storage-ingress.md)

Läs mer om Time Series-modeller:

> [!div class="nextstepaction"]
> [Datamodellering för Azure Time Series Insights (förhandsversion)](./time-series-insights-update-tsm.md)