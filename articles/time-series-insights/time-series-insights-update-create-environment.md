---
title: 'Självstudie: Konfigurera en för hands versions miljö – Azure Time Series Insights | Microsoft Docs'
description: Lär dig hur du konfigurerar din Azure Time Series Insights Preview-miljö.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/06/2019
ms.custom: seodec18
ms.openlocfilehash: 2dd08af518be9b3fec84e756edf9532ef669f22f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012667"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Självstudie: Konfigurera en Azure Time Series Insights för hands versions miljö

Den här självstudien vägleder dig genom processen för att skapa en Azure Time Series Insights-miljö (förhandsversion) med betala per användning (PAYG).

I den här självstudiekursen får du lära du dig att:

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

   [Sidan ![Azure IoT Solution Accelerators](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

1. Välj **Testa nu**på nästa sida.

   [Sidan ![enhets simulering](media/v2-update-provision/device-two-try.png)](media/v2-update-provision/device-two-try.png#lightbox)

1. På sidan **skapa lösning för enhets simulering** anger du följande parametrar:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Distributions namn** | Ange ett unikt värde för en ny resurs grupp. Angivna Azure-resurser skapas och tilldelas till resursgruppen. |
    | **Azure-prenumeration** | Välj den prenumeration där du vill skapa din Time Series Insightss miljö. |
    | **Azure-plats** | Välj den region där du vill lagra Time Series Insightss miljön. Observera att enhets simulatorn bara erbjuds i ett begränsat antal regioner, vilket innebär att om du inte ser din önskade region kan du välja att bara välja en plats för självstudien och sedan skapa en ny TSD-miljö när du är redo att gå vidare till nästa fas av b oarding.  |
    | **Distributions alternativ** | Välj **etablera ny IoT Hub**. |

    1. Välj **Skapa**.
    [Sidan ![enhets simulering](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. Efter cirka 20 minuter är Solution Accelerator klar.

    [Sidan ![enhets simulering](media/v2-update-provision/device-two-ready.png)](media/v2-update-provision/device-two-ready.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Skapa en förhands granskning PAYG-miljö

I det här avsnittet beskrivs hur du skapar en Azure Time Series Insights för hands versions miljö och ansluter den till IoT-hubben som skapats av IoT Solution Accelerator med hjälp av [Azure Portal](https://portal.azure.com/).

1. Logga in på Azure-portalen med ditt prenumerationskonto.

1. Välj **skapa en resurs** > **Sakernas Internet** > **Time Series Insights**.

   [![Välj Sakernas Internet och välj sedan Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. I fönstret **skapa Time Series Insights miljö** går du till fliken **grundläggande** och anger följande parametrar:

    | Parameter | Åtgärd |
    | --- | ---|
    | **Miljönamn** | Ange ett unikt namn för förhands gransknings miljön för Azure Time Series Insights. |
    | **Prenumeration** | Ange den prenumeration där du vill skapa Azure Time Series Insights för hands versions miljön. Ett bra tips är att använda samma prenumeration som resten av IoT-resurserna som skapas av enhets simulatorn. |
    | **Resursgrupp** | Välj en befintlig resurs grupp eller skapa en ny resurs grupp för den Azure Time Series Insights för hands versions miljö resursen. En resursgrupp är en container för Azure-resurser. Ett bra tips är att använda samma resurs grupp som andra IoT-resurser som skapas av enhets simulatorn. |
    | **Plats** | Välj en data Center region för din Azure Time Series Insights Preview-miljö. För att undvika ytterligare svars tid är det bäst att skapa Azure Time Series Insights för hands versions miljön i samma region som din IoT-hubb som skapats av enhets simulatorn. |
    | **Nivå** |  Välj **PAYG** (*betala per*användning). Det här är SKU för Azure Time Series Insights-produkten (förhandsversion). |
    | **Egenskaps-ID** | Ange ett värde som unikt identifierar tids serie instansen. Det värde som du anger i rutan **egenskaps-ID** kan inte ändras senare. I den här självstudien anger du **iothub-Connection-Device-ID**. Mer information om Time Series ID finns i [metod tips för att välja ett Time Series-ID](./time-series-insights-update-how-to-id.md). |
    | **Lagringskontonamn** | Ange ett globalt unikt namn för ett nytt lagrings konto.|
    |**Aktivera varmt Arkiv**|Välj **Ja** om du vill aktivera varmt arkiv.|
    |**Data lagring (i dagar)**|Välj standard alternativet 7 dagar. |

    Välj **Nästa: händelse källa**.

   [![s fönstret för att skapa en Time Series Insights miljö](media/v2-update-provision/payg-two-1-create.png)](media/v2-update-provision/payg-two-1-create.png#lightbox)
   [![fönster för att skapa en Time Series Insights-miljö](media/v2-update-provision/payg-two-2-create.png)](media/v2-update-provision/payg-two-2-create.png#lightbox)

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

1. Välj **Skapa**.

    [![granska + skapa sida med knappen Skapa](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Du kan se statusen för din distribution:

    [![meddelande om att distributionen har slutförts](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Du har åtkomst till din Azure Time Series Insights Preview-miljö som standard om du är ägare till Azure-prenumerationen. Kontrol lera att du har åtkomst:

   1. Sök efter din resurs grupp och välj sedan din nyligen skapade Azure Time Series Insights för hands version. 
      [![vald miljö](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. På sidan Azure Time Series Insights förhands granskning väljer du **data åtkomst principer**: [![principer för data åtkomst](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Kontrol lera att dina autentiseringsuppgifter visas:

      [![listade autentiseringsuppgifter](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Om dina autentiseringsuppgifter inte finns med i listan måste du ge dig själv behörighet att komma åt miljön genom att välja Lägg till och söka efter dina autentiseringsuppgifter. Läs [Bevilja åtkomst till data](./time-series-insights-data-access.md) om du vill veta mer om hur du anger behörigheter.

## <a name="stream-data"></a>Strömma data

Nu när du har distribuerat Time Series Insights miljön börjar du strömma data för analys.

1. Gå tillbaka till [sidan med Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators). Leta upp din lösning på Solution Accelerator-instrumentpanelen och välj sedan **Starta**:

    [![starta lösningen för enhets simulering](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Välj **gå till Solution Accelerator**.

    [![starta lösningen för enhets simulering](media/v2-update-provision/device-accelerator.png)](media/v2-update-provision/device-accelerator.png#lightbox)

1. Du omdirigeras till sidan **Enhetssimulering för Microsoft Azure IoT**. I det övre högra hörnet på sidan väljer du **ny simulering**.

    [Sidan ![Azure IoT-simulering](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. I fönstret **simulerings konfiguration** anger du följande parametrar:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Namn** | Ange ett unikt namn för en simulator. |
    | **Beskrivning** | Ange en definition. |
    | **Simuleringens varaktighet** | Ange till **Kör på obestämd tid**. |
    | **Enhetsmodell** | Klicka på + **Lägg till en enhets typ** <br />**Namn**: ange **hiss**. <br />**Belopp**: ange **3**. <br /> Lämna kvar standardvärdena |
    | **Mål-IoT Hub** | Ange till **Använd i förväg etablerad IoT Hub**. |

    [![parametrar som ska anges](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Välj **Starta simulering**.

    På instrument panelen för enhets simulering visas **aktiva enheter** och **Totalt antal meddelanden**.

    [![instrument panel för Azure IoT-simulering](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>Analysera data

I det här avsnittet utför du grundläggande analys på dina tidsseriedata med hjälp av [Azure Time Series Insights-utforskaren (förhandsversion)](./time-series-insights-update-explorer.md).

1. Gå till din Azure Time Series Insights-utforskare (förhandsversion) genom att välja URL:en från resurssidan på [Azure Portal](https://portal.azure.com/).

    [![URL: en för Preview Explorer-Time Series Insights](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. I Time Series Insights Explorer visas ett fält som sträcker sig över skärmens överkant. Det här är din tillgänglighets väljare. Se till att du har minst två 2 m markerade, och om det behövs expanderar du tids ramen genom att välja och dra väljarna handtag till vänster och höger.

1. Du kommer att kunna se **Time Series-instanser** till vänster.


    [![lista över överordnade instanser](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Välj den första Time Series-instansen. Välj sedan **Visa tryck**.

    [![valda Time Series-instansen med meny kommando för att Visa genomsnitts tryck](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Ett tids serie diagram visas. Ändra **intervallet** till **30 s**.

    [diagram över ![tids serie](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Upprepa steg 3 med de andra två tids serie instanserna så att du ser alla tre, som du ser i det här diagrammet:

    [![diagram för alla tids serier](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Välj tids periods väljaren i det övre högra hörnet. Här kan du välja särskilda start-och slut tider i millisekunder, eller välja bland förkonfigurerade alternativ som senaste timmen. Du kan också ändra standard tids zonen.

    [![ange tidsintervallet till en timme](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

    Nedan visas en skärmdump av diagram fönstret när du har kört simuleringen för en timme:

    [![diagram fönster](media/v2-update-provision/analyze-seven-time.png)](media/v2-update-provision/analyze-seven-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definiera och tillämpa en modell

I det här avsnittet tillämpar du en modell för att strukturera data. För att slutföra modellen definierar du typer, hierarkier och instanser. Mer information om data modellering finns i [tids serie modell](./time-series-insights-update-tsm.md).

1. I utforskaren väljer du fliken **Modell**:

   [fliken ![modell i Utforskaren](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. På fliken **typer** väljer du **Lägg till**.

   [![knappen Lägg till för typer](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Ange följande parametrar:

    | Parameter | Åtgärd |
    | --- | ---|
    | **Namn** | Ange **hiss** |
    | **Beskrivning** | Ange **Detta är en typ definition för hissen** |

    [![knappen Lägg till för typer](media/v2-update-provision/define-three-properties.png)](media/v2-update-provision/define-three-properties.png#lightbox)

1. Välj sedan fliken **variabler** . [![fliken Välj variabler](media/v2-update-provision/define-four-variables.png)](media/v2-update-provision/define-four-variables.png#lightbox)

1. Välj **+ Lägg till variabel** och fyll i följande värden för den första variabeln i hiss typen. Du kommer att skriva tre variabler totalt.

    | Parameter | Åtgärd |
    | --- | --- |
    | **Namn** | Ange **Genomsnittlig temperatur**. |
    | **Metod** | Välj **numerisk** |
    | **Värde** | Välj från förval: Välj **temperatur (dubbel)** . <br /> Obs! det kan ta några minuter innan **värdet** fylls i automatiskt efter att Azure Time Series Insights för hands versionen börjar ta emot händelser.|
    | **Sammansättningsåtgärd** | Expandera **Avancerade alternativ**. <br /> Välj **AVG**. |

    [![val för definition av temperatur](media/v2-update-provision/define-five-variable.png)](media/v2-update-provision/define-five-variable.png#lightbox)

    Välj **Använd**.

    Välj **+ Lägg till variabel** igen och ange följande värden:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Namn** | Ange **genomsnittlig vibration**. |
    | **Metod** | Välj **numerisk** |
    | **Värde** | Välj från förval: Välj **vibrationer (dubbel)** . <br /> Obs! det kan ta några minuter innan **värdet** fylls i automatiskt efter att Azure Time Series Insights för hands versionen börjar ta emot händelser.|
    | **Sammansättningsåtgärd** | Expandera **Avancerade alternativ**. <br /> Välj **AVG**. |

    [![val för att definiera vibrationer](media/v2-update-provision/define-five-vibration.png)](media/v2-update-provision/define-five-vibration.png#lightbox)

    Välj **Använd**.

    Välj **+ Lägg till variabel** igen och ange följande värden för den tredje och sista variabeln:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Namn** | Ange **basyta**. |
    | **Metod** | Välj **kategoriska** |
    | **Värde** | Välj från förval: Välj **basyta (dubbel)** . <br /> Obs! det kan ta några minuter innan **värdet** fylls i automatiskt efter att Azure Time Series Insights för hands versionen börjar ta emot händelser.|
    | **Kategorier** | <span style="text-decoration: underline">Etikett</span>  - <span style="text-decoration: underline">värden</span> <br /> Lägre: 1, 2, 3, 4 <br /> Mellan: 5, 6, 7, 8, 9 <br /> Versal: 10, 11, 12, 13, 14, 15 |
    | **Standard kategori** | Ange **okänd** |

    [![val för att definiera vibrationer](media/v2-update-provision/define-five-floor.png)](media/v2-update-provision/define-five-floor.png#lightbox)

    Välj **Använd**. Du bör se tre variabler som skapas:

    [![val för att definiera vibrationer](media/v2-update-provision/define-five-floor2.png)](media/v2-update-provision/define-five-floor2.png#lightbox)

   Välj **Spara**. Den **typ** som skapas visas:

    [![val för att definiera vibrationer](media/v2-update-provision/define-five-floor3.png)](media/v2-update-provision/define-five-floor3.png#lightbox)

1. Välj fliken **hierarkier** . Välj **+ Lägg till**.

    [fliken ![hierarkier med knappen Lägg till](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. I fönstret **Redigera hierarki** anger du följande parametrar:

   | Parameter | Åtgärd |
   | --- | ---|
   | **Namn** | Ange **Platshierarkin**. |
   |**Nivåer**| Ange **land** som namn på den första nivån <br> Välj **+ Lägg till nivå** <br> Ange **stad** för den andra nivån och välj sedan **+ Lägg till nivå** <br> Ange **build** som namn på den tredje och den slutgiltiga nivån |

   Välj **Spara**.

    [![hierarki fält med knappen Skapa](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Du kan se den hierarki som du skapade:

    [![information om hierarkin](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Navigera till **instanser**. Under **åtgärder** längst till höger, och välj Penn ikonen för att redigera den första instansen med följande värden:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Typ** | Välj **hiss**. |
    | **Namn** | Ange **hiss 1**|
    | **Beskrivning** | Ange **instans för hiss 1** |

    [![att välja knappen Redigera för en instans](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

    Navigera till **instans fälten** och ange följande:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Hierarkier** | Välj **platshierarki** |
    | **Land/region** | Ange **USA** |
    | **Ort** | Ange **Seattle** |
    | **Byggnad** | Ange **områdes nål** |

    [![att välja knappen Redigera för en instans](media/v2-update-provision/define-ten2-edit.png)](media/v2-update-provision/define-ten2-edit.png#lightbox)

    Välj **Spara**.

1. Upprepa steg 8 med de andra två instanserna med följande värden:

    <span style="text-decoration: underline;">För hiss 2</span>:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Typ** | Välj **hiss**. |
    | **Namn** | Ange **hiss 2**|
    | **Beskrivning** | Ange **instansen för hiss 2** |
    | **Hierarkier** | Välj **platshierarki** |
    | **Land/region** | Ange **USA** |
    | **Ort** | Ange **Seattle** |
    | **Byggnad** | Ange **Pacific Science Center** |

    <span style="text-decoration: underline;">För hiss 3</span>:

    | Parameter | Åtgärd |
    | --- | --- |
    | **Typ** | Välj **hiss**. |
    | **Namn** | Ange **hiss 3**|
    | **Beskrivning** | Ange **instansen för hiss 3** |
    | **Hierarkier** | Välj **platshierarki** |
    | **Land/region** | Ange **USA** |
    | **Ort** | Ange **New York** |
    | **Byggnad** | Ange **Empire-tillstånds byggnad** |

1. Gå tillbaka till fliken **analysera** för att visa diagram fönstret. Under **platshierarki**, expandera alla hierarkinivåer för att Visa tids serie instanserna:

   [![fliken analysera](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Under **Pacific Science Center**väljer du tids serie instansen **hiss 2**och väljer sedan **Visa medel temperatur**.

    [![Time Series-instans visar genomsnitts temperatur](media/v2-update-provision/define-eleven-temperature.png)](media/v2-update-provision/define-eleven-temperature.png#lightbox)

1. För samma instans, **hiss 2**, väljer du **Visa basyta**.

    [![Time Series-instans visar golv](media/v2-update-provision/define-twelve-floor.png)](media/v2-update-provision/define-twelve-floor.png#lightbox)

    Med din kategoriska-variabel kan du avgöra hur lång tid en hiss ägnat åt övre, nedre och mittersta golv.

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
