---
title: Skapa Azure-instrumentpaneler programmässigt
description: Använd en instrumentpanel i Azure-portalen som en mall för att programmässigt skapa Azure Dashboards. Innehåller JSON-referens.
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
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 5329a7b21aff7ecffc7153c7aa74ddb93bce75cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132013"
---
# <a name="programmatically-create-azure-dashboards"></a>Skapa Azure-instrumentpaneler programmässigt

I den här artikeln får du hjälp med att programmässigt skapa och publicera Azure-instrumentpaneler. Instrumentpanelen som visas nedan refereras i hela dokumentet.

![exempelinstrumentpanel](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Översikt

Delade instrumentpaneler i [Azure-portalen](https://portal.azure.com) är [resurser](../azure-resource-manager/management/overview.md) precis som virtuella datorer och lagringskonton. Du kan hantera resurser programmässigt med hjälp av [AZURE Resource Manager REST API:er](/rest/api/), Azure [CLI](/cli/azure)och [Azure PowerShell.](/powershell/azure/get-started-azureps)

Många funktioner bygger på dessa API:er för att underlätta resurshanteringen. Var och en av dessa API:er och verktyg erbjuder sätt att skapa, lista, hämta, ändra och ta bort resurser. Eftersom instrumentpaneler är resurser kan du välja ditt favorit-API eller verktyg som ska användas.

Oavsett vilka verktyg du använder, för att skapa en instrumentpanel programmässigt, skapar du en JSON-representation av instrumentpanelsobjektet. Det här objektet innehåller information om panelerna på instrumentpanelen. Den innehåller storlekar, befattningar, resurser som de är bundna till och eventuella användaranpassningar.

Det mest praktiska sättet att bygga upp detta JSON-dokument är att använda Azure-portalen. Du kan interaktivt lägga till och placera dina brickor. Exportera sedan JSON och skapa en mall från resultatet för senare användning i skript, program och distributionsverktyg.

## <a name="create-a-dashboard"></a>Skapa en instrumentpanel

Om du vill skapa en **instrumentpanel** väljer du Instrumentpanel på [Azure-portalmenyn](https://portal.azure.com) och väljer sedan **Ny instrumentpanel**.

![nytt instrumentpanelskommando](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Använd panelgalleriet för att hitta och lägga till paneler. Paneler läggs till genom att dra och släppa dem. Vissa paneler stöder storleksändring med hjälp av ett draghandtag.

![dra handtag för att ändra storlek](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Andra har fasta storlekar att välja mellan i sin snabbmeny.

![storlekar snabbmenyn för att ändra storlek](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Dela instrumentpanelen

När du har konfigurerat instrumentpanelen är nästa steg att publicera instrumentpanelen med kommandot **Dela.**

![dela en instrumentpanel](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Om du väljer **Dela** uppmanas du att välja vilken prenumeration och resursgrupp som ska publiceras på. Du måste ha skrivbehörighet till den prenumerations- och resursgrupp som du väljer. Mer information finns i [Lägga till eller ta bort rolltilldelningar med Azure RBAC och Azure-portalen](../role-based-access-control/role-assignments-portal.md).

![göra ändringar i delning och åtkomst](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Hämta JSON-representationen av instrumentpanelen

Publicering tar bara några sekunder. När det är klart är nästa steg att hämta JSON med kommandot **Hämta.**

![ladda ner JSON representation](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Skapa en mall från JSON

Nästa steg är att skapa en mall från denna JSON. Använd mallen programmässigt med lämpliga API:er för resurshantering, kommandoradsverktyg eller i portalen.

Du behöver inte helt förstå instrumentpanelen JSON struktur för att skapa en mall. I de flesta fall vill du bevara strukturen och konfigurationen för varje panel. Sedan parameterisera den uppsättning Azure-resurser som panelerna pekar på. Titta på din exporterade JSON-instrumentpanel och hitta alla förekomster av Azure-resurs-ID: er. Vår exempel instrumentpanel har flera paneler som alla pekar på en enda Virtuell Azure-dator. Det beror på att vår instrumentpanel bara tittar på den här enda resursen. Om du söker i exemplet JSON, som ingår i slutet av dokumentet, för "/prenumerationer", hittar du flera förekomster av detta ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Om du vill publicera den här instrumentpanelen för alla virtuella datorer i framtiden, parameterisera varje förekomst av den här strängen i JSON.

Det finns två metoder för API:er som skapar resurser i Azure:

* Tvingande API:er skapar en resurs i taget. Mer information finns i [Resurser](/rest/api/resources/resources).
* Ett mallbaserat distributionssystem som skapar flera, beroende resurser med ett enda API-anrop. Mer information finns i [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

Mallbaserad distribution stöder parameterisering och templating. Vi använder den här metoden i den här artikeln.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Skapa en instrumentpanel från mallen programmässigt med hjälp av en malldistribution

Azure erbjuder möjligheten att dirigera distributionen av flera resurser. Du skapar en distributionsmall som uttrycker uppsättningen resurser att distribuera och relationerna mellan dem.  JSON-formatet för varje resurs är detsamma som om du skapade dem en efter en. Skillnaden är att mallspråket lägger till några begrepp som variabler, parametrar, grundläggande funktioner med mera. Den här utökade syntaxen stöds bara i kontexten för en malldistribution. Det fungerar inte om det används med tvingande API: er diskuteras tidigare. Mer information finns [i Förstå strukturen och syntaxen för Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md).

Parameterisering bör göras med hjälp av mallens parametersyntax.  Du ersätter alla instanser av resurs-ID som vi hittade tidigare som visas här.

Exempel på JSON-egenskap med hårdkodat resurs-ID:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Exempel på JSON-egenskap som konverterats till en parameterversion baserat på mallparametrar

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Deklarera nödvändiga mallmetadata och parametrarna högst upp i JSON-mallen så här:

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
När du har konfigurerat mallen distribuerar du den med någon av följande metoder:

* [REST API:er](/rest/api/resources/deployments)
* [Powershell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](/cli/azure/group/deployment#az-group-deployment-create)
* [Distributionssidan för Azure-portalmallen](https://portal.azure.com/#create/Microsoft.Template)

Därefter ser du två versioner av vår exempel instrumentpanel JSON. Den första är den version som vi exporterade från portalen som redan var bunden till en resurs. Den andra är mallversionen som kan vara programmässigt bunden till alla virtuella datorer och distribueras med Azure Resource Manager.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>JSON representation av vårt exempel instrumentbräda innan templating

Det här exemplet visar vad du kan förvänta dig att se om du följde med i den här artikeln. Instruktionerna exporterade JSON-representationen av en instrumentpanel som redan har distribuerats. De hårdkodade resursidentifierarna visar att den här instrumentpanelen pekar på en viss virtuell Azure-dator.

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

### <a name="template-representation-of-our-example-dashboard"></a>Mallrepresentation av vår exempelinstrumentpanel

Mallversionen av instrumentpanelen har `virtualMachineName`definierat tre parametrar som kallas , `virtualMachineResourceGroup`och `dashboardName`.  Med parametrarna kan du peka instrumentpanelen på en annan virtuell Azure-dator varje gång du distribuerar. Den här instrumentpanelen kan konfigureras och distribueras programmatiskt för att peka på alla virtuella Azure-datorer. Testa den här funktionen genom att kopiera följande mall och klistra in den på [distributionssidan för Azure Portal-mallen](https://portal.azure.com/#create/Microsoft.Template).

I det här exemplet distribueras en instrumentpanel av sig själv, men med mallspråket kan du distribuera flera resurser och paketera en eller flera instrumentpaneler längs dem.

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

Nu när du har sett ett exempel på hur du använder en parameteriserad mall för att distribuera en instrumentpanel kan du prova att distribuera mallen med hjälp av [AZURE Resource Manager REST API:er](/rest/api/), Azure [CLI](/cli/azure)eller [Azure PowerShell- kommandon](/powershell/azure/get-started-azureps).
