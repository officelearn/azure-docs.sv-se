---
title: Använda AZURE Blockchain Workbench REST API:er
description: Scenarier för hur du använder REST-APIN för förhandsversionen av Azure Blockchain Workbench
ms.date: 03/05/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 3084fcf343bc42fe01bf352b6791916d62f63540
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672737"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Använda REST-APIN för förhandsversionen av Azure Blockchain Workbench

Azure Blockchain Workbench Preview REST API ger utvecklare och informationsarbetare ett sätt att skapa omfattande integrationer till blockchain-program. I den här artikeln beskrivs flera scenarier med hur du använder REST-API:et för Workbench. Anta till exempel att du vill skapa en anpassad blockchain-klient som gör att inloggade användare kan visa och interagera med sina tilldelade blockchain-program. Klienten kan använda Blockchain Workbench-API:et för att visa kontraktsinstanser och vidta åtgärder för smarta kontrakt.

## <a name="blockchain-workbench-api-endpoint"></a>Slutpunkt för Blockchain Workbench API

Blockchain Workbench API:er nås via en slutpunkt för distributionen. Så här hämtar du API-slutpunkts-URL:en för distributionen:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Resursgrupper**i det vänstra navigeringsfönstret .
1. Välj det resursgruppnamn som din distribuerade Blockchain Workbench.
1. Markera kolumnrubriken **TYP** om du vill sortera listan i alfabetisk ordning efter typ.
1. Det finns två resurser med typen **App Service**. Välj resurs av typen **App Service** *med* suffixet "-api".
1. Kopiera **URL-värdet** i **apptjänstöversikten,** som representerar API-slutpunkts-URL:en till din distribuerade Blockchain Workbench.

    ![URL till slutpunkt för App-tjänst-API](media/use-api/app-service-api.png)

## <a name="authentication"></a>Autentisering

Begäranden till REST-API:et för Blockchain Workbench skyddas med Azure Active Directory (Azure AD).

För att göra en autentiserat begäran till REST-API:erna kräver klientkoden autentisering med giltiga autentiseringsuppgifter innan du kan anropa API:et. Autentisering samordnas mellan de olika aktörerna av Azure AD och ger din klient en [åtkomsttoken](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#access-token) som bevis på autentiseringen. Token skickas sedan i HTTP-auktoriseringshuvudet för REST API-begäranden. Mer information om Azure AD-autentisering finns i [Azure Active Directory för utvecklare](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

Se [REST API-exempel](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples) för exempel på hur du autentiserar.

## <a name="using-postman"></a>Använda Postman

Om du vill testa eller experimentera med Api:er för Arbetsbänk kan du använda [Postman](https://www.postman.com) för att ringa API-anrop till distributionen. [Hämta en exempelsamling för Postman med Api-begäranden för Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman) från GitHub. Mer information om hur du ktticiserar och använder exempel-API-begäranden finns i README-filen.

## <a name="create-an-application"></a>Skapa ett program

Du använder två API-anrop för att skapa ett Blockchain Workbench-program. Den här metoden kan bara utföras av användare som är Workbench-administratörer.

Använd [programmet POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationspost) för att ladda upp programmets JSON-fil och få ett program-ID.

### <a name="applications-post-request"></a>Begäran om ansökan POST

Använd **parametern appFile** för att skicka konfigurationsfilen som en del av begärandetexten.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>Svar på programpost

Det skapade program-ID:t returneras i svaret. Du behöver program-ID för att associera konfigurationsfilen med kodfilen när du anropar nästa API.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>Begäran om post för kontraktskod

Använd [post-API:et för programkontraktskod](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/contractcodepost) genom att skicka program-ID:et för att överföra programmets soliditetskodfil. Nyttolasten kan vara en enda Solidity-fil eller en zippad fil som innehåller Soliditetsfiler.

Ersätt följande värden:

| Parameter | Värde |
|-----------|-------|
| {applicationId} | Returnera värde från programmen POST API. |
| {ledgerId} | Indexet för redovisningen. Värdet är vanligtvis 1. Du kan också kontrollera [redovisningstabellen](data-sql-management-studio.md) för värdet. |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>POST-svar för kontraktskod

Om det lyckas innehåller svaret det skapade kontraktskod-ID:et från [tabellen ContractCode](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>Tilldela roller till användare

Använd [rolltilldelningarna för programPOST GENOM](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) att skicka program-ID, användar-ID och programroll-ID för att skapa en mappning mellan användare och roll i det angivna blockchain-programmet. Den här metoden kan bara utföras av användare som är Workbench-administratörer.

### <a name="role-assignments-post-request"></a>Rolltilldelningar POST-begäran

Ersätt följande värden:

| Parameter | Värde |
|-----------|-------|
| {applicationId} | Returnera värde från PROGRAMMET POST API. |
| {userId} | Användar-ID-värde från [tabellen Användare](data-sql-management-studio.md). |
| {applicationRoleId} | Programroll-ID-värde som är associerat till program-ID från [tabellen ApplicationRole](data-sql-management-studio.md). |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>Rolltilldelningar POST-svar

Om det lyckas innehåller svaret det skapade rolltilldelnings-ID:et från [rollatilldelningstabellen](data-sql-management-studio.md).

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>Lista program

Använd [programmet GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget) för att hämta alla Blockchain Workbench-program för användaren. I det här exemplet har den inloggade användaren åtkomst till två program:

- [Överföring av tillgångar](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [Kyld transport](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>Ansökningar GET-begäran

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>Program GET svar

Svaret listar alla blockchain-program som en användare har åtkomst till i Blockchain Workbench. Blockchain Workbench-administratörer får alla blockchain-program. Icke-Workbench-administratörer får alla blockchain-program för vilka de har minst en associerad programroll eller en associerad smart kontraktsinstansroll.

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

Använd [programarbetsflöden FÅ API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget) för att lista alla arbetsflöden för ett angivet blockchain-program som en användare har åtkomst till i Blockchain Workbench. Alla blockkedjeprogram har ett eller flera arbetsflöden och varje arbetsflöde har noll eller fler instanser för smarta kontrakt. För ett blockchain-klientprogram som bara har ett arbetsflöde rekommenderar vi att du hoppar över användarupplevelseflödet som gör att användare kan välja lämpligt arbetsflöde.

### <a name="application-workflows-request"></a>Begäran om programarbetsflöden

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>Svar på programarbetsflöden

Blockchain Workbench-administratörer får varje blockchain-arbetsflöde. Icke-Workbench-administratörer får alla arbetsflöden för vilka de har minst en associerad programroll eller är associerade med en smart kontraktsinstansroll.

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

## <a name="create-a-contract-instance"></a>Skapa en kontraktsinstans

Använd [Contracts V2 POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contractsv2/contractpost) för att skapa en ny smart kontraktsinstans för ett arbetsflöde. Användare kan bara skapa en ny smart kontraktsinstans om användaren är associerad med en programroll, som kan initiera en smart kontraktsinstans för arbetsflödet.

> [!NOTE]
> I det här exemplet används version 2 av API:et. Api:er för kontrakt till version 2 ger mer detaljerad information för de associerade etableringsstatusfälten.

### <a name="contracts-post-request"></a>Begäran om post i avtal

Ersätt följande värden:

| Parameter | Värde |
|-----------|-------|
| {workflowId} | Arbetsflödes-ID-värde är kontraktets KonstruktorID från [tabellen Arbetsflöde](data-sql-management-studio.md). |
| {contractCodeId} | Kontraktskod-ID-värde från [tabellen ContractCode](data-sql-management-studio.md). Korrelera program-ID och redovisnings-ID för den kontraktsinstans som du vill skapa. |
| {connectionId} | Anslutnings-ID-värde från [tabellen Anslutning](data-sql-management-studio.md). |

Ange värden med hjälp av följande information för begäran:

| Parameter | Värde |
|-----------|-------|
| arbetsflödeFunktionID | ID från [tabellen Arbetsflödesfunktion](data-sql-management-studio.md). |
| arbetsflödeÅtgärderParametrar | Namnvärdespar av parametrar som skickas till konstruktorn. För varje parameter använder du arbetsflödetFunctionParameterID-värdet från tabellen [Arbetsflödesfunktionsparameter.](data-sql-management-studio.md) |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>Svar på kontrakt POST

Om det lyckas returnerar api:et för rolltilldelningar ContractActionID från [tabellen ContractActionParameter](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Lista smarta kontrakt-instanser för ett arbetsflöde

Använd [Contracts GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) för att visa alla smarta kontraktsinstanser för ett arbetsflöde. Eller så kan du tillåta användare att djupdykning i någon av de visade smarta kontrakt instanser.

### <a name="contracts-request"></a>Begäran om kontrakt

Ta till exempel i det här fallet en användare som vill interagera med någon av instanserna för det smarta kontrakt som ska utföras.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>Svar på kontrakt

Svaret visar alla smarta kontraktsinstanser i det angivna arbetsflödet. Workbench-administratörer får alla smarta kontraktsinstanser. Icke-Workbench-administratörer får varje smart kontraktsinstans för vilken de har minst en associerad programroll eller associeras med en smart kontraktsinstansroll.

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

Använd [API för kontraktsåtgärd GET](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) för att visa tillgängliga användaråtgärder med tanke på kontraktets tillstånd. 

### <a name="contract-action-request"></a>Begäran om kontraktsåtgärd

I det här exemplet tittar användaren på alla tillgängliga åtgärder för ett nytt smart kontrakt som de har skapat.

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>Svar på kontraktsåtgärder

Svaret visar alla åtgärder som en användare kan utföra utifrån det aktuella tillståndet för den angivna instansen för det smarta kontraktet.

* Ändra: Låter användaren ändra beskrivning och pris för en tillgång.
* Avsluta: Tillåter användaren att avsluta kontraktet för tillgången.

Användarna får alla tillämpliga åtgärder om användaren har en associerad programroll eller är associerad med en smart kontrakt-instansroll för det aktuella tillståndet för den angivna smarta kontrakt-instansen.

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

Använd [API:et för kontraktsåtgärd POST](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) för att vidta åtgärder för den angivna smarta kontraktsinstansen.

### <a name="contract-action-post-request"></a>Begäran om kontraktsåtgärd POST

I det här fallet bör du tänka på det scenario där en användare vill ändra beskrivningen och priset för en tillgång.

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

Användare kan bara utföra åtgärden utifrån det aktuella tillståndet för de specificerade smarta instanserna för det smarta kontraktet och användarens associerade programroll eller rollen för instansen för det smarta kontraktet.

### <a name="contract-action-post-response"></a>Post-svar för kontraktsåtgärd

Om posten lyckas, returneras ett HTTP 200 OK-svar utan svarstext.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Nästa steg

Referensinformation om Blockchain Workbench-API:er finns i [AZURE Blockchain Workbench REST API-referensen](https://docs.microsoft.com/rest/api/azure-blockchain-workbench).
