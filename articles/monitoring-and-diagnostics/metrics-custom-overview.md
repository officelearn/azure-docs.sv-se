---
title: Anpassade mått i Azure Monitor
description: Läs mer om anpassade mått i Azure Monitor och hur de modelleras.
author: ancav
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 1bdf1e1f5e58ecb0939d5876e0cef349e32de517
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344767"
---
# <a name="custom-metrics-in-azure-monitor"></a>Anpassade mått i Azure Monitor

När du distribuerar resurser och program i Azure kan vill du börja samla in telemetri för att få insikter om prestanda och hälsa. Azure gör vissa mått tillgängliga för dig out-of the box när du distribuerar resurser. Dessa kallas standard eller plattform mått. De här måtten är dock begränsad sin natur. Du kanske vill samla in vissa anpassade nyckeltal eller företagsspecifika mått för att ge djupare insikter.
De här ”anpassad” måtten kan samlas in via din programtelemetri, en agent som körs på din Azure-resurser eller utsidan även i övervakningssystemet och skickas direkt till Azure Monitor. När publicerats till Azure Monitor kan du bläddra, fråga och Avisera om anpassade mått för dina Azure-resurser och program sida vid sida standardmått som genereras av Azure.

## <a name="send-custom-metrics"></a>Skicka anpassade mått
Anpassade mått kan skickas till Azure Monitor via olika metoder.
- Instrumentera programmet med Application Insights SDK och skicka telemetri om anpassade till Azure Monitor 
- Installera tillägget Windows diagnostik på din [Azure VM](metrics-store-custom-guestos-resource-manager-vm.md), [VM-skalningsuppsättningen](metrics-store-custom-guestos-resource-manager-vmss.md), [klassisk virtuell dator](metrics-store-custom-guestos-classic-vm.md), eller [klassiska molntjänst](metrics-store-custom-guestos-classic-cloud-service.md)och skicka prestandaräknare till Azure Monitor 
- Installera den [InfluxDB Telegraf agenten](metrics-store-custom-linux-telegraf.md) på virtuell Linux-dator och skicka mått med hjälp av plugin-programmet Azure Monitor utdata
- Skicka anpassade mått [direkt till Azure Monitor REST API](metrics-store-custom-rest-api.md) https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics

När du skickar anpassade mått till Azure Monitor måste varje data punkter (eller värdet) rapporterade innehålla följande information:

### <a name="authentication"></a>Autentisering
Den entitet som skickar måttet måste ha en giltig Azure Active Directory-token i rubriken ”ägar” för begäran om du vill skicka anpassade mått till Azure Monitor. Det finns ett antal sätt att hämta en giltig ägartoken som stöds:
1. [Hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) -ger en identitet till en Azure-resurs själva (till exempel en virtuell dator). MSI har utformats för att tilldela resurser behörigheter att utföra vissa åtgärder – till exempel så att en resurs att skapa mått om sig själv. En resurs (eller dess MSI) kan ha behörighet ”övervakning mått Publisher” på en annan resurs, vilket gör att skapa mått för andra resurser samt MSI.
2. [AAD-tjänsthuvudnamn](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) – det här scenariot är en AAD-programmet (tjänst) kan tilldelas behörigheter att skapa mått om en Azure-resurs.
Azure Monitor verifierar applikations-token med hjälp av AAD offentliga nycklar för att autentisera begäran. Den befintliga ”övervakning mått” utgivarrollen har redan den här behörigheten, som är tillgängliga i Azure-portalen. Tjänstens huvudnamn, beroende på vilka resurser som den kommer anpassade mått för, kan få ”övervakning mått” utgivarroll definitionsområdet krävs (prenumeration, resursgrupp eller specifik resurs).

> [!NOTE]
> När begär en AAD-token för att skapa anpassade mått Kontrollera token begärs för publik/resursen är https://monitoring.azure.com/ (se till att du inkluderar avslutande ”/”)

### <a name="subject"></a>Subjekt
Den här egenskapen samlar in vilket Azure-resurs-ID som anpassade mått rapporteras för. Den här informationen ska kodas i URL: en för API-anrop som görs. Varje API kan bara skicka måttvärden för en enskild Azure-resurser.

> [!NOTE]
> Du kan inte generera anpassade mått mot resurs-ID för en resursgrupp eller prenumeration.
>
>

### <a name="region"></a>Region
Den här egenskapen fångar du sänder mått för resursen är distribuerad i vilka Azure-region. Mått måste genereras i samma Azure-övervakaren regionala slutpunkten som regionen som resursen distribueras i. Till exempel måste anpassade mått för en virtuell dator distribueras i västra USA skickas till WestUS regionala Azure Monitor-slutpunkten. Regioninformation är också kodad i URL: en för API-anrop.

> [!NOTE]
> Anpassade mått är endast tillgänglig i flera Azure-regioner med den offentliga förhandsversionen. En lista över regioner som stöds finns i ett senare avsnitt av den här artikeln.
>
>

### <a name="timestamp"></a>Tidsstämpel
Varje datapunkt som skickas till Azure Monitor måste markeras med en tidsstämpel. Den här tidsstämpeln avbildar datum/tid då var mätvärdet mäts/samlas in. Azure Monitor ska ta emot måttdata med tidsstämplar som är så långt det är 20 minuter tidigare, och när så är 5 minuter i framtiden.

### <a name="namespace"></a>Namnrymd
Namnområden är ett sätt att kategorisera eller gruppera liknande mått. Namnområden kan du få isolering mellan grupper av mått som kan samla in olika insights eller nyckeltal. Du kan till exempel ha ett namnområde som kallas *ContosoMemoryMetrics* dvs används spåra minne Använd mått som profilera din app och en annan namnrymd som kallas *ContosoAppTransaction* som spårar alla mått om användartransaktioner i ditt program.

### <a name="name"></a>Namn
Namnet på det mått som rapporteras. Namnet är vanligtvis beskrivande för att identifiera vad som mäts. Ett mått som mäter hur många byte av minne som används på en viss virtuell dator kan till exempel ha ett Måttnamn som ”byte i minnesanvändning”.

### <a name="dimension-keys"></a>Dimensionsnycklarna
En dimension är ett nyckel/värde-par som hjälper dig att ge ytterligare egenskaper om mått som samlas in. Ytterligare egenskaper kan samla in mer information om det mått som möjliggör djupare insikter. Måttet ”byte i minnesanvändning” kan till exempel ha en dimension-nyckel som heter ”Process” som samlar in hur många byte av minne förbrukar varje process på en virtuell dator. På så sätt kan du filtrera mått att se hur mycket minne-specifika processer använder eller för att identifiera de 5 främsta processerna efter minnesanvändning.
Varje anpassat mått kan ha upp till 10 dimensioner.

### <a name="dimension-values"></a>Dimensionsvärden
Om du rapporterar ett mått datapoint för varje dimension-nyckel på mått som rapporteras, finns det en motsvarande dimensionsvärde. Exempel: Om du vill rapportera det minne som används av ContosoApp på den virtuella datorn:

* Måttnamnet är *byte av minne som används*
* Dimensionen nyckeln skulle vara *Process*
* Värdet för dimensionen är *ContosoApp.exe*

När du publicerar ett värde, kan du bara ange ett enstaka dimensionsvärde per dimension nyckel. Om du samlar in samma minnesanvändning för flera processer på den virtuella datorn kan du rapportera flera mått värden för den tidsstämpeln. Varje värde anger en annan dimension-värdet för nyckeln processen dimension.

### <a name="metric-values"></a>Måttvärden
Azure Monitor lagrar alla mått med en minuts kornighet intervall. Vi förstår att behöva ett mått samlas in flera gånger (t.ex.) CPU-användning) eller mäts för många diskreta händelser (t.ex. logga in transaktionen fördröjning) under en viss minut. Om du vill begränsa antalet rådata så måste du generera och betala för i Azure Monitor kan du lokalt före sammanställa och generera värdena:

* Min: Minst observerade värdet från alla samples/mätningar under minuten
* Max: Max observerade värdet från alla samples/mätningar under minuten
* Sum: Summan av de observerade värdena från alla exempel/mätningar under minuten
* Antal: Antal exempel/mätningar som görs under minuten

Exempel: om det finns 4 inloggning transaktioner till din app under en baserat på en minut och den resulterande uppmätta fördröjning för var och en har:

|Transaktion 1|Transaktionen 2|Transaktionen 3|Transaktionen 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

Resulterande mått publikationen till Azure Monitor är:
* Min: 4
* Max: 16
* Sum: 40
* Antal: 4

Om ditt program kan inte aggregeras i förväg lokalt och behöver skapa varje diskreta exempel eller händelse omedelbart efter samling, kan du generera rådata måttvärden.
Till exempel varje gång en inloggning transaktion uppstod på din app du vill publicera ett mått till Azure Monitor med bara ett enda mått. För en inloggning transaktion som tog 12 ms sedan måttet därför är publikationen:
* Min: 12
* Max: 12
* Sum: 12
* Antal: 1

Den här processen kan du skapa flera värden för samma mått + dimensionskombination under en viss minut. Azure Monitor tar alla rådata som genereras för en viss minut sedan och sammanställa dem tillsammans.

### <a name="sample-custom-metric-publication"></a>Exemplet anpassade mått publikationen
I följande exempel skapar du ett anpassat mått som kallas ”byte i minnesanvändning” under namnområdet mått ”minne profil” för en virtuell dator. Måttet har en enda dimension som kallas ”Process”. Vi avger måttvärden för två olika processer för den givna tidsstämpeln:

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
> Application Insights, Windows Azure Diagnostics-tillägget och InfluxData Telegraf agenten konfigurerats redan för att generera måttvärden mot rätt slutpunkt i regionala och utföra alla ovanstående egenskaper i varje koldioxidutsläpp.
>
>

## <a name="custom-metric-definitions"></a>Anpassade Måttdefinitioner
Det finns inget behov att definiera ett anpassat mått i Azure Monitor förväg innan den har genererats. Eftersom varje mått datapunkt som publicerats innehåller namnområdet, namn och dimensionsinformation, skapas första gången ett anpassat mått har genererats till Azure Monitor en måttdefinition automatiskt. Den här måttdefinition är sedan identifierbart resurser måttet utfärdades mot via definitionerna av mått.

> [!NOTE]
> Azure Monitor stöder ännu inte definiera ”Units” för ett anpassat mått.

## <a name="using-custom-metrics"></a>Med anpassade mått
När anpassade mått har skickats till Azure Monitor kan du bläddra dem via Azure portal kan skicka frågor till dem via Azure Monitor REST-API: er eller skapa aviseringar för dem så att du kan bli meddelad när vissa villkor är uppfyllda.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Bläddra bland dina anpassade mått via Azure portal
1.  Gå till [Azure Portal](https://portal.azure.com)
2.  Välj Monitor-bladet
3.  Klicka på mått
4.  Välj en resurs som du har genererat anpassade mått mot
5.  Välj det mått namnområdet för din anpassade mått
6.  Välj de anpassa mått

## <a name="supported-regions"></a>Regioner som stöds
Under den offentliga förhandsversionen finns bara möjligheten att publicera anpassade mått i en delmängd av Azure-regioner. Det innebär att mått kan bara publiceras för resurser i en av regionerna som stöds. I tabellen nedan visas uppsättning stöds Azure-regioner för anpassade mått och motsvarande slutpunkten mått för resurser i dessa regioner ska publiceras till.

|Azure-region|Regionala endpoint prefix|
|---|---|
|Östra USA|https://eastus.monitoring.azure.com/|
|Södra centrala USA|https://southcentralus.monitoring.azure.com/|
|Västra centrala USA|https://westcentralus.monitoring.azure.com/|
|Västra USA 2|https://westus2.monitoring.azure.com/|
|Sydostasien|https://southeastasia.monitoring.azure.com/|
|Norra Europa|https://northeurope.monitoring.azure.com/|
|Västra Europa|https://westeurope.monitoring.azure.com/|

## <a name="quotas-and-limits"></a>Kvoter och begränsningar
Azure Monitor inför följande användningsbegränsningar i anpassade mått.

|Kategori|Gräns|
|---|---|
|Aktiv tid serien/prenumerationer/region|50,000|
|Dimensionsnycklarna per mått|10|
|Stränglängd för mått-namnområden, Måttnamn, dimensionsnycklarna och dimensionsvärden|256 tecken|
En aktiv tidsserier definieras som en unik kombination av mått, dimension nyckel, värde som har haft måttvärden publicerade under de senaste 12 timmarna.

## <a name="next-steps"></a>Nästa steg
Använda anpassade mått från olika tjänster 
 - [Virtuell dator](metrics-store-custom-guestos-resource-manager-vm.md)
 - [Virtual Machine Scale Sets](metrics-store-custom-guestos-resource-manager-vmss.md)
 - [Virtuell dator (klassisk)](metrics-store-custom-guestos-classic-vm.md)
 - [Linux-dator med Telegraf agent](metrics-store-custom-linux-telegraf.md)
 - [REST-API](metrics-store-custom-rest-api.md)
 - [Molntjänst (klassisk)](metrics-store-custom-guestos-classic-cloud-service.md)
 