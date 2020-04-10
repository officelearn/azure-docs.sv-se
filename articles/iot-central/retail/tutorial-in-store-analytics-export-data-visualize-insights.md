---
title: Självstudiekurs - Exportera data och visualisera insikter i Azure IoT Central
description: I den här självstudien kan du lära dig hur du exporterar data från IoT Central och visualiserar insikter i en Power BI-instrumentpanel.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: 6062e8a74af4bb0a19d02ccf9a4c50da0cc4a7c5
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000107"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Självstudiekurs: Exportera data från Azure IoT Central och visualisera insikter i Power BI



I de två föregående självstudierna har du skapat och anpassat ett IoT Central-program med hjälp av programmallen **I butik - utcheckning.** I den här självstudien konfigurerar du ditt IoT Central-program för att exportera telemetri som samlats in från enheterna. Du använder sedan Power BI för att skapa en anpassad instrumentpanel för butikshanteraren för att visualisera de insikter som härletts från telemetrin.

I den här självstudien får du lära dig hur man:
> [!div class="checklist"]
> * Konfigurera ett IoT Central-program för att exportera telemetri till en händelsehubb.
> * Använd Logic Apps för att skicka data från en händelsehubb till en Power BI-direktuppspelningsdatauppsättning.
> * Skapa en Power BI-instrumentpanel för att visualisera data i datauppsättningen för direktuppspelning.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* För att slutföra de två föregående självstudierna [skapar du ett analysprogram i Butik i Azure IoT Central](./tutorial-in-store-analytics-create-app.md) och anpassa [operatörsinstrumentpanelen och hanterar enheter i Azure IoT Central](./tutorial-in-store-analytics-customize-dashboard.md).
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.
* Ett Power BI-konto. Om du inte har ett Power BI-konto registrerar du dig för en [kostnadsfri Power BI Pro-testperiod](https://app.powerbi.com/signupredirect?pbi_source=web) innan du börjar.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du skapar händelsehubben och logikappen måste du skapa en resursgrupp för att hantera dem. Resursgruppen ska vara på samma plats som ditt **IoT Central-program för utcheckning – kassan.** Så här skapar du en resursgrupp:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I den vänstra navigeringen väljer du **Resursgrupper**. Välj sedan **Lägg till**.
1. För **prenumeration**väljer du namnet på den Azure-prenumeration som du använde för att skapa ditt IoT Central-program.
1. Ange **Resource group** _butiksanalys_*.
1. För **regionen**väljer du samma region som du valde för IoT Central-programmet.
1. Välj **Granska + Skapa**.
1. Välj **Granska ** i fönstret **Granska + Skapa**.

Du har nu en resursgrupp som heter **butiksanalys** i din prenumeration.

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Innan du kan konfigurera återförsäljarövervakningsprogrammet för att exportera telemetri måste du skapa en händelsehubb för att ta emot exporterade data. Följande steg visar hur du skapar din händelsehubb:

1. I Azure-portalen väljer du **Skapa en resurs** längst upp till vänster på skärmen.
1. Ange _Event Hubs_ **i Sök på Marketplace**och tryck sedan på **Retur**.
1. På sidan **Händelsehubbar** väljer du **Skapa**.
1. Gör följande på sidan **Skapa namnområde:**
    * Ange ett unikt namn för namnområdet, till exempel _analys av dittnamn-butik._ Systemet kontrollerar om namnet är tillgängligt.
    * Välj den **grundläggande** prisnivån.
    * Välj samma **prenumeration** som du använde för att skapa ditt IoT Central-program.
    * Välj resursgruppen **för butiksanalys.**
    * Välj samma plats som du använde för ditt IoT Central-program.
    * Välj **Skapa**. Du kan behöva vänta några minuter på att systemet ska etablera resurserna.
1. Navigera till resursgruppen **för butiksanalys** i portalen. Vänta tills distributionen har slutförts. Du kan behöva välja **Uppdatera för** att uppdatera distributionsstatusen. Du kan också kontrollera status för händelsehubbens namnområde i **meddelandena**.
1. Välj **namnområdet För händelsehubbar**i resursgruppen för **butiksanalys** . Startsidan för **namnområdet Event Hubs** visas i portalen.

Nu har du ett **namnområde för eventhubbar**kan du skapa en **händelsehubb** som ska användas med ditt IoT Central-program:

1. Välj **+ Event Hub**på startsidan för **namnområdet Event Hubs** i portalen .
1. På sidan **Skapa händelsehubb** anger du _store-telemetri_ som namn och väljer sedan **Skapa**.

Du har nu en händelsehubb som du kan använda när du konfigurerar dataexport från ditt IoT Central-program:

![Händelsehubb](./media/tutorial-in-store-analytics-visualize-insights/event-hub.png)

## <a name="configure-data-export"></a>Konfigurera dataexport

Nu när du har en händelsehubb kan du konfigurera ditt **analysprogram i butik för** att exportera telemetri från de anslutna enheterna. Följande steg visar hur du konfigurerar exporten:

1. Logga in på ditt **IoT Central-program i butik** .
1. Välj **Dataexport** i den vänstra rutan.
1. Välj **Nya > Azure-händelsehubbar**.
1. Ange _telemetriexport_ som **visningsnamn**.
1. Välj **namnområdet Event Hubs**.
1. Välj händelsehubben **för butikstelemetri.**
1. Stäng av **enheter** och **enhetsmallar** i avsnittet **Data som ska exporteras.**
1. Välj **Spara**.

Dataexporten kan ta några minuter att börja skicka telemetri till händelsehubben. Du kan se status för exporten på sidan **Dataexport:**

![Konfiguration för kontinuerlig dataexport](./media/tutorial-in-store-analytics-visualize-insights/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>Skapa Power BI-datauppsättningar

Power BI-instrumentpanelen visar data från ditt program för butiksövervakning. I den här lösningen använder du Power BI-strömmande datauppsättningar som datakälla för Power BI-instrumentpanelen. I det här avsnittet definierar du schemat för strömmande datauppsättningar så att logikappen kan vidarebefordra data från händelsehubben. Följande steg visar hur du skapar två strömmande datauppsättningar för miljösensorer och en strömmande datauppsättning för beläggningssensorn:

1. Logga in på ditt **Power BI**-konto.
1. Välj **Arbetsytor**och välj sedan **Skapa en arbetsyta**.
1. På sidan **Skapa en arbetsyta** anger du Analyser i butik – _utcheckning_ som **arbetsytans namn**.
1. Bläddra längst ned på sidan **Välkommen till analyssidan för butik – utcheckning** och välj **Hoppa över**.
1. På arbetsytans sida väljer du **Skapa > Strömmande datauppsättning**.
1. På sidan **Ny strömmande datauppsättning** väljer du **API**och väljer sedan **Nästa**.
1. Ange _zon 1-sensor_ som **datauppsättningsnamn**.
1. Ange de tre **värdena från strömmar** i följande tabell:

    | Värdenamn  | Värdetyp |
    | ----------- | ---------- |
    | Tidsstämpel   | DateTime   |
    | Fuktighet    | Tal     |
    | Temperatur | Tal     |

1. Växla **historisk dataanalys** på.
1. Välj **Skapa** och sedan **Klar**.
1. Skapa en annan strömmande datauppsättning som kallas **Zon 2-sensor** med samma schema och inställningar som **zon 1-sensorstreamingdatauppsättningen.**

Du har nu två strömmande datauppsättningar. Logikappen dirigerar telemetri från de två miljösensorer som är anslutna till din **analys i butik – kassaprogrammet** till dessa två datauppsättningar:

![Zondatauppsättningar](./media/tutorial-in-store-analytics-visualize-insights/dataset-1.png)

Den här lösningen använder en strömmande datauppsättning för varje sensor eftersom det inte är möjligt att använda filter på strömmande data i Power BI.

Du behöver också en strömmande datauppsättning för beläggningstelemetrin:

1. På arbetsytans sida väljer du **Skapa > Strömmande datauppsättning**.
1. På sidan **Ny strömmande datauppsättning** väljer du **API**och väljer sedan **Nästa**.
1. Ange _beläggningssensor_ som **datauppsättningsnamn**.
1. Ange de fem **värdena från strömmen** i följande tabell:

    | Värdenamn     | Värdetyp |
    | -------------- | ---------- |
    | Tidsstämpel      | DateTime   |
    | Kölängd 1 | Tal     |
    | Kölängd 2 | Tal     |
    | Uppehåll Tid 1   | Tal     |
    | Uppehåll Tid 2   | Tal     |

1. Växla **historisk dataanalys** på.
1. Välj **Skapa** och sedan **Klar**.

Du har nu en tredje strömmande datauppsättning som lagrar värden från den simulerade beläggningssensorn. Den här sensorn rapporterar kölängden vid de två utcheckningarna i butiken och hur länge kunderna väntar i dessa köer:

![Beläggningsdatauppsättning](./media/tutorial-in-store-analytics-visualize-insights/dataset-2.png)

## <a name="create-a-logic-app"></a>Skapa en logikapp

I den här lösningen läser logikappen telemetri från händelsehubben, tolkar data och skickar den sedan till de Power BI-strömmande datauppsättningar som du har skapat.

Innan du skapar logikappen behöver du enhets-ID:erna för de två RuuviTag-sensorerna som du anslöt till ditt IoT [Central-program i programmet Skapa ett analysprogram i Azure IoT Central:](./tutorial-in-store-analytics-create-app.md)

1. Logga in på ditt **IoT Central-program i butik** .
1. Välj **Enheter** i den vänstra rutan. Välj sedan **RuuviTag**.
1. Anteckna **enhets-ID:na**. I följande skärmdump är **ID:erna f5dcf4ac32e8** och **e29ffc8d5326**:

    ![Enhets-ID:er](./media/tutorial-in-store-analytics-visualize-insights/device-ids.png)

Följande steg visar hur du skapar logikappen i Azure-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com) och välj **Skapa en resurs** längst upp till vänster på skärmen.
1. I **Sök på Marketplace**anger du Logic _App_och trycker sedan på **Retur**.
1. På sidan **Logikapp** väljer du **Skapa**.
1. På sidan **Skapa logikapp:**
    * Ange ett unikt namn för logikappen, till exempel _analys av dittnamn-butik._
    * Välj samma **prenumeration** som du använde för att skapa ditt IoT Central-program.
    * Välj resursgruppen **för butiksanalys.**
    * Välj samma plats som du använde för ditt IoT Central-program.
    * Välj **Skapa**. Du kan behöva vänta några minuter på att systemet ska etablera resurserna.
1. Navigera till din nya logikapp i Azure-portalen.
1. På sidan **Logic Apps Designer** bläddrar du nedåt och väljer Tom Logic **App**.
1. I **Sökkopplingar och utlösare**anger du _Event Hubs_.
1. I **Utlösare**väljer du **När händelser är tillgängliga i Event Hub**.
1. Ange _Store-telemetri_ som **anslutningsnamn**och välj **namnområdet För eventhubbar**.
1. Välj policyn **RootManageSharedAccess** och välj **Skapa**.
1. I **närhändelser är tillgängliga i** åtgärden Event Hub:
    * Välj **store-telemetri i** **Event Hub-namnet**.
    * Välj **program/json**i **innehållstyp**.
    * Ställ in **intervallet** till tre och **frekvensen** till sekunder
1. Välj **Spara** om du vill spara logikappen.

Om du vill lägga till logiken i logikappdesignen väljer du **Kodvy:**

1. Ersätt `"actions": {},` med följande JSON. Byt ut de `[YOUR RUUVITAG DEVICE ID 1]` `[YOUR RUUVITAG DEVICE ID 2]` två platshållarna och med de ID:er som du noterade av dina två RuuviTag-enheter:

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. Välj **Spara** och välj sedan **Designer** om du vill visa den visuella versionen av logiken som du har lagt till:

    ![Logikappdesign](./media/tutorial-in-store-analytics-visualize-insights/logic-app.png)

1. Välj **Växla per DeviceID** om du vill expandera åtgärden. Välj sedan **Zon 1-miljö**och välj **Lägg till en åtgärd**.
1. I **Sökkopplingar och åtgärder**anger du Power **BI**och trycker sedan på **Retur**.
1. Markera åtgärden **Lägg till rader i en datauppsättning (förhandsgranskning).**
1. Välj **Logga in** och följ anvisningarna för att logga in på ditt Power BI-konto.
1. När inloggningsprocessen är klar i raden Lägg till i **en datauppsättningsåtgärd:**
    * Välj **Analys i butik – utcheckning** som arbetsyta.
    * Välj **Zon 1-sensor** som datauppsättning.
    * Välj **RealTimeData** som tabell.
    * Välj **Lägg till ny parameter** och välj sedan fälten **Tidsstämpel,** **Luftfuktighet**och **Temperatur.**
    * Välj **fältet Tidsstämpel** och välj sedan **x-opt-enqueuedtime** i listan **Dynamiskt innehåll.**
    * Markera **fältet Luftfuktighet** och välj sedan **Visa mer** bredvid **Parse Telemetri**. Välj sedan **luftfuktighet**.
    * Välj fältet **Temperatur** och välj sedan **Visa mer** bredvid **Parse Telemetri**. Välj sedan **temperatur**.
    * Välj **Spara** för att spara ändringarna. **Zon 1-miljöåtgärden** ser ut ![som följande skärmbild: Zon 1-miljö](./media/tutorial-in-store-analytics-visualize-insights/zone-1-action.png)
1. Välj **miljöåtgärden Zon 2** och välj **Lägg till en åtgärd**.
1. I **Sökkopplingar och åtgärder**anger du Power **BI**och trycker sedan på **Retur**.
1. Markera åtgärden **Lägg till rader i en datauppsättning (förhandsgranskning).**
1. I åtgärden **Lägg till rader i en datauppsättning 2:**
    * Välj **Analys i butik – utcheckning** som arbetsyta.
    * Välj **Zon 2-sensor** som datauppsättning.
    * Välj **RealTimeData** som tabell.
    * Välj **Lägg till ny parameter** och välj sedan fälten **Tidsstämpel,** **Luftfuktighet**och **Temperatur.**
    * Välj **fältet Tidsstämpel** och välj sedan **x-opt-enqueuedtime** i listan **Dynamiskt innehåll.**
    * Markera **fältet Luftfuktighet** och välj sedan **Visa mer** bredvid **Parse Telemetri**. Välj sedan **luftfuktighet**.
    * Välj fältet **Temperatur** och välj sedan **Visa mer** bredvid **Parse Telemetri**. Välj sedan **temperatur**.
    Välj **Spara** för att spara ändringarna.  **Zon 2-miljöåtgärden** ser ut ![som följande skärmbild: Zon 2-miljö](./media/tutorial-in-store-analytics-visualize-insights/zone-2-action.png)
1. Välj åtgärden **Beläggning** och välj sedan åtgärden **Växla efter gränssnitt.**
1. Välj åtgärden **Dwell Time-gränssnitt** och välj **Lägg till en åtgärd**.
1. I **Sökkopplingar och åtgärder**anger du Power **BI**och trycker sedan på **Retur**.
1. Markera åtgärden **Lägg till rader i en datauppsättning (förhandsgranskning).**
1. I **raden Lägg till i en datauppsättningsåtgärd:**
    * Välj **Analys i butik – utcheckning** som arbetsyta.
    * Välj **Beläggningssensor** som datauppsättning.
    * Välj **RealTimeData** som tabell.
    * Välj **Lägg till ny parameter** och välj sedan fälten **Tidsstämpel**, **Uppehållstid 1**och **Dwell Time 2.**
    * Välj **fältet Tidsstämpel** och välj sedan **x-opt-enqueuedtime** i listan **Dynamiskt innehåll.**
    * Markera fältet **Uppehållstid 1** och välj sedan **Visa mer** **bredvid Parse Telemetri**. Välj sedan **DwellTime1**.
    * Välj fältet **Uppehållstid 2** och välj sedan **Visa mer** **bredvid Parse Telemetri**. Välj sedan **DwellTime2**.
    * Välj **Spara** för att spara ändringarna. **Åtgärden Dwell Time interface** ser ut ![som följande skärmdump: Inflyttningsåtgärd](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-1.png)
1. Välj **gränssnittsåtgärden Personer räkna** och välj **Lägg till en åtgärd**.
1. I **Sökkopplingar och åtgärder**anger du Power **BI**och trycker sedan på **Retur**.
1. Markera åtgärden **Lägg till rader i en datauppsättning (förhandsgranskning).**
1. I **raden Lägg till i en datauppsättningsåtgärd:**
    * Välj **Analys i butik – utcheckning** som arbetsyta.
    * Välj **Beläggningssensor** som datauppsättning.
    * Välj **RealTimeData** som tabell.
    * Välj **Lägg till ny parameter** och välj sedan fälten **Tidsstämpel,** **Kölängd 1**och **Kölängd 2.**
    * Välj **fältet Tidsstämpel** och välj sedan **x-opt-enqueuedtime** i listan **Dynamiskt innehåll.**
    * Välj fältet **Kölängd 1** och välj sedan **Visa mer** **bredvid Parse Telemetri**. Välj sedan **count1**.
    * Välj fältet **Kölängd 2** och välj sedan **Visa mer** **bredvid Parse Telemetri**. Välj sedan **count2**.
    * Välj **Spara** för att spara ändringarna. Åtgärden **Antal personer** ser ut som ![följande skärmbild: Åtgärden Beläggning](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-2.png)

Logikappen körs automatiskt. Om du vill se status för varje körning navigerar du till sidan **Översikt** för logikappen i Azure-portalen:

## <a name="create-a-power-bi-dashboard"></a>Skapa en Power BI-instrumentpanel

Nu har du telemetri som flödar från ditt IoT Central-program via din händelsehubb. Sedan tolkar logikappen händelsehubbens meddelanden och lägger till dem i en Power BI-direktuppspelningsdatauppsättning. Nu kan du skapa en Power BI-instrumentpanel för att visualisera telemetrin:

1. Logga in på ditt **Power BI**-konto.
1. Välj **Arbetsytor > Analyser i butik – utcheckning**.
1. Välj **Skapa > instrumentpanel .**
1. Ange **Store-analys** som instrumentpanelsnamn och välj **Skapa**.

### <a name="add-line-charts"></a>Lägga till linjediagram

Lägg till fyra linjediagramplattor för att visa temperatur och luftfuktighet från de två miljösensorerna. Använd informationen i följande tabell för att skapa panelerna. Om du vill lägga till varje bricka börjar du med att välja **... (Fler alternativ) > Lägg till panel**. Välj **Anpassade strömmande data**och välj sedan **Nästa:**

| Inställning | Diagram #1 | Diagram #2 | Diagram #3 | Diagram #4 |
| ------- | -------- | -------- | -------- | -------- |
| Datauppsättning | Zon 1-sensor | Zon 1-sensor | Zon 2-sensor | Zon 2-sensor |
| Visualiseringstyp | Linjediagram | Linjediagram | Linjediagram | Linjediagram |
| Axel | Tidsstämpel | Tidsstämpel | Tidsstämpel | Tidsstämpel |
| Värden | Temperatur | Fuktighet | Temperatur | Fuktighet |
| Tidsfönster | 60 minuter | 60 minuter | 60 minuter | 60 minuter |
| Titel | Temperatur (1 timme) | Luftfuktighet (1 timme) | Temperatur (1 timme) | Luftfuktighet (1 timme) |
| Underrubrik | Zon 1 | Zon 1 | Zon 2 | Zon 2 |

Följande skärmbild visar inställningarna för det första diagrammet:

![Inställningar för linjediagram](./media/tutorial-in-store-analytics-visualize-insights/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Lägga till kort för att visa miljödata

Lägg till fyra kortplattor för att visa de senaste temperatur- och fuktighetsvärdena från de två miljösensorerna. Använd informationen i följande tabell för att skapa panelerna. Om du vill lägga till varje bricka börjar du med att välja **... (Fler alternativ) > Lägg till panel**. Välj **Anpassade strömmande data**och välj sedan **Nästa:**

| Inställning | Kort #1 | Kort #2 | Kort #3 | Kort #4 |
| ------- | ------- | ------- | ------- | ------- |
| Datauppsättning | Zon 1-sensor | Zon 1-sensor | Zon 2-sensor | Zon 2-sensor |
| Visualiseringstyp | Kort | Kort | Kort | Kort |
| Fält | Temperatur | Fuktighet | Temperatur | Fuktighet |
| Titel | Temperatur (F) | Fuktighet (%) | Temperatur (F) | Fuktighet (%) |
| Underrubrik | Zon 1 | Zon 1 | Zon 2 | Zon 2 |

Följande skärmbild visar inställningarna för det första kortet:

![Kortinställningar](./media/tutorial-in-store-analytics-visualize-insights/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Lägga till paneler för att visa beläggningsdata för utcheckning

Lägg till fyra kortpaneler för att visa kölängd och uppehållstid för de två kassorna i butiken. Använd informationen i följande tabell för att skapa panelerna. Om du vill lägga till varje bricka börjar du med att välja **... (Fler alternativ) > Lägg till panel**. Välj **Anpassade strömmande data**och välj sedan **Nästa:**

| Inställning | Kort #1 | Kort #2 | Kort #3 | Kort #4 |
| ------- | ------- | ------- | ------- | ------- |
| Datauppsättning | Inflyttningssensor | Inflyttningssensor | Inflyttningssensor | Inflyttningssensor |
| Visualiseringstyp | Grupperat stående stapeldiagram | Grupperat stående stapeldiagram | Mätare | Mätare |
| Axel    | Tidsstämpel | Tidsstämpel | Ej tillämpligt | Ej tillämpligt |
| Värde | Uppehåll Tid 1 | Uppehåll Tid 2 | Kölängd 1 | Kölängd 2 |
| Tidsfönster | 60 minuter | 60 minuter |  Ej tillämpligt | Ej tillämpligt |
| Titel | Uppehållstid | Uppehållstid | Kölängd | Kölängd |
| Underrubrik | Kassan 1 | Kassan 2 | Kassan 1 | Kassan 2 |

Ändra storlek på och ordna om panelerna på instrumentpanelen så att de ser ut så här:

![Power BI-instrumentpanel](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard.png)

Du kan lägga till några ytterligare grafikresurser för att ytterligare anpassa instrumentpanelen:

![Power BI-instrumentpanel](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med ditt IoT Central-program kan du ta bort det genom att logga in på programmet och navigera till sidan **Programinställningar** i avsnittet **Administration.**

Om du vill behålla programmet men minska kostnaderna i samband med det inaktiverar du dataexporten som skickar telemetri till händelsehubben.

Du kan ta bort händelsehubben och logikappen i **Azure-portalen**genom att ta bort resursgruppen som kallas butiksanalys .

Du kan ta bort dina Power BI-datauppsättningar och instrumentpanelen genom att ta bort arbetsytan från sidan Power BI-inställningar för arbetsytan.

## <a name="next-steps"></a>Efterföljande moment

Dessa tre självstudier har visat dig en end-to-end-lösning som använder i **butik analytics - kassan** IoT Central programmall. Du har anslutit enheter till programmet, använt IoT Central för att övervaka enheterna och använt Power BI för att skapa en instrumentpanel för att visa insikter från enhetens telemetri. Ett rekommenderat nästa steg är att utforska en av de andra IoT Central-programmallarna:

> [!div class="nextstepaction"]
> * [Skapa energilösningar med IoT Central](../energy/overview-iot-central-energy.md)
> * [Skapa lösningar för myndigheter med IoT Central](../government/overview-iot-central-government.md)
> * [Skapa lösningar för hälso- och sjukvården med Azure IoT Central](../healthcare/overview-iot-central-healthcare.md)
