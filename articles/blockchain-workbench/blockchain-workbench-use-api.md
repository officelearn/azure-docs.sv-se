---
title: Med hjälp av Azure Blockchain arbetsstationen REST API
description: Scenarier för hur du använder Azure Blockchain arbetsstationen REST API
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/2/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 27ed94b3ce14c57e369b0c80d4c53b72a5ae40a8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="using-the-azure-blockchain-workbench-rest-api"></a>Med hjälp av Azure Blockchain arbetsstationen REST API 

Azure Blockchain arbetsstationen REST API ger utvecklare och IT-anställda ett sätt att bygga rika integreringar avseende blockchain program. Det här dokumentet vägleder dig genom flera viktiga metoder för REST-API-arbetsstationen. Föreställ dig ett scenario där en utvecklare som vill skapa en anpassad blockchain-klient, vilket gör att inloggade användare att visa och interagera med sina tilldelade blockchain program. Klienten kan användare visa kontrakt instanser och vidtar åtgärder på smart kontrakt. Klienten använder arbetsstationen REST API i kontexten för den inloggade användaren för att göra följande:

* Lista över program
* Lista över arbetsflöden för ett program
* Visa smart kontraktet instanser för ett arbetsflöde
* Lista över tillgängliga åtgärder för ett kontrakt
* Utföra en åtgärd för ett kontrakt

## <a name="list-applications"></a>Lista över program

När en användare har loggat in blockchain klienten, är den första uppgiften att hämta alla Blockchain arbetsstationen blockchain program för användaren. Användaren har åtkomst till två program i det här scenariot:

1.  Överföringen tillgången
2.  Kylda transport

Använd den [program GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications 
Authorization : Bearer {access token}
```

Svaret visar alla blockchain program som en användare har åtkomst till i Blockchain arbetsstationen. Blockchain arbetsstationen administratörer få alla blockchain program, medan icke arbetsstationen administratörer få alla blockchains som de har minst en associerade program eller en associerad smart kontrakt-instans.

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

## <a name="list-workflows-for-an-application"></a>Lista över arbetsflöden för ett program

När en användare väljer tillämpliga blockchain programmet, i det här fallet tillgångsinformation överföra hämtar blockchain klienten alla arbetsflöden för specifika blockchain programmet. Användare kan sedan välja tillämpliga arbetsflödet innan visas alla instanser för smart kontraktet för arbetsflödet. Varje blockchain program har ett eller flera arbetsflöden och varje arbetsflöde har noll eller kontraktet instanser för smartkort. När du skapar blockchain klientprogram, rekommenderas det att hoppa över att tillåta användare att välja lämplig arbetsflödet när det är endast ett arbetsflöde för programmets blockchain användaren upplevelse flödet. I det här fallet har tillgångsinformation överföra bara ett arbetsflöde, kallas även tillgångsinformation överföra.

Använd den [program arbetsflöden GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

Svaret visar alla arbetsflöden för det angivna blockchain program som en användare har åtkomst till i Blockchain arbetsstationen. Blockchain arbetsstationen administratörer få alla blockchain arbetsflöden, medan icke arbetsstationen administratörer få alla arbetsflöden som de har minst en roll för associerade program och är kopplad till en roll för smart kontrakt-instans.

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

## <a name="list-smart-contract-instances-for-a-workflow"></a>Visa smart kontraktet instanser för ett arbetsflöde

När en användare väljer tillämpliga arbetsflödet, det här fallet tillgångsinformation överföra, hämtar blockchain klienten alla smarta kontraktet instanser för angivna arbetsflödet. Du kan använda den här informationen för att visa alla instanser för smart kontraktet för arbetsflödet och Tillåt användare att ingående till någon av instanserna visas smart kontraktet. Överväg att en användare vill interagera med någon av instanserna smart kontrakt att vidta åtgärder i det här exemplet.

Använd den [kontrakt GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

Svaret visar alla smarta kontraktet instanser av angivet arbetsflöde. Arbetsstationen administratörer få alla instanser av smart kontrakt, medan icke arbetsstationen administratörer få alla kontrakt instanser som de har minst en roll för associerade program för smartkort eller är kopplad till en roll för smart kontrakt-instans.

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

## <a name="list-available-actions-for-a-contract"></a>Lista över tillgängliga åtgärder för ett kontrakt

När en användare har valt att ingående till ett kontrakt, visas blockchain klienten sedan alla tillgängliga åtgärder för användaren får tillståndet för kontraktet. I det här exemplet är användaren ute på alla tillgängliga åtgärder för ett nytt smart kontrakt som de skapats:

* Ändra: Används att ändra den beskrivning och pris för en tillgång.
* Avsluta: Används att avsluta kontraktet för tillgången.

Använd den [minimera åtgärd GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractactionget):

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

Svaret visar alla åtgärder som en användare kan ta anges det aktuella tillståndet för den angivna smart kontrakt-instansen. Användarna får alla tillämpliga åtgärder om användaren har en associerad programrollen eller är associerad med en smart kontraktet instans roll för det aktuella tillståndet för den angivna smart kontrakt-instansen.

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

En användare kan välja att vidta åtgärder för den angivna smart kontrakt-instansen. I det här fallet Överväg ett scenario där en användare vill ändra den beskrivning och pris för en tillgång till följande:

* Beskrivning: ”Min uppdaterade bil”
* Pris: 54321

Använd den [minimera åtgärd efter API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractactionpost):

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

Användare kan bara utföra åtgärden med dagens i angivna smart kontrakt-instans och associerade program användarrollen eller smart kontraktet instans roll. Om efter lyckas, returneras ett HTTP 200 OK svar med ingen svarstext.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Nästa steg

* [Azure Blockchain arbetsstationen REST API-referens](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)