---
title: Definiera en ny enhetstyp i Azure IoT Central | Microsoft Docs
description: I den här självstudien får du lära dig hur du som byggare definierar en ny enhetstyp i Azure IoT Central-programmet. Du kan definiera telemetri, tillstånd, egenskaper och inställningar för din typ.
author: dominicbetts
ms.author: dobett
ms.date: 01/28/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 3f20708308a9b5d7c09d6a498d659afac8a508c1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58090805"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application-new-ui-design"></a>Självstudier: Definiera en ny enhetstyp i Azure IoT Central-programmet (nytt användargränssnitt)

I den här självstudien får du lära dig hur du som byggare använder en enhetsmall för att definiera en ny typ av enhet i Azure IoT Central-programmet. Enhetsmallen definierar telemetri, tillstånd, egenskaper och inställningar för din enhetstyp.

För att du ska kunna testa programmet innan du ansluter en riktig enhet genererar IoT Central en simulerad enhet utifrån enhetsmallen när du skapar den.

I den här självstudien skapar du enhetsmallen **Ansluten luftkonditioneringsenhet**. En ansluten luftkonditioneringsenhet:

* Skickar telemetri såsom temperatur och luftfuktighet.
* Rapporterar tillstånd, till exempel om den är på eller av.
* Har enhetsegenskaper såsom version av inbyggd programvara och serienummer.
* Har inställningar såsom måltemperatur.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en ny enhetsmall
> * Lägga till telemetri till din enhet
> * Visa simulerad telemetri
> * Definiera händelsemått
> * Visa simulerade händelser
> * Definiera tillståndsmätning
> * Visa simulerat tillstånd
> * Använda inställningar och egenskaper
> * Använda kommandon
> * Visa din simulerade enhet på instrumentpanelen

## <a name="prerequisites"></a>Förutsättningar

Du behöver ett Azure IoT Central-program för att kunna genomföra den här självstudien. Om du har genomfört snabbstarten för att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md) kan du återanvända det program som du skapade i den snabbstarten. I annat fall utför du följande steg för att skapa ett tomt Azure IoT Central-program:

1. Gå till sidan [Application Manager](https://aka.ms/iotcentral) (Programhanterare) i Azure IoT Central.

2. Ange den e-postadress och det lösenord som du använder för att få åtkomst till din Azure-prenumeration:

    ![Ange ditt organisationskonto](./media/tutorial-define-device-type/sign-in.png)

3. Om du vill skapa ett nytt program för Azure IoT Central, Välj **nytt program**:

    ![Sidan Application Manager (Programhanterare) i Azure IoT Central](./media/tutorial-define-device-type/iotcentralhome.png)

4. Skapa ett nytt Azure IoT Central-program:
    
   * Välj **Utvärdering**. Du behöver inte en Azure-prenumeration för att skapa ett utvärderingsprogram.
    
      Mer information om kataloger och prenumerationer finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
    
   * Välj **Anpassat program**.
    
   * Om du vill kan du välja ett eget programnamn, till exempel **Contoso Air Conditioners**. Du får ett unikt URL-prefix från Azure IoT Central. Du kan ändra URL-prefixet till något som är enklare att komma ihåg.
    
   * Välj **Skapa**.

     ![Sidan Skapa program i Azure IoT Central](./media/tutorial-define-device-type/iotcentralcreate.png)

     Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).

## <a name="create-a-device-template"></a>Skapa en mall för enhet

Som byggare kan du skapa och redigera enhetsmallar i programmet. När du skapar en enhetsmall genererar Azure IoT Central en simulerad enhet utifrån mallen. Den simulerade enheten genererar telemetri som gör att du kan testa programmets beteende innan du ansluter en riktig enhet.

Om du vill lägga till en ny enhetsmall i programmet går du till sidan **Enhetsmallar**. Du väljer den **enheten mallar** på den vänstra navigeringsmenyn.

![Sidan Enhetsmallar](./media/tutorial-define-device-type/devicetemplates.png)

## <a name="add-a-device-template"></a>Lägga till en enhetsmall

Följande steg visar hur du skapar en ny enhetsmall kallad **Ansluten luftkonditioneringsenhet** för enheter som skickar temperaturtelemetri till programmet:

1. På den **enheten mallar** väljer **+ ny**:

    ![Sidan Enhetsmallar, Skapa enhetsmall](./media/tutorial-define-device-type/newtemplate.png)

2. På sidan visas de mallar som du kan välja bland.

    ![Mallbibliotek för enhet](./media/tutorial-define-device-type/devicetemplatelibrary.png)

3. Välj den **anpassade**, ange **anslutna luftkonditionering** som namnet på din enhet mallen och välj sedan **skapa**. Du kan även ladda upp en bild av enheten som är synlig för operatörer i Device Explorer:

    ![Anpassad enhet](./media/tutorial-define-device-type/createcustomdevice.png)

4. I enhetsmallen **Ansluten luftkonditioneringsenhet** kontrollerar du att du är på fliken **Mått**, där du definierar telemetrin. Varje enhetsmall som du definierar har separata flikar där du kan:

   * Ange _mått_, exempelvis telemetri, händelse och tillstånd som skickas av enheten.

   * Definiera de _inställningar_ som används för att styra enheten.

   * Definiera de _egenskaper_ som är enhetens metadata.

   * Definiera de _kommandon_ som ska köras direkt på enheten.

   * Definiera de _regler_ som är kopplade till enheten.

   * Anpassa enhetens _instrumentpanel_ för dina operatörer.

     ![Luftkonditioneringsmått](./media/tutorial-define-device-type/airconmeasurements.png)

     > [!NOTE]
     > Om du vill ändra namnet på mallen enheten, väljer du mallnamnet överst på sidan.

5. Lägg till telemetri-mätning temperatur, Välj **+ ny mätning**. Välj sedan **Telemetri** som måttenhet:

    ![Mått för ansluten luftkonditioneringsenhet](./media/tutorial-define-device-type/airconmeasurementsnew.png)

6. Varje typ av telemetri som du definierar för en enhetsmall innehåller [konfigurationsalternativ](howto-set-up-template.md) som:

   * Visningsalternativ.

   * Information om telemetrin.

   * Simuleringsparametrar.

     För att konfigurera telemetri för **Temperatur** använder du informationen i följande tabell:

     | Inställning              | Värde         |
     | -------------------- | -----------   |
     | Visningsnamn         | Temperatur   |
     | Fältnamn           | temperatur   |
     | Enheter                | F             |
     | Min                  | 60            |
     | Max                  | 110           |
     | Antal decimaler       | 0             |

     Du kan även välja en färg för telemetrivisningen. Välj för att spara telemetri definitionen **spara**:

     ![Konfigurera temperatursimulering](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Efter en kort stund visar fliken **Mått** ett diagram över temperaturtelemetrin från den simulerade anslutna luftkonditioneringsenheten. Använd kontrollerna för att hantera synlighet eller sammansättning eller redigera telemetridefinitionen:

    ![Visa temperatursimulering](./media/tutorial-define-device-type/viewsimulation.png)

8. Du kan även anpassa diagrammet med kontrollerna **Rad**, **Staplad** och **Redigera tidsintervall**:

    ![Anpassa diagrammet](./media/tutorial-define-device-type/customizechart.png)

## <a name="add-an-event-measurement"></a>Lägga till ett händelsemått

Använd händelser för att definiera tidpunktsdata som enheten skickar när en händelse, till exempel ett komponentfel, inträffar. Azure IoT Central kan simulera enhetshändelser så att du kan testa programmets beteende innan du ansluter en riktig enhet. Definiera händelsemått för enhetsmallen i vyn **Mått**.

1. Att lägga till den **fläkt meddelar fel** händelse, mått, Välj **+ ny mätning**. Välj sedan **Händelse** som måttyp:

    ![Mått för ansluten luftkonditioneringsenhet](./media/tutorial-define-device-type/eventnew.png)

2. Varje typ av händelse som du definierar för en enhetsmall innehåller [konfigurationsalternativ](howto-set-up-template.md) som:

   * Visningsnamn.

   * Fältnamn.

   * Allvarlighetsgrad.

     För att konfigurera händelsen **Fel på fläktmotor** använder du informationen i följande tabell:

     | Inställning              | Värde             |
     | -------------------- | -----------       |
     | Visningsnamn         | Fel på fläktmotor   |
     | Fältnamn           | fläktmotorfel       |
     | Severity             | Fel             |

     Välj för att spara händelsedefinitionen **spara**:

     ![Konfigurera händelsemätning](./media/tutorial-define-device-type/eventconfiguration.png)

3. Efter en kort stund visar fliken **Mått** ett diagram över de händelser som genereras slumpmässigt från den simulerade anslutna luftkonditioneringsenheten. Använd kontrollerna för att hantera synlighet eller redigera händelsedefinitionen:

    ![Visa händelsesimulering](./media/tutorial-define-device-type/eventview.png)

1. Välj händelsen i diagrammet om du vill se mer information om händelsen:

    ![Visa händelseinformation](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-a-state-measurement"></a>Definiera en tillståndsmätning

Du kan använda tillstånd för att definiera och visualisera tillståndet för enheten eller dess komponenter över en viss tidsperiod. Azure IoT Central kan simulera enhetstillstånd så att du kan testa programmets beteende innan du ansluter en riktig enhet. Du definierar tillståndsmått för enhetstypen i vyn **Mått**.

1. Att lägga till en **fläkt läge** tillstånd, mått, Välj **+ ny mätning**. Välj sedan **Tillstånd** som måttyp:

    ![Tillståndsmått för ansluten luftkonditioneringsenhet](./media/tutorial-define-device-type/statenew.png)

2. Varje typ av tillstånd som du definierar för en enhetsmall innehåller [konfigurationsalternativ](howto-set-up-template.md) som exempelvis:

   * Visningsnamn.

   * Fältnamn.

   * Värden med valfria visningsetiketter.

   * Färg för varje värde.

     För att konfigurera tillståndet **Fläktläge** använder du informationen i följande tabell:

     | Inställning              | Värde             |
     | -------------------- | -----------       |
     | Visningsnamn         | Fläktläge          |
     | Fältnamn           | fläktläge           |
     | Värde                | 1                 |
     | Visningsetikett        | Drift         |
     | Värde                | 0                 |
     | Visningsetikett        | Stoppad           |

     Välj för att spara tillståndet mätning definitionen **spara**:

     ![Konfigurera tillståndsmått](./media/tutorial-define-device-type/stateconfiguration.png)

3. Efter en kort stund visar fliken **Mått** ett diagram över de tillstånd som genereras slumpmässigt från den simulerade anslutna luftkonditioneringsenheten. Använd kontrollerna för att hantera synlighet eller redigera tillståndsdefinitionen:

    ![Visa tillståndssimulering](./media/tutorial-define-device-type/stateview.png)

4. Om för många datapunkter skickas från enheten på kort tid, visas tillståndsmåttet med ett annat utseende. Välj diagrammet för att se alla datapunkter inom den tidsperioden visas i kronologisk ordning. Du kan även begränsa tidsintervallet för att se måtten i detalj.

## <a name="settings-properties-and-commands"></a>Inställningar, egenskaper och kommandon

Inställningar, egenskaper och kommandon är olika värden som definieras i en enhetsmall och som associeras med varje enskild enhet:

* Du använder _inställningar_ för att skicka konfigurationsdata till en enhet från programmet. Till exempel kan en operatör använda en inställning för att ändra enhetens telemetriintervall från två sekunder till fem sekunder. När en operatör ändrar en inställning, markeras inställningen som väntande i användargränssnittet tills enheten bekräftar den.

* Du använder _egenskaper_ för att definiera metadata som associeras med enheten. Det finns två kategorier med egenskaper:
    
  * Du använder _programegenskaper_ för att registrera information om enheten i programmet. Du kan till exempel använda programegenskaper för att registrera en enhets plats och dess senaste servicedatum. Dessa egenskaper lagras i programmet och synkroniseras inte med enheten. En operatör kan tilldela egenskaper värden.

  * Du använder _enhetsegenskaper_ för göra så att en enhet kan skicka egenskapsvärden till programmet. De här egenskaperna kan bara ändras av enheten. För operatörer är enhetsegenskaper skrivskyddade. I det här scenariot med en ansluten luftkonditioneringsenhet är versionen av inbyggd programvara och enhetens serienummer enhetsegenskaper som rapporteras av enheten.
    
    Mer information finns i [Egenskaper](howto-set-up-template.md#properties) i instruktionerna om hur du konfigurerar en enhetsmall.

* Du använder _kommandon_ för att fjärrhantera din enhet från ditt program. Du kan köra kommandon direkt på enheten från molnet för att styra enheterna. En operatör kan exempelvis köra kommandon, t.ex. omstart, för att omedelbart starta om enheten.

## <a name="use-settings"></a>Använd inställningar

Du använder *inställningar* för att göra så att en operatör kan skicka konfigurationsdata till en enhet. I det här avsnittet lägger du till en inställning i enhetsmallen **Ansluten luftkonditioneringsenhet** som gör att en operatör kan ange måltemperaturen för den anslutna luftkonditioneringsenheten.

1. Gå till fliken **Inställningar** för enhetsmallen **Ansluten luftkonditioneringsenhet**.

2. Du kan skapa inställningar av olika typer, till exempel nummer eller text. Välj **nummer** och Lägg till ett antal inställning till din enhet.

3. För att konfigurera inställningen **Ange temperatur** använder du informationen i följande tabell:

    | Fält                | Värde           |
    | -------------------- | -----------     |
    | Visningsnamn         | Ange temperatur |
    | Fältnamn           | angeTemperatur  |
    | Måttenhet      | F               |
    | Antal decimaler       | 1               |
    | Minimivärde        | 20              |
    | Maxvärde        | 200             |
    | Initialt värde        | 80              |
    | Beskrivning          | Ange måltemperaturen för luftkonditioneringsenheten |

    Välj sedan **spara**:

    ![Konfigurera inställningen Ange temperatur](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > När enheten bekräftar en inställningsändring ändras statusen för inställningen ändras till **synkroniserad**.

4. Du kan anpassa layouten för fliken **Inställningar** genom att flytta och ändra storlek på inställningspanelerna:

    ![Anpassa layout för inställningar](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Använda egenskaper

Du använder *programegenskaper* för att lagra information om enheten i programmet. I det här avsnittet lägger du till programegenskaper i enhetsmallen **Ansluten luftkonditioneringsenhet** för att lagra enhetens plats och senaste servicedatum. Dessa egenskaper kan redigeras i programmet. Enheten rapporterar även egenskaper som serienummer och version för inbyggd programvara som är skrivskyddade i programmet.

1. Gå till fliken **Egenskaper** för enhetsmallen **Ansluten luftkonditioneringsenhet**.

1. Du kan skapa olika typer av enhetsegenskaper, till exempel nummer eller text. Om du vill lägga till en platsegenskap i enhetsmallen väljer du **Location**. För att konfigurera platsegenskapen använder du informationen i följande tabell:

    | Fält                | Värde                |
    | -------------------- | -------------------- |
    | Visningsnamn         | Plats             |
    | Fältnamn           | location             |
    | Initialt värde        | Seattle, WA          |
    | Beskrivning          | Enhetsplats      |

    Låt standardvärdena stå kvar i de andra fälten.

    ![Konfigurera enhetsegenskaperna](./media/tutorial-define-device-type/configureproperties.png)

    Välj **Spara**.

1. Om du vill lägga till en egenskap för senaste servicedatum i enhetsmallen väljer du **Datum**.

1. För att konfigurera egenskapen för senaste servicedatum använder du informationen i följande tabell:

    | Fält                | Värde                   |
    | -------------------- | ----------------------- |
    | Visningsnamn         | Senaste servicedatum       |
    | Fältnamn           | serviceDate             |
    | Initialt värde        | 1/1/2019                |
    | Beskrivning          | Senaste service           |

    ![Konfigurera enhetsegenskaperna](./media/tutorial-define-device-type/configureproperties2.png)

    Välj **Spara**.

1. Du kan anpassa layouten för fliken **Egenskaper** genom att flytta och ändra storlek på egenskapspanelerna.

1. Om du vill lägga till en enhetsegenskap såsom version av inbyggd programvara i enhetsmallen väljer du **Enhetsegenskap**.

1. För att konfigurera version av inbyggd programvara använder du informationen i följande tabell:

    | Fält                | Värde                   |
    | -------------------- | ----------------------- |
    | Visningsnamn         | Version av inbyggd programvara        |
    | Fältnamn           | versionInbyggdProgramvara         |
    | Datatyp            | text                    |
    | Beskrivning          | Luftkonditioneringsenhetens version av inbyggd programvara |

    ![Konfigurera version av inbyggd programvara](./media/tutorial-define-device-type/configureproperties3.png)

    Välj **Spara**.

1. Om du vill lägga till en enhetsegenskap såsom ett serienummer i enhetsmallen väljer du **Enhetsegenskap**.

1. För att konfigurera serienumret använder du informationen i följande tabell:

    | Fält                | Värde                   |
    | -------------------- | ----------------------- |
    | Visningsnamn         | Serienummer           |
    | Fältnamn           | serieNummer            |
    | Datatyp            | text                    |
    | Beskrivning          | Serienumret för luftkonditioneringsenheten  |

    ![Konfigurera serienummer](./media/tutorial-define-device-type/configureproperties4.png)

    Välj **Spara**.

    > [!NOTE]
    > Enhetsegenskap skickas från enheten till programmet. Värdena för version av inbyggd programvara och serienummer uppdateras när den riktiga enheten ansluts till IoT Central.

## <a name="use-commands"></a>Använda kommandon

Du använder _kommandon_ så att en operatör kan köra kommandon direkt på enheten. I det här avsnittet lägger du till ett kommando i enhetsmallen **Ansluten luftkonditioneringsenhet** som gör att en operatör kan echo-skicka ett visst meddelande på displayen på den anslutna luftkonditioneringsenheten.

1. Gå till fliken **Kommandon** för enhetsmallen **Ansluten luftkonditioneringsenhet** och redigera mallen.

1. Välj **+ nytt kommando** att lägga till ett kommando till enheten och börja konfigurera din nya kommandot.

1. Använd informationen i följande tabell för att konfigurera det nya kommandot:

    | Fält                | Värde           |
    | -------------------- | -----------     |
    | Visningsnamn         | Ekokommando    |
    | Fältnamn           | eko            |
    | Standardvärde för tidsgräns      | 30              |
    | Visningstyp         | text            |
    | Beskrivning          | Enhetskommando  |  

    Du kan lägga till ytterligare indata till kommandot genom att välja **+** för **indata fält**.

    ![Förbereda för att lägga till en inställning](./media/tutorial-define-device-type/commandsecho1.png)

     Välj **Spara**.

1. Du kan anpassa layouten för fliken **Kommandon** genom att flytta och ändra storlek på kommandopanelerna.

## <a name="view-your-simulated-device"></a>Visa din simulerade enhet

Nu när du har definierat enhetsmallen **Ansluten luftkonditioneringsenhet** kan du anpassa dess **Instrumentpanel** så att den innehåller de mått, inställningar och egenskaper som du definierat. Sedan kan du förhandsgranska instrumentpanelen som operatör:

1. Välj fliken **Instrumentpanel** för enhetsmallen **Ansluten luftkonditioneringsenhet**.

1. Välj **linjediagram** lägga till komponenten till den **instrumentpanelen**.

1. Konfigurera komponenten **Linjediagram** med hjälp av informationen i följande tabell:

    | Inställning      | Värde       |
    | ------------ | ----------- |
    | Rubrik        | Temperatur |
    | Tidsintervall   | Senaste 30 minuterna |
    | Mått     | Temperatursensor (Välj **synlighet** bredvid **temperatur**) |

    ![Inställningar för linjediagram](./media/tutorial-define-device-type/linechartsettings.png)

    Välj sedan **Spara**.

1. Välj den **Händelsehistorik** komponenten med hjälp av informationen i följande tabell:

    | Inställning      | Värde       |
    | ------------ | ----------- |
    | Rubrik        | Fläktmotorhändelser |
    | Tidsintervall   | Senaste 30 minuterna |
    | Mått     | Sprider meddelar fel (Välj **synlighet** bredvid **fläkt meddelar fel**) |

    ![Inställningar för händelse](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Välj sedan **Spara**.

1. Konfigurera komponenten **Tillståndshistorik** med hjälp av informationen i följande tabell:

    | Inställning      | Värde       |
    | ------------ | ----------- |
    | Rubrik        | Fläktläge |
    | Tidsintervall   | Senaste 30 minuterna |
    | Mått | Sprider läge (Välj **synlighet** bredvid **fläkt läge**) |

    ![Inställningar för linjediagram](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Välj sedan **Spara**.

1. Om du vill lägga till enhetens inställningar och egenskaper på instrumentpanelen väljer du **Inställningar och egenskaper**. Välj **Lägg till/ta bort** lägger du till inställningar eller egenskaper som du skulle vilja se i instrumentpanelen.

1. Konfigurera komponenten **Inställningar och egenskaper** med hjälp av informationen i följande tabell:

    | Inställning                 | Värde         |
    | ----------------------- | ------------- |
    | Rubrik                   | Enhetsegenskaper |
    | Inställningar och egenskaper | Ange temperatur<br/>Serienummer<br/>Version av inbyggd programvara |

    Inställningar och egenskaper som du tidigare har definierat på sidorna **Inställningar och Egenskaper** visas i **Tillgängliga kolumner**.

    ![Ange inställningar för temperaturegenskap](./media/tutorial-define-device-type/propertysettings4.png)

    Välj sedan **Spara**.

1. Du kan nu se simulerade data för din anslutna luftkonditionering på instrumentpanelen. Du kan redigera paneler och layout för instrumentpanelen:

    ![Visa instrumentpanel](./media/tutorial-define-device-type/dashboard.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Skapa en ny enhetsmall
> * Lägga till telemetri till din enhet
> * Visa simulerad telemetri
> * Definiera enhetshändelser
> * Visa simulerade händelser
> * Definiera ditt tillstånd
> * Visa simulerat tillstånd
> * Använda inställningar och egenskaper
> * Använda kommandon
> * Visa din simulerade enhet på instrumentpanelen

Nu när du har definierat en enhetsmall i Azure IoT Central-programmet föreslår vi följande steg:

* [Konfigurera regler och åtgärder för din enhet](tutorial-configure-rules.md)
* [Anpassa operatörsvyer](tutorial-customize-operator.md)