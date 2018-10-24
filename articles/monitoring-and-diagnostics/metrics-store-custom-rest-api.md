---
title: Skicka anpassade mått för en Azure-resurs av Azure Monitor-måtten lagra med en REST-API
description: Skicka anpassade mått för en Azure-resurs av Azure Monitor-måtten lagra med en REST-API
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: d36697e6b5765ecf35ed9b3add45cff6c33823a5
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49958233"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-using-a-rest-api"></a>Skicka anpassade mått för en Azure-resurs av Azure Monitor-måtten lagra med en REST-API

Den här artikeln visar hur du skickar anpassade mått för Azure-resurser till Azure Monitor metrics store via ett REST-API.  När mått som är i Azure Monitor kan göra du allt med dem kan du göra med standardmått, till exempel diagram, aviseringar, skicka dem till andra externa verktyg, osv.  

>[!NOTE] 
>REST API tillåter endast skicka anpassade mått för Azure-resurser. Du kan använda för att skicka mått för resurser i olika miljöer eller lokalt, [Application Insights](../application-insights/app-insights-api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Skapa och auktorisera ett huvudnamn för tjänsten att skapa mått 

Skapa tjänstens huvudnamn i Azure Active Directory-klienten med hjälp av instruktionerna i [skapa ett huvudnamn för tjänsten](../active-directory/develop/howto-create-service-principal-portal.md). 

Observera följande när du går igenom den här processen: 

- Du kan placera i valfri URL för inloggnings URL.  
- Skapa nya klienthemligheten för den här appen  
- Spara den och klient-ID för användning i senare steg.  

Ge appen som skapats i steg 1 ”övervakning mått Publisher”-behörigheter till resursen som du vill generera måtten mot. Om du planerar att använda appen för att skapa anpassade mått mot många resurser, kan du ge behörigheterna resource group eller på prenumerationsnivån. 

## <a name="get-an-authorization-token"></a>Hämta en autentiseringstoken
Öppna en kommandotolk och kör följande kommando
```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step> " -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Spara åtkomsttoken från svaret

![Åtkomsttoken](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Skapa mått via REST API 

1. Klistra in följande JSON i en fil och spara den som custommetric.json på den lokala datorn. Uppdatera parametern time i JSON-filen. 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20”, 
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

1. I Kommandotolken, publicera måttdata 
    - Azure-Region – måste matcha distributionsregionen för resursen du sänder mått för. 
    - Resurs-ID – resurs-ID för Azure-resursen du spårar mått jämfört med.  
    - Åtkomsttoken – klistra in den token som anskaffats tidigare

    ```Shell 
    curl -X POST curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId> /metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
    ```
1. Ändra tidsstämpel och värdena i JSON-filen. 
1. Upprepa föregående två steg ett par gånger så att du har data i flera minuter.

## <a name="troubleshooting"></a>Felsökning 
Om du får ett fel med någon del av processen, överväger följande

1. Du kan inte utfärda måtten mot en prenumeration eller resursgrupp som din Azure-resurs. 
1. Du kan inte lägga ett mått i arkivet som är över 20 minuter. Arkivet mått är optimerat för avisering och i realtid diagram. 
2. Antalet dimensionsnamn måste matcha värden och vice versa. Kontrollera värdena. 
2. Du kan avger måtten mot region som inte stöder anpassade mått. Se [anpassat mått (förhandsversion) regioner som stöds](metrics-custom-overview.md#supported-regions) 



## <a name="view-your-metrics"></a>Visa dina mått 

1. Logga in på Azure-portalen 

1. I den vänstra menyn, klickar du på **Övervakare** 

1. På sidan övervakaren **mått**. 

   ![Åtkomsttoken](./media/metrics-store-custom-rest-api/metrics.png) 

1. Ändra sammanställningsperioden till **senaste 30 minuterna**.  

1. I den *resource* listrutan och välj den resurs som du genererat mått jämfört med.  

1. I den *namnområden* listrutan, väljer **QueueProcessing** 

1. I den *mått* nedrullningsbar listruta, Välj **QueueDepth**.  

 
## <a name="next-steps"></a>Nästa steg
- Läs mer om [anpassade mått](metrics-custom-overview.md).