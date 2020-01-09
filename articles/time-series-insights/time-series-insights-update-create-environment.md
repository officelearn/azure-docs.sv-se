---
title: 'Självstudie: Konfigurera en för hands versions miljö – Azure Time Series Insights | Microsoft Docs'
description: 'Självstudie: Lär dig hur du konfigurerar en miljö i Azure Time Series Insights för hands versionen.'
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 12/26/2019
ms.custom: seodec18
ms.openlocfilehash: 6adb48b9d6c490b60302f93101506ec53679ae4f
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530403"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Självstudie: Konfigurera en Azure Time Series Insights för hands versions miljö

Den här självstudien vägleder dig genom processen att skapa en Azure Time Series Insights *för hands* versions förhands gransknings miljö (PAYG).

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
>
> * Skapa en Azure Time Series Insights-miljö (förhandsversion).
> * Anslut Azure Time Series Insights för hands versions miljö till en IoT Hub.
> * Kör ett lösningsacceleratorexempel för att sända data till förhandsversionen av Azure Time Series Insights-miljön.
> * Utföra grundläggande analys av data.
> * Definiera en typ av och hierarki för tidsseriemodell och associera dem med dina instanser.
> * Använd Power BI-anslutningen och visualisera data i Power BI.

>[!TIP]
> [IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators) tillhandahåller förkonfigurerade lösningar i företags klass som du kan använda för att påskynda utvecklingen av anpassade IoT-lösningar.

Registrera dig för en [kostnads fri Azure-prenumeration](https://azure.microsoft.com/free/) om du inte redan har en.

## <a name="prerequisites"></a>Krav

* Du måste minst ha **deltagar** rollen för Azure-prenumerationen. Mer information finns i [Hantera åtkomst med hjälp av rollbaserad åtkomst kontroll och Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Skapa en enhetssimulering

I det här avsnittet ska du skapa tre simulerade enheter som skickar data till en Azure IoT Hub-instans.

1. Gå till [Sidan för Azure IoT-lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators). Sidan visar flera fördefinierade exempel. Logga in med ditt Azure-konto. Välj sedan **Enhetssimulering**.

   [Sidan ![Azure IoT Solution Accelerators.](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. Välj **Testa nu**på nästa sida. Ange sedan de nödvändiga parametrarna på sidan **skapa lösning för enhets simulering** .

   Parameter|Beskrivning
   ---|---
   **Distributions namn** | Det unika värdet används för att skapa en ny resurs grupp. Angivna Azure-resurser skapas och tilldelas till resursgruppen.
   **Azure-prenumeration** | Ange samma prenumeration som användes för att skapa din Time Series Insights-miljö i föregående avsnitt.
   **Distributions alternativ** | Välj **etablera nya IoT Hub** för att skapa en ny IoT-hubb som är speciell för den här självstudien.
   **Azure-plats** | Ange samma region som användes för att skapa din Time Series Insights-miljö i föregående avsnitt.

   När du är klar väljer du **skapa** för att etablera lösningens Azure-resurser. Det kan ta upp till 20 minuter att slutföra den här processen.

   [![etablera lösningen för enhets simulering.](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. När etableringen har slutförts visas två uppdateringar som visar att distributions statusen har flyttats från **etablering** till **klar**. 

   >[!IMPORTANT]
   > Ange inte Solution Accelerator ännu! Behåll den här webb sidan öppen eftersom du kommer tillbaka till den senare.

   [lösnings etableringen för enhets simulering för ![har slutförts.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Granska nu de nyligen skapade resurserna i Azure Portal. På sidan **resurs grupper** ser du att en ny resurs grupp har skapats med hjälp av **lösnings namnet** som angavs i det sista steget. Anteckna de resurser som har skapats för enhets simuleringen.

   [resurser för ![enhets simulering.](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Skapa en förhands granskning PAYG-miljö

I det här avsnittet beskrivs hur du skapar en Azure Time Series Insights för hands versions miljö och ansluter den till IoT-hubben som skapats av IoT Solution Accelerator med hjälp av [Azure Portal](https://portal.azure.com/).

1. Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure-prenumerations konto. 
1. Välj **+ skapa en resurs** i det övre vänstra hörnet. 
1. Välj kategorin **Sakernas Internet** och välj sedan **Time Series Insights**. 

   [![väljer du Time Series Insights miljö resursen.](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. I fönstret **skapa Time Series Insights miljö** går du till fliken **grundläggande** och anger följande parametrar:

    | Parameter | Åtgärd |
    | --- | ---|
    | **Miljönamn** | Ange ett unikt namn för förhands gransknings miljön för Azure Time Series Insights. |
    | **Prenumeration** | Ange den prenumeration där du vill skapa Azure Time Series Insights för hands versions miljön. Ett bra tips är att använda samma prenumeration som resten av IoT-resurserna som skapas av enhets simulatorn. |
    | **Resursgrupp** | Välj en befintlig resurs grupp eller skapa en ny resurs grupp för den Azure Time Series Insights för hands versions miljö resursen. En resursgrupp är en container för Azure-resurser. Ett bra tips är att använda samma resurs grupp som andra IoT-resurser som skapas av enhets simulatorn. |
    | **Plats** | Välj en data Center region för din Azure Time Series Insights Preview-miljö. För att undvika ytterligare svars tid är det bäst att skapa Azure Time Series Insights för hands versions miljön i samma region som din IoT-hubb som skapats av enhets simulatorn. |
    | **Nivå** |  Välj **PAYG** (*betala per*användning). Det här är SKU för Azure Time Series Insights-produkten (förhandsversion). |
    | **Egenskapsnamn** | Ange ett värde som unikt identifierar tids serie instansen. Det värde som du anger i rutan **egenskaps-ID** kan inte ändras senare. I den här självstudien anger du ***iothub-Connection-Device-ID***. Mer information om Time Series ID finns i [metod tips för att välja ett Time Series-ID](./time-series-insights-update-how-to-id.md). |
    | **Lagringskontonamn** | Ange ett globalt unikt namn för ett nytt lagrings konto.|
    |**Aktivera varmt Arkiv**|Välj **Ja** om du vill aktivera varmt arkiv. Du kan komma tillbaka senare och aktivera den här inställningen. |
    |**Data lagring (i dagar)**|Välj standard alternativet 7 dagar. |

    Välj **Nästa: händelse källa**.

   [![ny Time Series Insights miljö konfiguration.](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

   [![konfigurera Time Series-ID för miljön.](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

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
   | **IoT Hub konsument grupp** | Välj **nytt**, ange ett unikt namn och välj sedan **+ Lägg till**. Konsument gruppen måste vara ett unikt värde i Azure Time Series Insights Preview. |
   | **Egenskap för tidsstämpel** | Det här värdet används för att identifiera **timestamp** -egenskapen i dina inkommande telemetridata. I den här självstudien lämnar du rutan tom. Den här simulatorn använder den inkommande tidsstämpeln från IoT Hub som är standardinställningen för Time Series Insights. |

   Välj **Granska + Skapa**.

   [![konfigurera den skapade IoT-hubben som en händelse källa.](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. Välj **Skapa**.

    [![på sidan Granska + skapa med knappen Skapa.](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    Du kan se statusen för din distribution:

    [![meddelande om att distributionen har slutförts.](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. Du har åtkomst till din Azure Time Series Insights Preview-miljö som standard om du är ägare till Azure-prenumerationen. Kontrol lera att du har åtkomst:

   1. Sök efter din resurs grupp och välj sedan din nyligen skapade Azure Time Series Insights för hands version. 

      [![välja och visa din miljö.](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. På sidan Azure Time Series Insights förhands granskning väljer du **data åtkomst principer**:

      [![kontrol lera principer för data åtkomst.](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. Kontrol lera att dina autentiseringsuppgifter visas:

      Om dina autentiseringsuppgifter inte finns med i listan måste du ge dig själv behörighet att komma åt miljön genom att välja Lägg till och söka efter dina autentiseringsuppgifter. Läs [Bevilja åtkomst till data](./time-series-insights-data-access.md) om du vill veta mer om hur du anger behörigheter.

## <a name="stream-data"></a>Strömma data

Nu när du har distribuerat Time Series Insights miljön börjar du strömma data för analys.

1. Gå tillbaka till din [Instrumentpanel för lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators#dashboard). Logga in igen, om det behövs, med samma Azure-konto som du har använt i den här självstudien. Välj din "enhets lösning" och **gå sedan till Solution Accelerator** för att starta din distribuerade lösning.

   [instrument panel för ![Solution Accelerators.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Webb programmet för enhets simulering börjar genom att du tillfrågas om du vill ge webb programmet det **loggade du in och läsa din profil** behörighet. Med den här behörigheten kan programmet hämta den användar profil information som krävs för att stödja programmets funktion.

   [godkännande av webb program för ![enhets simulering.](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. Välj **+ ny simulering**. Ange de parametrar som krävs efter att installations sidan för **simulering** har lästs in.

    | Parameter | Åtgärd |
    | --- | --- |
    | **Namn** | Ange ett unikt namn för en simulator. |
    | **Beskrivning** | Ange en definition. |
    | **Simuleringens varaktighet** | Ange till **Kör på obestämd tid**. |
    | **Enhetsmodell** | Klicka på + **Lägg till en enhets typ** <br />**Namn**: ange **hiss**. <br />**Belopp**: ange **3**. <br /> Lämna kvar standardvärdena |
    | **Mål-IoT Hub** | Ange till **Använd i förväg etablerad IoT Hub**. |

    [![Konfigurera parametrar och starta.](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    Välj **Starta simulering**.

    På instrument panelen för enhets simulering visas **aktiva enheter** och **Totalt antal meddelanden**.

    [![instrument panelen för Azure IoT-simulering.](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Analysera data

I det här avsnittet utför du grundläggande analys på dina tidsseriedata med hjälp av [Azure Time Series Insights-utforskaren (förhandsversion)](./time-series-insights-update-explorer.md).

1. Gå till din Azure Time Series Insights-utforskare (förhandsversion) genom att välja URL:en från resurssidan på [Azure Portal](https://portal.azure.com/).

    [![URL: en för Preview Explorer-Time Series Insights.](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. I Time Series Insights Explorer visas ett fält som sträcker sig över skärmens överkant. Det här är din tillgänglighets väljare. Se till att du har minst två 2 m markerade och expandera sedan tids ramen genom att välja och dra väljarna handtag till vänster och höger.

1. Du kommer att kunna se **Time Series-instanser** till vänster.

    [![lista över överordnade instanser.](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. Välj serie instansen för första gången. Välj sedan **Visa temperatur**.

    [![valda Time Series-instansen med meny kommando för att Visa medel temperatur.](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    Ett tids serie diagram visas. Ändra **intervallet** till **30 s**.

1. Upprepa föregående steg med de andra två tids serie instanserna så att du ser alla tre, som du ser i det här diagrammet:

    [![diagram för alla tids serier.](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. Välj tids periods väljaren i det övre högra hörnet. Här kan du välja särskilda start-och slut tider i millisekunder, eller välja bland förkonfigurerade alternativ, till exempel de **senaste 30 minuterna**. Du kan också ändra standard tids zonen.

    [![ange tidsintervallet till de senaste 30 minuterna.](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    Solution Accelerator-förloppet under de **senaste 30 minuterna** visas nu i Time Series Insights Explorer.

## <a name="define-and-apply-a-model"></a>Definiera och tillämpa en modell

I det här avsnittet tillämpar du en modell för att strukturera data. För att slutföra modellen definierar du typer, hierarkier och instanser. Mer information om data modellering finns i [tids serie modell](./time-series-insights-update-tsm.md).

1. I utforskaren väljer du fliken **Modell**:

   [![Visa fliken modell i Utforskaren.](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

   På fliken **typer** väljer du **+ Lägg till**.

1. Ange följande parametrar:

    | Parameter | Åtgärd |
    | --- | ---|
    | **Namn** | Ange **hiss** |
    | **Beskrivning** | Ange **Detta är en typ definition för hissen** |

1. Välj sedan fliken **variabler** . 

   Välj **+ Lägg till variabel** och fyll i följande värden för den första variabeln i hiss typen. Du kommer att skriva tre variabler totalt.

    | Parameter | Åtgärd |
    | --- | --- |
    | **Namn** | Ange **Genomsnittlig temperatur**. |
    | **Metod** | Välj **numerisk** |
    | **Värde** | Välj från förval: Välj **temperatur (dubbel)** . <br /> Obs! det kan ta några minuter innan **värdet** fylls i automatiskt efter att Azure Time Series Insights för hands versionen börjar ta emot händelser.|
    | **Sammansättningsåtgärd** | Expandera **Avancerade alternativ**. <br /> Välj **AVG**. |

    Välj **Använd**. Sedan, **+ Lägg till variabel** igen och ange följande värden:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Namn** | Ange **genomsnittlig vibration**. |
    | **Metod** | Välj **numerisk** |
    | **Värde** | Välj från förval: Välj **vibrationer (dubbel)** . <br /> Obs! det kan ta några minuter innan **värdet** fylls i automatiskt efter att Azure Time Series Insights för hands versionen börjar ta emot händelser.|
    | **Sammansättningsåtgärd** | Expandera **Avancerade alternativ**. <br /> Välj **AVG**. |

    Välj **Använd**. Sedan, **+ Lägg till variabel** igen och ange följande värden för den tredje och sista variabeln:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Namn** | Ange **basyta**. |
    | **Metod** | Välj **kategoriska** |
    | **Värde** | Välj från förval: Välj **basyta (dubbel)** . <br /> Obs! det kan ta några minuter innan **värdet** fylls i automatiskt efter att Azure Time Series Insights för hands versionen börjar ta emot händelser.|
    | **Kategorier** | <span style="text-decoration: underline">Etikett</span>  - <span style="text-decoration: underline">värden</span> <br /> Lägre: 1, 2, 3, 4 <br /> Mellan: 5, 6, 7, 8, 9 <br /> Versal: 10, 11, 12, 13, 14, 15 |
    | **Standard kategori** | Ange **okänd** |

    [![lägga till typer av variabler.](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    Välj **Använd**.

1. Välj **Spara**. Du bör se tre variabler som skapats.

    [![när du har lagt till typen, se den i vyn modell.](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. Välj fliken **hierarkier** . Välj sedan **+ Lägg till**.
   
   I fönstret **Redigera hierarki** anger du följande parametrar:

   | Parameter | Åtgärd |
   | --- | ---|
   | **Namn** | Ange **Platshierarkin**. |
   |**Nivåer**| Ange **land** som namn på den första nivån <br> Välj **+ Lägg till nivå** <br> Ange **stad** för den andra nivån och välj sedan **+ Lägg till nivå** <br> Ange **build** som namn på den tredje och den slutgiltiga nivån |

   Välj **Spara**.

   [![Visa din nya hierarki i vyn modell.](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

1. Navigera till **instanser**. Under **åtgärder** längst till höger, och välj Penn ikonen för att redigera den första instansen med följande värden:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Typ** | Välj **hiss**. |
    | **Namn** | Ange **hiss 1**|
    | **Beskrivning** | Ange **instans för hiss 1** |

    Navigera till **instans fälten** och ange följande värden:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Hierarkier** | Välj **platshierarki** |
    | **Land/region** | Ange **USA** |
    | **Ort** | Ange **Seattle** |
    | **Byggnad** | Ange **områdes nål** |

    Välj **Spara**.

1. Upprepa föregående steg med de andra två instanserna medan du använder följande värden:

    **För hiss 2:**

    | Parameter | Åtgärd |
    | --- | --- |
    | **Typ** | Välj **hiss**. |
    | **Namn** | Ange **hiss 2**|
    | **Beskrivning** | Ange **instansen för hiss 2** |
    | **Hierarkier** | Välj **platshierarki** |
    | **Land/region** | Ange **USA** |
    | **Ort** | Ange **Seattle** |
    | **Byggnad** | Ange **Pacific Science Center** |

    **För hiss 3:**

    | Parameter | Åtgärd |
    | --- | --- |
    | **Typ** | Välj **hiss**. |
    | **Namn** | Ange **hiss 3**|
    | **Beskrivning** | Ange **instansen för hiss 3** |
    | **Hierarkier** | Välj **platshierarki** |
    | **Land/region** | Ange **USA** |
    | **Ort** | Ange **New York** |
    | **Byggnad** | Ange **Empire-tillstånds byggnad** |

    [![Visa de uppdaterade instanserna.](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. Gå tillbaka till fliken **analysera** för att visa diagram fönstret. Under **platshierarki**, expandera alla hierarkinivåer för att Visa tids serie instanserna:

    [![Visa alla hierarkier i diagramvyn.](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. Under **Pacific Science Center**väljer du tids serie instansen **hiss 2**och väljer sedan **Visa medel temperatur**.

1. För samma instans, **hiss 2**, väljer du **Visa basyta**.

    Med din kategoriska-variabel kan du avgöra hur lång tid en hiss ägnat åt övre, nedre och mittersta golv.

    [![visualisera hiss 2 med hierarki och data.](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Rensa resurser

Nu när du har slutfört självstudien rensar du de resurser som du har skapat:

1. På den vänstra menyn i [Azure Portal](https://portal.azure.com)väljer du **alla resurser**, letar reda på Azure Time Series Insights resurs gruppen.
1. Ta antingen bort hela resurs gruppen (och alla resurser som ingår i den) genom att välja **ta bort** eller ta bort varje resurs individuellt.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:  

* Skapa och kör acceleratorn för enhetssimulering.
* Skapa en Azure Time Series Insights-miljö (förhandsversion) med betala per användning.
* Anslut Azure Time Series Insights Preview-miljö till en IoT-hubb.
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
