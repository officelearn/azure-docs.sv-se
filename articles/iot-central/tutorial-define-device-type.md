---
title: Definiera en ny enhetstyp i Azure IoT Central | Microsoft Docs
description: I den här självstudien får du lära dig hur du som byggare definierar en ny enhetstyp i Azure IoT Central-programmet. Du kan definiera telemetri, tillstånd, egenskaper och inställningar för din typ.
author: tbhagwat3
ms.author: tanmayb
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 8c69a863d8719a0e4eba30a6d5a619210e7f95b8
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50156042"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Självstudie: Definiera en ny enhetstyp i Azure IoT Central-programmet

I den här självstudien får du lära dig hur du som byggare använder en enhetsmall för att definiera en ny typ av enhet i Azure IoT Central-programmet. Enhetsmallen definierar telemetri, tillstånd, egenskaper och inställningar för din enhetstyp.

För att du ska kunna testa programmet innan du ansluter en riktig enhet genererar Azure IoT Central en simulerad enhet utifrån enhetsmallen när du skapar den.

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

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver ett Azure IoT Central-program för att kunna genomföra den här självstudien. Om du har genomfört snabbstarten för att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md) kan du återanvända det program som du skapade i den snabbstarten. I annat fall utför du följande steg för att skapa ett tomt Azure IoT Central-program:

1. Gå till sidan [Application Manager](https://aka.ms/iotcentral) (Programhanterare) i Azure IoT Central.

2. Ange den e-postadress och det lösenord som du använder för att få åtkomst till din Azure-prenumeration:

   ![Ange ditt organisationskonto](./media/tutorial-define-device-type/sign-in.png)

3. Börja skapa ett nytt Azure IoT Central-program genom att välja **Nytt program**:

    ![Sidan Application Manager (Programhanterare) i Azure IoT Central](./media/tutorial-define-device-type/iotcentralhome.png)

4. Skapa ett nytt Azure IoT Central-program:
    
    * Välj **Kostnadsfri**. Det finns inget prenumerationskrav för den kostnadsfria utvärderingsversionen på 7 dagar.
    
       Mer information om kataloger och prenumerationer finns på sidan om [hur du skapar Azure IoT Central-programmet](howto-create-application.md).
    
    * Välj **Anpassat program**.
    
    * Om du vill kan du välja ett eget programnamn, till exempel **Contoso Air Conditioners**. Du får ett unikt URL-prefix från Azure IoT Central. Du kan ändra URL-prefixet till något som är enklare att komma ihåg.
    
    * Välj **Skapa**.

    ![Sidan Skapa program i Azure IoT Central](./media/tutorial-define-device-type/iotcentralcreatenew.png)

    Mer information finns på sidan om att [skapa Azure IoT Central-programmet](howto-create-application.md).

## <a name="create-a-new-custom-device-template"></a>Skapa en ny anpassad enhetsmall

Som byggare kan du skapa och redigera enhetsmallar i programmet. När du skapar en enhetsmall genererar Azure IoT Central en simulerad enhet utifrån mallen. Den simulerade enheten genererar telemetri som gör att du kan testa programmets beteende innan du ansluter en fysisk enhet.

Om du vill lägga till en ny enhetsmall för programmet går du till sidan **Programverktyg**. För att göra det väljer du **Programverktyg** på den vänstra navigeringsmenyn.

![Sidan Application Builder](./media/tutorial-define-device-type/builderhome.png)

## <a name="add-a-device-and-define-telemetry"></a>Lägga till en enhet och definiera telemetri

Följande steg visar hur du skapar en ny enhetsmall kallad **Ansluten luftkonditioneringsenhet** för enheter som skickar temperaturtelemetri till programmet:

1. På sidan **Programverktyg** väljer du **Skapa enhetsmall**:

    ![Skapa enhetsmall på sidan Programverktyg](./media/tutorial-define-device-type/builderhomedevices.png)

2. På sidan **Enhetsmallar** väljer du **Anpassad**. Med en **anpassad** enhetsmall kan du definiera alla egenskaper och beteenden för den anslutna luftkonditioneringsenheten:

    ![Enheter](./media/tutorial-define-device-type/builderhomedevicescustom.png)

3. På sidan för att **skapa en ny enhetsmall** anger du **Ansluten luftkonditioneringsenhet** som namnet på enheten och väljer sedan **Skapa**. Du kan även ladda upp en bild av enheten som är synlig för operatörer i Device Explorer:

    ![Anpassad enhet](./media/tutorial-define-device-type/createcustomdevice.png)

4. I enhetsmallen **Ansluten luftkonditioneringsenhet** kontrollerar du att du är på sidan **Mått**, där du definierar telemetrin. Varje enhetsmall som du definierar har separata sidor där du kan:

    * Ange mått som telemetri, händelse och tillstånd, som skickas av enheten.
    
    * Definiera de inställningar som används för att styra enheten.
    
    * Definiera de egenskaper som är enhetsmetadata.

    * Definiera kommandon som ska köras direkt på enheten.
    
    * Definiera de regler som är kopplade till enheten.
    
    * Anpassa enhetens instrumentpanelen enheten för dina operatörer.

    När du definierar enhetsmallen väljer du först **Redigera mall** för att redigera mallen. När du är klar väljer du **Klar**. 

    ![Luftkonditioneringsmått](./media/tutorial-define-device-type/airconmeasurements.png)

    > [!NOTE]
    > Om du vill ändra namn på enheten eller enhetsmallen klickar du på texten längst upp på sidan.

5. För att lägga till temperaturtelemetrimått väljer du **Nytt mått**. Välj sedan **Telemetri** som måttenhet:

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

    Du kan även välja en färg för telemetrivisningen. För att spara telemetridefinition väljer du **Spara**:

    ![Konfigurera temperatursimulering](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Efter en kort stund visar sidan **Mått** ett diagram över temperaturtelemetrin från den simulerade anslutna luftkonditioneringsenheten. Använd kontrollerna för att hantera synlighet eller sammansättning eller redigera telemetridefinitionen:

    ![Visa temperatursimulering](./media/tutorial-define-device-type/viewsimulation.png)

8. Du kan även anpassa diagrammet med kontrollerna **Rad**, **Staplad** och **Redigera tidsintervall**:

    ![Anpassa diagrammet](./media/tutorial-define-device-type/customizechart.png)

## <a name="define-event-measurement"></a>Definiera händelsemått

Du kan använda händelsen för att definiera tidpunktsdata som skickas av enheten för att ange något viktigt, till exempel ett fel eller ett komponentfel. Utöver telemetrimätningar kan Azure IoT Central simulera enhetshändelser så att du kan testa programmets beteende innan du ansluter en fysisk enhet. Du definierar händelsemått för enhetstypen i vyn **Mått**.

1. För att lägga till händelsemåttet **Fel på fläktmotor** väljer du **Nytt mått**. Välj sedan **Händelse** som måttyp:

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

    Spara händelsedefinitionen genom att välja **Spara**:

    ![Konfigurera händelsemätning](./media/tutorial-define-device-type/eventconfiguration.png)

3. Efter en kort stund visar sidan **Mått** ett diagram över de händelser som genereras från den simulerade anslutna luftkonditioneringsenheten. Använd kontrollerna för att hantera synlighet eller redigera händelsedefinitionen:

    ![Visa händelsesimulering](./media/tutorial-define-device-type/eventview.png)

1. Klicka på händelsen i diagrammet om du vill se mer information om händelsen:

    ![Visa händelseinformation](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-state-measurement"></a>Definiera tillståndsmätning

Du kan använda Tillstånd för att definiera och visualisera tillståndet för enheten eller dess komponenter över en viss tidsperiod. Utöver telemetrimätningar kan Azure IoT Central simulera enhetstillstånd så att du kan testa programmets beteende innan du ansluter en fysisk enhet. Du definierar tillståndsmått för enhetstypen i vyn **Mått**.

1. För att lägga till måttet **Fläktläge** väljer du **Nytt mått**. Välj sedan **Tillstånd** som måttyp:

    ![Tillståndsmått för ansluten luftkonditioneringsenhet](./media/tutorial-define-device-type/statenew.png)

2. Varje typ av tillstånd som du definierar för en enhetsmall innehåller [konfigurationsalternativ](howto-set-up-template.md) som:

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

    För att spara definitionen för tillståndsmått väljer du **Spara**:

    ![Konfigurera tillståndsmått](./media/tutorial-define-device-type/stateconfiguration.png)

3. Efter en kort stund visar sidan **Mått** ett diagram över de tillstånd som genereras från den simulerade anslutna luftkonditioneringsenheten. Använd kontrollerna för att hantera synlighet eller redigera tillståndsdefinitionen:

    ![Visa tillståndssimulering](./media/tutorial-define-device-type/stateview.png)

4. Om det finns för många datapunkter som skickas av enheten på kort tid visas tillståndsmåttet med ett annat utseende enligt exemplet nedan. Om du klickar på diagrammet visas alla datapunkter inom den tidsperioden i kronologisk ordning. Du kan även begränsa tidsintervallet för att se måtten i detalj.

    ![Visa tillståndsinformation](./media/tutorial-define-device-type/stateviewdetail.png)

## <a name="settings-properties-and-commands"></a>Inställningar, egenskaper och kommandon

Inställningar, egenskaper och kommandon är olika värden som definieras i en enhetsmall och som associeras med varje enskild enhet:

* Du använder _inställningar_ för att skicka konfigurationsdata till en enhet från programmet. Till exempel kan en operatör använda en inställning för att ändra enhetens telemetriintervall från två sekunder till fem sekunder. När en operatör ändrar en inställning markeras inställningen som väntande i användargränssnittet tills enheten bekräftar att den har genomfört inställningsändringen.

* Du använder _egenskaper_ för att definiera metadata som associeras med enheten. Det finns två kategorier med egenskaper:
    
    * Du använder _programegenskaper_ för att registrera information om enheten i programmet. Du kan till exempel använda programegenskaper för att registrera en enhets plats och dess senaste servicedatum. Dessa egenskaper lagras i programmet och synkroniseras inte med enheten. En operatör kan tilldela egenskaper värden.

    * Du använder _enhetsegenskaper_ för göra så att en enhet kan skicka egenskapsvärden till programmet. De här egenskaperna kan bara ändras av enheten. För operatörer är enhetsegenskaper skrivskyddade. I det här scenariot med en ansluten luftkonditioneringsenhet är versionen av inbyggd programvara och enhetens serienummer enhetsegenskaper som rapporteras av enheten. 
    
    Mer information finns i [Egenskaper][lnk-define-template] i instruktionerna om hur du konfigurerar en enhetsmall.

* Du använder _kommandon_ för att fjärrhantera din enhet från ditt program. Du kan köra kommandon direkt på enheten från molnet för att styra enheterna. En operatör kan exempelvis köra kommandon, t.ex. omstart, för att omedelbart starta om enheten.

## <a name="use-settings"></a>Använd inställningar

Du använder *inställningar* för att göra så att en operatör kan skicka konfigurationsdata till en enhet. I det här avsnittet lägger du till en inställning i enhetsmallen **Ansluten luftkonditioneringsenhet** som gör att en operatör kan ange måltemperaturen för den anslutna luftkonditioneringsenheten.

1. Gå till sidan **Inställningar** för enhetsmallen **Ansluten luftkonditioneringsenhet**:

    ![Förbereda för att lägga till en inställning](./media/tutorial-define-device-type/deviceaddsetting.png)

    Du kan skapa inställningar av olika typer, till exempel nummer eller text.

2. Välj **Nummer** för att lägga till en nummerinställning till enheten.

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

    Välj sedan **Spara**:

    ![Konfigurera inställningen Ange temperatur](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > När enheten bekräftar en inställningsändring ändras statusen för inställningen ändras till **synkroniserad**.

4. Du kan anpassa layouten för sidan **Inställningar** genom att flytta och ändra storlek på inställningspanelerna:

    ![Anpassa layout för inställningar](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Använda egenskaper 

Du använder *programegenskaper* för att lagra information om enheten i programmet. I det här avsnittet lägger du till programegenskaper i enhetsmallen **Ansluten luftkonditioneringsenhet** för att lagra enhetens plats och senaste servicedatum. Observera att båda dessa är redigerbara egenskaper för enheten. Det finns även skrivskyddade enhetsegenskaper som rapporteras av enheten och som inte kan ändras, till exempel enhetens serienummer och version av inbyggd programvara.
 
1. Gå till sidan **Egenskaper** för enhetsmallen **Ansluten luftkonditioneringsenhet**:

    ![Förbereda för att lägga till en egenskap](./media/tutorial-define-device-type/deviceaddproperty.png)

    Du kan skapa olika typer av enhetsegenskaper, till exempel nummer eller text. Om du vill lägga till en platsegenskap i enhetsmallen väljer du **Location**.

1. För att konfigurera platsegenskapen använder du informationen i följande tabell:

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
    | Initialt värde        | 1/1/2018                |
    | Beskrivning          | Senaste service           |

    ![Konfigurera enhetsegenskaperna](./media/tutorial-define-device-type/configureproperties2.png)

    Välj **Spara**.

5. Du kan anpassa layouten för sidan **Egenskaper** genom att flytta och ändra storlek på egenskapspanelerna:

    ![Anpassa layout för egenskaper](./media/tutorial-define-device-type/propertieslayout.png)

1. Om du vill lägga till en enhetsegenskap såsom version av inbyggd programvara i enhetsmallen väljer du **Enhetsegenskap**.

1.  För att konfigurera version av inbyggd programvara använder du informationen i följande tabell:

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

1. Gå till sidan **Kommandon** för enhetsmallen **Ansluten luftkonditioneringsenhet** och redigera mallen. 

1. Klicka på **Nytt kommando** för att lägga till ett kommando till enheten och börja konfigurera det nya kommandot.

   Du kan skapa olika typer av kommandon beroende på dina krav. 

1. Använd informationen i följande tabell för att konfigurera det nya kommandot:

    | Fält                | Värde           |
    | -------------------- | -----------     |
    | Visningsnamn         | Ekokommando    |
    | Fältnamn           | eko            |
    | Standardvärde för tidsgräns      | 30              |
    | Visningstyp         | text            |
    | Beskrivning          | Enhetskommando  |  

    Du kan lägga till ytterligare indata till kommandot genom att klicka på **+** för **Indatafält**.

    ![Förbereda för att lägga till en inställning](media/tutorial-define-device-type/commandsecho1.png)

     Välj **Spara**.

1. Du kan anpassa layouten för sidan **Kommandon** genom att flytta och ändra storlek på kommandopanelerna:

    ![Anpassa layout för inställningar](media/tutorial-define-device-type/commandstileresize1.png)

## <a name="view-your-simulated-device"></a>Visa din simulerade enhet

Nu när du har definierat enhetsmallen **Ansluten luftkonditioneringsenhet** kan du anpassa dess **Instrumentpanel** så att den innehåller de mått, inställningar och egenskaper som du definierat. Sedan kan du förhandsgranska instrumentpanelen som operatör:

1. Välj sidan **Instrumentpanel** för enhetsmallen **Ansluten luftkonditioneringsenhet**:

    ![Instrumentpaneler för Ansluten luftkonditioneringsenhet](./media/tutorial-define-device-type/aircondashboards.png)

1. Välj **Linjediagram** för att lägga till komponenten på **Instrumentpanelen**:

    ![Instrumentpanelskomponenter](./media/tutorial-define-device-type/dashboardcomponents1.png)

1. Konfigurera komponenten **Linjediagram** med hjälp av informationen i följande tabell:

    | Inställning      | Värde       |
    | ------------ | ----------- |
    | Rubrik        | Temperatur |
    | Tidsintervall   | Senaste 30 minuterna |
    | Mått     | temperatur (välj **Synlighet** bredvid **temperatur**) |

    ![Inställningar för linjediagram](./media/tutorial-define-device-type/linechartsettings.png)

    Välja sedan **Spara**.

1. Konfigurera komponenten **Händelsehistorik** med hjälp av informationen i följande tabell:

    | Inställning      | Värde       |
    | ------------ | ----------- |
    | Rubrik        | Händelser |
    | Tidsintervall   | Senaste 30 minuterna |
    | Mått     | Fel på fläktmotor (välj **Synlighet** bredvid **Fel på fläktmotor**) |

    ![Inställningar för linjediagram](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Välja sedan **Spara**.

1. Konfigurera komponenten **Tillståndshistorik** med hjälp av informationen i följande tabell:

    | Inställning      | Värde       |
    | ------------ | ----------- |
    | Rubrik        | Fläktläge |
    | Tidsintervall   | Senaste 30 minuterna |
    | Mått | Fläktläge (välj **Synlighet** bredvid **Fläktläge**) |

    ![Inställningar för linjediagram](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Välja sedan **Spara**.

1. För att lägga till temperaturinställningen på instrumentpanelen väljer du **Inställningar och egenskaper**. Klicka på **Lägg till/ta bort** för att lägga till de inställningar eller egenskaper som du vill ha på instrumentpanelen. 

    ![Instrumentpanelskomponenter](./media/tutorial-define-device-type/dashboardcomponents4.png)

1. Konfigurera komponenten **Inställningar och egenskaper** med hjälp av informationen i följande tabell:

    | Inställning                 | Värde         |
    | ----------------------- | ------------- |
    | Rubrik                   | Ange måltemperatur |
    | Inställningar och egenskaper | Ange temperatur |

    Inställningar och egenskaper som du tidigare har definierat på sidorna Inställningar och Egenskaper visas i tillgängliga kolumner. 

    ![Ange inställningar för temperaturegenskap](./media/tutorial-define-device-type/propertysettings4.png)

    Välj sedan **OK**.

1. För att lägga till enhetens serienummer på instrumentpanelen väljer du **Inställningar och egenskaper**:

    ![Instrumentpanelskomponenter](./media/tutorial-define-device-type/dashboardcomponents3.png)

1. Konfigurera komponenten **Inställningar och egenskaper** med hjälp av informationen i följande tabell:

    | Inställning                 | Värde         |
    | ----------------------- | ------------- |
    | Rubrik                   | Serienummer |
    | Inställningar och egenskaper | Serienummer |

    ![Egenskapsinställningar för serienummer](./media/tutorial-define-device-type/propertysettings5.png)

    Välj sedan **OK**.

1. För att lägga till enhetens version av inbyggd programvara på instrumentpanelen väljer du **Inställningar och egenskaper**:

    ![Instrumentpanelskomponenter](./media/tutorial-define-device-type/dashboardcomponents4.png)

1. Konfigurera komponenten **Inställningar och egenskaper** med hjälp av informationen i följande tabell:

    | Inställning                 | Värde            |
    | ----------------------- | ---------------- |
    | Rubrik                   | Version av inbyggd programvara |
    | Inställningar och egenskaper | Version av inbyggd programvara |

    ![Egenskapsinställningar för serienummer](./media/tutorial-define-device-type/propertysettings6.png)

    Välj sedan **OK**.

1. Om du vill visa instrumentpanelen som operatör inaktiverar du **Redigera mall** längst upp till höger på sidan.

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

[lnk-define-template]: /azure/iot-central/howto-set-up-template#properties