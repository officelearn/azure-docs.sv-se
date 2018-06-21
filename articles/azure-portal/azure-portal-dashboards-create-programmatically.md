---
title: Skapa programmässigt Azure instrumentpaneler | Microsoft Docs
description: Den här artikeln förklaras hur du skapar programmässigt Azure instrumentpaneler.
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
ms.author: adamab
ms.openlocfilehash: dafada5cecbc6345da46bc3a32fc3b91eb72313a
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295519"
---
# <a name="programmatically-create-azure-dashboards"></a>Skapa programmässigt Azure instrumentpaneler

Det här dokumentet går igenom processen att skapa och publicera Azure instrumentpaneler programmässigt. Instrumentpanelen nedan refereras i hela dokumentet.

![exempel på en instrumentpanel](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Översikt

Delade instrumentpaneler i Azure är [resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) på samma sätt som virtuella datorer och storage-konton.  Därför kan de kan hanteras via programmering den [Azure Resource Manager REST API: er](/rest/api/), [Azure CLI](https://docs.microsoft.com/cli/azure), [Azure PowerShell-kommandon](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-4.2.0), och många [ Azure-portalen](https://portal.azure.com) funktioner bygga på dessa API: er för att underlätta resurshantering.  

Var och en av dessa API: er och verktyg erbjuder sätt att skapa, visa, hämta, ändra och ta bort resurser.  Eftersom instrumentpaneler resurser bör du välja din favorit API / verktyget om du vill använda.

Oavsett vilket verktyg du använder, måste du skapa en JSON-representation av instrumentpanelens objekt innan du kan anropa någon resurs skapa API. Det här objektet innehåller information om delarna (kallas även panelerna) på instrumentpanelen. Den omfattar storlekar, positioner, de är kopplade till resurser och eventuella användaranpassningar.

Det mest praktiskt sättet att bygga upp den här JSON-dokumentet är att använda [portalen](https://portal.azure.com/) interaktivt lägga till och placera dina paneler. Sedan kan du exportera JSON. Slutligen kan skapa du en mall från resultatet för senare användning i skript, program och distributionsverktyg.

## <a name="create-a-dashboard"></a>Skapa en instrumentpanel

Om du vill skapa en ny instrumentpanel kommandot ny instrumentpanel portalens huvudfönstret.

![den nya instrumentpanelen kommandot](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Du kan sedan använda galleriet sida vid sida för att söka efter och Lägg till paneler. Paneler läggs genom att dra och släppa dem. Vissa paneler stöder storleksändring via en dra-referens, medan andra stöd åtgärdas storlekar som visas i deras snabbmenyn.

### <a name="drag-handle"></a>Dra handtaget
![Dra handtaget](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Fast storlek via snabbmenyn
![snabbmenyn för storlekar](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Dela instrumentpanelen

När du har konfigurerat instrumentpanelen enligt dina önskemål nästa steg är att publicera instrumentpanelen (med kommandot Share) och sedan använda resursläsaren för att hämta JSON.

![Dela kommando](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Klicka på kommandot Share visar en dialogruta där du uppmanas att välja vilken prenumeration och resurs grupp att publicera till. Tänk på att [du måste ha skrivbehörighet](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) i gruppen prenumeration och resurs som du väljer.

![dela och åtkomst](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Hämta JSON-representation av instrumentpanelen

Publicering tar bara några sekunder.  När du är klar, är nästa steg att gå till den [Resursläsaren](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) att hämta JSON.

![Bläddra i resursläsaren](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

Från resursutforskaren, navigerar du till gruppen prenumeration och resurs som du har valt. Klicka sedan på den nyligen publicerade instrumentpanelen resursen avslöjar JSON.

![Resource explorer json](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Skapa en mall från JSON

Nästa steg är att skapa en mall från den här JSON så att den kan återanvändas programmässigt med lämpliga resource Manager API: er, kommandoradsverktyg, eller i portalen.

Det är inte nödvändigt att förstå JSON-strukturen instrumentpanelen för att skapa en mall. I de flesta fall som du vill bevara struktur och konfiguration för varje bricka och parameterstyra uppsättning Azure resurser som de är du pekar på. Titta på den exportera JSON-instrumentpanelen och hitta alla förekomster av Azure resurs-ID. Våra exempel instrumentpanelen har flera paneler som alla pekar på en enda virtuell Azure-dator. Det beror på att våra instrumentpanel ser endast på den här enskild resurs. Om du söker exempel json (ingår i slutet av dokumentet) för ”/ prenumerationer”, du hittar flera förekomster av detta Id.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Om du vill publicera den här instrumentpanelen för alla virtuella datorer måste i framtiden du parameterstyra varje förekomst av den här strängen i JSON. 

Det finns två varianter av API: er som skapar resurser i Azure. [Tvingande API: er](https://docs.microsoft.com/rest/api/resources/resources) som skapar en resurs i taget, och en [mallbaserade distribution](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) system som kan samordna skapandet av flera beroende resurser med ett enda API-anrop. Denna stöd inbyggt för parameterization och templating så att vi använda den i vårt exempel.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Skapa en instrumentpanel programmässigt i mallen med hjälp av en för malldistribution

Azure ger dig möjlighet att samordna distribution av flera resurser. Du skapar en Distributionsmall för som representerar en uppsättning resurser för att distribuera samt relationer mellan dem.  JSON-format för alla resurser är samma som om du skapar dem en i taget. Skillnaden är att den [språk för mallen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) lägger till några begrepp som variabler, parametrar, grundläggande funktioner och mycket mer. Den här utökade syntaxen stöds bara i kontexten för en för malldistribution och fungerar inte om du använder med den tvingande API: er som nämnts tidigare.

Om du kommer den här vägen sedan parameterization ska göras med hjälp av parametern mallsyntaxen.  Du kan ersätta alla instanser av resurs-id påträffades tidigare som visas här.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Exempel JSON-egenskapen med hårdkodade resurs-Id
`id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Exempel JSON egenskapen konverteras till en parametriserade version utifrån mallparametrar

`id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"`

Du måste också deklarera vissa nödvändiga mallen metadata och parametrar överst i json-mall så här:

```json

{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

__Du kan se mallen full, arbeta i slutet av det här dokumentet.__

När du har särskild mallen du distribuerar den med hjälp av den [REST API: er](https://docs.microsoft.com/rest/api/resources/deployments), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), [Azure CLI](https://docs.microsoft.com/cli/azure/group/deployment#az_group_deployment_create), eller [portalens mallsidan för distribution ](https://portal.azure.com/#create/Microsoft.Template).

Här är två versioner av våra exempel instrumentpanelen JSON. Först är den version som vi har exporterats från portalen som redan är bunden till en resurs. Andra är den mallversion som programmässigt kan bindas till någon virtuell dator och distribueras via Azure Resource Manager.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>JSON-representation av våra exempel instrumentpanelen (före templating)

Det här är vad du kan förvänta dig att se om du följer du anvisningarna tidigare att hämta JSON-representation av en instrumentpanel som redan har distribuerats. Observera hårdkodade resursidentifierare som visar att den här instrumentpanelen pekar på en specifik virtuell Azure-dator.

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

### <a name="template-representation-of-our-example-dashboard"></a>Mall-representation av våra exempel instrumentpanelen

Mall-versionen av instrumentpanelen har definierat tre parametrar __virtualMachineName__, __virtualMachineResourceGroup__, och __dashboardName__.  Parametrarna kan du peka den här instrumentpanelen på en annan Azure virtuell dator varje gång du distribuerar. De parametrar ID markeras för att visa att den här instrumentpanelen kan via programmering kan konfigureras och distribueras för att peka på alla virtuella Azure-datorn. Det enklaste sättet att testa den här funktionen är att kopiera följande mall och klistrar in det i den [distribution av Azure portal mallsidan](https://portal.azure.com/#create/Microsoft.Template). 

Det här exemplet används en instrumentpanel med sig själv, men språk för mallen kan du distribuera flera resurser och paketera en eller flera instrumentpaneler sida dem. 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
