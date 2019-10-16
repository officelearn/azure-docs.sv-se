---
title: Använda REST API för Azure Blockchain Workbench
description: Scenarier för hur du använder för hands versionen av Azure blockchain Workbench REST API
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 2f751cc4f0037b8d60034636a0c512989113e441
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72328863"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Använda Azure blockchain Workbench Preview REST API

Azure blockchain Workbench Preview REST API ger utvecklare och informations anställda ett sätt att bygga omfattande integreringar för blockchain-program. Det här dokumentet leder dig genom flera viktiga metoder för REST API:erna för Workbench. Anta till exempel att en utvecklare vill skapa en anpassad blockchain-klient. Den här blockchain-klienten tillåter att inloggade användare kan visa och interagera med sina tilldelade blockchain-program. Klienten kan låta användare visa kontraktsinstanser och vidta åtgärder på smarta kontrakt. -Klienten använder Workbench-REST API i kontexten för den inloggade användaren för att utföra följande åtgärder:

* Lista program
* Lista arbetsflöden för ett program
* Lista smarta kontrakt-instanser för ett arbetsflöde
* Lista tillgängliga åtgärder för ett kontrakt
* Utföra en åtgärd för ett kontrakt

Blockchain-programmen som används i scenarierna kan [laddas ned från GitHub](https://github.com/Azure-Samples/blockchain).

## <a name="list-applications"></a>Lista program

När en användare har loggat in på blockchain-klienten, är den första uppgiften att hämta alla blockchain Workbench-program för användaren. Användaren i det här scenariot har åtkomst till två program:

1. [Till gångs överföring](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
2. [Kyld transport](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

Använd [GET API för programmet](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

Svaret visar en lista över alla blockchain-program som en användare har åtkomst till i blockchain Workbench. Blockchain Workbench-administratörer får alla blockchain-program. Icke-Workbench-administratörer får alla Blockchains för vilka de har minst en associerad program roll eller en tillhör ande roll för smart kontrakt instans.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Lista arbetsflöden för ett program

När en användare väljer tillämpligt blockchain-program (t. ex. **till gångs överföring**) hämtar blockchain-klienten alla arbets flöden för det specifika blockchain-programmet. Användare kan sedan välja tillämpliga arbetsflöden innan alla instanser för smarta kontrakt för arbetsflödet visas. Alla blockkedjeprogram har ett eller flera arbetsflöden och varje arbetsflöde har noll eller fler instanser för smarta kontrakt. För ett blockchain-klientprogram som bara har ett arbets flöde rekommenderar vi att du hoppar över användar upplevelsen som gör att användarna kan välja lämpligt arbets flöde. I det här fallet har **till gångs överföring** endast ett arbets flöde, även kallat **till gångs överföring**.

Använd [GET API för programarbetsflöden](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

Svaret listar alla arbetsflöden för ett specifikt blockkedjeprogram som en användare har åtkomst till i Blockchain Workbench. Blockchain Workbench-administratörer får alla blockchain-arbetsflöden. Icke-Workbench-administratörer får alla arbets flöden för vilka de har minst en associerad program roll eller associerats med en roll för en smart kontrakt instans.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Lista smarta kontrakt-instanser för ett arbetsflöde

När en användare väljer tillämpligt arbets flöde, kommer den här Skift läges **överföringen**, blockchain-klienten hämta alla smarta kontrakt instanser för det angivna arbets flödet. Du kan använda den här informationen för att visa alla smarta kontrakt instanser för arbets flödet. Eller så kan du ge användarna en djup inblick i alla visade smarta kontrakt instanser. Ta till exempel i det här fallet en användare som vill interagera med någon av instanserna för det smarta kontrakt som ska utföras.

Använd [Contracts GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) (GET API för kontrakt):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

Svaret visar alla smarta kontraktet-instanser för det angivna arbetsflödet. Workbench-administratörer får alla smarta kontrakt instanser. Icke-Workbench-administratörer får varje smart kontrakts instans för vilken de har minst en associerad program roll eller associeras med en roll för en smart kontrakt instans.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Lista tillgängliga åtgärder för ett kontrakt

När en användare bestämmer sig för att ta sig in i ett kontrakt kan blockchain-klienten Visa de tillgängliga användar åtgärderna enligt kontraktets status. I det här exemplet tittar användaren på alla tillgängliga åtgärder för ett nytt smart kontrakt som de skapat:

* Ändra: Låter användaren ändra beskrivning och pris för en tillgång.
* Avsluta: tillåter att användaren avslutar kontraktet för till gången.

Använd [POST API för kontraktsåtgärden](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget):

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

Svaret visar alla åtgärder som en användare kan utföra utifrån det aktuella tillståndet för den angivna instansen för det smarta kontraktet. Användarna får alla tillämpliga åtgärder om användaren har en associerad programroll eller är associerad med en smart kontrakt-instansroll för det aktuella tillståndet för den angivna smarta kontrakt-instansen.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Utföra en åtgärd för ett kontrakt

En användare kan sedan välja att vidta åtgärder för den angivna smarta kontrakt-instansen. I det här fallet bör du tänka på scenariot där en användare vill ändra beskrivningen och priset för en till gång till följande åtgärd:

* Beskrivning: "Min uppdaterade bil"
* Pris: 54321

Använd [Contract Action POST API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) (Kontraktsåtgärd för POST API):

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

Användare kan bara utföra åtgärden utifrån det aktuella tillståndet för de specificerade smarta instanserna för det smarta kontraktet och användarens associerade programroll eller rollen för instansen för det smarta kontraktet. Om posten lyckas, returneras ett HTTP 200 OK-svar utan svarstext.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens för Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)