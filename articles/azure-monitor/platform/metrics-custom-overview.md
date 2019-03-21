---
title: Anpassade mått i Azure Monitor
description: Läs mer om anpassade mått i Azure Monitor och hur de modelleras.
author: ancav
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 8602027431fdf2c1378834419977606bab5c6921
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287272"
---
# <a name="custom-metrics-in-azure-monitor"></a>Anpassade mått i Azure Monitor

När du distribuerar resurser och program i Azure kan vill du börja samla in telemetri för att få insikter om prestanda och hälsa. Azure gör vissa mått tillgängliga för dig direkt. De här måtten kallas standard- eller plattform. De är dock begränsad sin natur. Du kanske vill samla in vissa anpassade nyckeltal eller företagsspecifika mått för att ge djupare insikter.
Dessa **anpassade** mått samlas in via din programtelemetri, en agent som körs på Azure-resurser eller även en utifrån övervakningssystemet och skickas direkt till Azure Monitor. När de har publicerats till Azure Monitor kan du bläddra, fråga, och Avisera om anpassade mått för dina Azure-resurser och program sida vid sida med standardmått som genereras av Azure.

## <a name="send-custom-metrics"></a>Skicka anpassade mått
Anpassade mått kan skickas till Azure Monitor via flera olika sätt:
- Instrumentera programmet med hjälp av Azure Application Insights SDK och skicka telemetri om anpassade till Azure Monitor. 
- Installera tillägget Windows Azure Diagnostics SÄKERHETSSPECIFIKA på din [Azure VM](collect-custom-metrics-guestos-resource-manager-vm.md), [virtual machine scale Sets](collect-custom-metrics-guestos-resource-manager-vmss.md), [klassisk virtuell dator](collect-custom-metrics-guestos-vm-classic.md), eller [klassiska molntjänster](collect-custom-metrics-guestos-vm-cloud-service-classic.md) och skicka prestandaräknare till Azure Monitor. 
- Installera den [InfluxData Telegraf agenten](collect-custom-metrics-linux-telegraf.md) på virtuell Linux-dator och skicka mått med hjälp av Azure Monitor utdata plugin-programmet.
- Skicka anpassade mått [direkt till Azure Monitor REST API](../../azure-monitor/platform/metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`.

När du skickar anpassade mått till Azure Monitor, varje datapunkt, eller värdet som rapporteras måste innehålla följande information.

### <a name="authentication"></a>Authentication
Om du vill skicka anpassade mått till Azure Monitor, den entitet som skickar måttet måste en giltig Azure Active Directory (Azure AD)-token i den **ägar** huvudet i begäran. Det finns ett antal sätt att hämta en giltig ägartoken som stöds:
1. [Hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Ger en identitet till en Azure-resurs, till exempel en virtuell dator. Hanterad tjänstidentitet (MSI) är utformad för att ge behörighet att utföra vissa åtgärder för resurser. Ett exempel att tillåta att en resurs att skapa mått om sig själv. En resurs eller dess MSI kan beviljas **övervakning mått Publisher** behörigheter på en annan resurs. Med den här behörigheten kan MSI generera mått för andra resurser samt.
2. [Azure AD-tjänstobjekt](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals). I det här scenariot, en Azure AD-programmet eller tjänsten, kan du tilldela behörigheter att skapa mått om en Azure-resurs.
För att autentisera begäran verifierar applikations-token med hjälp av Azure AD-offentliga nycklar i Azure Monitor. Den befintliga **övervakning mått Publisher** rollen har redan den här behörigheten. Det är tillgängligt i Azure-portalen. Tjänstens huvudnamn, beroende på vilka resurser som den genererar anpassade mått för, kan ges den **övervakning mått Publisher** rollen definitionsområdet krävs. Exempel är en prenumeration, resursgrupp eller specifik resurs.

> [!NOTE]  
> När du begär en Azure AD-token för att skapa anpassade mått kan du se till att den publik eller resursen som token begärs för är https://monitoring.azure.com/. Se till att du inkluderar avslutande ”/”.

### <a name="subject"></a>Subjekt
Den här egenskapen samlar in vilket Azure-resurs-ID som anpassade mått rapporteras för. Den här informationen ska kodas i URL: en för API-anrop som görs. Varje API kan bara skicka måttvärden för en enskild Azure-resurser.

> [!NOTE]  
> Du kan inte generera anpassade mått mot resurs-ID för en resursgrupp eller prenumeration.
>
>

### <a name="region"></a>Region
Den här egenskapen samlar in den resurs som du mått för distribueras i vilka Azure-region. Mått måste genereras i samma Azure-övervakaren regionala slutpunkten som regionen som resursen distribueras i. Anpassade mått för en virtuell dator distribueras i västra USA måste till exempel skickas till WestUS regionala Azure Monitor-slutpunkten. Regioninformation är också kodad i URL: en för API-anrop.

> [!NOTE]  
> Anpassade mått är bara tillgängliga i en delmängd av Azure-regioner under den offentliga förhandsversionen. En lista över regioner som stöds finns i ett senare avsnitt av den här artikeln.
>
>

### <a name="timestamp"></a>Tidsstämpel
Varje datapunkt som skickas till Azure Monitor måste markeras med en tidsstämpel. Den här tidsstämpeln avbildar datum/tid då mätvärdet mäts eller samlas in. Azure Monitor accepterar måttdata med tidsstämplar som är så långt det är 20 minuter tidigare och 5 minuter i framtiden. Tidsstämpeln måste vara i ISO 8601-format.

### <a name="namespace"></a>Namnområde
Namnområden är ett sätt att kategorisera eller gruppera liknande mått. Du kan uppnå isolering mellan grupper av mått som kan samla in olika insights eller nyckeltal med hjälp av namnområden. Du kan till exempel ha ett namnområde som kallas **ContosoMemoryMetrics** som spårar minnesanvändning mått som profilera din app. En annan namnrymd som kallas **ContosoAppTransaction** kan spåra alla mått om användartransaktioner i ditt program.

### <a name="name"></a>Namn
**Namn på** är namnet på det mått som rapporteras. Namnet är vanligtvis beskrivande för att identifiera vad mäts. Ett exempel är ett mått som mäter för minne byte som används på en viss virtuell dator. Det kan ha ett Måttnamn som **byte i minnesanvändning**.

### <a name="dimension-keys"></a>Dimensionsnycklarna
En dimension är en nyckel eller ett värde-par som hjälper dig att ge ytterligare egenskaper om mått som samlas in. Med ytterligare egenskaper kan du samla in mer information om mått, som möjliggör djupare insikter. Till exempel den **byte i minnesanvändning** mått kan ha en dimension-nyckel som heter **processen** som samlar in hur många byte av minne varje process på en virtuell dator använder. Med den här nyckeln kan filtrera du mått att se hur mycket minne som använder vissa processer eller för att identifiera de översta fem processerna efter minnesanvändning.
Dimensioner är valfria, inte alla mått kanske dimensioner. Ett anpassat mått kan ha upp till 10 dimensioner.

### <a name="dimension-values"></a>Dimensionsvärden
Om du rapporterar en metrisk datapunkt för varje dimension-nyckel på mått som rapporteras, finns det en motsvarande dimensionsvärde. Du kanske exempelvis vill rapportera det minne som används av ContosoApp på den virtuella datorn:

* Måttnamnet är **minne byte som används**.
* Dimensionen nyckeln skulle vara **processen**.
* Värdet för dimensionen är **ContosoApp.exe**.

När du publicerar ett värde, kan du bara ange ett enstaka dimensionsvärde per dimension nyckel. Om du samlar in samma minnesanvändning för flera processer på den virtuella datorn kan du rapportera flera mått värden för den tidsstämpeln. Varje måttvärde skulle ange en annan dimension-värde för den **processen** dimension nyckel.
Dimensioner är valfria, inte alla mått kanske dimensioner. Om ett mått inlägg definierar dimensionsnycklarna, är motsvarande värden obligatoriska.

### <a name="metric-values"></a>Måttvärden
Azure Monitor lagrar alla mått med en minuts kornighet intervall. Vi förstår att under en viss minuten, ett mått kan behöva samlas in flera gånger. Ett exempel är CPU-användning. Eller kanske måste mätas för många diskreta händelser. Ett exempel är inloggning transaktion svarstider. Om du vill begränsa antalet rådata så måste du generera och betala för i Azure Monitor kan du lokalt före sammanställa och generera värdena:

* **Min**: Det minsta observerade värdet från alla exemplen och mätning av faktisk användning under minuten.
* **Max**: Det maximala observerade värdet från alla exemplen och mätning av faktisk användning under minuten.
* **Summa**: Summan av de observerade värdena från alla exemplen och mätning av faktisk användning under minuten.
* **Antal**: Antal prover och mätningar som görs under minuten.

Exempel: om det finns 4 inloggning transaktioner till din app under ett givet en minut, den resulterande mäts svarstider för var och en kan vara på följande sätt:

|Transaktion 1|Transaktionen 2|Transaktionen 3|Transaktionen 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

Sedan blir den resulterande mått publikationen till Azure Monitor som följer:
* Min: 4
* Max: 16
* Sum: 40
* Antal: 4

Om ditt program kan inte aggregeras i förväg lokalt och behöver skapa varje diskreta exempel eller händelse omedelbart efter samling, kan du generera rådata måttvärden. Till exempel varje gång som en transaktion för inloggning som inträffar i din app du publicerar ett mått till Azure Monitor med bara ett enda mått. Så för inloggning transaktioner som tog 12 ms, mått publikationen är enligt följande:
* Min: 12
* Max: 12
* Sum: 12
* Antal: 1

Med den här processen kan du generera flera värden för samma mått plus dimensionskombination under en viss minut. Azure Monitor kan du sedan tar alla rådata som genereras för en viss minut och aggregerar dem tillsammans.

### <a name="sample-custom-metric-publication"></a>Exemplet anpassade mått publikationen
I följande exempel skapar du ett anpassat mått som kallas **minne byte som används** under mått namnområdet **minne profil** för en virtuell dator. Måttet har en enda dimension som kallas **processen**. För den givna tidsstämpeln räknar vi varje måttvärden för två olika processer:

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
> Application Insights, diagnostics-tillägg och InfluxData Telegraf agenten konfigurerats redan för att generera måttvärden mot rätt slutpunkt i regionala och utföra alla föregående egenskaper i varje koldioxidutsläpp.
>
>

## <a name="custom-metric-definitions"></a>Anpassade måttdefinitioner
Det finns inget behov av att fördefiniera ett anpassat mått i Azure Monitor innan den har genererats. Varje mått datapunkt som publicerats innehåller namnområdet, namn och dimensionsinformation. Så att den första gången ett anpassat mått har genererats till Azure Monitor, skapas automatiskt en måttdefinition. Den här måttdefinition är sedan identifierbart resurser måttet genereras mot via definitionerna av mått.

> [!NOTE]  
> Azure Monitor stöder inte ännu definiera **enheter** för ett anpassat mått.

## <a name="using-custom-metrics"></a>Med anpassade mått
När anpassade mått har skickats till Azure Monitor kan du bläddra bland dem via Azure portal och skicka frågor till dem via Azure Monitor REST-API: er. Du kan också skapa aviseringar på dem för att meddela dig när vissa villkor uppfylls.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Bläddra bland dina anpassade mått via Azure portal
1.  Gå till [Azure-portalen](https://portal.azure.com).
2.  Välj den **övervakaren** fönstret.
3.  Välj **Mått**.
4.  Välj en resurs som du har genererat anpassade mått mot.
5.  Välj det mått namnområdet för din anpassade mått.
6.  Välj den anpassade måtten.

## <a name="supported-regions"></a>Regioner som stöds
Den offentliga förhandsversionen är möjligheten att publicera anpassade mått endast tillgänglig i en delmängd av Azure-regioner. Den här begränsningen innebär att mått kan publiceras bara för resurser i en av regionerna som stöds. I följande tabell visar uppsättningen stöds Azure-regioner för anpassade mått. Här visas även de motsvarande slutpunkter som mått för resurser i dessa regioner ska publiceras till:

|Azure-region|Regionala endpoint prefix|
|---|---|
|Östra USA| https:\//eastus.monitoring.azure.com/ |
|Södra centrala USA| https:\//southcentralus.monitoring.azure.com/ |
|Västra centrala USA| https:\//westcentralus.monitoring.azure.com/ |
|Västra USA 2| https:\//westus2.monitoring.azure.com/ |
|Sydostasien| https:\//southeastasia.monitoring.azure.com/ |
|Norra Europa| https:\//northeurope.monitoring.azure.com/ |
|Västra Europa| https:\//westeurope.monitoring.azure.com/ |

## <a name="quotas-and-limits"></a>Kvoter och begränsningar
Azure Monitor inför följande användningsbegränsningar i anpassade mått:

|Kategori|Gräns|
|---|---|
|Tid serien/prenumerationer/region|50,000|
|Dimensionsnycklarna per mått|10|
|Stränglängd för mått-namnområden, Måttnamn, dimensionsnycklarna och dimensionsvärden|256 tecken|

En aktiv tidsserier definieras som en unik kombination av mått, dimension nyckel och värde som har haft måttvärden publicerade under de senaste 12 timmarna.

## <a name="next-steps"></a>Nästa steg
Använda anpassade mått från olika tjänster: 
 - [Virtual Machines](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Virtual machine scale Sets](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure-datorer (klassisk)](collect-custom-metrics-guestos-vm-classic.md)
 - [Linux-dator med hjälp av Telegraf-agent](collect-custom-metrics-linux-telegraf.md)
 - [REST-API](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [Klassiska molntjänster](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 
