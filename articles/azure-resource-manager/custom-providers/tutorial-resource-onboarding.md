---
title: Självstudiekurs - onboarding av resurser
description: Med onboarding av resurser via anpassade leverantörer kan du manipulera och utöka befintliga Azure-resurser.
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 22d1dcd997a4ddb94aba184c5dace4c00509054d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649941"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Självstudiekurs: Onboarding av resurser med Azure Custom Providers

I den här självstudien distribuerar du till Azure en anpassad resursleverantör som utökar Azure Resource Manager-API:et med resurstypen Microsoft.CustomProviders/associations. Självstudien visar hur du utökar befintliga resurser som ligger utanför resursgruppen där den anpassade providerinstansen finns. I den här självstudien drivs den anpassade resursleverantören av en Azure-logikapp, men du kan använda alla offentliga API-slutpunkter.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien måste du veta:

* Funktionerna för [Azure Custom Providers](overview.md).
* Grundläggande information om [resursanlökar med anpassade leverantörer](concepts-resource-onboarding.md).

## <a name="get-started-with-resource-onboarding"></a>Komma igång med resursboarding

I den här självstudien finns det två bitar som måste distribueras: den anpassade providern och associationen. Om du vill göra processen enklare kan du också använda en enda mall som distribuerar båda.

Mallen använder dessa resurser:

* Microsoft.CustomProviders/resourceProviders
* Microsoft.Logic/arbetsflöden
* Microsoft.CustomProviders/associationer

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

### <a name="deploy-the-custom-provider-infrastructure"></a>Distribuera den anpassade providerinfrastrukturen

Den första delen av mallen distribuerar den anpassade providerinfrastrukturen. Den här infrastrukturen definierar effekten av associationsresursen. Om du inte är bekant med anpassade leverantörer läser du [Grunderna för anpassade leverantörer](overview.md).

Nu ska vi distribuera den anpassade providerinfrastrukturen. Antingen kopiera, spara och distribuera föregående mall eller följa med och distribuera infrastrukturen med hjälp av Azure-portalen.

1. Gå till [Azure-portalen](https://portal.azure.com).

2. Sök efter **mallar** i **Alla tjänster** eller med hjälp av huvudsökrutan:

   ![Sök efter mallar](media/tutorial-resource-onboarding/templates.png)

3. Välj **Lägg till** i fönstret **Mallar:**

   ![Välj Lägg till](media/tutorial-resource-onboarding/templatesadd.png)

4. Under **Allmänt**anger du ett **namn** och **en beskrivning** för den nya mallen:

   ![Mallnamn och beskrivning](media/tutorial-resource-onboarding/templatesdescription.png)

5. Skapa Resource Manager-mallen genom att kopiera i JSON-mallen från avsnittet "Kom igång med resursbelöningen" i den här artikeln:

   ![Skapa en Azure Resource Manager-mall](media/tutorial-resource-onboarding/templatesarmtemplate.png)

6. Välj **Lägg till** om du vill skapa mallen. Om den nya mallen inte visas väljer du **Uppdatera**.

7. Välj den nyskapade mallen och välj sedan **Distribuera:**

   ![Markera den nya mallen och välj sedan Distribuera](media/tutorial-resource-onboarding/templateselectspecific.png)

8. Ange inställningarna för de obligatoriska fälten och välj sedan prenumerations- och resursgruppen. Du kan lämna rutan **Anpassad resursprovider i** tom.

   | Inställningsnamn | Krävs? | Beskrivning |
   | ------------ | -------- | ----------- |
   | Location | Ja | Platsen för resurserna i mallen. |
   | Namn på Logikapp | Inga | Namnet på logikappen. |
   | Namn på anpassad resursprovider | Inga | Den anpassade resursleverantörens namn. |
   | Id för anpassad resursprovider | Inga | En befintlig anpassad resursprovider som stöder associationsresursen. Om du anger ett värde här hoppas logikappen och den anpassade providerdistributionen över. |
   | Associationsnamn | Inga | Namnet på associationsresursen. |

   Exempelparametrar:

   ![Ange mallparametrar](media/tutorial-resource-onboarding/templatescustomprovider.png)

9. Gå till distributionen och vänta tills den är klar. Du bör se något i stil med följande skärmdump. Du bör se den nya associationsresursen som en utdata:

   ![Lyckad distribution](media/tutorial-resource-onboarding/customproviderdeployment.png)

   Här är resursgruppen med **Visa dolda typer** markerade:

   ![Distribuera anpassad provider](media/tutorial-resource-onboarding/showhidden.png)

10. Utforska fliken Logikapp **Kör historik** för att se anropen för associationen skapa:

    ![Logikapp kör historik](media/tutorial-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Distribuera ytterligare associationer

När du har konfigurerat den anpassade providerinfrastrukturen kan du enkelt distribuera fler associationer. Resursgruppen för ytterligare associationer behöver inte vara samma som resursgruppen där du distribuerade den anpassade providerinfrastrukturen. Om du vill skapa en association måste du ha behörigheterna Microsoft.CustomProviders/resourceproviders/write på det angivna custom resource provider-ID:t.

1. Gå till den anpassade providern **Microsoft.CustomProviders/resourceProviders** resurs i resursgruppen för den tidigare distributionen. Du måste markera kryssrutan **Visa dolda typer:**

   ![Gå till resursen](media/tutorial-resource-onboarding/showhidden.png)

2. Kopiera egenskapen Resurs-ID för den anpassade providern.

3. Sök efter **mallar** i **Alla tjänster** eller med hjälp av huvudsökrutan:

   ![Sök efter mallar](media/tutorial-resource-onboarding/templates.png)

4. Markera den tidigare skapade mallen och välj sedan **Distribuera:**

   ![Markera den tidigare skapade mallen och välj sedan Distribuera](media/tutorial-resource-onboarding/templateselectspecific.png)

5. Ange inställningarna för de obligatoriska fälten och välj sedan prenumerationen och en annan resursgrupp. För inställningen **Anpassad resursprovider-ID** anger du det resurs-ID som du kopierade från den anpassade providern som du distribuerade tidigare.

6. Gå till distributionen och vänta tills den är klar. Den bör nu bara distribuera den nya associationsresursen:

   ![Ny associationsresurs](media/tutorial-resource-onboarding/createdassociationresource.png)

Om du vill kan du gå tillbaka till logikappen **Körhistorik** och se att ett annat samtal gjordes till logikappen. Du kan uppdatera logikappen för att utöka ytterligare funktioner för varje skapad association.

## <a name="getting-help"></a>Få hjälp

Om du har frågor om Azure Custom Providers kan du prova att fråga dem på [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). En liknande fråga kan redan ha besvarats, så kontrollera först innan du postar. Lägg till `azure-custom-providers` taggen för att få ett snabbt svar!

