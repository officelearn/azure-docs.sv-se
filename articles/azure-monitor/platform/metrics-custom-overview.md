---
title: Anpassade mått i Azure Monitor
description: Lär dig mer om anpassade mått i Azure Monitor och hur de modelleras.
author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: e104877ef641a87eac4ba19bb3342c6e029bf80c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294583"
---
# <a name="custom-metrics-in-azure-monitor"></a>Anpassade mått i Azure Monitor

När du distribuerar resurser och program i Azure, bör du börja samla in telemetri för att få insikter om deras prestanda och hälsa. Azure gör vissa mått tillgängliga för dig utanför ramarna. Dessa mått kallas standard eller plattform. Men de är begränsade till sin natur. Du kanske vill samla in några anpassade resultatindikatorer eller affärsspecifika mått för att ge djupare insikter.
Dessa **anpassade** mått kan samlas in via din programtelemetri, en agent som körs på dina Azure-resurser eller till och med ett övervakningssystem utifrån och skickas direkt till Azure Monitor. När de har publicerats på Azure Monitor kan du bläddra bland, fråga och avisera anpassade mått för dina Azure-resurser och -program sida vid sida med standardmåtten som skickas ut av Azure.

## <a name="send-custom-metrics"></a>Skicka anpassade mått
Anpassade mått kan skickas till Azure Monitor via flera metoder:
- Instrumentera ditt program med hjälp av SDK för Azure Application Insights och skicka anpassad telemetri till Azure Monitor. 
- Installera WAD-tillägget (Windows Azure Diagnostics) på din [Azure VM,](collect-custom-metrics-guestos-resource-manager-vm.md) [skalningsuppsättning för virtuella datorer,](collect-custom-metrics-guestos-resource-manager-vmss.md) [klassisk virtuell dator](collect-custom-metrics-guestos-vm-classic.md)eller klassiska [molntjänster](collect-custom-metrics-guestos-vm-cloud-service-classic.md) och skicka prestandaräknare till Azure Monitor. 
- Installera [InfluxData Telegraf-agenten](collect-custom-metrics-linux-telegraf.md) på din virtuella Azure Linux-dator och skicka mått med hjälp av plugin-programmet Fördrörning av Azure Monitor.
- Skicka anpassade mått [direkt till AZURE Monitor REST API](../../azure-monitor/platform/metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`.

När du skickar anpassade mått till Azure Monitor måste varje datapunkt eller värde som rapporteras innehålla följande information.

### <a name="authentication"></a>Autentisering
Om du vill skicka anpassade mått till Azure Monitor behöver entiteten som skickar måttet en giltig Azure Active Directory -token (Azure AD) i **innehavarhuvudet** för begäran. Det finns några sätt att skaffa en giltig innehavartoken som stöds:
1. [Hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Ger en identitet till en Azure-resurs själv, till exempel en virtuell dator. Msi (Managed Service Identity) är utformat för att ge resurser behörighet att utföra vissa åtgärder. Ett exempel är att låta en resurs släppa ut mått om sig själv. En resurs, eller dess MSI, kan beviljas **övervakningsmått** Publisher-behörigheter för en annan resurs. Med den här behörigheten kan MSI även generera mått för andra resurser.
2. [Huvudnamn för Azure AD-tjänsten](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals). I det här fallet kan ett Azure AD-program, eller tjänst, tilldelas behörigheter för att avge mått om en Azure-resurs.
För att autentisera begäran validerar Azure Monitor programtoken med hjälp av offentliga Azure AD-nycklar. Den befintliga **rollen Övervakningsmått publisher** har redan den här behörigheten. Den är tillgänglig i Azure-portalen. Tjänstens huvudnamn, beroende på vilka resurser den avger anpassade mått för, kan ges rollen **Övervakningsmått utgivare** vid det scope som krävs. Exempel är en prenumeration, resursgrupp eller specifik resurs.

> [!NOTE]  
> När du begär en Azure AD-token för att avge anpassade mått, `https://monitoring.azure.com/`se till att målgruppen eller resursen som token begärs för är . Var noga med att inkludera den avslutande "/".

### <a name="subject"></a>Subjekt
Den här egenskapen fångar in vilket Azure-resurs-ID som det anpassade måttet rapporteras för. Den här informationen kodas i URL:en för api-anropet som görs. Varje API kan bara skicka måttvärden för en enda Azure-resurs.

> [!NOTE]  
> Du kan inte avge anpassade mått mot resurs-ID för en resursgrupp eller prenumeration.
>
>

### <a name="region"></a>Region
Den här egenskapen fångar vilken Azure-region resursen du släpper ut mått för distribueras i. Mått måste skickas till samma regionala slutpunkt för Azure Monitor när den region som resursen distribueras i. Till exempel måste anpassade mått för en virtuell dator som distribueras i västra USA skickas till WestUS regionala Azure Monitor-slutpunkten. Regioninformationen kodas också i URL:en för API-anropet.

> [!NOTE]  
> Under den offentliga förhandsversionen är anpassade mått endast tillgängliga i en delmängd av Azure-regioner. En lista över regioner som stöds dokumenteras i ett senare avsnitt i den här artikeln.
>
>

### <a name="timestamp"></a>Tidsstämpel
Varje datapunkt som skickas till Azure Monitor måste markeras med en tidsstämpel. Den här tidsstämpeln fångar DateTime då måttvärdet mäts eller samlas in. Azure Monitor accepterar måttdata med tidsstämplar så långt som 20 minuter tidigare och 5 minuter i framtiden. Tidsstämpeln måste vara i ISO 8601-format.

### <a name="namespace"></a>Namnområde
Namnområden är ett sätt att kategorisera eller gruppera liknande mått tillsammans. Genom att använda namnområden kan du uppnå isolering mellan grupper av mått som kan samla in olika insikter eller resultatindikatorer. Du kan till exempel ha ett namnområde som kallas **contosomemorymetrics** som spårar mätvärden för minnesanvändning som profilerar din app. Ett annat namnområde som kallas **contosoapptransaction** kan spåra alla mått om användartransaktioner i ditt program.

### <a name="name"></a>Namn
**Namn** är namnet på det mått som rapporteras. Vanligtvis är namnet beskrivande nog för att identifiera vad som mäts. Ett exempel är ett mått som mäter antalet minnesbyte som används på en viss virtuell dator. Det kan ha ett måttnamn som **Minnesbyte som används**.

### <a name="dimension-keys"></a>Dimensionsnycklar
En dimension är ett nyckel- eller värdepar som hjälper till att beskriva ytterligare egenskaper för det mått som samlas in. Genom att använda ytterligare egenskaper kan du samla in mer information om måttet, vilket möjliggör djupare insikter. Måttet **Minnesbyte som används** kan till exempel ha en dimensionsnyckel som kallas **Process** som fångar hur många byte minne varje process på en virtuell dator förbrukar. Genom att använda den här nyckeln kan du filtrera måttet för att se hur mycket minne specifika processer använder eller för att identifiera de fem processer som används av minne.
Dimensioner är valfria, inte alla mått kan ha dimensioner. Ett anpassat mått kan ha upp till 10 dimensioner.

### <a name="dimension-values"></a>Dimensionsvärden
När du rapporterar en måttdatapunkt finns det ett motsvarande dimensionsvärde för varje dimensionsnyckel på det mått som rapporteras. Du kanske till exempel vill rapportera det minne som används av ContosoApp på den virtuella datorn:

* Måttnamnet skulle vara **Minnesbyte som används**.
* Dimensionsnyckeln skulle vara **Process**.
* Dimensionsvärdet skulle vara **ContosoApp.exe**.

När du publicerar ett måttvärde kan du bara ange ett värde för en dimension per dimensionsnyckel. Om du samlar in samma minnesanvändning för flera processer på den virtuella datorn kan du rapportera flera måttvärden för den tidsstämpeln. Varje måttvärde anger ett annat dimensionsvärde för **processdimensionsnyckeln.**
Dimensioner är valfria, inte alla mått kan ha dimensioner. Om ett måttinlägg definierar dimensionsnycklar är motsvarande dimensionsvärden obligatoriska.

### <a name="metric-values"></a>Måttvärden
Azure Monitor lagrar alla mått med en minuts detaljerade intervall. Vi förstår att under en viss minut kan ett mått behöva samplas flera gånger. Ett exempel är CPU-användning. Eller det kan behöva mätas för många diskreta händelser. Ett exempel är inloggningstransaktionsfördynadnad. Om du vill begränsa antalet råvärden som du måste släppa ut och betala för i Azure Monitor kan du lokalt föra ihop och avge värdena:

* **Min**: Det minsta observerade värdet från alla prover och mätningar under minuten.
* **Max**: Det högsta observerade värdet från alla prover och mätningar under minuten.
* **Summa**: Summeringen av alla observerade värden från alla prover och mätningar under minuten.
* **Antal**: Antalet prover och mätningar som tagits under minuten.

Om det till exempel fanns 4 inloggningstransaktioner till din app under en viss minut, kan de resulterande uppmätta svaren för varje vara följande:

|Transaktion 1|Transaktion 2|Transaktion 3|Transaktion 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

Då blir den resulterande måttpubliceringen till Azure Monitor följande:
* Min: 4
* Max: 16
* Summa: 40
* Antal: 4

Om ditt program inte kan föra ihop lokalt och behöver avge varje diskret exempel eller händelse omedelbart efter insamling, kan du avge råmåttvärdena. Varje gång en inloggningstransaktion till exempel inträffar i din app publicerar du ett mått till Azure Monitor med bara ett mått. Så för en inloggningstransaktion som tog 12 ms, skulle den metriska publikationen vara följande:
* Min: 12
* Max: 12
* Summa: 12
* Antal: 1

Med den här processen kan du avge flera värden för samma mått plus dimensionskombination under en viss minut. Azure Monitor tar sedan alla råvärden som avges för en viss minut och sammanställer dem tillsammans.

### <a name="sample-custom-metric-publication"></a>Exempel på anpassad måttpublikation
I följande exempel skapar du ett anpassat mått som kallas **Minnesbyte som används** under den metriska namnområdet **Minnesprofil** för en virtuell dator. Måttet har en enda dimension som kallas **Process**. För den angivna tidsstämpeln avger vi metriska värden för två olika processer:

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
> Application Insights, diagnostiktillägget och TillströmningData Telegraf-agenten är redan konfigurerade för att avge måttvärden mot rätt regional slutpunkt och bära alla föregående egenskaper i varje utsläpp.
>
>

## <a name="custom-metric-definitions"></a>Anpassade måttdefinitioner
Du behöver inte fördefiniera ett anpassat mått i Azure Monitor innan det skickas ut. Varje metrisk datapunkt som publiceras innehåller namnområdes-, namn- och dimensionsinformation. Så första gången ett anpassat mått genereras till Azure Monitor skapas en måttdefinition automatiskt. Den här måttdefinitionen kan sedan identifieras på alla resurser som måttet släpps ut mot via måttdefinitionerna.

> [!NOTE]  
> Azure Monitor har ännu inte stöd för att definiera **enheter** för ett anpassat mått.

## <a name="using-custom-metrics"></a>Använda anpassade mått
När anpassade mått har skickats till Azure Monitor kan du bläddra bland dem via Azure-portalen och fråga dem via AZURE Monitor REST API:er. Du kan också skapa aviseringar på dem för att meddela dig när vissa villkor är uppfyllda.

> [!NOTE]
> Du måste vara en läsar- eller deltagarroll för att kunna visa anpassade mått.

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Bläddra bland dina anpassade mått via Azure-portalen
1.    Gå till [Azure-portalen](https://portal.azure.com).
2.    Markera **fönstret Bildskärm.**
3.    Välj **Mått**.
4.    Välj en resurs som du har avgett anpassade mått mot.
5.    Välj måttnamnområdet för ditt anpassade mått.
6.    Välj det anpassade måttet.

## <a name="supported-regions"></a>Regioner som stöds
Under den offentliga förhandsversionen är möjligheten att publicera anpassade mått endast tillgänglig i en delmängd av Azure-regioner. Den här begränsningen innebär att mått endast kan publiceras för resurser i en av de regioner som stöds. I följande tabell visas en uppsättning Azure-regioner som stöds för anpassade mått. I den visas också motsvarande slutpunkter som mått för resurser i dessa regioner ska publiceras för att:

|Azure-region |Prefix för regional slutpunkt|
|---|---|
| **USA och Kanada** | |
|USA, västra centrala | https:\//westcentralus.monitoring.azure.com/ |
|USA, västra 2       | https:\//westus2.monitoring.azure.com/ |
|USA, norra centrala | https:\//northcentralus.monitoring.azure.com
|USA, södra centrala| https:\//southcentralus.monitoring.azure.com/ |
|USA, centrala      | https:\//centralus.monitoring.azure.com |
|Kanada, centrala | https:\//canadacentral.monitoring.azure.comc
|USA, östra| https:\//eastus.monitoring.azure.com/ |
| **Europa** | |
|Europa, norra    | https:\//northeurope.monitoring.azure.com/ |
|Europa, västra     | https:\//westeurope.monitoring.azure.com/ |
|Storbritannien, södra | https:\//uksouth.monitoring.azure.com
|Frankrike, centrala | https:\//francecentral.monitoring.azure.com |
| **Afrika** | |
|Sydafrika North | https:\//southafricanorth.monitoring.azure.com
| **Asien** | |
|Indien, centrala | https:\//centralindia.monitoring.azure.com
|Australien, östra | https:\//australiaeast.monitoring.azure.com
|Japan, östra | https:\//japaneast.monitoring.azure.com
|Sydostasien  | https:\//southeastasia.monitoring.azure.com |
|Asien, östra | https:\//eastasia.monitoring.azure.com
|Sydkorea, centrala   | https:\//koreacentral.monitoring.azure.com


## <a name="quotas-and-limits"></a>Kvoter och begränsningar
Azure Monitor inför följande användningsgränser för anpassade mått:

|Kategori|Gräns|
|---|---|
|Aktiva tidsserier/prenumerationer/region|50 000|
|Dimensionsnycklar per mått|10|
|Stränglängd för måttnamnområden, måttnamn, dimensionsnycklar och dimensionsvärden|256 tecken|

En aktiv tidsserie definieras som en unik kombination av mått, dimensionsnyckel eller dimensionsvärde som har haft måttvärden publicerade under de senaste 12 timmarna.

## <a name="next-steps"></a>Nästa steg
Använd anpassade mått från olika tjänster: 
 - [Virtuella datorer](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Skaluppsättning för virtuella datorer](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Virtuella Azure-datorer (klassisk)](collect-custom-metrics-guestos-vm-classic.md)
 - [Linux Virtual Machine med Telegraf-agenten](collect-custom-metrics-linux-telegraf.md)
 - [REST API](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [Klassiska molntjänster](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 
