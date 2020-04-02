---
title: 'Självstudiekurs: Konfigurera en förhandsgranskningsmiljö – Insikter i Azure Time Series | Microsoft-dokument'
description: 'Självstudiekurs: Lär dig hur du konfigurerar en miljö i förhandsversionen av Azure Time Series Insights.'
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 02/20/2020
ms.custom: seodec18
ms.openlocfilehash: 856410f2166ae442e8beadf36eac312748407b13
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529738"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Självstudiekurs: Konfigurera en förhandsversion av Insikter i Azure Time Series Insights

Den här självstudien guidar dig genom processen att skapa en Azure Time Series Insights Preview *pay-as-you-go* (PAYG) miljö.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * Skapa en Azure Time Series Insights-miljö (förhandsversion).
> * Anslut förhandsversionen av Azure Time Series Insights till en IoT-hubb.
> * Kör ett lösningsacceleratorexempel för att sända data till förhandsversionen av Azure Time Series Insights-miljön.
> * Utföra grundläggande analys av data.
> * Definiera en typ av och hierarki för tidsseriemodell och associera dem med dina instanser.

>[!TIP]
> [IoT-lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators) tillhandahåller förkonfigurerade lösningar i företagsklass som du kan använda för att påskynda utvecklingen av anpassade IoT-lösningar.

Registrera dig för en [kostnadsfri Azure-prenumeration](https://azure.microsoft.com/free/) om du inte redan har en.

## <a name="prerequisites"></a>Krav

* Du måste ha minst rollen **Deltagare** för Azure-prenumerationen. Mer information finns i [Hantera åtkomst med hjälp av rollbaserad åtkomstkontroll och Azure-portalen](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Skapa en enhetssimulering

I det här avsnittet skapar du tre simulerade enheter som skickar data till en Azure IoT Hub-instans.

1. Gå till [Sidan för Azure IoT-lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators). Sidan visar flera fördefinierade exempel. Logga in med ditt Azure-konto. Välj sedan **Enhetssimulering**.

   [![Sidan Azure IoT-lösningsacceleratorer.](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. På nästa sida väljer du **Försök nu**. Ange sedan de parametrar som krävs på **sidan Skapa enhetssimuleringslösning.**

   Parameter|Beskrivning
   ---|---
   **Namn på distribution** | Det här unika värdet används för att skapa en ny resursgrupp. Angivna Azure-resurser skapas och tilldelas till resursgruppen.
   **Azure-prenumeration** | Ange samma prenumeration som användes för att skapa time series insights-miljön i föregående avsnitt.
   **Distributionsalternativ** | Välj **Etablera ny IoT-hubb** om du vill skapa en ny IoT-hubb som är specifik för den här självstudien.
   **Azure-plats** | Ange samma region som användes för att skapa time series insights-miljön i föregående avsnitt.

   När du är klar väljer du **Skapa** för att etablera lösningens Azure-resurser. Det kan ta upp till 20 minuter att slutföra den här processen.

   [![Etablera enhetens simuleringslösning.](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. När etableringen är klar visas två meddelanden som meddelar att distributionstillståndet har flyttats från **etablering** till **klar**. 

   >[!IMPORTANT]
   > Gå inte in i din lösningsaccelerator än! Håll den här webbsidan öppen eftersom du kommer tillbaka till den senare.

   [![Enhetssimuleringslösningsetableringar har slutförts.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Inspektera nu de nyskapade resurserna i Azure-portalen. På sidan **Resursgrupper** märker du att en ny resursgrupp skapades med hjälp av **lösningsnamnet** som angavs i det sista steget. Anteckna de resurser som har skapats för enhetssimuleringen.

   [![Enhetssimuleringsresurser.](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Skapa en PAYG-förhandsgranskningsmiljö

I det här avsnittet beskrivs hur du skapar en förhandsversion av Azure Time Series Insights Och ansluter den till IoT-hubben som skapats av IoT Solution Accelerator med [Azure-portalen](https://portal.azure.com/).

1. Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-prenumerationskonto. 
1. Välj **+ skapa en resurs** i det övre vänstra hörnet. 
1. Välj kategorin **Sakernas Internet** och välj sedan **Time Series Insights**. 

   [![Välj miljöresursen Time Series Insights.](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. Ange följande parametrar på fliken Skapa insikter i **tidsserier:** **Basics**

    | Parameter | Åtgärd |
    | --- | ---|
    | **Miljönamn** | Ange ett unikt namn för förhandsversionen av Azure Time Series Insights. |
    | **Prenumeration** | Ange den prenumeration där du vill skapa förhandsversionen av Azure Time Series Insights. En bra metod är att använda samma prenumeration som resten av IoT-resurser som skapas av enhetssimulatorn. |
    | **Resursgrupp** | Välj en befintlig resursgrupp eller skapa en ny resursgrupp för miljöresursen för förhandsversionen av Azure Time Series Insights. En resursgrupp är en container för Azure-resurser. En bra metod är att använda samma resursgrupp som de andra IoT-resurser som skapas av enhetssimulatorn. |
    | **Location** | Välj en datacenterregion för din förhandsversion av Azure Time Series Insights. För att undvika ytterligare svarstid är det bäst att skapa din Azure Time Series Insights Preview-miljö i samma region som din IoT-hubb som skapats av enhetssimulatorn. |
    | **Nivå** |  Välj **PAYG** *(betala per du kör).* Det här är SKU för Azure Time Series Insights-produkten (förhandsversion). |
    | **Egenskapsnamn** | Ange ett värde som unikt identifierar din tidsserieinstans. Värdet som du anger i rutan **Egenskaps-ID** kan inte ändras senare. För den här självstudien anger du ***iothub-connection-device-id***. Om du vill veta mer om Time Series ID läser du [Metodtips för att välja ett tidsserie-ID](./time-series-insights-update-how-to-id.md). |
    | **Namn på lagringskonto** | Ange ett globalt unikt namn för ett nytt lagringskonto.|
    |**Aktivera varm butik**|Välj **Ja** för att aktivera varm butik. Du kan komma tillbaka senare och aktivera den här inställningen. |
    |**Lagring av data (i dagar)**|Välj standardalternativet på 7 dagar. |

    Välj **Nästa: Händelsekälla**.

   [![Ny miljökonfiguration för Time Series Insights.](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

   [![Konfigurera tidsserie-ID för miljön.](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

1. Ange följande parametrar på fliken **Händelsekälla:**

   | Parameter | Åtgärd |
   | --- | --- |
   | **Vill du skapa en händelsekälla?** | Välj **Ja**.|
   | **Namn** | Ange ett unikt värde för händelsekällans namn. |
   | **Typ av källa** | Välj **IoT Hub**. |
   | **Välj ett nav** | Välj **Välj befintlig**. |
   | **Prenumeration** | Välj den prenumeration som du använde för enhetssimulatorn. |
   | **IoT Hub-namn** | Välj det IoT-hubbnamn som du skapade för enhetssimulatorn. |
   | **Åtkomstprincip för IoT-hubb** | Välj **iothubowner**. |
   | **Konsumentgrupp för IoT Hub** | Välj **Ny**, ange ett unikt namn och välj sedan **+ Lägg till**. Konsumentgruppen måste vara ett unikt värde i förhandsversionen av Azure Time Series Insights. |
   | **Egenskap för tidsstämpel** | Det här värdet används för att identifiera **egenskapen Tidsstämpel** i inkommande telemetridata. Lämna den här rutan tom för den här självstudien. Den här simulatorn använder den inkommande tidsstämpeln från IoT Hub som är standardinställningen för Time Series Insights. |

   Välj **Granska + Skapa**.

   [![Konfigurera den skapade IoT-hubben som en händelsekälla.](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. Välj **Skapa**.

    [![Granska + Skapa sida, med knappen Skapa.](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    Du kan granska status för distributionen:

    [![Meddelande om att distributionen är klar.](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. Du har tillgång till din Azure Time Series Insights Preview-miljö som standard om du är ägare till Azure-prenumerationen. Kontrollera att du har åtkomst:

   1. Sök efter din resursgrupp och välj sedan din nyligen skapade Azure Time Series Insights Preview-miljö. 

      [![Välj och visa din miljö.](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. På sidan Förhandsgranskning av Azure Time Series Insights väljer du **Dataåtkomstprinciper:**

      [![Verifiera principer för dataåtkomst.](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. Kontrollera att dina autentiseringsuppgifter visas:

      Om dina autentiseringsuppgifter inte finns med i listan måste du ge dig själv behörighet att komma åt miljön genom att välja Lägg till och söka efter dina autentiseringsuppgifter. Läs [Bevilja åtkomst till data](./time-series-insights-data-access.md) om du vill veta mer om hur du anger behörigheter.

## <a name="stream-data"></a>Strömma data

Nu när du har distribuerat din Time Series Insights-miljö kan du börja strömma data för analys.

1. Gå tillbaka till din [Instrumentpanel för lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators#dashboard). Logga in igen, om det behövs, med samma Azure-konto som du har använt i den här självstudien. Välj din "Enhetslösning" och gå sedan **till din lösningsaccelerator** för att starta den distribuerade lösningen.

   [![Instrumentpanelen för lösningsacceleratorer.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Webbappen för enhetssimulering börjar med att du uppmanas att ge webbprogrammet **inloggningen och läsa din** profilbehörighet. Med den här behörigheten kan programmet hämta den användarprofilinformation som krävs för att stödja programmets funktion.

   [![Medgivande för webbprogram för enhetssimulering.](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. Välj **+ Ny simulering**. När sidan **Simuleringsinställning** har läses in anger du de parametrar som krävs.

    | Parameter | Åtgärd |
    | --- | --- |
    | **Namn** | Ange ett unikt namn för en simulator. |
    | **Beskrivning** | Ange en definition. |
    | **Simuleringens varaktighet** | Ange till **Kör på obestämd tid**. |
    | **Enhetsmodell** | Klicka på + **Lägg till en enhetstyp** <br />**Namn**: Ange **Hiss**. <br />**Belopp:** Ange **3**. <br /> Lämna de återstående standardvärdena |
    | **Mål-IoT Hub** | Ange till **Använd i förväg etablerad IoT Hub**. |

    [![Konfigurera parametrar och starta.](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    Välj **Starta simulering**.

    I instrumentpanelen för enhetssimulering visas **aktiva enheter** och totalt **antal meddelanden.**

    [![Instrumentpanelen för Azure IoT-simulering.](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Analysera data

I det här avsnittet utför du grundläggande analys på dina tidsseriedata med hjälp av [Azure Time Series Insights-utforskaren (förhandsversion)](./time-series-insights-update-explorer.md).

1. Gå till din Azure Time Series Insights-utforskare (förhandsversion) genom att välja URL:en från resurssidan på [Azure Portal](https://portal.azure.com/).

    [![Time Series Insights Förhandsgranska explorer URL.](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. I utforskaren Time Series Insights visas ett fält som sträcker sig överst på skärmen. Det här är din tillgänglighetsväljare. Se till att du har minst två 2 m markerat och utöka tidsramen om det behövs genom att markera och dra plockhandtagen åt vänster och höger.

1. **Tidsserieinstanser** visas på vänster sida.

    [![Lista över oparenterade instanser.](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. Välj första gången serieinstansen. Välj sedan **Visa temperatur**.

    [![Vald tidsserieförekomst med menykommando för att visa medeltemperatur.](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    Ett tidsseriediagram visas. Ändra **intervallet** till **30s**.

1. Upprepa föregående steg med de andra två tidsserieinstanserna så att du visar alla tre, som visas i det här diagrammet:

    [![Diagram för alla tidsserier.](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. Välj tidssplockaren i det övre högra hörnet. Här kan du välja specifika start- och sluttider ner till millisekunden, eller välja mellan förkonfigurerade alternativ som **Senaste 30 minuterna**. Du kan också ändra standardtidszonen.

    [![Ställ in tidsintervallet till de sista 30 minuterna.](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    Lösningsacceleratorns framsteg under de **senaste 30 minuterna** visas nu i Utforskaren För Time Series Insights.

## <a name="define-and-apply-a-model"></a>Definiera och tillämpa en modell

I det här avsnittet tillämpar du en modell för att strukturera data. För att slutföra modellen definierar du typer, hierarkier och instanser. Mer information om datamodellering finns i [Time Series Model](./time-series-insights-update-tsm.md).

1. I utforskaren väljer du fliken **Modell**:

   [![Visa fliken Modell i utforskaren.](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

   Välj + Lägg **till**på fliken **Typer** .

1. Ange följande parametrar:

    | Parameter | Åtgärd |
    | --- | ---|
    | **Namn** | Ange **hiss** |
    | **Beskrivning** | Ange **Det här är en typdefinition för Hiss** |

1. Välj sedan fliken **Variabler.** 

   Välj **+ Lägg till variabel** och fyll i följande värden för den första variabeln i hisstypen. Du kommer att skapa totalt tre variabler.

    | Parameter | Åtgärd |
    | --- | --- |
    | **Namn** | Ange **Genomsnittlig temperatur**. |
    | **Typ** | Välj **Numeriskt** |
    | **Värde** | Välj från förinställning: Välj **temperatur (Dubbel)**. <br /> Det kan ta några minuter innan **värde** fylls i automatiskt efter att Förhandsversionen av Azure Time Series Insights börjar ta emot händelser.|
    | **Sammansättningsåtgärd** | Expandera **avancerade alternativ**. <br /> Välj **AVG**. |

    Välj **Använd**. Sedan **+ Lägg till variabel** igen och ange följande värden:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Namn** | Ange **genomsnittlig vibration**. |
    | **Typ** | Välj **Numeriskt** |
    | **Värde** | Välj från förinställning: Välj **vibration (Dubbel)**. <br /> Det kan ta några minuter innan **värde** fylls i automatiskt efter att Förhandsversionen av Azure Time Series Insights börjar ta emot händelser.|
    | **Sammansättningsåtgärd** | Expandera **avancerade alternativ**. <br /> Välj **AVG**. |

    Välj **Använd**. Sedan **+ Lägg till variabel** igen och ange följande värden för den tredje och sista variabeln:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Namn** | Ange **golv**. |
    | **Typ** | Välj **kategorisk** |
    | **Värde** | Välj från förinställning: Välj **Golv (Dubbel)**. <br /> Det kan ta några minuter innan **värde** fylls i automatiskt efter att Förhandsversionen av Azure Time Series Insights börjar ta emot händelser.|
    | **Kategorier** | <span style="text-decoration: underline">Etikettvärden </span>   -  <span style="text-decoration: underline">Values</span> <br /> Lägre: 1,2,3,4 <br /> Mitten: 5,6,7,8,9 <br /> Övre: 10,11,12,13,14,15 |
    | **Standardkategori** | Ange **okänd** |

    [![Lägg till typvariabler.](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    Välj **Använd**.

1. Välj **Spara**. Tre variabler skapas och visas.

    [![När du har lagt till typen granskar du den i modellvyn.](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. Välj fliken **Hierarkier.** Välj sedan **+ Lägg till**.
   
   Ange följande parametrar i fönstret **Redigera hierarki:**

   | Parameter | Åtgärd |
   | --- | ---|
   | **Namn** | Ange **Platshierarkin**. |
   |**Nivåer**| Ange **land** som namnet på den första nivån <br> Välj **+ Lägg till nivå** <br> Ange **Ort** för den andra nivån och välj sedan **+ Lägg till nivå** <br> Ange **byggnad** som namnet på den tredje och sista nivån |

   Välj **Spara**.

   [![Visa den nya hierarkin i modellvyn.](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

1. Navigera till **instanser**. Under **Åtgärder** längst till höger och välj pennikonen för att redigera den första förekomsten med följande värden:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Typ** | Välj **Hiss**. |
    | **Namn** | Ange **hiss 1**|
    | **Beskrivning** | Ange **instans för Hiss 1** |

    Navigera till **instansfält** och ange följande värden:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Hierarkier** | Välj **platshierarki** |
    | **Land/region** | Ange **USA** |
    | **Staden** | Ange **Seattle** |
    | **Byggnad** | Ange **utrymme nål** |

    Välj **Spara**.

1. Upprepa föregående steg med de andra två instanserna när du använder följande värden:

    **För hiss 2:**

    | Parameter | Åtgärd |
    | --- | --- |
    | **Typ** | Välj **Hiss**. |
    | **Namn** | Ange **Hiss 2**|
    | **Beskrivning** | Ange **instans för Hiss 2** |
    | **Hierarkier** | Välj **platshierarki** |
    | **Land/region** | Ange **USA** |
    | **Staden** | Ange **Seattle** |
    | **Byggnad** | Gå in **i Pacific Science Center** |

    **För hiss 3:**

    | Parameter | Åtgärd |
    | --- | --- |
    | **Typ** | Välj **Hiss**. |
    | **Namn** | Ange **Hiss 3**|
    | **Beskrivning** | Ange **instans för Hiss 3** |
    | **Hierarkier** | Välj **platshierarki** |
    | **Land/region** | Ange **USA** |
    | **Staden** | Ange **New York** |
    | **Byggnad** | Ange **Empire State Building** |

    [![Visa de uppdaterade instanserna.](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. Gå tillbaka till fliken **Analysera** för att visa diagramfönstret. Expandera alla hierarkinivåer under **Platshierarki**för att visa tidsserieinstanserna:

    [![Visa alla hierarkier i diagramvyn.](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. Under **Pacific Science Center**väljer du Time Series Instance Elevator **2**och väljer sedan **Visa medeltemperatur**.

1. I samma exempel väljer **Hiss 2**, **Visa golv**.

    Med din kategoriska variabel kan du bestämma hur mycket tid hissen spenderade på de övre, nedre och mellersta våningarna.

    [![Visualisera Hiss 2 med hierarki och data.](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Rensa resurser

Nu när du har slutfört självstudien rensar du de resurser du har skapat:

1. På den vänstra menyn i [Azure-portalen](https://portal.azure.com)väljer du **Alla resurser**, hitta din Azure Time Series Insights-resursgrupp.
1. Ta antingen bort hela resursgruppen (och alla resurser i den) genom att välja **Ta bort** eller ta bort varje resurs individuellt.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:  

* Skapa och kör acceleratorn för enhetssimulering.
* Skapa en Azure Time Series Insights-miljö (förhandsversion) med betala per användning.
* Anslut förhandsversionen av Azure Time Series Insights till en iot-hubb.
* Kör ett lösningsacceleratorexempel för att sända data till förhandsversionen av Azure Time Series Insights-miljön.
* Utföra en grundläggande analys av data.
* Definiera en typ av och hierarki för tidsseriemodell och associera dem med dina instanser.

Nu när du vet hur du skapar din egen Azure Time Series Insights-miljö (förhandsversion) kan du lära dig mer om viktiga begrepp i Azure Time Series Insights.

Läs om lagringskonfiguration för Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Lagring och inkommande för Azure Time Series Insights (förhandsversion)](./time-series-insights-update-storage-ingress.md)

Läs mer om Time Series-modeller:

> [!div class="nextstepaction"]
> [Datamodellering för Azure Time Series Insights (förhandsversion)](./time-series-insights-update-tsm.md)

Läs mer om Ansluta din miljö till Power BI

> [!div class="nextstepaction"]
> [Visualisera data från Time Series Insights i Power BI](./how-to-connect-power-bi.md)
