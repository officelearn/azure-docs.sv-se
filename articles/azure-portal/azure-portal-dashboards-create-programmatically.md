---
title: Skapa Azure-instrumentpaneler programmässigt | Microsoft Docs
description: Den här artikeln förklarar hur du programmässigt skapar Azure-instrumentpaneler.
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
ms.openlocfilehash: ed3737b2f64fb4aad3f1418f08e909bf720b42f3
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103850"
---
# <a name="programmatically-create-azure-dashboards"></a>Skapa Azure-instrumentpaneler programmässigt

Det här dokumentet beskriver steg för att skapa och publicera Azure-instrumentpaneler programmässigt. Instrumentpanelen visas nedan refererar till hela dokumentet.

![exempel på en instrumentpanel](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Översikt

Delade instrumentpaneler i Azure är [resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) precis som virtuella datorer och storage-konton.  Därför kan de kan hanteras via programmering den [Azure Resource Manager REST API: er](/rest/api/), [Azure CLI](https://docs.microsoft.com/cli/azure), [Azure PowerShell-kommandon](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-4.2.0), och många [ Azure-portalen](https://portal.azure.com) funktioner bygger på dessa API: er för att underlätta resurshantering.  

Var och en av dessa API: er och verktyg erbjuder olika sätt att skapa, visa, hämta, ändra och ta bort resurser.  Eftersom instrumentpaneler är resurser, kan du välja din favorit-API / verktyget om du vill använda.

Oavsett vilket verktyg som du använder, måste du skapa en JSON-representation av instrumentpanelens objekt innan du kan anropa någon resursskapande API. Det här objektet innehåller information om delarna (alias) paneler) på instrumentpanelen. Den innehåller storlekar, positioner, de är kopplade till resurser och eventuella användaranpassningar.

Det mest praktiskt sättet att bygga upp det här JSON-dokumentet är att använda [portalen](https://portal.azure.com/) att interaktivt lägga till och placera dina paneler. Sedan kan du exportera JSON. Slutligen kan skapa du en mall från resultatet för senare användning i skript, program och distributionsverktyg.

## <a name="create-a-dashboard"></a>Skapa en instrumentpanel

Använd kommandot ny instrumentpanel för att skapa en ny instrumentpanel, på portalens huvudskärmen.

![ny instrumentpanel-kommando](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Du kan sedan använda panelgalleriet för att söka efter och lägga till paneler. Panelerna har lagts till genom att dra och släppa dem. Vissa paneler stöder storleksändring via en handtaget, medan andra support åtgärdar storlekar som kan ses i sina snabbmenyn.

### <a name="drag-handle"></a>Handtaget
![Handtaget](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Fasta storlekar via snabbmenyn
![storlekar snabbmenyn](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Dela instrumentpanelen

När du har konfigurerat instrumentpanelen om du vill ha dem i nästa steg är att publicera instrumentpanelen (med kommandot Share) och sedan använda resursläsaren för att hämta JSON.

![kommandot Share](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Klicka på kommandot Share visas en dialogruta där du uppmanas att välja vilken prenumeration och resursgrupp grupp att publicera till. Tänk på att [du måste ha skrivbehörighet](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) i gruppen prenumeration och resursgrupp som du väljer.

![delning och åtkomst](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Hämta JSON-representation av instrumentpanelen

Publicering tar bara några sekunder.  När det är klart, nästa steg är att gå till den [Resursläsaren](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) att hämta JSON.

![Bläddra resursläsaren](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

Från resursutforskaren, navigerar du till gruppen prenumeration och resursgrupp som du har valt. Klicka på resursen för nyligen publicerade instrumentpanelen att visa JSON.

![Resource explorer json](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Skapa en mall från JSON

Nästa steg är att skapa en mall från den här JSON så att den kan återanvändas programmässigt med lämpliga resource Manager API: er, kommandoradsverktyg, eller i hanteringsportalen.

Det är inte nödvändigt att helt förstå JSON-strukturen instrumentpanelen för att skapa en mall. I de flesta fall som du vill bevara struktur och konfiguration av varje panel och Parameterisera uppsättning Azure-resurser pekar. Titta på den exporterade JSON-instrumentpanelen och hitta alla förekomster av Azure resource ID: N. Vårt exempel instrumentpanelen har flera paneler som alla pekar på en enda Azure-dator. Det beror på att vår instrumentpanel bara tittar på den här enskild resurs. Om du söker i exempel-json (ingår i slutet av dokumentet) för ”/ prenumerationer”, du hittar flera förekomster av detta Id.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Om du vill publicera den här instrumentpanelen för alla virtuella datorer måste i framtiden du Parameterisera varje förekomst av den här strängen i JSON. 

Det finns två varianter av API: er som skapar resurser i Azure. [Tvingande API: er](https://docs.microsoft.com/rest/api/resources/resources) som skapar en resurs i taget, och en [mallbaserad distribution](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) system som kan dirigera skapandet av flera, beroende resurser med ett enda API-anrop. Det senare stöd inbyggt för parameterisering och mall så vi använder den i vårt exempel.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programmässigt skapa en instrumentpanel från din mall med hjälp av en för malldistribution

Azure ger dig möjlighet att samordna distribution av flera resurser. Du kan skapa en Distributionsmall som uttrycker uppsättning resurser för att distribuera samt relationer mellan dem.  JSON-format för varje resurs är samma som om du skapade dem en i taget. Skillnaden är att den [Mallspråket](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) lägger till några begrepp som variabler, parametrar, grundläggande funktioner och mer. Den här utökade syntaxen stöds bara i samband med en för malldistribution och fungerar inte om det används med de tvingande API: erna som beskrivs ovan.

Om du ska den här vägen så parameterisering ska göras med hjälp av parametern mallsyntaxen.  Du kan ersätta alla instanser av resurs-id som påträffades tidigare som visas här.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Exempel-JSON-egenskap med hårdkodade resurs-Id
`id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Exempel-JSON-egenskap som konverteras till en parametriserade version utifrån mallparametrar

`id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"`

Du måste också deklarera vissa nödvändiga mallmetadata och parametrarna som överst i json-mall så här:

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

__Du kan se den fullständiga, fungerande mallen i slutet av det här dokumentet.__

När du har särskild mallen kan du distribuera den med hjälp av den [REST API: er](https://docs.microsoft.com/rest/api/resources/deployments), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), [Azure CLI](https://docs.microsoft.com/cli/azure/group/deployment#az-group-deployment-create), eller [portalens mallsidan för distribution ](https://portal.azure.com/#create/Microsoft.Template).

Här följer två versioner av våra exempel instrumentpanelen JSON. Först är den version som vi har exporterat från portalen som redan har bundits till en resurs. Andra är den versionen av principmallen som programmässigt kan bindas till virtuella datorer och distribueras med Azure Resource Manager.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>JSON-representation av våra exempel instrumentpanelen (före mall)

Det här är vad du kan förvänta dig att se om du följer du de tidigare anvisningarna för att hämta JSON-representation av en instrumentpanel som redan har distribuerats. Observera de hårdkodade resursidentifierarna som visar att den här instrumentpanelen pekar på en specifik Azure-dator.

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
        "metadata": { }
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

### <a name="template-representation-of-our-example-dashboard"></a>Mall-representation av våra exempel instrumentpanel

Mallversionen av instrumentpanelen har definierat tre parametrar __virtualMachineName__, __virtualMachineResourceGroup__, och __dashboardName__.  Parametrarna kan du peka den här instrumentpanelen på en annan Azure-dator varje gång du distribuerar. Parametriserade ID: n är markerade för att visa att den här instrumentpanelen kan via programmering kan konfigureras och distribueras för att peka till alla Azure-dator. Det enklaste sättet att testa den här funktionen är att kopiera följande mall och klistra in den i den [distribution av Azure portal mallsidan](https://portal.azure.com/#create/Microsoft.Template). 

Det här exemplet distribuerar en instrumentpanel påverkar i sig, men Mallspråket kan du distribuera flera resurser och paketera en eller flera instrumentpaneler längs sida dem. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}


```
