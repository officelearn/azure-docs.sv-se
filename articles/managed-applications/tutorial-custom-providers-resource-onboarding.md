---
title: Resurs onboarding med Azure-anpassade leverantörer
description: Med resurs onboarding via anpassade providers kan du ändra och utöka befintliga Azure-resurser.
services: managed-applications
ms.service: managed-applications
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: c722b4dc3219f76f8c7c571af3613996fec9e69c
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608682"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Självstudie: resurs onboarding med Azure-anpassade leverantörer

Den här självstudien distribuerar en anpassad resurs leverantör till Azure som utökar Azure Resource Manager-API: t med resurs typen "Microsoft. CustomProviders/associationer". Det här exemplet visar hur du utökar befintliga resurser som ligger utanför resurs gruppen där den anpassade Provider-instansen finns. I det här exemplet drivs den anpassade resurs leverantören av en Azure Logic-app, men alla offentliga API-slutpunkter kan användas.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien måste du känna till följande:

* Funktioner i [Azure anpassad Provider](custom-providers-overview.md) .
* [Resurs onboarding med anpassade providers](concepts-custom-providers-resourceonboarding.md).

## <a name="getting-started-with-resource-onboarding"></a>Komma igång med resurs onboarding

För det här exemplet finns det två delar som måste distribueras: den anpassade providern och associationen. För att förenkla exemplet finns det en enda mall som kan användas för att distribuera båda.

Mallen kommer att använda följande resurser:

* Microsoft. CustomProviders/resourceProviders
* Microsoft. Logic/arbets flöden
* Microsoft. CustomProviders/associationer

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "allowedValues": [
                "australiaeast",
                "eastus",
                "westeurope"
            ],
            "metadata": {
                "description": "Location for the resources."
            }
        },
        "logicAppName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the logic app to be created."
            }
        },
        "customResourceProviderName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the custom provider to be created."
            }
        },
        "customResourceProviderId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of an existing custom provider. Provide this to skip deployment of new logic app and custom provider."
            }
        },
        "associationName": {
            "type": "string",
            "defaultValue": "myAssociationResource",
            "metadata": {
                "description": "Name of the custom resource that is being created."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "condition": "[empty(parameters('customResourceProviderId'))]",
            "name": "customProviderInfrastructureTemplate",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "logicAppName": {
                            "type": "string",
                            "defaultValue": "[parameters('logicAppName')]"
                        }
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Logic/workflows",
                            "apiVersion": "2017-07-01",
                            "name": "[parameters('logicAppName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "state": "Enabled",
                                "definition": {
                                    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
                                    "actions": {
                                        "Switch": {
                                            "cases": {
                                                "Case": {
                                                    "actions": {
                                                        "CreateCustomResource": {
                                                            "inputs": {
                                                                "body": {
                                                                    "properties": "@addProperty(triggerBody().Body['properties'], 'myDynamicProperty', 'myDynamicValue')"
                                                                },
                                                                "statusCode": 200
                                                            },
                                                            "kind": "Http",
                                                            "type": "Response"
                                                        }
                                                    },
                                                    "case": "CREATE"
                                                }
                                            },
                                            "default": {
                                                "actions": {
                                                    "DefaultHttpResponse": {
                                                        "inputs": {
                                                            "statusCode": 200
                                                        },
                                                        "kind": "Http",
                                                        "type": "Response"
                                                    }
                                                }
                                            },
                                            "expression": "@triggerBody().operationType",
                                            "type": "Switch"
                                        }
                                    },
                                    "contentVersion": "1.0.0.0",
                                    "outputs": {},
                                    "parameters": {},
                                    "triggers": {
                                        "CustomProviderWebhook": {
                                            "inputs": {
                                                "schema": {}
                                            },
                                            "kind": "Http",
                                            "type": "Request"
                                        }
                                    }
                                }
                            }
                        },
                        {
                            "type": "Microsoft.CustomProviders/resourceProviders",
                            "apiVersion": "2018-09-01-preview",
                            "name": "[parameters('customResourceProviderName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "resourceTypes": [
                                    {
                                        "name": "associations",
                                        "mode": "Secure",
                                        "routingType": "Webhook,Cache,Extension",
                                        "endpoint": "[[listCallbackURL(concat(resourceId('Microsoft.Logic/workflows', parameters('logicAppName')), '/triggers/CustomProviderWebhook'), '2017-07-01').value]"
                                    }
                                ]
                            }
                        }
                    ],
                    "outputs": {
                        "customProviderResourceId": {
                            "type": "string",
                            "value": "[resourceId('Microsoft.CustomProviders/resourceProviders', parameters('customResourceProviderName'))]"
                        }
                    }
                }
            }
        },
        {
            "type": "Microsoft.CustomProviders/associations",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('associationName')]",
            "location": "global",
            "properties": {
                "targetResourceId": "[if(empty(parameters('customResourceProviderId')), reference('customProviderInfrastructureTemplate').outputs.customProviderResourceId.value, parameters('customResourceProviderId'))]",
                "myCustomInputProperty": "myCustomInputValue",
                "myCustomInputObject": {
                    "Property1": "Value1"
                }
            }
        }
    ],
    "outputs": {
        "associationResource": {
            "type": "object",
            "value": "[reference(parameters('associationName'), '2018-09-01-preview', 'Full')]"
        }
    }
}
```

### <a name="deploy-the-custom-provider-infrastructure"></a>Distribuera infrastrukturen för anpassad Provider

Den första delen av mallen distribuerar den anpassade providerns infrastruktur. Den här infrastrukturen definierar effekterna av resursen "associationer". Om du inte är bekant med anpassade providers, se [grunderna för anpassade providers](custom-providers-overview.md).

Nu ska vi distribuera den anpassade providern för infrastruktur, antingen kopiera, Spara och distribuera ovanstående mall eller följa tillsammans och distribuera via Azure Portal.

1. Öppna Azure Portal från https://portal.azure.com.

2. Sök i "mallar" i `All Services` eller i det mittersta Sök fältet. 

![Sök efter mallar](media/custom-providers-resource-onboarding/templates.png)

3. Tryck på knappen `+ Add` längst upp till vänster på bladet "mallar".

![Mall Lägg till ny](media/custom-providers-resource-onboarding/templatesadd.png)

4. Fyll i fälten "namn" och "Beskrivning" under "allmänt" för den nya mallen.

![Mallens namn och beskrivning](media/custom-providers-resource-onboarding/templatesdescription.png)

5. Fyll i Resource Manager-mallen genom att kopiera JSON-mallen från "komma igång med resurs onboarding"

![Fyll i Resource Manager-mall](media/custom-providers-resource-onboarding/templatesarmtemplate.png)

6. Tryck på `Add`-knappen för att skapa en ny mall. Om den nya mallen inte visas trycker du på `Refresh`.

7. Välj den nya mallen och tryck på `Deploy` knappen.

![Välj den nyligen skapade mallen och distribuera](media/custom-providers-resource-onboarding/templateselectspecific.png)

8. Ange inställnings parametrar för de obligatoriska fälten och Välj prenumerationen och resurs gruppen. "ID för anpassad resurs leverantör" kan lämnas tomt.

| Inställningsnamn | Krävs | Beskrivning |
| ------------ | -------- | ----------- |
| Plats | *Ja* | Platsen för resurserna i mallen. |
| Namn på Logic app | *Nej* | Namnet på den logiska appen. |
| Namn på anpassad resurs leverantör | *Nej* | Namnet på den anpassade resurs leverantören. |
| ID för anpassad resurs leverantör | *Nej* | En befintlig anpassad resurs leverantör som stöder resursen "Association". Om du anger detta hoppas logik appen och den anpassade providern att distribueras. |
| Kopplings namn | *Nej* | Namnet på Associations resursen. |

Exempel parametrar:

![Parametrar för inmatad mall](media/custom-providers-resource-onboarding/templatescustomprovider.png)

9. Navigera till distributionen och vänta tills den är klar. Den ska visas som slutförd och den nya resursen "Association" visas.

Distributionen lyckades:

![Distribution av anpassad Provider](media/custom-providers-resource-onboarding/customproviderdeployment.png)

Resurs grupp med "Visa dolda typer":

![Distribution av anpassad Provider](media/custom-providers-resource-onboarding/showhidden.png)

10. Utforska fliken för Logic app "kör historik" för att se anropen för "Association"-skapandet.

![Körnings historik för Logic app](media/custom-providers-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Distribuera ytterligare associationer

När vi har konfigurerat infrastrukturen för anpassad Provider kan vi enkelt distribuera ytterligare "associationer". Resurs gruppen för ytterligare "associationer" behöver inte vara samma som den resurs grupp som infrastrukturen för anpassad provider har distribuerats till. För att kunna skapa en Association krävs behörigheterna "Microsoft. CustomProviders/resourceproviders/Write" på det angivna "ID för anpassad Provider".

1. Navigera till den anpassade providern "Microsoft. CustomProviders/resourceProviders" i resurs gruppen för den tidigare distributionen. Kryss rutan Visa dolda typer måste markeras.

![Distribution av anpassad Provider](media/custom-providers-resource-onboarding/showhidden.png)

2. Kopiera egenskapen "Resource ID" för den anpassade providern.

3. Sök i "mallar" i `All Services` eller i det mittersta Sök fältet. 

![Sök efter mallar](media/custom-providers-resource-onboarding/templates.png)

4. Välj den mall som skapats tidigare och tryck på `Deploy` knappen.

![Välj den mall som skapats tidigare och distribuera](media/custom-providers-resource-onboarding/templateselectspecific.png)

5. Ange inställnings parametrar för de obligatoriska fälten och Välj prenumerationen och en annan resurs grupp. För inställningen "anpassad resurs leverantörs-ID" anger du det kopierade resurs-ID: t för den tidigare distribuerade anpassade providern.

6. Navigera till distributionen och vänta tills den är klar. Den bör nu bara distribuera den nya resursen "associationer".

![Skapad Association](media/custom-providers-resource-onboarding/createdassociationresource.png)

Alternativt kan du gå tillbaka till Logic-appen "körnings historik" och se att ett annat anrop gjordes till Logic-appen. Logic-appen kan uppdateras för att utöka ytterligare funktionalitet för varje skapad Association.

## <a name="looking-for-help"></a>Söker efter hjälp

Om du har frågor om Azure-anpassade leverantörer kan du prova med att ställa [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-custom-providers). En liknande fråga kanske redan har blivit ombeddd och besvarad, så kontrol lera först innan du publicerar. Lägg till taggen `azure-custom-providers` för att få ett snabbt svar!

