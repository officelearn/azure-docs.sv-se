---
title: Strukturen för Azure-instrumentpaneler | Microsoft-dokument
description: Gå igenom JSON-strukturen för en Azure Dashboard med hjälp av en exempelinstrumentpanel. Innehåller referens till resursegenskaper.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/20/2019
ms.author: mblythe
ms.openlocfilehash: 18125e119e7ffdd2f8fa8ca3c5c1b12c8c9a94e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75640371"
---
# <a name="the-structure-of-azure-dashboards"></a>Strukturen för Azure-instrumentpaneler
Det här dokumentet går igenom strukturen på en Azure-instrumentpanel med hjälp av följande instrumentpanel som exempel:

![exempelinstrumentpanel](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Eftersom delade [Azure-instrumentpaneler är resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)kan den här instrumentpanelen representeras som JSON.  Följande JSON representerar instrumentpanelen som visualiseras ovan.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

## <a name="common-resource-properties"></a>Vanliga resursegenskaper

Låt oss bryta ner de relevanta delarna av JSON.  Egenskaperna på den översta nivån, __id,__ __namn,__ __typ,__ __plats__och __taggar__ delas mellan alla Azure-resurstyper. Det vill än, de har inte mycket att göra med instrumentpanelens innehåll.

### <a name="the-id-property"></a>Egenskapen ID

Azure-resurs-ID, med förbehåll för [namngivningskonventionerna för Azure-resurser](/azure/architecture/best-practices/resource-naming). När portalen skapar en instrumentpanel väljer den vanligtvis ett ID i form av ett guid, men du kan använda alla giltiga namn när du skapar dem programmässigt. 

### <a name="the-name-property"></a>Namnegenskapen
Namnet är det segment i resurs-ID som inte innehåller information om prenumeration, resurstyp eller resursgrupp. I huvudsak är det det sista segmentet i resurs-ID.

### <a name="the-type-property"></a>Egenskapen type
Alla instrumentpaneler är av typen __Microsoft.Portal/dashboards__.

### <a name="the-location-property"></a>Platsegenskapen
Till skillnad från andra resurser har instrumentpaneler inte en körningskomponent.  För instrumentpaneler anger platsen den primära geografiska platsen som lagrar instrumentpanelens JSON-representation. Värdet ska vara en av de platskoder som kan hämtas med hjälp av [plats-API:et på prenumerationsresursen](https://docs.microsoft.com/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>Egenskapen tags
Taggar är en vanlig funktion i Azure-resurser som gör att du kan organisera din resurs efter godtyckliga namnvärdespar. För instrumentpaneler finns det en speciell tagg som kallas __dold rubrik__. Om instrumentpanelen har den här egenskapen ifylld används den som visningsnamn för instrumentpanelen i portalen. Azure-resurs-ID kan inte döpas om, men taggar kan. Den här taggen ger dig ett sätt att få ett namnbytesbart visningsnamn för instrumentpanelen.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>Egenskapsobjektet
Egenskapsobjektet innehåller två egenskaper, __linser__ och __metadata__. Egenskapen __linser__ innehåller information om panelerna på instrumentpanelen.  Egenskapen __metadata__ finns där för potentiella framtida funktioner.

### <a name="the-lenses-property"></a>Linserna egendom
Egenskapen __linser__ innehåller instrumentpanelen. Observera att linserna objektet i det här exemplet innehåller en enda egenskap som kallas "0". Linser är ett grupperingskoncept som för närvarande inte implementeras i instrumentpaneler. För nu, alla dina instrumentpaneler har denna enda egenskap på linsen objektet, återigen, kallas "0".

### <a name="the-lens-object"></a>Linsobjektet
Objektet under "0" innehåller två egenskaper, __ordning__ och __delar__.  I den aktuella versionen av instrumentpaneler är __ordningen__ alltid 0. Egenskapen __parts__ innehåller ett objekt som definierar de enskilda delarna (kallas även paneler) på instrumentpanelen.

__Reservdelsobjektet__ innehåller en egenskap för varje del, där namnet på egenskapen är ett tal. Detta nummer är inte signifikant. 

### <a name="the-part-object"></a>Delobjektet
Varje enskilt delobjekt har en __position__och __metadata__.

### <a name="the-position-object"></a>Positionsobjektet
Egenskapen __position__ innehåller storleks- och platsinformationen för den del som uttrycks som __x,__ __y,__ __rowSpan__och __colSpan__. Värdena är i form av rutnätsenheter. Dessa rutnätsenheter visas när instrumentpanelen är i anpassningsläge som visas här. Om du vill att en panel ska ha en bredd på två rutnätsenheter, en höjd på en rutnätsenhet och en plats i det övre vänstra hörnet av instrumentpanelen ser positionsobjektet ut så här:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![rutnätsenheter](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>Metadataobjektet
Varje del har en metadataegenskap, ett objekt har bara en obligatorisk egenskap som kallas __typ__. Den här strängen talar om för portalen vilken panel som ska visas. Vår exempelinstrumentpanel använder dessa typer av paneler:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart`– Används för att visa övervakningsmått
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart`– Används för att visa med text eller bilder med grundläggande formatering för listor, länkar, etc.
1. `Extension[azure]/HubsExtension/PartType/VideoPart`– Används för att visa videor från YouTube, Channel9 och alla andra typer av video som fungerar i en HTML-videotagg.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart`– Används för att visa namn och status för en virtuell Azure-dator.

Varje typ av del har sin egen konfiguration. De möjliga konfigurationsegenskaperna kallas __indata,__ __inställningar__och __tillgång__. 

### <a name="the-inputs-object"></a>Indataobjektet
Indataobjektet innehåller vanligtvis information som binder en panel till en resursinstans.  Den virtuella datorn delen i vårt exempel instrumentpanel innehåller en enda indata som använder Azure resurs-ID för att uttrycka bindningen.  Det här resurs-ID-formatet är konsekvent i alla Azure-resurser.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
Måttdiagramdelen har en enda indata som uttrycker resursen att binda till, samt information om de mått som visas. Här är indata för panelen som visar mätvärdena Nätverk i och Nätverk ut.

```json
“inputs”:
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
                {
                    "name": "Network In",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                },
                {
                    "name": "Network Out",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                }
            ]
        }
    }
]

```

### <a name="the-settings-object"></a>Inställningsobjektet
Inställningsobjektet innehåller konfigurerbara element i en del.  I vår exempelinstrumentpanel använder Markdown-delen inställningar för att lagra det anpassade markdown-innehållet samt en konfigurerbar titel och underrubrik.

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

På samma sätt har videopanelen sina egna inställningar som innehåller en pekare till videon som ska spelas upp, en autoplay-inställning och valfri titelinformation.

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="the-asset-object"></a>Tillgångsobjektet
Paneler som är bundna till första klass hanterbara portalobjekt (kallas tillgångar) har den här relationen uttryckt via tillgångsobjektet.  I vår exempelinstrumentpanel innehåller panelen för virtuella datorer den här tillgångsbeskrivningen.  Egenskapen __idInputName__ talar om för portalen att ID-indata innehåller den unika identifieraren för tillgången, i det här fallet resurs-ID. De flesta Azure-resurstyper har resurser definierade i portalen.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`
