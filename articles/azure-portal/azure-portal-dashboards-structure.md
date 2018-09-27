---
title: Strukturen för Azure-instrumentpaneler | Microsoft Docs
description: Den här artikeln förklarar JSON-strukturen för en Azure-instrumentpanel
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: dougeby
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: cwatson
ms.openlocfilehash: 405e0d5184880a00c07de55bd968210fa28e45fc
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393085"
---
# <a name="the-structure-of-azure-dashboards"></a>Strukturen för Azure-instrumentpaneler
Det här dokumentet beskriver strukturen för en Azure-instrumentpanel, med följande instrumentpanel som exempel:

![exempel på en instrumentpanel](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Eftersom delade [Azure-instrumentpaneler finns resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), den här instrumentpanelen kan representeras som JSON.  Följande JSON representerar instrumentpanelen visualiseras ovan.

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

Lås oss de relevanta avsnitten för JSON.  Egenskaperna översta __id__, __namn__, __typ__, __plats__, och __taggar__ egenskaper är delas mellan alla Azure-resurstyper. Det vill säga behöver de inte mycket att göra med instrumentpanelens innehåll.

### <a name="the-id-property"></a>Id-egenskapen

Azure-resurs-id, omfattas av den [namngivningskonventioner för Azure-resurser](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). När du skapar en instrumentpanel i portalen det vanligtvis väljer ett id i form av ett guid, men du kan använda valfritt giltigt namn när du skapar dem via programmering. 

### <a name="the-name-property"></a>Egenskapen name
Namnet är segmentet i resurs-Id som inte innehåller den prenumeration eller resurstyp resursinformation för gruppen. Det är i grunden innebär det sista segmentet i resurs-id.

### <a name="the-type-property"></a>Egenskapen type
Alla instrumentpaneler som är av typen __Microsoft.Portal/dashboards__.

### <a name="the-location-property"></a>Egenskapen location
Till skillnad från andra resurser har instrumentpaneler en runtime-komponent.  Instrumentpaneler anger platsen den primära geografiska plats som lagrar instrumentpanelens JSON-representation. Värdet ska vara en av plats-koder som kan hämtas med hjälp av den [platser API på prenumerationer resursen](https://docs.microsoft.com/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>Egenskapen taggar
Taggar är en vanlig funktion för Azure-resurser som hjälper dig att organisera din resurs av godtyckligt namn-värdepar. Instrumentpaneler finns en särskild tagg kallas __dolda rubrik__. Om din instrumentpanel har den här egenskapen ifylld, används det som visningsnamnet för instrumentpanelen i portalen. Azure resurs-ID kan inte döpas om, men kan taggar. Den här taggen ger dig ett sätt att ha ett formatmallen namn för din instrumentpanel.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>För egenskapsobjektet
För egenskapsobjektet innehåller två egenskaper __linser__ och __metadata__. Den __linser__ egenskapen innehåller information om panelerna (alias) delar) på instrumentpanelen.  Den __metadata__ egenskap finns det för eventuella framtida funktioner.

### <a name="the-lenses-property"></a>Egenskapen linser
Den __linser__ egenskapen innehåller instrumentpanelen. Observera att linserna objekt i det här exemplet innehåller en enskild egenskap med namnet ”0”. Linser är en gruppering koncept som inte implementeras för närvarande i instrumentpaneler. Alla dina instrumentpaneler har den här egenskapen på lins objektet igen, kallas ”0” för tillfället.

### <a name="the-lens-object"></a>Objektet lins
Objektet under ”0” innehåller två egenskaper __ordning__ och __delar__.  I den aktuella versionen av instrumentpaneler, __ordning__ är alltid 0. Den __delar__ egenskapen innehåller ett objekt som definierar de enskilda delarna (alias) paneler) på instrumentpanelen.

Den __delar__ objektet innehåller en egenskap för varje del, där namnet på egenskapen är ett tal. Det här talet är inte viktig. 

### <a name="the-part-object"></a>En del objekt
Varje enskild del objekt har en __position__, och __metadata__.

### <a name="the-position-object"></a>Placera objekt
Den __position__ egenskapen innehåller informationen och plats för delen uttryckt i form av __x__, __y__, __rowSpan__, och __colSpan__. Värdena är i rutnätsenheter. Dessa grid-enheter är synliga när instrumentpanelen är i läget anpassa som visas här. Om du vill att en panel har en bredden på två grid enheter en höjden på en grid enhet och en plats i övre vänstra hörnet på instrumentpanelen och sedan position obejct ser ut så här:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![Grid-enheter](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>Metadata-objektet
Varje del har en metadata-egenskap, ett objekt har endast en obligatorisk egenskap som kallas __typ__. Den här strängen visar portalen vilka rutan för att visa. Vårt exempel instrumentpanelen använder dessa typer av paneler:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` – Används för att visa övervakning av mått
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` – Används för att visa text och bilder med grundläggande formatering för listor, länkar, osv.
1. `Extension[azure]/HubsExtension/PartType/VideoPart` Används för att visa videor från YouTube, Channel 9 och någon annan typ av video som fungerar i en video html-tagg.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` Används för att visa namn och status för virtuella Azure-datorer.

Varje typ av en del har sin egen konfiguration. Möjliga konfigurationsegenskaperna kallas __indata__, __inställningar__, och __tillgången__. 

### <a name="the-inputs-object"></a>Indata-objekt
Indata-objekt innehåller vanligtvis information som binder en panel till en resursinstans.  Den virtuella datorn som en del i vårt exempel på en instrumentpanel innehåller en enda indata som använder Azure-resurs-id för att uttrycka bindningen.  Det här resurs-id-formatet är konsekvent för alla Azure-resurser.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
Mått diagrammet delen har en enda indata som uttrycker resursen som ska bindas till, samt information om metric(s) som visas. Här är indata för den ikon som visar mått för ingående och utgående nätverk.

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
Inställningsobjektet innehåller de konfigurerbara elementen i en del.  I vårt exempel på en instrumentpanel används den Markdown-delen för att lagra innehåll samt en konfigurerbar rubrik och underrubrik anpassade markdown.

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

På samma sätt har videopanelen sina egna inställningar som innehåller en pekare till videon spelas upp, spela upp automatiskt inställningen och valfritt rubrikinformation.

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

### <a name="the-asset-object"></a>Objektet tillgång
Paneler som är bundna till första klassens hanterbara portal objekt (kallas tillgångar) har den här relationen uttryckt via objektet tillgången.  I vårt exempel instrumentpanelen innehåller panelen VM beskrivningen tillgången.  Den __idInputName__ egenskapen visar portalen att ID: t indata innehåller den unika identifieraren för tillgången, i det här fallet resurs-id. De flesta Azure resurstyper ha tillgångar som definierats i portalen.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`