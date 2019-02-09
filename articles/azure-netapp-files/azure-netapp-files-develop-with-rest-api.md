---
title: Utveckla för Azure NetApp-filer med REST API | Microsoft Docs
description: Beskriver hur du kommer igång med hjälp av Azure NetApp filer REST API.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to-article
ms.date: 02/06/2019
ms.author: b-juche
ms.openlocfilehash: 169638fed9a513b8ed835076c049ee21979085fe
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967530"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Utveckla för Azure NetApp-filer med REST API 

REST-API: et för tjänsten Azure NetApp Files definierar HTTP-åtgärder mot resurser, till exempel NetApp-konto, poolen kapacitet, volymer och ögonblicksbilder. Den här artikeln hjälper dig att komma igång med hjälp av Azure NetApp filer REST API.

## <a name="access-the-azure-netapp-files-rest-api"></a>Komma åt Azure NetApp filer REST-API  

1. [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om du inte gjort det redan.
2. Skapa ett huvudnamn för tjänsten i Azure Active Directory (AD Azure):
    1. Kontrollera att du har [behörighet](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

    1. Ange följande kommando i Azure CLI:  

            az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE--password $YOURGENERATEDPASSWORDGOESHERE

    Kommandoutdata liknar följande exempel:  

            { 
                "appId": "appIDgoeshere", 
                "displayName": "APPNAME", 
                "name": "http://APPNAME", 
                "password": "supersecretpassword", 
                "tenant": "tenantIDgoeshere" 
            } 

    Behåll kommandoutdata.  Du behöver den `appId`, `password`, och `tenant` värden. 

3. Begär en OAuth-åtkomsttoken:

    Exemplen i den här artikeln använder vi cURL.  Du kan också använda olika API-verktyg som [Postman](https://www.getpostman.com/), [sömnlöshet](https://insomnia.rest/), och [Paw](https://paw.cloud/).  

    Ersätt variablerna i följande exempel med utdata från kommandot från steg 2 ovan. 

        curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token

    Utdata innehåller en åtkomsttoken som liknar följande exempel:

        eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9

    Token som visas är giltig i 3 600 sekunder. Efter det kan behöva du begära en ny token. 
    Spara token till en textredigerare.  Du behöver den för nästa steg.

4. Skicka ett test-anrop och omfattar token för att verifiera din åtkomst till REST API:

        curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2016-08-01

## <a name="examples-using-the-api"></a>Exempel med hjälp av API  

Den här artikeln använder följande URL för baslinje för begäranden. Den här URL: en pekar till roten i namnområdet Azure NetApp-filer. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15`

Du bör ersätta den `subID` och `resourceGroups` värden i följande exempel med dina egna värden. 

### <a name="get-request-examples"></a>Hämta exempel på begäran

Du kan använda en GET-begäran till frågeobjekt Azure NetApp filer i en prenumeration, enligt följande exempel: 

        #get NetApp accounts 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15

        #get capacity pools for NetApp account 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2017-08-15

        #get volumes in NetApp account & capacity pool 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2017-08-15

        #get snapshots for a volume 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2017-08-15

### <a name="put-request-examples"></a>PLACERA begäran-exempel

Du kan använda en PUT-begäran för att skapa nya objekt i Azure NetApp filer, enligt följande exempel. Brödtexten i PUT-begäran kan ta med JSON-formaterade data för att ändringarna eller den kan du ange en fil att läsa från. 

        #create a NetApp account  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2017-08-15

        #create a capacity pool  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2017-08-15

        #create a volume  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2017-08-15

        #create a volume snapshot  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2017-08-15

### <a name="json-examples"></a>JSON-exempel

I följande exempel visas hur du skapar en NetApp-konto:

    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 

I följande exempel visas hur du skapar en pool kapacitet: 

    {
        "name": "MYNETAPPACCOUNT/POOLNAME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
        "location": "westus2",
        "properties": {
            "name": "POOLNAME"
            "size": "4398046511104",
            "serviceLevel": "Premium"
        }
    }

I följande exempel visas hur du skapar en ny volym: 

    {
        "name": "MYNEWVOLUME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
        "location": "westus2",
        "properties": {
            "serviceLevel": "Premium",
            "usageThreshold": "322122547200",
            "creationToken": "MY-FILEPATH",
            "snapshotId": "",
            "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
            }
    }

I följande exempel visas hur du skapar en ögonblicksbild av en volym: 

    {
        "name": "apitest2/apiPool01/apiVol01/snap02",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
        "location": "westus2",
        "properties": {
            "name": "snap02",
            "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
        }
    }

> [!NOTE] 
> Du måste ange `fileSystemId` för att skapa en ögonblicksbild.  Du kan hämta den `fileSystemId` värde med en GET-begäran till en volym. 

## <a name="next-steps"></a>Nästa steg

[Se Azure NetApp filer REST API-referens](https://docs.microsoft.com/rest/api/netapp/)
