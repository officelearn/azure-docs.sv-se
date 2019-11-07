---
title: Anpassade mått i Azure Monitor
description: Lär dig mer om anpassade mått i Azure Monitor och hur de modelleras.
author: ancav
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 744958fc44a8d10bbc8ca5d44af8c473548ae5ca
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669168"
---
# <a name="custom-metrics-in-azure-monitor"></a>Anpassade mått i Azure Monitor

När du distribuerar resurser och program i Azure vill du börja samla in telemetri för att få insikter om prestanda och hälsa. Azure gör vissa mått tillgängliga i rutan. Dessa mått kallas standard eller plattform. De är dock begränsade. Du kanske vill samla in vissa anpassade prestanda indikatorer eller företagsspecifika mått för att ge djupare insikter.
Dessa **anpassade** mått kan samlas in via din programtelemetri, en agent som körs på dina Azure-resurser eller till och med ett externt övervaknings system och skickas direkt till Azure Monitor. När de har publicerats till Azure Monitor kan du bläddra i, fråga och avisering om anpassade mått för dina Azure-resurser och-program sida vid sida med de standard mått som genereras av Azure.

## <a name="send-custom-metrics"></a>Skicka anpassade mått
Anpassade mått kan skickas till Azure Monitor via flera metoder:
- Instrumentera ditt program genom att använda Azure Application Insights SDK och skicka anpassad telemetri till Azure Monitor. 
- Installera Windows Azure-diagnostik-tillägget (WAD) på din [virtuella Azure-dator](collect-custom-metrics-guestos-resource-manager-vm.md), [skalnings uppsättning för virtuell dator](collect-custom-metrics-guestos-resource-manager-vmss.md), [klassisk virtuell dator](collect-custom-metrics-guestos-vm-classic.md)eller [klassisk Cloud Services](collect-custom-metrics-guestos-vm-cloud-service-classic.md) och skicka prestanda räknare till Azure Monitor. 
- Installera [InfluxDatain-agenten](collect-custom-metrics-linux-telegraf.md) på din virtuella Azure Linux-dator och skicka mått med hjälp av plugin-programmet för Azure Monitor-utdata.
- Skicka anpassade mått [direkt till Azure Monitor REST API](../../azure-monitor/platform/metrics-store-custom-rest-api.md)`https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`.

När du skickar anpassade mått till Azure Monitor måste varje data punkt eller värde rapporter innehålla följande information.

### <a name="authentication"></a>Autentisering
För att kunna skicka in anpassade mått till Azure Monitor måste entiteten som skickar måttet ha en giltig Azure Active Directory-token (Azure AD) **i huvud rubriken** för begäran. Det finns några sätt att hämta en giltig Bearer-token:
1. [Hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Ger en identitet till en Azure-resurs, till exempel en virtuell dator. Hanterad tjänstidentitet (MSI) är utformat för att ge resurser behörighet att utföra vissa åtgärder. Ett exempel är att låta en resurs generera mått om sig själv. En resurs, eller dess MSI, kan beviljas **övervaknings mått utgivar** behörigheter för en annan resurs. Med den här behörigheten kan MSI genererar mått för andra resurser också.
2. [Azure AD-tjänstens huvud namn](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals). I det här scenariot kan ett Azure AD-program eller-tjänst tilldelas behörigheter för att generera mått för en Azure-resurs.
För att autentisera begäran verifierar Azure Monitor programtoken med hjälp av offentliga Azure AD-nycklar. Den befintliga **övervaknings mått utgivar** rollen har redan den här behörigheten. Den finns i Azure Portal. Tjänstens huvud namn, beroende på vilka resurser som den utvärderar anpassade mått för, kan få rollen som **övervaknings mått utgivar** roll i det omfånget som krävs. Exempel är en prenumeration, en resurs grupp eller en angiven resurs.

> [!NOTE]  
> När du begär en Azure AD-token för att skicka anpassade mått måste du se till att den mål grupp eller resurs som token begärs för är https://monitoring.azure.com/. Se till att ta med efterföljande "/".

### <a name="subject"></a>Subjekt
Den här egenskapen registrerar vilket Azure-resurs-ID som det anpassade måttet rapporteras för. Den här informationen kommer att kodas i URL: en för det API-anrop som görs. Varje API kan bara skicka Mät värden för en enda Azure-resurs.

> [!NOTE]  
> Du kan inte generera anpassade mått mot resurs-ID för en resurs grupp eller prenumeration.
>
>

### <a name="region"></a>Region
Den här egenskapen registrerar vilken Azure-region som den resurs som du skickar måtten för distribueras i. Mått måste genereras till samma Azure Monitor regionala slut punkten som den region som resursen har distribuerats i. Till exempel måste anpassade mått för en virtuell dator som har distribuerats i västra USA skickas till den västra regionala Azure Monitor slut punkten. Region informationen är också kodad i URL-adressen till API-anropet.

> [!NOTE]  
> Under den offentliga för hands versionen är anpassade mått endast tillgängliga i en delmängd av Azure-regioner. En lista över regioner som stöds finns dokumenterade i ett senare avsnitt i den här artikeln.
>
>

### <a name="timestamp"></a>Tidsstämpel
Varje data punkt som skickas till Azure Monitor måste markeras med en tidsstämpel. Den här tidsstämpeln fångar in det datum/tid-värde som Metric-värdet mäts eller samlas in. Azure Monitor accepterar mått data med tidsstämplar så långt som 20 minuter under de senaste och 5 minuterna i framtiden. Tidsstämpeln måste anges i formatet ISO 8601.

### <a name="namespace"></a>Namnområde
Namn områden är ett sätt att kategorisera eller gruppera likartade mått tillsammans. Genom att använda namn rymder kan du isolera olika grupper av mått som kan samla in olika insikter eller prestanda indikatorer. Du kan till exempel ha ett namn område med namnet **contosomemorymetrics** som spårar minnes användnings mått som innehåller en profil för din app. Ett annat namn område med namnet **contosoapptransaction** kan spåra alla mått för användar transaktioner i programmet.

### <a name="name"></a>Namn
**Namn** är namnet på det mått som rapporteras. Vanligt vis är namnet tillräckligt beskrivande för att hjälpa dig att identifiera vad som mäts. Ett exempel är ett mått som mäter antalet minnes byte som används på en specifik virtuell dator. Det kan ha ett Metric-namn som **minnes byte som används**.

### <a name="dimension-keys"></a>Dimensions nycklar
En dimension är ett nyckel-eller värdepar som hjälper dig att beskriva ytterligare egenskaper för det mått som samlas in. Genom att använda ytterligare egenskaper kan du samla in mer information om måttet, vilket ger djupare insikter. Till exempel kan **minnes byte som används** måttet ha en dimensions nyckel kallad **process** som samlar in hur många byte minne varje process på en virtuell dator förbrukar. Genom att använda den här nyckeln kan du filtrera måttet för att se hur mycket minnesbaserade processer som använder eller för att identifiera de fem översta processerna efter minnes användning.
Mått är valfria, men alla mått kan inte ha några mått. Ett anpassat mått kan ha upp till 10 dimensioner.

### <a name="dimension-values"></a>Dimensions värden
När du rapporterar en mått data punkt, för varje dimensions nyckel för det mått som rapporteras, finns ett motsvarande dimensions värde. Du kanske till exempel vill rapportera det minne som används av ContosoApp på den virtuella datorn:

* Mått namnet skulle vara **minnes byte som används**.
* Dimensions nyckeln skulle **bearbetas**.
* Dimension svärdet skulle vara **ContosoApp. exe**.

När du publicerar ett mått värde kan du bara ange ett enskilt dimensions värde per dimensions nyckel. Om du samlar in samma minnes användning för flera processer på den virtuella datorn kan du rapportera flera mått värden för den tidsstämpeln. Varje mått värde anger ett annat dimensions värde för **process** dimensions nyckeln.
Mått är valfria, men alla mått kan inte ha några mått. Om ett mått post definierar dimensions nycklar är motsvarande dimensions värden obligatoriska.

### <a name="metric-values"></a>Mät värden
Azure Monitor lagrar alla mått med en minuts precisions intervall. Vi förstår att ett Mät värde kan behöva samplas flera gånger under en bestämd minut. Ett exempel är CPU-användning. Eller så kanske det måste mätas för många diskreta händelser. Ett exempel är en fördröjning för inloggnings transaktioner. Om du vill begränsa antalet råa värden som du måste generera och betala för i Azure Monitor kan du lokalt församla och generera värdena:

* **Min**: det lägsta observerade värdet från alla exempel och mätningar under minuten.
* **Max**: det högsta observerade värdet från alla exempel och mätningar under minuten.
* **Sum: summan**av alla observerade värden från alla exempel och mätningar under minuten.
* **Count**: antalet prover och mätningar som tagits under minuten.

Om det exempelvis fanns 4 inloggnings transaktioner till din app under en angiven minut, kan de resulterande fördröjningarna för var och en av dem vara följande:

|Transaktion 1|Transaktion 2|Transaktion 3|Transaktion 4|
|---|---|---|---|
|7 MS|4 MS|13 MS|16 MS|
|

Sedan skulle den resulterande mått publikationen till Azure Monitor vara följande:
* Min: 4
* Max: 16
* Sum: 40
* Antal: 4

Om programmet inte kan föraggregeras lokalt och måste generera varje separat exempel eller händelse direkt vid insamling, kan du generera värdena för rå mått. Varje gång en inloggnings transaktion inträffar i din app, kan du till exempel publicera ett mått för att Azure Monitor med bara ett enda mått. För en inloggnings transaktion som tog 12 MS är mått publikationen följande:
* Min: 12
* Max: 12
* Sum: 12
* Antal: 1

Med den här processen kan du generera flera värden för samma mått och dimensions kombination under en bestämd minut. Azure Monitor tar sedan alla RAW-värden som har spridits för en bestämd minut och sammanställer dem tillsammans.

### <a name="sample-custom-metric-publication"></a>Exempel på anpassad mått publicering
I följande exempel skapar du ett anpassat mått som kallas **minnes byte som används i** **minnes profilen** för mått namn området för en virtuell dator. Måttet har en enda dimension som heter **process**. För den aktuella tidsstämpeln genererar vi mått värden för två olika processer:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> Application Insights, Diagnostics-tillägget och InfluxDatain-agenten redan har kon figurer ATS för att generera mått värden mot rätt regionala slut punkt och överföra alla föregående egenskaper i varje utsläpp.
>
>

## <a name="custom-metric-definitions"></a>Anpassade mått definitioner
Det finns inget behov av att fördefiniera ett anpassat mått i Azure Monitor innan det genereras. Varje Mät data punkt som publiceras innehåller namn område, namn och dimensions information. Första gången ett anpassat mått genereras till Azure Monitor skapas en mått definition automatiskt. Den här mått definitionen kan sedan identifieras på alla resurser som måttet genereras mot via mått definitionerna.

> [!NOTE]  
> Azure Monitor har ännu inte stöd för att definiera **enheter** för ett anpassat mått.

## <a name="using-custom-metrics"></a>Använda anpassade mått
När anpassade mått har skickats till Azure Monitor kan du bläddra igenom dem via Azure Portal och fråga dem via Azure Monitor REST-API: er. Du kan också skapa aviseringar på dem för att meddela dig när vissa villkor uppfylls.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Bläddra bland dina anpassade mått via Azure Portal
1.  Gå till [Azure-portalen](https://portal.azure.com).
2.  Välj fönstret **övervaka** .
3.  Välj **Mått**.
4.  Välj en resurs som du har skickat anpassade mått mot.
5.  Välj namn området mått för ditt anpassade mått.
6.  Välj det anpassade måttet.

## <a name="supported-regions"></a>Regioner som stöds
Under den offentliga för hands versionen är möjligheten att publicera anpassade mått bara tillgänglig i en delmängd av Azure-regioner. Den här begränsningen innebär att mått endast kan publiceras för resurser i någon av de regioner som stöds. I följande tabell visas en lista över Azure-regioner som stöds för anpassade mått. Den listar även motsvarande slut punkter som Mät värden för resurser i dessa regioner ska publiceras på:

|Azure-region |Regionalt slut punkts prefix|
|---|---|
| **USA och Kanada** | |
|Västra centrala USA | https:\//westcentralus.monitoring.azure.com/ |
|Västra USA 2       | https:\//westus2.monitoring.azure.com/ |
|Norra centrala USA | https:\//northcentralus.monitoring.azure.com
|Södra centrala USA| https:\//southcentralus.monitoring.azure.com/ |
|Centrala USA      | https:\//centralus.monitoring.azure.com |
|Centrala Kanada | https:\//canadacentral.Monitoring.Azure.comc
|Östra USA| https:\//eastus.monitoring.azure.com/ |
| **Europa** | |
|Norra Europa    | https:\//northeurope.monitoring.azure.com/ |
|Västra Europa     | https:\//westeurope.monitoring.azure.com/ |
|Storbritannien, södra | https:\//uksouth.monitoring.azure.com
|Frankrike, centrala | https:\//francecentral.monitoring.azure.com |
| **Centralafrika** | |
|Sydafrika, norra | https:\//southafricanorth.monitoring.azure.com
| **Asien** | |
|Indien, centrala | https:\//centralindia.monitoring.azure.com
|Östra Australien | https:\//australiaeast.monitoring.azure.com
|Östra Japan | https:\//japaneast.monitoring.azure.com
|Sydostasien  | https:\//southeastasia.monitoring.azure.com |
|Östasien | https:\//eastasia.monitoring.azure.com
|Sydkorea, centrala   | https:\//koreacentral.monitoring.azure.com


## <a name="quotas-and-limits"></a>Kvoter och begränsningar
Azure Monitor tillämpar följande användnings gränser för anpassade mått:

|Kategori|Gräns|
|---|---|
|Aktiv tids serie/prenumerationer/region|50 000|
|Dimensions nycklar per mått|10|
|Sträng längd för mått namn rymder, Metric-namn, dimensions nycklar och dimensions värden|256 tecken|

En aktiv tids serie definieras som en unik kombination av mått, dimensions nyckel eller dimensions värde som har haft mått värden publicerade under de senaste 12 timmarna.

## <a name="next-steps"></a>Nästa steg
Använd anpassade mått från olika tjänster: 
 - [Virtuella datorer](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Skalnings uppsättning för virtuell dator](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure Virtual Machines (klassisk)](collect-custom-metrics-guestos-vm-classic.md)
 - [Virtuell Linux-dator med hjälp av teleympkvistar-agenten](collect-custom-metrics-linux-telegraf.md)
 - [REST API](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [Klassisk Cloud Services](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 
