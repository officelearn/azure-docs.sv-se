---
title: Skicka anpassade mått för en Azure-resurs till arkivet som Azure Monitor-mått med hjälp av ett REST-API
description: Skicka anpassade mått för en Azure-resurs till arkivet som Azure Monitor-mått med hjälp av ett REST-API
author: lingliw
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/12/19
ms.author: v-lingwu
ms.subservice: metrics
ms.openlocfilehash: aa842979bf86410e9dab97d6209f336eb6b02bd3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60253884"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Skicka anpassade mått för en Azure-resurs till arkivet som Azure Monitor-mått med hjälp av ett REST-API

Den här artikeln visar hur du skickar anpassade mått för Azure-resurser till Azure Monitor metrics store via ett REST-API. När mått som är i Azure Monitor kan göra du allt med dem som du gör med standardmått. Exempel diagram, aviseringar och skicka dem till andra externa verktyg.  

>[!NOTE]  
>REST API tillåter endast skicka anpassade mått för Azure-resurser. Du kan använda för att skicka mått för resurser i olika miljöer eller lokalt, [Application Insights](../../azure-monitor/app/api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Skapa och auktorisera ett huvudnamn för tjänsten att skapa mått 

Skapa ett huvudnamn för tjänsten i Azure Active Directory-klienten med hjälp av instruktionerna i [skapa ett huvudnamn för tjänsten](../../active-directory/develop/howto-create-service-principal-portal.md). 

Observera följande när du går igenom den här processen: 

- Du kan ange valfri URL för URL-inloggningen.  
- Skapa en ny klienthemlighet för den här appen.  
- Spara den och klient-ID för användning i senare steg.  

Ge appen som skapats i steg 1, övervakning av mått utgivare och behörigheter till resursen som du vill generera måtten mot. Om du planerar att använda appen för att skapa anpassade mått mot många resurser, kan du ge behörigheterna resource group eller på prenumerationsnivån. 

## <a name="get-an-authorization-token"></a>Hämta en autentiseringstoken
Öppna en kommandotolk och kör följande kommando:

```shell
curl -X POST https://login.partner.microsoftonline.cn/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step> " -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Spara åtkomsttoken från svaret.

![Åtkomsttoken](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Skapa mått via REST API 

1. Klistra in följande JSON i en fil och spara den som **custommetric.json** på den lokala datorn. Uppdatera parametern time i JSON-fil: 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20", 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

2. Efter måttdata i Kommandotolk-fönster: 
   - **azureRegion**. Måste matcha distributionsregionen för resursen som du de mått för. 
   - **resourceID**.  Resurs-ID för Azure-resursen du spårar mått jämfört med.  
   - **AccessToken**. Klistra in den token som du hämtade tidigare.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
3. Ändra tidsstämpel och värdena i JSON-filen. 
4. Upprepa föregående två steg ett par gånger, så att du har data i flera minuter.

## <a name="troubleshooting"></a>Felsökning 
Om du får ett felmeddelande med en del av processen, bör följande felsökningsinformation:

1. Du kan inte utfärda måtten mot en prenumeration eller resursgrupp som din Azure-resurs. 
1. Du kan inte lägga ett mått i arkivet som är över 20 minuter. Arkivet mått är optimerat för avisering och i realtid diagram. 
2. Antalet dimensionsnamn måste matcha värdena och vice versa. Kontrollera värdena. 
2. Du kan avger måtten mot en region som inte stöder anpassade mått. Se [regioner som stöds](../../azure-monitor/platform/metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>Visa dina mått 

1. Logga in på Azure Portal. 

1. I den vänstra menyn och väljer **övervakaren**. 

1. På den **övervakaren** väljer **mått**. 

   ![Välja mått](./media/metrics-store-custom-rest-api/metrics.png) 

1. Ändra sammanställningsperioden till **senaste 30 minuterna**.  

1. I den **resource** nedrullningsbara menyn, Välj den resurs som du genererat mått jämfört med.  

1. I den **namnområden** nedrullningsbara menyn och välj **QueueProcessing**. 

1. I den **mått** nedrullningsbara menyn och välj **QueueDepth**.  

 
## <a name="next-steps"></a>Nästa steg
- Läs mer om [anpassade mått](../../azure-monitor/platform/metrics-custom-overview.md).
