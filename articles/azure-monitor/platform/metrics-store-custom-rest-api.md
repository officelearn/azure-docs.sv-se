---
title: Skicka mått till Azure Monitor-måttdatabasen med REST API
description: Skicka anpassade mått för en Azure-resurs till Azure Monitor-måttarkivet med hjälp av ett REST API
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 84709c022631543101889f784231158ebb96b6f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662272"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Skicka anpassade mått för en Azure-resurs till Azure Monitor-måttarkivet med hjälp av ett REST API

Den här artikeln visar hur du skickar anpassade mått för Azure-resurser till Azure Monitor-måttarkivet via ett REST API. När måtten finns i Azure Monitor kan du göra alla de saker med dem som du gör med standardmått. Exempel är att kartlägga, avisera och dirigera dem till andra externa verktyg.  

>[!NOTE]  
>REST API tillåter endast att skicka anpassade mått för Azure-resurser. Om du vill skicka mått för resurser i olika miljöer eller lokalt kan du använda [Application Insights](../../azure-monitor/app/api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Skapa och auktorisera ett tjänsthuvudnamn för att generera mått 

Skapa ett tjänsthuvudnamn i din Azure Active Directory-klient med hjälp av instruktionerna som finns på [Skapa ett tjänsthuvudnamn](../../active-directory/develop/howto-create-service-principal-portal.md). 

Observera följande medan du går igenom den här processen: 

- Du kan ange valfri webbadress för inloggningsadressen.  
- Skapa en ny klienthemlighet för den här appen.  
- Spara nyckeln och klient-ID:et för användning i senare steg.  

Ge appen som skapats som en del av steg 1, Övervakningsmått Publisher, behörigheter till den resurs du vill generera mått mot. Om du planerar att använda appen för att generera anpassade mått mot många resurser kan du bevilja dessa behörigheter på resursgrupps- eller prenumerationsnivå. 

## <a name="get-an-authorization-token"></a>Hämta en auktoriseringstoken
Öppna en kommandotolk och kör följande kommando:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Spara åtkomsttoken från svaret.

![Åtkomsttoken](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Avge måttet via REST API 

1. Klistra in följande JSON i en fil och spara den som **custommetric.json** på den lokala datorn. Uppdatera tidsparametern i JSON-filen: 
    
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

1. I kommandotolksfönstret bokför du måttdata: 
   - **azureRegion**. Måste matcha distributionsregionen för resursen som du släpper ut mått för. 
   - **resourceID**.  Resurs-ID för Azure-resursen som du spårar måttet mot.  
   - **AccessToken**. Klistra in den token som du har skaffat tidigare.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Ändra tidsstämpel och värden i JSON-filen. 
1. Upprepa de föregående två stegen några gånger, så att du har data i flera minuter.

## <a name="troubleshooting"></a>Felsökning 
Om du får ett felmeddelande med någon del av processen bör du tänka på följande felsökningsinformation:

1. Du kan inte utfärda mått mot en prenumeration eller resursgrupp som din Azure-resurs. 
1. Du kan inte lägga ett mått i butiken som är över 20 minuter gammal. Måttarkivet är optimerat för aviseringar och realtidsdiagram. 
2. Antalet dimensionsnamn ska matcha värdena och vice versa. Kontrollera värdena. 
2. Du kanske släpper ut mått mot en region som inte stöder anpassade mått. Se [regioner som stöds](../../azure-monitor/platform/metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>Visa dina mätvärden 

1. Logga in på Azure Portal. 

1. Välj **Övervaka**till vänstermenyn . 

1. Välj **Mått**på sidan **Övervakare** . 

   ![Välj mått](./media/metrics-store-custom-rest-api/metrics.png) 

1. Ändra aggregeringsperioden till **Sista 30 minuterna**.  

1. I **den** nedrullningsbara menyn resurs väljer du den resurs som du har avgett måttet mot.  

1. Välj **Köbearbetning**på den **nedrullningsbara** menyn Namnområden . 

1. Välj **QueueDepth**på den **nedrullningsbara** menyn Mått .  

 
## <a name="next-steps"></a>Nästa steg
- Läs mer om [anpassade mått](../../azure-monitor/platform/metrics-custom-overview.md).

