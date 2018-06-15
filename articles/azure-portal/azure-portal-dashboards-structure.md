---
title: Strukturen för Azure instrumentpaneler | Microsoft Docs
description: Den här artikeln förklarar JSON-strukturen för en Azure-instrumentpanel
services: azure-portal
documentationcenter: ''
author: adamab
manager: dougeby
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: adamab
ms.openlocfilehash: fa50b31a0c8e9077658106039c4fbd2eef8b4367
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165187"
---
# <a name="the-structure-of-azure-dashboards"></a>Strukturen för Azure instrumentpaneler
Det här dokumentet beskriver hur strukturen för en Azure instrumentpanel med följande instrumentpanelen som exempel:

![exempel på en instrumentpanel](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Eftersom delade [Azure instrumentpaneler är resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), den här instrumentpanelen kan representeras som JSON.  Följande JSON representerar instrumentpanelen visualiseras ovan.

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

## <a name="common-resource-properties"></a>Allmänna resursegenskaper för

Nu ska vi dela upp de relevanta avsnitten i JSON.  Egenskaperna översta __id__, __namn__, __typen__, __plats__, och __taggar__ egenskaper är gemensamma för alla typer av Azure-resurs. Det vill säga har de inte mycket att göra med innehåll på instrumentpanelen.

### <a name="the-id-property"></a>Id-egenskapen

Azure-resurs-id, omfattas av den [namngivningskonventioner för resurser i Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). När du skapar en instrumentpanel i portalen väljer vanligtvis ett id i form av ett guid, men du kan använda valfritt giltigt namn när du skapar dem via programmering. 

### <a name="the-name-property"></a>Egenskapen name
Namnet är segmentet i resurs-Id som inte innehåller prenumeration, resurstypen eller grupp resursinformation. I princip är det sista segmentet i resurs-id.

### <a name="the-type-property"></a>Egenskapen type
Alla instrumentpaneler är av typen __Microsoft.Portal/dashboards__.

### <a name="the-location-property"></a>Egenskapen location
Till skillnad från andra resurser inte instrumentpaneler runtime-komponent.  För instrumentpaneler anger platsen den primära geografiska plats som lagrar på instrumentpanelen JSON-representation. Värdet måste vara ett nummer plats som kan hämtas med hjälp av den [platser API på resursen prenumerationer](https://docs.microsoft.com/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>Egenskapen taggar
Taggar är en vanlig funktion för Azure-resurser som kan du ordna resurs av godtyckligt namn-värdepar. För instrumentpaneler, finns en särskild tagg kallas __dolda rubrik__. Om din instrumentpanel har den här egenskapen fyllts, används det som visningsnamnet för instrumentpanelen i portalen. Det går inte att byta namn på Azure resurs-ID, men kan taggar. Den här taggen ger dig ett sätt att ha ett formatmallen namn för instrumentpanelen.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>Egenskaper för objektet
Egenskaper för objektet innehåller två egenskaper __linser__ och __metadata__. Den __linser__ egenskap innehåller information om panelerna (kallas även delar) på instrumentpanelen.  Den __metadata__ egenskapen finns det för eventuella framtida funktioner.

### <a name="the-lenses-property"></a>Egenskapen linser
Den __linser__ egenskap innehåller instrumentpanelen. Observera att linserna objekt i det här exemplet innehåller en enda egenskap som kallas ”0”. Linser är ett begrepp, gruppering som inte har implementerats i instrumentpaneler. Nu är har alla dina instrumentpaneler den här egenskapen på linsen objektet igen, kallas ”0”.

### <a name="the-lens-object"></a>Objektet lins
Objektet under ”0” innehåller två egenskaper __ordning__ och __delar__.  I den aktuella versionen av instrumentpaneler, __ordning__ är alltid 0. Den __delar__ -egenskapen innehåller ett objekt som definierar de enskilda delarna (kallas även panelerna) på instrumentpanelen.

Den __delar__ objektet innehåller en egenskap för varje del, där namnet på egenskapen är ett tal. Det här antalet är inte viktig. 

### <a name="the-part-object"></a>En del objekt
Varje enskild del-objekt har en __position__, och __metadata__.

### <a name="the-position-object"></a>Placera objekt
Den __position__ egenskapen innehåller information om storlek och plats för del uttryckt __x__, __y__, __rowSpan__, och __colSpan__. Värdena är i rutnätsenheter. Dessa rutnätsenheter är synliga när instrumentpanelen är i läget anpassa som visas här. Om du vill att en panel har två rutnätsenheter bredden en höjden för ett Rutnätsenhet och en plats i övre vänstra hörnet på instrumentpanelen och sedan position obejct ser ut så här:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![rutnät-enheter](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>Metadataobjektet
Varje del har en metadataegenskap, ett objekt har endast en obligatorisk egenskap som kallas __typen__. Den här strängen talar om portalen vilka rutan för att visa. Våra exempel instrumentpanelen använder dessa typer av paneler:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` – Används för att visa övervakning mått
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` – Används för att visa med text och bilder med grundläggande formatering för listor, länkar, osv.
1. `Extension[azure]/HubsExtension/PartType/VideoPart` Används för att visa videor från YouTube, Channel9 och andra typer av video som fungerar i en video HTML-tagg.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` Används för att visa namn och status för en virtuell Azure-dator.

Varje typ av en del har sin egen konfiguration. De möjliga konfigurationsegenskaperna kallas __indata__, __inställningar__, och __tillgången__. 

### <a name="the-inputs-object"></a>Indata-objekt
Indata-objekt innehåller vanligen information som binder en panel till en resurs.  Den virtuella datorn som en del i vårt exempel på en instrumentpanel innehåller en enda indata som använder Azure-resurs-id för att uttrycka bindningen.  Det här resurs-id-formatet är konsekvent på alla Azure-resurser.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
Mått diagramdel har en enkel indata som representerar resursen som ska bindas till, samt information om metric(s) som ska visas. Här är indata för panelen som visar mått nätverk In- och nätverk.

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
Inställningsobjektet innehåller konfigurerbara elementen i en del.  I vårt exempel på en instrumentpanel använder Markdown-del inställningar för att lagra innehåll samt en konfigurerbar rubrik och underrubrik anpassade markdown.

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

På liknande sätt har panelen video de egna inställningarna som innehåller en pekare till videon att spela upp, automatisk uppspelning inställningen och valfria rubrik.

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

### <a name="the-asset-object"></a>Objektet tillgångsinformation
Paneler som är bundna till första klass hanterbara portal objekt (kallas tillgångar) har den här relationen uttryckt via objektet tillgången.  I vårt exempel instrumentpanelen innehåller panelen virtuella beskrivningen tillgången.  Den __idInputName__ egenskapen anger portalen att ange id som innehåller den unika identifieraren för tillgången, i det här fallet resurs-id. De flesta Azure resurstyper har tillgångar som definierats i portalen.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`