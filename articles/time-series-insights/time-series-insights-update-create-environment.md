---
title: Konfiguration av Azure Time Series Insights (förhandsversion) – Självstudie om att konfigurera en miljö i förhandsversionen av Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du konfigurerar miljön i förhandsversionen av Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 9ad957d6378b1279f1ca51939eb4802b0ce7d78f
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322626"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Självstudie: Konfigurera en Azure Time Series Insights-miljö (förhandsversion)

Den här självstudien vägleder dig genom processen för att skapa en Azure Time Series Insights-miljö (förhandsversion) med betala per användning (PAYG). I den här guiden får du lära dig att:

* Skapa en Azure Time Series Insights-miljö (förhandsversion).
* Ansluta Azure Time Series Insights-miljön (förhandsversion) till en händelsehubb i Azure Event Hubs.
* Köra en vindkraftverkssimulering för att strömma data till Azure Time Series Insights-miljön (förhandsversion).
* Utföra grundläggande analys av data.
* Definiera en typ av och hierarki för tidsseriemodell och associera dem med dina instanser.

# <a name="create-a-device-simulation"></a>Skapa en enhetssimulering

I det här avsnittet skapar du tre simulerade enheter som ska skicka data till en IoT-hubb.

1. Gå till [startsidan för Azure IoT-lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators). Startsidan för Azure IoT-Lösningsacceleratorer visar flera fördefinierade exempel. Logga in på ditt Azure-konto. Välj sedan **Enhetssimulering**.

   ![Startsidan för Azure IoT-lösningsacceleratorer][1]

   Klicka på **Testa nu**.

1. Ange de obligatoriska parametrarna på sidan **Skapa enhetssimuleringslösning**:

   | Parameter | Beskrivning |
   | --- | --- |
   | Namn på lösning |    Ett unikt värde som används för att skapa en ny resursgrupp. De angivna Azure-resurserna | skapas och tilldelas till resursgruppen. |
   | Prenumeration | Ange samma prenumeration som användes för att skapa TSI-miljön |
   | Region |   Ange samma region som användes för att skapa TSI. |
   | Distribuera valfria Azure-resurser    | Lämna IoT Hub markerad, eftersom de simulerade enheterna kommer att använda den för att ansluta/strömma data. |

   När du har angett de obligatoriska parametrarna klickar du på **Skapa lösning**. Vänta ungefär 10–15 minuter på att lösningen ska distribueras.

   ![Skapa en enhetssimuleringslösning][2]

1. På **lösningsacceleratorns instrumentpanel** klickar du på knappen **Starta**:

   ![Starta enhetssimuleringslösningen][3]

1. Du omdirigeras till sidan **Microsoft Azure IoT Device Simulation** (Enhetssimulering för Microsoft Azure IoT). Klicka på **+ Ny simulering** uppe till höger på skärmen.

   ![Sida för Azure IoT-simulering][4]

1.  Fyll i de obligatoriska parametrarna på följande sätt:

    ![Parametrar som ska fyllas i][5]

    |||
    | --- | --- |
    | **Namn** | Ange ett unikt namn för en simulator |
    | **Beskrivning** | Ange en definition |
    | **Simuleringens varaktighet** | Ange till `Run indefinitely` |
    | **Enhetsmodell** | **Namn**: Ange `Chiller`-**belopp**: Ange `3` |
    | **Mål-IoT Hub** | Ange till `Use pre-provisioned IoT Hub` |

    När du har fyllt i de obligatoriska parametrarna klickar du på **Starta simulering**.

1. På instrumentpanelen för enhetssimulering granskar du **Aktiva enheter** och **Meddelanden per sekund**.

    ![Instrumentpanel för Azure IoT-simulering][6]

## <a name="list-device-simulation-properties"></a>Lista över egenskaper för enhetssimulering

Innan du skapar en Azure Time Series Insights-miljö behöver du namnen på din IoT-hubb, prenumerationen och resursgruppens namn.

1. Gå till **instrumentpanelen för lösningsacceleratorn** och logga in med samma Azure-prenumerationskonto. Hitta den enhetssimulering som du skapade i föregående steg.

1. Klicka på enhetssimulatorn och sedan på **Starta**. Klicka på den **Azure-hanteringsportallänk** som visas på höger sida.

    ![Simulatorlistor][7]

1. Notera namnen på IoT-hubb, prenumeration och resursgrupp.

    ![Azure Portal][8]

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Skapa en Time Series Insights-miljö (förhandsversion) med PAYG (betala per användning)

I det här avsnittet beskrivs hur du skapar en Azure Time Series Insights-miljö (förhandsversion) med hjälp av [Azure-portalen](https://portal.azure.com/).

1. Logga in på Azure-portalen med ditt prenumerationskonto.

1. Välj **Skapa en resurs**.

1. Välj kategorin **Sakernas Internet** och välj sedan **Time Series Insights**.

   ![Välj Skapa en resurs, sedan Sakernas Internet och därefter Time Series Insights][9]

1. Fyll i fälten på sidan på följande sätt:

   | | |
   | --- | ---|
   | **Miljönamn** | Välj ett unikt namn för Azure Time Series Insights-miljön (förhandsversion). |
   | **Prenumeration** | Ange din prenumeration där du vill skapa Azure Time Series Insights-miljön (förhandsversion). Det är god praxis att använda samma prenumeration som resten av dina IoT-resurser som skapats av enhetssimulatorn. |
   | **Resursgrupp** | En resursgrupp är en container för Azure-resurser. Välj en befintlig resursgrupp eller skapa en ny för Azure Time Series Insights-miljöresursen (förhandsversion). Det är god praxis att använda samma resursgrupp som resten av dina IoT-resurser som skapats av enhetssimulatorn. |
   | **Plats** | Välj en datacenterregion för Azure Time Series Insights-miljön (förhandsversion). För att undvika kostnader för tillagd bandbredd och fördröjning är det bäst att hålla Azure Time Series Insights-miljön (förhandsversion) i samma region som andra IoT-resurser. |
   | **Nivå** |  Välj `PAYG`, som betyder betala per användning. Det här är SKU för Azure Time Series Insights-produkten (förhandsversion). |
   | **Egenskaps-ID** | Identifierar tidsserien unikt. Observera att det här fältet är oföränderligt och inte kan ändras senare. För den här självstudien anger du fältet till `iothub-connection-device-id`. Mer information om tidsserie-ID finns i avsnittet om [hur du väljer ett tidserie-ID](./time-series-insights-update-how-to-id.md). |
   | **Lagringskontonamn** | Ange ett globalt unikt namn för ett nytt lagringskonto som ska skapas. |

   När du har fyllt i fälten ovan klickar du på **Nästa: Händelsekälla**.

   ![Klicka på Nästa: Händelsekälla][10]

1. På sidan fyller du i fälten på följande sätt:

   | | |
   | --- | --- |
   | **Skapa en händelsekälla?** | Ange `Yes`|
   | **Namn** | Kräver ett unikt värde som används för att namnge händelsekällan.|
   | **Källtyp** | Ange `IoT Hub` |
   | **Välja en hubb?** | Ange `Select Existing` |
   | **Prenumeration** | Ange den prenumeration som du använde för enhetssimulatorn. |
   | **IoT-hubbnamn** | Ange det IoT-hubbnamn som du skapade för enhetssimulatorn. |
   | **Åtkomstprincip för IoT-hubb** | Ange `iothubowner` |
   | **Konsumentgrupp för IoT-hubb** | Du behöver en unik konsumentgrupp för en förhandsversion av Azure Time Series Insights. |
   | **Tidsstämpel** | Det här fältet används för att identifiera tidsstämpelsegenskapen i inkommande telemetridata. För den här självstudien fyller du inte i fältet. Den här simulatorn använder den inkommande tidsstämpeln från IoT Hub som är standardinställningen för Time Series Insights.|

   Så här skapar du en unik konsumentgrupp:

   1. Klicka på **Ny** intill fältet **Konsumentgrupp för IoT-hubb**:

      ![Klicka på Nästa: Händelsekälla][11]

   1. Ge konsumentgruppen ett unikt namn och klicka på **Lägg till**:

      ![Klicka på Lägg till][12]

   När du har fyllt i fälten ovan klickar du på **Granska + skapa**.

      ![Granska och skapa][13]

1. Granska alla fält på granskningssidan och klicka på **Skapa**.

   ![Skapa][14]

1. Du kan se status för distributionen.

   ![Distributionen är klar][15]

1. Du bör få åtkomst till din tidsseriemiljö om du äger klientorganisationen. Så här ser du till att du har åtkomst:

   * Gå till din nyligen skapade Azure Time Series Insights-miljö (förhandsversion). Du kan göra det genom att söka efter din resursgrupp. Klicka sedan på tidsseriemiljön:

      ![Distributionen är klar][16]

   * På sidan för Azure Time Series Insights (förhandsversion) går du till **Dataåtkomstprinciper**.

     ![Dataåtkomstprinciper][17]

   * Kontrollera att dina autentiseringsuppgifter visas.

     ![Ange autentiseringsuppgifterna][18]

   Om dina autentiseringsuppgifter inte visas måste du ge dig själv behörighet att komma åt miljön. Läs [Bevilja åtkomst till data](./time-series-insights-data-access.md) om du vill veta mer om hur du anger behörigheter.

## <a name="analyze-data-in-your-environment"></a>Analysera data i din miljö

I det här avsnittet utför du grundläggande analys på dina tidsseriedata med hjälp av [Azure Time Series Insights-utforskaren (förhandsversion)](./time-series-insights-update-explorer.md).

1. Gå till din Azure Time Series Insights-utforskare (förhandsversion) genom att klicka på URL:en från resurssidan på [Azure-portalen](https://portal.azure.com/).

   ![URL för Time Series Insights-utforskare][19]

1. I utforskaren väljer du noderna för **instanser utan överordnade objekt** för att se alla Azure Time Series Insights (förhandsversion) i miljön.

   ![Lista över instanser utan överordnade objekt][20]

1. I den tidsserie som visas klickar du på den första instansen. Sedan klickar du på **Show Avg pressure** (Visa genomsnittligt tryck).

   ![Visa genomsnittligt tryck][21]

1. Ett tidsseriediagram bör visas till höger:

   ![Tidsseriediagram][22]

1. Upprepa **steg 3** med de andra två tidsserierna. Alla tidsserier kan sedan visas på nedanstående sätt:

   ![Alla tidsseriediagram][23]

1. Ändra **tidsintervallet** för att visa trender för tidsserier under den senaste timmen. Välj alternativrutan **Från** enligt nedanstående:

   ![Välj alternativet Från][24]

1. Ändra tiden i alternativrutan **Från** till att visa händelser från den senaste timmen:

   ![Välj alternativet Från][25]

1. Du kan sedan jämföra tryck mellan alla tre enheter under den senaste timmen:

   ![Välj alternativet Från][26]

## <a name="define-and-apply-a-model"></a>Definiera och tillämpa en modell

I det här avsnittet tillämpar du en modell för att strukturera data. För att slutföra modellen definierar du typer, hierarkier och instanser. Mer information om datamodellering finns i avsnittet om [tidsseriemodeller](./time-series-insights-update-tsm.md).

1. I utforskaren väljer du fliken **Modell**:

   ![Välj fliken Modell][27]

1. Klicka sedan på **+ Lägg till** för att lägga till en typ. På höger sida öppnas en typredigerare.

   ![Klicka på Lägg till][28]

1. Definiera sedan tre variabler: Tryck, Temperatur och Fuktighet i en Typ. Ange följande fält:

   | | |
   | --- | ---|
   | **Namn** | Ange `Chiller` |
   | **Beskrivning** | Ange `This is a type definition of Chiller` |

   * Definiera nu Tryck med tre variabler:

      | | |
      | --- | ---|
      | **Namn** | Ange `Avg Pressure` |
      | **Värde** | Välj **tryck (Double)**. Observera att det kan ta några minuter för det här fältet att fyllas i när Azure Time Series Insights börjar ta emot händelser |
      | **Sammansättningsåtgärd** | Välj `AVG` |

      ![Lägg till en variabel][29]

      Klicka på **+ Variabel** för att lägga till nästa variabel.

   * Definiera nu Temperatur:

      | | |
      | --- | ---|
      | **Namn** | Ange `Avg Temperature` |
      | **Värde** | Välj **temperatur (Double)**. Observera att det kan ta några minuter för det här fältet att fyllas i när Azure Time Series Insights börjar ta emot händelser |
      | **Sammansättningsåtgärd** | Välj `AVG`|

      ![Definiera Temperatur][30]

   * Definiera nu Fuktighet:

      | | |
      | --- | ---|
      | **Namn** | Ange `Max Humidity` |
      | **Värde** | Välj **fuktighet (Double)**. Observera att det kan ta några minuter för det här fältet att fyllas i när Azure Time Series Insights börjar ta emot händelser |
      | **Sammansättningsåtgärd** | Välj `MAX`|

      ![Definiera Temperatur][31]

   När du definierat variabler klickar du på **Skapa**.

1. Du kan se att din typ har lagts till:

   ![Se typ som har lagts till][32]

1. Nästa steg är att lägga till en hierarki. I avsnittet **Hierarkier** väljer du **+ Lägg till** för att skapa en ny hierarki:

   ![Lägga till en hierarki][33]

1. Definiera Hierarki. Ange fälten på följande sätt:

   | | |
   | --- | ---|
   | **Namn** | Ange `Location Hierarchy` |
   | **Nivå 1** | Ange `Country` |
   | **Nivå 2** | Ange `City` |
   | **Nivå 3** | Ange `Building` |

   När du har fyllt i fälten ovan klickar du på **Skapa**.

   ![Definiera en hierarki][34]

1. Du kan se att hierarkin har skapats:

   ![Visa hierarkin][35]

1. När du har definierat hierarkin klickar du på **Instanser** till vänster. När instanserna visas klickar du på den första instansen och väljer **Redigera**:

   ![Redigera en instans][36]

1. På höger sida visas en textredigerare. Lägg till följande fält:

   | | |
   | --- | --- |
   | **Typ** | Välj `Chiller` |
   | **Beskrivning** | Ange `Instance for Chiller-01.1` |
   | **Hierarkier** | Aktivera `Location Hierarchy` |
   | **Land/region** | Ange `USA` |
   | **Ort** | Ange `Seattle` |
   | **Byggnad** | Ange `Space Needle` |

    När du har fyllt i fälten ovan klickar du på **Spara**.

   ![Spara ett kylaggregat][37]

1. Upprepa föregående steg för de andra sensorerna. Använd följande fält:

   * För Kylaggregat 01.2:

     | | |
     | --- | --- |
     | **Typ** | Välj `Chiller` |
     | **Beskrivning** | Ange `Instance for Chiller-01.2` |
     | **Hierarkier** | Aktivera `Location Hierarchy` |
     | **Land/region** | Ange `USA` |
     | **Ort** | Ange `Seattle` |
     | **Byggnad** | Ange `Pacific Science Center` |

   * För Kylaggregat 01.3:

     | | |
     | --- | --- |
     | **Typ** | Välj `Chiller` |
     | **Beskrivning** | Ange `Instance for Chiller-01.1` |
     | **Hierarkier** | Aktivera `Location Hierarchy` |
     | **Land/region** | Ange `USA` |
     | **Ort** | Ange `New York` |
     | **Byggnad** | Ange `Empire State Building` |

1. Gå till fliken **Analysera** och uppdatera sidan. Expandera alla hierarkinivåer för att hitta tidsserien.

   ![Visa fliken Analysera][38]

1. Utforska tidsserien under den senaste timmen genom att ändra **Quick Times** (Snabba tider) till den senaste timmen:

   ![Utforska den senaste timmen][39]

1. Klicka på tidsserierna under **Pacific Science Center** och klicka på **Visa högsta fuktighet**.

   ![Visa högsta fuktighet][40]

1. Tidsserien för **Högsta fuktighet** med en intervallstorlek på 1 minut öppnas. Vänsterklicka på en region för att filtrera ett intervall. Högerklicka sedan och zooma för att analysera händelser under tidsperioden:

   ![Visa, filtrera och zooma][41]

   ![Visa, filtrera och zooma][42]

1. Du kan även vänsterklicka på en region och sedan högerklicka för att visa händelseinformation:

   ![Visa, filtrera och zooma][43]

   ![Visa, filtrera och zooma][44]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:  

* Skapa och kör acceleratorn för enhetssimulering.
* Skapa en Azure Time Series Insights-miljö (förhandsversion) med betala per användning.
* Ansluta Azure Time Series Insights-miljön (förhandsversion) till en händelsehubb.
* Köra en vindkraftverkssimulering för att strömma data till Azure Time Series Insights-miljön (förhandsversion).
* Utföra en grundläggande analys av data.
* Definiera en typ av och hierarki för tidsseriemodell och associera dem med dina instanser.

Nu när du vet hur du skapar din egen Azure Time Series Insights-miljö (förhandsversion) kan du lära dig mer om viktiga begrepp i Azure Time Series Insights.

Läs om lagringskonfiguration för Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Lagring och inkommande för Azure Time Series Insights (förhandsversion)](./time-series-insights-update-storage-ingress.md)

Läs mer om Time Series-modeller:

> [!div class="nextstepaction"]
> [Datamodellering för Azure Time Series Insights (förhandsversion)](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/device-one-accelerator.png
[2]: media/v2-update-provision/device-two-create.png
[3]: media/v2-update-provision/device-three-launch.png
[4]: media/v2-update-provision/device-four-iot-sim-page.png
[5]: media/v2-update-provision/device-five-params.png
[6]: media/v2-update-provision/device-six-listings.png
[7]: media/v2-update-provision/device-seven-dashboard.png
[8]: media/v2-update-provision/device-eight-portal.png

[9]: media/v2-update-provision/payg-one-azure.png
[10]: media/v2-update-provision/payg-two-create.png
[11]: media/v2-update-provision/payg-three-new.png
[12]: media/v2-update-provision/payg-four-add.png
[13]: media/v2-update-provision/payg-five-event-source.png
[14]: media/v2-update-provision/payg-six-review.png
[15]: media/v2-update-provision/payg-seven-deploy.png
[16]: media/v2-update-provision/payg-eight-environment.png
[17]: media/v2-update-provision/payg-nine-data-access.png
[18]: media/v2-update-provision/payg-ten-verify.png

[19]: media/v2-update-provision/analyze-one-portal.png
[20]: media/v2-update-provision/analyze-two-unparented.png
[21]: media/v2-update-provision/analyze-three-show-pressure.png
[22]: media/v2-update-provision/analyze-four-chart.png
[23]: media/v2-update-provision/analyze-five-chart.png
[24]: media/v2-update-provision/analyze-six-from.png
[25]: media/v2-update-provision/analyze-seven-change-from.png
[26]: media/v2-update-provision/analyze-eight-all.png

[27]: media/v2-update-provision/define-one-model.png
[28]: media/v2-update-provision/define-two-add.png
[29]: media/v2-update-provision/define-three-variable.png
[30]: media/v2-update-provision/define-four-avg.png
[31]: media/v2-update-provision/define-five-humidity.png
[32]: media/v2-update-provision/define-six-type.png
[33]: media/v2-update-provision/define-seven-hierarchy.png
[34]: media/v2-update-provision/define-eight-add-hierarchy.png
[35]: media/v2-update-provision/define-nine-created.png
[36]: media/v2-update-provision/define-ten-edit.png
[37]: media/v2-update-provision/define-eleven-chiller.png
[38]: media/v2-update-provision/define-twelve.png
[39]: media/v2-update-provision/define-thirteen-explore.png
[40]: media/v2-update-provision/define-fourteen-show-max.png
[41]: media/v2-update-provision/define-fifteen-filter.png
[42]: media/v2-update-provision/define-sixteen.png
[43]: media/v2-update-provision/define-seventeen.png
[44]: media/v2-update-provision/define-eighteen.png