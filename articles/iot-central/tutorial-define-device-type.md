---
title: Definiera en ny enhetstyp i Azure IoT Central | Microsoft Docs
description: I den här självstudien får du lära dig hur du som byggare definierar en ny enhetstyp i Azure IoT Central-programmet. Du kan definiera telemetri, tillstånd, egenskaper och inställningar för din typ.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: tutorial
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: e1488b708bbbee67362d834a9a703520d37bef37
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201680"
---
# <a name="1---define-a-new-device-type-in-your-azure-iot-central-application"></a>1 – Definiera en ny enhetstyp i Azure IoT Central-programmet

I den här självstudien får du lära dig hur du som byggare använder en enhetsmall för att definiera en ny typ av enhet i Azure IoT Central-programmet. Enhetsmallen definierar telemetri, tillstånd, egenskaper och inställningar för din enhetstyp.

För att du ska kunna testa programmet innan du ansluter en riktig enhet genererar Azure IoT Central en simulerad enhet utifrån enhetsmallen när du skapar den.

I den här självstudien skapar du enhetsmallen **Ansluten luftkonditioneringsenhet**. En ansluten luftkonditioneringsenhet:

* Skickar telemetri såsom temperatur och luftfuktighet.
* Rapporterar tillstånd, till exempel om den är på eller av.
* Har egenskaper såsom version av inbyggd programvara och serienummer.
* Har inställningar såsom måltemperatur och fläkthastighet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en ny enhetsmall
> * Lägga till telemetri till din enhet
> * Visa simulerad telemetri
> * Definiera händelsemätning
> * Visa simulerade händelser
> * Definiera tillståndsmätning
> * Visa simulerat tillstånd
> * Använda enhetsegenskaper
> * Använda enhetsinställningar

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver ett Azure IoT Central-program för att kunna genomföra den här snabbstarten. Om du har genomfört snabbstarten för att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md) kan du återanvända det program som du skapade i den snabbstarten. I annat fall utför du följande steg för att skapa ett tomt Azure IoT Central-program:

1. Gå till sidan [Application Manager](https://aka.ms/iotcentral) (Programhanterare) i Azure IoT Central.

1. Ange den e-postadress och det lösenord som du använder för att få åtkomst till din Azure-prenumeration:

   ![Ange ditt organisationskonto](media/tutorial-define-device-type/sign-in.png)

1. För att börja skapa ett nytt Azure IoT Central-program väljer du **Nytt program**:

    ![Sidan Application Manager (Programhanterare) i Azure IoT Central](media/tutorial-define-device-type/iotcentralhome.png)

1. Så här skapar du ett nytt Azure IoT Central-program:

    1. Välj ett eget programnamn som **Contoso luftkonditioneringsenheter**. Du får ett unikt URL-prefix från Azure IoT Central. Du kan ändra det här URL-prefixet till något som är enklare att komma ihåg.
    1. Välj en Azure Active Directory och en Azure-prenumeration som ska användas. Mer information om kataloger och prenumerationer finns på sidan om [hur du skapar ett Azure IoT Central-program](howto-create-application.md).
    1. Du kan antingen använda en befintlig resursgrupp eller skapa en ny resursgrupp med ett valfritt namn. Exempel: **contoso-rg**.
    1. Välj den geografiska region som är närmast dig.
    1. Välj programmallen **Anpassat program**.
    1. Välj betalningsplanen **Free 30 Day Trial Application** (Kostnadsfritt 30-dagars utvärderingsprogram).
    1. Välj sedan **Skapa**.

    ![Sidan Skapa program i Azure IoT Central](media/tutorial-define-device-type/iotcentralcreate.png)

Mer information finns på sidan för att [skapa ett Azure IoT Central-program](howto-create-application.md).

## <a name="create-a-new-custom-device-template"></a>Skapa en ny anpassad enhetsmall

Som byggare kan du skapa och redigera enhetsmallar i programmet. När du skapar en enhetsmall genererar Azure IoT Central en simulerad enhet utifrån mallen. Den simulerade enheten genererar telemetri som gör att du kan testa programmets beteende innan du ansluter en fysisk enhet.

Om du vill lägga till en ny enhetsmall för programmet går du till sidan **Programverktyg**. För att göra det väljer du **Programverktyg** på den vänstra navigeringsmenyn:

    ![Application Builder page](media/tutorial-define-device-type/builderhome.png)

## <a name="add-a-device-and-define-telemetry"></a>Lägga till en enhet och definiera telemetri

Följande steg visar hur du skapar en ny enhetsmall kallad **Ansluten luftkonditioneringsenhet** för enheter som skickar temperaturtelemetri till programmet:

1. På sidan **Programverktyg** väljer du **Skapa enhetsmall**:

    ![Skapa enhetsmall på sidan Programverktyg](media/tutorial-define-device-type/builderhomedevices.png)

1. På sidan **Enhetsmallar** väljer du **Anpassad**. Med en **anpassad** enhetsmall kan du definiera alla egenskaper och beteenden för den anslutna luftkonditioneringsenheten:

    ![Enheter](media/tutorial-define-device-type/builderhomedevicescustom.png)

1. På sidan för att **skapa en ny enhetsmall** anger du **Ansluten luftkonditioneringsenhet** som namnet på enheten och väljer sedan **Skapa**. Du kan även ladda upp en bild av enheten som är synlig för operatörer i Device Explorer:

    ![Anpassad enhet](media/tutorial-define-device-type/createcustomdevice.png)

1. I enhetsmallen **Ansluten luftkonditioneringsenhet** kontrollerar du att du är på sidan **Mått**, där du definierar telemetrin. Varje enhetsmall som du definierar har separata sidor där du kan:

    * Ange mått som telemetri, händelse och tillstånd, som skickas av enheten.
    * Definiera de inställningar som används för att styra enheten.
    * Definiera de egenskaper som används för att registrera information om enheten.
    * Definiera de regler som är kopplade till enheten.
    * Anpassa enhetens instrumentpanelen enheten för dina operatörer.

    ![Luftkonditioneringsmått](media/tutorial-define-device-type/airconmeasurements.png)

    > [!NOTE]
    > Om du vill ändra namn på enheten eller enhetsmallen klickar du på texten längst upp på sidan.

1. För att lägga till temperaturtelemetrimått väljer du **Nytt mått**. Välj sedan **Telemetri** som måttenhet:

    ![Mått för ansluten luftkonditioneringsenhet](media/tutorial-define-device-type/airconmeasurementsnew.png)

1. Varje typ av telemetri som du definierar för en enhetsmall innehåller [konfigurationsalternativ](howto-set-up-template.md) som:

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

    ![Konfigurera temperatursimulering](media/tutorial-define-device-type/temperaturesimulation.png)

1. Efter en kort stund visar sidan **Mått** ett diagram över temperaturtelemetrin från den simulerade anslutna luftkonditioneringsenheten. Använd kontrollerna för att hantera synlighet eller sammansättning eller redigera telemetridefinitionen:

    ![Visa temperatursimulering](media/tutorial-define-device-type/viewsimulation.png)

1. Du kan även anpassa diagrammet med kontrollerna **Rad**, **Staplad** och **Redigera tidsintervall**:

    ![Anpassa diagrammet](media/tutorial-define-device-type/customizechart.png)

## <a name="define-event-measurement"></a>Definiera händelsemätning
Du kan använda händelsen för att definiera tidpunktsdata som skickas av enheten för att ange något viktigt, till exempel ett fel eller ett komponentfel. Utöver telemetrimätningar kan Azure IoT Central simulera enhetshändelser så att du kan testa programmets beteende innan du ansluter en fysisk enhet. Du definierar händelsemått för enhetstypen i vyn **Mått**.

1. För att lägga till händelsemåttet **Fel på fläktmotor** väljer du **Nytt mått**. Välj sedan **Händelse** som måttyp:

    ![Mått för ansluten luftkonditioneringsenhet](media/tutorial-define-device-type/eventnew.png)

1. Varje typ av händelse som du definierar för en enhetsmall innehåller [konfigurationsalternativ](howto-set-up-template.md) som:

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

    ![Konfigurera händelsemätning](media/tutorial-define-device-type/eventconfiguration.png)

1. Efter en kort stund visar sidan **Mått** ett diagram över de händelser som genereras från den simulerade anslutna luftkonditioneringsenheten. Använd kontrollerna för att hantera synlighet eller redigera händelsedefinitionen:

    ![Visa händelsesimulering](media/tutorial-define-device-type/eventview.png)

1. Klicka på händelsen i diagrammet om du vill se mer information om händelsen:

    ![Visa händelseinformation](media/tutorial-define-device-type/eventviewdetail.png)


## <a name="define-state-measurement"></a>Definiera tillståndsmätning
Du kan använda Tillstånd för att definiera och visualisera tillståndet för enheten eller dess komponenter över en viss tidsperiod. Utöver telemetrimätningar kan Azure IoT Central simulera enhetstillstånd så att du kan testa programmets beteende innan du ansluter en fysisk enhet. Du definierar tillståndsmått för enhetstypen i vyn **Mått**.

1. För att lägga till måttet **Fläktläge** väljer du **Nytt mått**. Välj sedan **Tillstånd** som måttyp:

    ![Tillståndsmått för ansluten luftkonditioneringsenhet](media/tutorial-define-device-type/statenew.png)

1. Varje typ av tillstånd som du definierar för en enhetsmall innehåller [konfigurationsalternativ](howto-set-up-template.md) som:

    * Visningsnamn.
    * Fältnamn.
    * Värden med valfria visningsetiketter.
    * Färg för varje värde

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

    ![Konfigurera tillståndsmått](media/tutorial-define-device-type/stateconfiguration.png)

1. Efter en kort stund visar sidan **Mått** ett diagram över de tillstånd som genereras från den simulerade anslutna luftkonditioneringsenheten. Använd kontrollerna för att hantera synlighet eller redigera tillståndsdefinitionen:

    ![Visa tillståndssimulering](media/tutorial-define-device-type/stateview.png)

1. Om det finns för många datapunkter som skickas av enheten på kort tid visas tillståndsmåttet med ett annat utseende enligt exemplet nedan. Om du klickar på diagrammet visas alla datapunkter inom den tidsperioden i kronologisk ordning. Du kan även begränsa tidsintervallet för att se måttkurvan i diagrammet.

    ![Visa tillståndsinformation](media/tutorial-define-device-type/stateviewdetail.png)

## <a name="properties-device-properties-and-settings"></a>Egenskaper, enhetsegenskaper och inställningar

Egenskaper, enhetsegenskaper och inställningar är olika värden som definieras i en enhetsmall och som associeras med varje enskild enhet:

* Du använder _inställningar_ för att skicka konfigurationsdata till en enhet från programmet. Till exempel kan en operatör använda en inställning för att ändra enhetens telemetriintervall från två sekunder till fem sekunder. När en operatör ändrar en inställning markeras inställningen som väntande i användargränssnittet tills enheten bekräftar att den har genomfört inställningsändringen.
* Du använder _egenskaper_ för att registrera information om enheten i programmet. Till exempel kan du använda egenskaper för att registrera en enhets serienummer eller enhetstillverkarens telefonnummer. Egenskaper lagras i programmet och synkroniseras inte med enheten. En operatör kan tilldela egenskaper värden.
* Du använder _enhetsegenskaper_ för göra så att en enhet kan skicka egenskapsvärden till programmet. De här egenskaperna kan bara ändras av enheten. För operatörer är enhetsegenskaper skrivskyddade.

## <a name="use-settings"></a>Använd inställningar

Du använder _inställningar_ för att göra så att en operatör kan skicka konfigurationsdata till en enhet. I det här avsnittet lägger du till en inställning i enhetsmallen **Ansluten luftkonditioneringsenhet** som gör att en operatör kan ange måltemperaturen för den anslutna luftkonditioneringsenheten.

1. Gå till sidan **Inställningar** för enhetsmallen **Ansluten luftkonditioneringsenhet**:

    ![Förbereda för att lägga till en inställning](media/tutorial-define-device-type/deviceaddsetting.png)

    Du kan skapa inställningar av olika typer, till exempel nummer eller text.

1. Välj **Nummer** för att lägga till en nummerinställning till enheten.

1. För att konfigurera inställningen **Ange temperatur** använder du informationen i följande tabell:

    | Fält                | Värde           |
    | -------------------- | -----------     |
    | Visningsnamn         | Ange temperatur |
    | Fältnamn           | angeTemperatur  |
    | Måttenhet  | F               |
    | Antal decimaler       | 1               |
    | Minvärde        | 20              |
    | Maxvärde        | 200             |
    | Initialt värde        | 80              |
    | Beskrivning          | Ange måltemperaturen för luftkonditioneringsenheten |

    Välj sedan **Spara**:

    ![Konfigurera inställningen Ange temperatur](media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > När enheten bekräftar en inställningsändring ändras statusen för inställningen ändras till **synkroniserad**.

1. Du kan anpassa layouten för sidan **Inställningar** genom att flytta och ändra storlek på inställningspanelerna:

    ![Anpassa layout för inställningar](media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Använda egenskaper

Du använder _egenskaper_ för att lagra information om enheten i programmet. I det här avsnittet lägger du till egenskaper i enhetsmallen **Ansluten luftkonditioneringsenhet** för att lagra enhetens serienummer och version av inbyggd programvara för varje enhet.

1. Gå till sidan **Egenskaper** för enhetsmallen **Ansluten luftkonditioneringsenhet**:

    ![Förbereda för att lägga till en egenskap](media/tutorial-define-device-type/deviceaddproperty.png)

    Du kan skapa egenskaper av olika typer, till exempel nummer eller text. Om du vill lägga till en egenskap för serienummer i enhetsmallen väljer du **Text**.

1. För att konfigurera egenskapen serienummer använder du informationen i följande tabell:

    | Fält                | Värde                |
    | -------------------- | -------------------- |
    | Visningsnamn         | Serienummer        |
    | Fältnamn           | serieNummer         |
    | Initialt värde        | cac00001             |
    | Beskrivning          | Enhetens serienummer |

    Låt standardvärdena stå kvar i de andra fälten.

    ![Konfigurera enhetsegenskaperna](media/tutorial-define-device-type/configureproperties.png)

    Välja sedan **Spara**.

1. Om du vill lägga till en egenskap för versionen av inbyggd programvara i enhetsmallen väljer du **Text**

1. För att konfigurera egenskapen version av inbyggd programvara använder du informationen i följande tabell:

    | Fält                | Värde                   |
    | -------------------- | ----------------------- |
    | Visningsnamn         | Version av inbyggd programvara        |
    | Fältnamn           | versionInbyggdProgramvara         |
    | Initialt värde        | 0,1                     |
    | Beskrivning          | Enhetens version av inbyggd programvara |

    ![Konfigurera enhetsegenskaperna](media/tutorial-define-device-type/configureproperties2.png)

    Välja sedan **Spara**.

1. Du kan anpassa layouten för sidan **Egenskaper** genom att flytta och ändra storlek på egenskapspanelerna:

    ![Anpassa layout för egenskaper](media/tutorial-define-device-type/propertieslayout.png)

## <a name="view-your-simulated-device"></a>Visa din simulerade enhet

Nu när du har definierat enhetsmallen **Ansluten luftkonditioneringsenhet** kan du anpassa dess **Instrumentpanel** så att den innehåller de mått, inställningar och egenskaper som du definierat. Sedan kan du förhandsgranska instrumentpanelen som operatör:

1. Välj sidan **Instrumentpanel** för enhetsmallen **Ansluten luftkonditioneringsenhet**:

    ![Instrumentpaneler för Ansluten luftkonditioneringsenhet](media/tutorial-define-device-type/aircondashboards.png)

1. Välj **Linjediagram** för att lägga till komponenten på **Instrumentpanelen**:

    ![Instrumentpanelskomponenter](media/tutorial-define-device-type/dashboardcomponents1.png)

1. Konfigurera komponenten **Linjediagram** med hjälp av informationen i följande tabell:

    | Inställning      | Värde       |
    | ------------ | ----------- |
    | Rubrik        | Temperatur |
    | Tidsintervall   | Senaste 30 minuterna |
    | Mått | temperatur (välj **Synlighet** bredvid **temperatur**) |

    ![Inställningar för linjediagram](media/tutorial-define-device-type/linechartsettings.png)

    Välja sedan **Spara**.

1. Konfigurera komponenten **Händelsediagram** med hjälp av informationen i följande tabell:

    | Inställning      | Värde       |
    | ------------ | ----------- |
    | Rubrik        | Händelser |
    | Tidsintervall   | Senaste 30 minuterna |
    | Mått | Fel på fläktmotor (välj **Synlighet** bredvid **Fel på fläktmotor**) |

    ![Inställningar för linjediagram](media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Välja sedan **Spara**.

1. Konfigurera komponenten **Tillståndsdiagram** med hjälp av informationen i följande tabell:

    | Inställning      | Värde       |
    | ------------ | ----------- |
    | Rubrik        | Fläktläge |
    | Tidsintervall   | Senaste 30 minuterna |
    | Mått | Fläktläge (välj **Synlighet** bredvid **Fläktläge**) |

    ![Inställningar för linjediagram](media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Välja sedan **Spara**.

1. För att lägga till temperaturinställningen på instrumentpanelen väljer du **Inställningar och egenskaper**:

    ![Instrumentpanelskomponenter](media/tutorial-define-device-type/dashboardcomponents4.png)

1. Konfigurera komponenten **Inställningar och egenskaper** med hjälp av informationen i följande tabell:

    | Inställning                 | Värde         |
    | ----------------------- | ------------- |
    | Rubrik                   | Ange måltemperatur |
    | Inställningar och egenskaper | Ange temperatur |

    ![Egenskapsinställningar för serienummer](media/tutorial-define-device-type/propertysettings3.png)

    Välja sedan **Spara**.

1. För att lägga till enhetens serienummer på instrumentpanelen väljer du **Inställningar och egenskaper**:

    ![Instrumentpanelskomponenter](media/tutorial-define-device-type/dashboardcomponents3.png)

1. Konfigurera komponenten **Inställningar och egenskaper** med hjälp av informationen i följande tabell:

    | Inställning                 | Värde         |
    | ----------------------- | ------------- |
    | Rubrik                   | Serienummer |
    | Inställningar och egenskaper | Serienummer |

    ![Egenskapsinställningar för serienummer](media/tutorial-define-device-type/propertysettings1.png)

    Välja sedan **Spara**.

1. För att lägga till enhetens version av inbyggd programvara på instrumentpanelen väljer du **Inställningar och egenskaper**:

    ![Instrumentpanelskomponenter](media/tutorial-define-device-type/dashboardcomponents4.png)

1. Konfigurera komponenten **Inställningar och egenskaper** med hjälp av informationen i följande tabell:

    | Inställning                 | Värde            |
    | ----------------------- | ---------------- |
    | Rubrik                   | Version av inbyggd programvara |
    | Inställningar och egenskaper | Version av inbyggd programvara |

    ![Egenskapsinställningar för serienummer](media/tutorial-define-device-type/propertysettings2.png)

    Välja sedan **Spara**.

1. Om du vill visa instrumentpanelen som operatör inaktiverar du **Designläge** längst upp till höger på sidan.

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
> * Använda enhetsegenskaper
> * Använda enhetsinställningar

Nu när du har definierat en enhetsmall i Azure IoT Central-programmet föreslår vi följande steg:

* [Konfigurera regler och åtgärder för din enhet](tutorial-configure-rules.md)
* [Anpassa operatörsvyer](tutorial-customize-operator.md)
