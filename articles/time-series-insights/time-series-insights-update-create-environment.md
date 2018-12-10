---
title: Självstudie om Azure Time Series Insights (förhandsversion) | Microsoft Docs
description: Läs mer om Azure Time Series Insights (förhandsversion)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/26/2018
ms.openlocfilehash: ed25d03f7c592476b9284790ac12f9954661a42b
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872313"
---
# <a name="azure-time-series-insights-preview-tutorial"></a>Självstudie om Azure Time Series Insights (förhandsversion)

Den här självstudien vägleder dig genom processen att skapa en miljö i förhandsversionen av Azure Time Series Insights (TSI) som fylls med data från simulerade enheter. I den här guiden får du lära dig att:

* Skapa en TSI-miljö (förhandsversion).
* Ansluta TSI-miljön (förhandsgranskning) till en händelsehubb.
* Köra en vindkraftverkssimulering för att strömma data till miljön i förhandsversionen av TSI.
* Utföra grundläggande analys av data.
* Definiera en typ av och hierarki för tidsseriemodell och associera den med dina instanser.

## <a name="create-a-time-series-insights-preview-environment"></a>Skapa en Time Series Insights-miljö (förhandsversion)

I det här avsnittet beskrivs hur du skapar en Azure TSI-miljö (förhandsversion) med hjälp av [Azure-portalen](https://portal.azure.com/).

1. Logga in på Azure-portalen med ditt prenumerationskonto
1. Välj **+ skapa en resurs** i det övre vänstra hörnet.
1. Välj kategorin **Sakernas Internet** och välj sedan **Time Series Insights**.

  ![tutorial-one][1]

1. På sidan för Time Series Insights-miljö fyller du i de obligatoriska parametrarna och klickar på **Next: Event Source** (Nästa: Händelsekälla)

  ![tutorial-two][2]

1. På sidan  **Händelsekälla** fyller du i de obligatoriska parametrarna och klickar på **Granska och skapa**.

  ![tutorial-three][3]

1. Granska all information och klicka på **Skapa** för att börja etablera miljön.

  ![tutorial-four][4]

1. Du får ett meddelande när distributionen har slutförts.

  ![tutorial-five][5]

## <a name="send-events-to-your-tsi-environment"></a>Skicka händelser till TSI-miljön

I det här avsnittet använder du en simulator för vindkraftsenheter för att skicka händelser till din TSI-miljö via en händelsehubb.

  1. I Azure-portalen går du till din händelsehubbresurs och ansluter den till TSI-miljön. Lär dig [hur du ansluter resursen till en befintlig händelsehubb](./time-series-insights-how-to-add-an-event-source-eventhub.md).

  1. På Event Hubs-resurssidan går du till **Policyer för delad åtkomst** och sedan **RootManageSharedAccessKey**. Kopiera den **anslutningssträng – primär nyckel** som visas här.

      ![tutorial-six][6]

  1. Gå till [https://tsiclientsample.azurewebsites.net/windFarmGen.html]( https://tsiclientsample.azurewebsites.net/windFarmGen.html). Den här webbappen simulerar vindkraftsenheter.
  1. Klistra in den anslutningssträng som kopierats från steg 3 i **händelsehubbens anslutningssträng**

      ![tutorial-seven][7]

  1. Klicka på **Klicka för att starta** för att push-överföra händelser till din händelsehubb. I det här skedet laddas en fil med namnet `instances.json` till datorn. Spara filen eftersom vi behöver den senare.

  1. Gå tillbaka till händelsehubben. Du bör nu se att de nya händelserna tas emot av hub.d

     ![tutorial-eight][8]

## <a name="analyze-data-in-your-environment"></a>Analysera data i din miljö

I det här avsnittet utför du grundläggande analys på dina tidsseriedata med hjälp av Time Series Insights-uppdateringsutforskaren.

  1. Gå till din Time Series Insights-uppdateringsutforskare genom att klicka på URL:en från resurssidan på Azure-portalen.

      ![tutorial-nine][9]

  1. I utforskaren klickar du på noderna för **instanser utan överordnade objekt** för att se alla Time Series-instanser i miljön.

     ![tutorial-ten][10]

  1. I den här självstudien analyserar vi data som skickats under den senaste dagen. För att göra det klickar du på **Quick Times** (Snabba tider) och väljer alternativet **Senaste 24 timmarna**.

     ![tutorial-eleven][11]

  1. Välj **Sensor_0** och välj **Show Avg Value** (Visa genomsnittligt värde) för att visualisera data som skickas från den här tidsserieinstansen.

     ![tutorial-twelve][12]

  1. På samma sätt kan du rita data som kommer från andra tidsserieinstanser för att utföra grundläggande analyser.

     ![tutorial-thirteen][13]

## <a name="define-a-type--hierarchy"></a>Definiera en typ och hierarki 

I det här avsnittet skapar du en typ och hierarki och associerar dem med dina tidsserieinstanser. Läs mer om [Time Series-modeller](./time-series-insights-update-tsm.md).

  1. I utforskaren klickar du på fliken **Modell** i appfältet.

     ![tutorial-fourteen][14]

  1. I avsnittet Typer klickar du på **+ Lägg till**. Detta gör att du kan skapa en ny typ av Time Series-modell.

     ![tutorial-fifteen][15]

  1. I typredigeraren anger du en **Namn** och en **Beskrivning**, och skapar variabler för värdena **Genomsnittligt**, **Min** och **Max** värdena enligt det som visas nedan. Spara typen genom att klicka på **Skapa**.

     ![tutorial-sixteen][16]

     ![tutorial-seventeen][17]

  1. I avsnittet **Hierarkier** klickar du på **+ Lägg till**. Detta gör att du kan skapa en ny hierarki för Time Series-modell.

     ![tutorial-eighteen][18]

  1. I hierarkiredigeraren anger du **Namn** och lägger till hierarkinivåer enligt nedan. Spara hierarkin genom att klicka på **Skapa**.

     ![tutorial-nineteen][19]

     ![tutorial-twenty][20]

  1. I avsnittet **Instanser** markerar du en instans och klickar på **Redigera**. Då kan du associera en typ och en hierarki med den här instansen.

     ![tutorial-twenty-one][21]

  1. I instansredigeraren väljer du den typ och den hierarki som definierats i stegen 3 och 5 ovan enligt vad som visas.

     ![tutorial-twenty-two][22]

  1. Alternativt kan du göra detta för alla instanser på en gång genom att redigera den `instances.json`-fil som laddades ned tidigare. I den här filen ersätter du alla **typeId**- och **hierarchyId**-fält med det ID som hämtades från stegen 3 och 5 ovan.

  1. I avsnittet **Instanser** klickar du på **Ladda upp JSON** och laddar upp den redigerade `instances.json`-filen enligt nedan.

     ![tutorial-twenty-three][23]

  1. Gå till fliken **Analys** och uppdatera webbläsaren. Du bör nu se alla instanser som är associerade med den typ och den hierarki som definierats ovan.

     ![tutorial-twenty-four][24]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:  

* Skapa en TSI-miljö (förhandsversion).
* Ansluta TSI-miljön (förhandsgranskning) till en händelsehubb.
* Köra en vindkraftverkssimulering för att strömma data till TSI-miljön (förhandsversion).
* Utföra grundläggande analys av data.
* Definiera en typ av och hierarki för tidsseriemodell och associera den med dina instanser.

Nu när du vet hur du skapar din egen TSI-uppdateringsmiljö kan du läsa mer om de viktiga begreppen i TSI.

Läs om TSI-lagringskonfiguration:

> [!div class="nextstepaction"]
> [Lagring och inkommande i Azure TSI (förhandsversion)](./time-series-insights-update-storage-ingress.md)

Läs mer om Time Series-modeller:

> [!div class="nextstepaction"]
> [Datamodellering i Azure TSI (förhandsversion)](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/tutorial-one.png
[2]: media/v2-update-provision/tutorial-two.png
[3]: media/v2-update-provision/tutorial-three.png
[4]: media/v2-update-provision/tutorial-four.png
[5]: media/v2-update-provision/tutorial-five.png
[6]: media/v2-update-provision/tutorial-six.png
[7]: media/v2-update-provision/tutorial-seven.png
[8]: media/v2-update-provision/tutorial-eight.png
[9]: media/v2-update-provision/tutorial-nine.png
[10]: media/v2-update-provision/tutorial-ten.png
[11]: media/v2-update-provision/tutorial-eleven.png
[12]: media/v2-update-provision/tutorial-twelve.png
[13]: media/v2-update-provision/tutorial-thirteen.png
[14]: media/v2-update-provision/tutorial-fourteen.png
[15]: media/v2-update-provision/tutorial-fifteen.png
[16]: media/v2-update-provision/tutorial-sixteen.png
[17]: media/v2-update-provision/tutorial-seventeen.png
[18]: media/v2-update-provision/tutorial-eighteen.png
[19]: media/v2-update-provision/tutorial-nineteen.png
[20]: media/v2-update-provision/tutorial-twenty.png
[21]: media/v2-update-provision/tutorial-twenty-one.png
[22]: media/v2-update-provision/tutorial-twenty-two.png
[23]: media/v2-update-provision/tutorial-twenty-three.png
[24]: media/v2-update-provision/tutorial-twenty-four.png