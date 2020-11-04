---
title: Paritet mellan offentliga och suveräna regioner
titleSuffix: Azure Machine Learning
description: Den här artikeln innehåller en lista över paritet mellan offentliga moln och 21Vianet-regionerna Azure Government, Azure Germany och Azure Kina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 08/24/2020
ms.custom: references_regions
ms.openlocfilehash: 0300dab1fa7d0ef0d20c4236cc452135b3ade20e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305746"
---
# <a name="azure-machine-learning-sovereign-cloud-parity"></a>Azure Machine Learning suveräna moln paritet

Lär dig mer om vilka Azure Machine Learning funktioner som är tillgängliga i de suveräna moln regionerna. 

I listan över globala Azure-regioner finns det flera "suverän"-regioner som hanterar vissa marknader. Till exempel Azure Government och Azure Kina 21Vianet-regionerna. För närvarande Azure Machine Learning distribueras till följande suveräna moln regioner:

* Azure Government regionerna **US-Arizona** och **US-Virginia**.
* Azure Kina 21Vianet region **Kina – öst-2**.

> [!TIP]
> För att skilja mellan suveräna och icke-suveräna regioner använder den här artikeln termen __offentligt moln__ för att referera till icke-suveräna regioner.

Vi strävar efter att tillhandahålla maximal paritet mellan våra offentliga moln och suveräna regioner. Alla Azure Machine Learning funktioner är tillgängliga i dessa regioner inom **30 dagar från ga** (allmän tillgänglighet) i vårt offentliga moln. Vi aktiverar också ett SELECT-antal för hands versions funktioner i dessa regioner. Nedan visas aktuella paritets skillnader mellan våra suveräna och offentliga moln.

## <a name="azure-government"></a>Azure Government 

| Funktion | Status för offentligt moln  | US-Virginia | US-Arizona| 
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|:-------------:|
| **Automatiserad maskininlärning** | | | |
| Skapa och köra experiment i antecknings böcker                                    | Allmän tillgänglighet (GA)                   | JA                | JA         |
| Skapa och köra experiment i Studio Web Experience                        | Offentlig för hands version       | JA                | JA         |
| Branschledande prognos funktioner                                  | Allmän tillgänglighet (GA)                   | JA                | JA         |
| Stöd för djup inlärning och andra avancerade lärare                      | Allmän tillgänglighet (GA)                   | JA                | JA         |
| Stöd för stora data mängder (upp till 100 GB)                                          | Offentlig för hands version       | JA                | JA         |
| Azure Databricks-integrering                                              | Allmän tillgänglighet (GA)                   | NO                 | NO          |
| SQL-, CosmosDB-och HDInsight-integreringar                                   | Allmän tillgänglighet (GA)                   | JA                | JA         |
| **Machine Learning pipelines** |   |  | | 
| Skapa, köra och publicera pipelines med Azure ML SDK                   | Allmän tillgänglighet (GA)                   | JA                | JA         |
| Skapa pipeline-slutpunkter med Azure ML SDK                           | Allmän tillgänglighet (GA)                   | JA                | JA         |
| Skapa, redigera och ta bort schemalagda körningar av pipelines med Azure ML SDK | Allmän tillgänglighet (GA)                   | Ja               | Ja        |
| Visa körnings information för pipeline i Studio                                        | Allmän tillgänglighet (GA)                   | JA                | JA         |
| Skapa, köra, visualisera och publicera pipeliner i Azure ML designer          | Allmän tillgänglighet (GA)      | JA                | JA         |
| Azure Databricks integrering med ML pipeline                             | Allmän tillgänglighet (GA)                   | NO                 | NO          |
| Skapa pipeline-slutpunkter i Azure ML-designer                             | Allmän tillgänglighet (GA)      | JA                | JA         |
| **Integrerade antecknings böcker** |   |  | | 
| Arbets ytans bärbara och fildelning                                        | Allmän tillgänglighet (GA)                   | JA                | JA         |
| R-och python-stöd                                                       | Allmän tillgänglighet (GA)                   | JA                | JA         |
| Stöd för virtuellt nätverk                                                    | Offentlig för hands version       | NO                 | NO          |
| **Beräkninsinstans** |   |  | | 
| Hanterade beräknings instanser för integrerade antecknings böcker                         | Allmän tillgänglighet (GA)                   | JA                | JA         |
| Jupyter, JupyterLab-integrering                                            | Allmän tillgänglighet (GA)                   | JA                | JA         |
| Stöd för Virtual Network (VNet)                                             | Offentlig för hands version       | JA                | JA         |
| **SDK-support** |  |  | | 
| Stöd för R SDK                                                              | Offentlig för hands version       | JA                | JA         |
| Stöd för python SDK                                                         | Allmän tillgänglighet (GA)                   | JA                | JA         |
| **Säkerhet** |   | | | 
| Stöd för Virtual Network (VNet) för utbildning                                | Allmän tillgänglighet (GA)                   | JA                | JA         |
| Stöd för Virtual Network (VNet) för härledning                               | Allmän tillgänglighet (GA)                   | JA                | JA         |
| Beräknings slut punktens autentisering                                            | Offentlig för hands version       | JA                | JA         |
| Privat arbets plats länk                                                     | Offentlig för hands version       | NO                 | NO          |
| ACI bakom VNet                                                            | Offentlig för hands version       | NO                 | NO          |
| ACR bakom VNet                                                            | Offentlig för hands version       | NO                 | NO          |
| Privat IP för AKS-kluster                                                  | Offentlig för hands version       | NO                 | NO          |
| **Beräkning** |   | | |
| kvot hantering över arbets ytor                                         | Allmän tillgänglighet (GA)                   | JA                | JA         |
| **Data för Machine Learning** |   | | |
| Skapa, Visa eller redigera data uppsättningar och data lager från SDK: n                  | Allmän tillgänglighet (GA)                   | JA                | JA         |
| Skapa, Visa eller redigera data uppsättningar och data lager från användar gränssnittet                   | Allmän tillgänglighet (GA)                   | JA                | JA         |
| Visa, redigera eller ta bort data uppsättnings drift övervakare från SDK                   | Offentlig för hands version       | JA                | JA         |
| Visa, redigera eller ta bort data uppsättnings avvikelse övervakare från användar gränssnittet                    | Offentlig för hands version       | JA                | JA         |
| **Machine Learning-livscykel** |   | | |
| Modell profilering                                                            | Allmän tillgänglighet (GA)                   | JA                | SIGNATUR     |
| Azure DevOps-tillägget för Machine Learning & Azure ML CLI         | Allmän tillgänglighet (GA)                   | JA                | JA         |
| FPGA-baserade Maskinvaruaccelererade modeller                                     | Allmän tillgänglighet (GA)                   | NO                 | NO          |
| Visual Studio-kod integrering                                             | Offentlig för hands version       | NO                 | NO          |
| Event Grid-integrering                                                     | Offentlig för hands version       | NO                 | NO          |
| Integrera Azure Stream Analytics med Azure Machine Learning               | Offentlig för hands version       | NO                 | NO          |
| **Märkning** |   | | |
| Märka projekt Hanteringsportal                                        | Allmän tillgänglighet (GA)                   | JA                | JA         |
| Labeler-portalen                                                            | Allmän tillgänglighet (GA)                   | JA                | JA         |
| Etikettera med privat personal styrka                                          | Allmän tillgänglighet (GA)                   | JA                | JA         |
| ML assisterad märkning (bild klassificering och objekt identifiering)           | Offentlig för hands version       | JA                | JA         |
| **Ansvarsfull ML** |   | | |
| Förklaring i användar gränssnittet                                                       | Offentlig för hands version       | NO                 | NO          |
| WhiteNoise Toolkit för differentiell sekretess                                    | OSS                  | NO                 | NO          |
| anpassade taggar i Azure Machine Learning för att implementera datablad              | Allmän tillgänglighet (GA)                   | NO                 | NO          |
| Skälighet AzureML-integrering                                               | Offentlig för hands version       | NO                 | NO          |
| Tolknings-SDK                                                      | Allmän tillgänglighet (GA)                   | JA                | JA         |
| **Utbildning** |   | | |
| Strömning i experiment loggen                                              | Allmän tillgänglighet (GA)                   | JA                | JA         |
| Förstärka inlärning                                                     | Offentlig för hands version       | NO                 | NO          |
| Experimentering gränssnitt                                                         | Allmän tillgänglighet (GA)                   | JA                | JA         |
| .NET-integrering ML.NET 1,0                                                | Allmän tillgänglighet (GA)                   | JA                | JA         |
| **Störningar** |   | | |
| Batch-inferencing                                                          | Allmän tillgänglighet (GA)                   | JA                | JA         |
| Data Box Edge med FPGA                                                    | Offentlig för hands version       | NO                 | NO          |
| **Övrigt** |   | | |
| Open Datasets                                                              | Offentlig för hands version       | JA                | JA         |
| Anpassad Kognitiv sökning                                                    | Offentlig för hands version       | JA                | JA         |
| Många modeller                                                                | Offentlig för hands version       | NO                 | NO          |


### <a name="azure-government-scenarios"></a>Azure Government scenarier

| Scenario                                                    | US-Virginia | US-Arizona| Begränsningar  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **Allmän säkerhets inställning** |   | | |
| Privat nätverkskommunikation mellan tjänster                                     | NO | NO | Ingen privat länk för närvarande | 
| Inaktivera/kontrol lera Internet åtkomst (inkommande och utgående) och särskilt VNet | SIGNATUR| SIGNATUR   | ACR bakom VNet är inte tillgängligt i Azure Government-dubbel kontroll av ACI | 
| Placering för alla associerade resurser/tjänster  | JA | JA |  |
| Kryptering i vila och under överföring.                                                 | JA | JA |  |
| Rot-och SSH-åtkomst till beräknings resurser.                                          | JA | JA |  |
| Upprätthålla säkerheten för distribuerade system (instanser, slut punkter osv.), inklusive Endpoint Protection, uppdatering och loggning |  SIGNATUR|  SIGNATUR |ACI bakom VNet och privat slut punkt är inte tillgänglig för närvarande |                                  
| Kontroll (inaktivera/begränsa/begränsa) användning av ACI/AKS-integrering                    | SIGNATUR| SIGNATUR |ACI bakom VNet och privat slut punkt är inte tillgänglig för närvarande|
| Rollbaserad åtkomst kontroll i Azure (Azure RBAC) – skapa anpassade roller                           | JA | JA |  |
| Kontrol lera åtkomsten till ACR-avbildningar som används av ML-tjänsten (Azure tillhandahålls/underhålls respektive anpassad)  |SIGNATUR|  SIGNATUR | ACR bakom privat slut punkt och VNet stöds inte i Azure Government |
| **Allmän Machine Learning tjänst användning** |  | | |
| Möjlighet att ha en utvecklings miljö för att skapa en modell, träna den modellen, vara värd för den som en slut punkt och använda den via en webapp     | JA | JA |  |
| Möjlighet att hämta data från ADLS (Data Lake Storage)                                 |JA | JA |  |
| Möjlighet att hämta data från Azure Blob Storage                                       |JA | JA |  |



### <a name="additional-azure-government-limitations"></a>Ytterligare Azure Government begränsningar

* För Azure Machine Learning beräknings instanser är möjligheten att uppdatera en token som varar mer än 24 timmar inte tillgänglig i Azure Government.
* Modell profilering stöder inte 4 processorer i den US-Arizona regionen.   
* Exempel på bärbara datorer kanske inte fungerar i Azure Government om de behöver åtkomst till offentliga data.
* IP-adresser: CLI-kommandot som används i instruktionerna för [VNet och Tvingad tunnel trafik](how-to-secure-training-vnet.md#forced-tunneling) returnerar inte IP-intervall. Använd [Azure IP-intervall och service märken för Azure Government](https://www.microsoft.com/download/details.aspx?id=57063) i stället.
* För schemalagda pipeliner ger vi också en BLOB-baserad utlösnings funktion. Den här mekanismen stöds inte för CMK-arbetsytor. Om du vill aktivera en BLOB-baserad utlösare för CMK-arbetsytor måste du göra ytterligare inställningar. Mer information finns i [utlösa en körning av en Machine Learning-pipeline från en Logic app](how-to-trigger-published-pipeline.md).
* Brand väggar: när du använder en Azure Government Region lägger du till följande ytterligare värdar i brand Väggs inställningen:

    * För Arizona använder du: `usgovarizona.api.ml.azure.us`
    * För Virginia-användning: `usgovvirginia.api.ml.azure.us`
    * För båda: `graph.windows.net` 


## <a name="azure-china-21vianet"></a>Azure Kina 21Vianet 

| Funktion                                       | Status för offentligt moln | CH-öst-2 | CH-Nord-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **Automatiserad maskininlärning** |    | | |
| Skapa och köra experiment i antecknings böcker                                    | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| Skapa och köra experiment i Studio Web Experience                        | Offentlig för hands version   | JA       | E.t.        |
| Branschledande prognos funktioner                                  | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| Stöd för djup inlärning och andra avancerade lärare                      | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| Stöd för stora data mängder (upp till 100 GB)                                          | Offentlig för hands version   | JA       | E.t.        |
| Azure Databricks-integrering                                              | Allmän tillgänglighet (GA)               | NO        | E.t.        |
| SQL-, CosmosDB-och HDInsight-integreringar                                   | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| **Machine Learning pipelines** |    | | |
| Skapa, köra och publicera pipelines med Azure ML SDK                   | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| Skapa pipeline-slutpunkter med Azure ML SDK                           | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| Skapa, redigera och ta bort schemalagda körningar av pipelines med Azure ML SDK | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| Visa körnings information för pipeline i Studio                                        | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| Skapa, köra, visualisera och publicera pipeliner i Azure ML designer          | Allmän tillgänglighet (GA)  | JA       | E.t.        |
| Azure Databricks integrering med ML pipeline                             | Allmän tillgänglighet (GA)               | NO        | E.t.        |
| Skapa pipeline-slutpunkter i Azure ML-designer                             | Allmän tillgänglighet (GA)   | JA       | E.t.        |
| **Integrerade antecknings böcker** |   | | |
| Arbets ytans bärbara och fildelning                                        | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| R-och python-stöd                                                       | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| Stöd för virtuellt nätverk                                                    | Offentlig för hands version   | NO        | E.t.        |
| **Beräkninsinstans** |    | | |
| Hanterade beräknings instanser för integrerade antecknings böcker                         | Allmän tillgänglighet (GA)               | NO        | E.t.        |
| Jupyter, JupyterLab-integrering                                            | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| Stöd för Virtual Network (VNet)                                             | Offentlig för hands version   | JA       | E.t.        |
| **SDK-support** |    | | |
| Stöd för R SDK                                                              | Offentlig för hands version   | JA       | E.t.        |
| Stöd för python SDK                                                         | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| **Säkerhet** |   | | |
| Stöd för Virtual Network (VNet) för utbildning                                | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| Stöd för Virtual Network (VNet) för härledning                               | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| Beräknings slut punktens autentisering                                            | Offentlig för hands version   | JA       | E.t.        |
| Privat arbets plats länk                                                     | Offentlig för hands version   | NO        | E.t.        |
| ACI bakom VNet                                                            | Offentlig för hands version   | NO        | E.t.        |
| ACR bakom VNet                                                            | Offentlig för hands version   | NO        | E.t.        |
| Privat IP för AKS-kluster                                                  | Offentlig för hands version   | NO        | E.t.        |
| **Beräkning** |   | | |
| kvot hantering över arbets ytor                                         | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| **Data för Machine Learning** | | | |
| Skapa, Visa eller redigera data uppsättningar och data lager från SDK: n                  | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| Skapa, Visa eller redigera data uppsättningar och data lager från användar gränssnittet                   | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| Visa, redigera eller ta bort data uppsättnings drift övervakare från SDK                   | Offentlig för hands version   | JA       | E.t.        |
| Visa, redigera eller ta bort data uppsättnings avvikelse övervakare från användar gränssnittet                    | Offentlig för hands version   | JA       | E.t.        |
| **Machine Learning-livscykel** |    | | |
| Modell profilering                                                            | Allmän tillgänglighet (GA)               | SIGNATUR   | E.t.        |
| Azure DevOps-tillägget för Machine Learning & Azure ML CLI         | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| FPGA-baserade Maskinvaruaccelererade modeller                                     | Allmän tillgänglighet (GA)               | NO        | E.t.        |
| Visual Studio-kod integrering                                             | Offentlig för hands version   | NO        | E.t.        |
| Event Grid-integrering                                                     | Offentlig för hands version   | JA       | E.t.        |
| Integrera Azure Stream Analytics med Azure Machine Learning               | Offentlig för hands version   | NO        | E.t.        |
| **Märkning** |    | | |
| Märka projekt Hanteringsportal                                        | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| Labeler-portalen                                                            | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| Etikettera med privat personal styrka                                          | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| ML assisterad märkning (bild klassificering och objekt identifiering)           | Offentlig för hands version   | JA       | E.t.        |
| **Ansvarsfull ML** |    | | |
| Förklaring i användar gränssnittet                                                       | Offentlig för hands version   | NO        | E.t.        |
| WhiteNoise Toolkit för differentiell sekretess                                    | OSS              | NO        | E.t.        |
| anpassade taggar i Azure Machine Learning för att implementera datablad              | Allmän tillgänglighet (GA)               | NO        | E.t.        |
| Skälighet AzureML-integrering                                               | Offentlig för hands version   | NO        | E.t.        |
| Tolknings-SDK                                                      | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| **Utbildning** |    | | |
| Strömning i experiment loggen                                              | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| Förstärka inlärning                                                     | Offentlig för hands version   | NO        | E.t.        |
| Experimentering gränssnitt                                                         | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| .NET-integrering ML.NET 1,0                                                | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| **Störningar** |   | | |
| Batch-inferencing                                                          | Allmän tillgänglighet (GA)               | JA       | E.t.        |
| Data Box Edge med FPGA                                                    | Offentlig för hands version   | NO        | E.t.        |
| **Övrigt** |    | | |
| Open Datasets                                                              | Offentlig för hands version   | JA       | E.t.        |
| Anpassad Kognitiv sökning                                                    | Offentlig för hands version   | JA       | E.t.        |
| Många modeller                                                                | Offentlig för hands version   | NO        | E.t.        |



### <a name="additional-azure-china-limitations"></a>Ytterligare begränsningar i Azure Kina

* Azure Kina har begränsad VM-SKU, särskilt för GPU SKU. Den har bara NCv3 Family (V100).
* REST API slut punkter skiljer sig från globala Azure. Använd följande tabell för att hitta REST API slut punkten för regionerna i Azure Kina:

    | REST-slutpunkt                 | Global Azure                                 | China-Government                           |
    |------------------|--------------------------------------------|--------------------------------------------|
    | Hanteringsplanet | `https://management.azure.com/`              | `https://management.chinacloudapi.cn/`       |
    | Dataplanet       | `https://{location}.experiments.azureml.net` | `https://{location}.experiments.ml.azure.cn` |
    | Azure Active Directory              | `https://login.microsoftonline.com`          | `https://login.chinacloudapi.cn`             |

* Exempel datorn kanske inte fungerar, om den behöver åtkomst till offentliga data.
* IP-adressintervall: CLI-kommandot som används i instruktionerna för [VNet-Tvingad tunnel](how-to-secure-training-vnet.md#forced-tunneling) returnerar inte IP-intervall. Använd [Azures IP-adressintervall och service märken för Azure Kina](https://www.microsoft.com//download/details.aspx?id=57062) i stället.
* För hands versionen av Azure Machine Learning beräknings instanser stöds inte i en arbets yta där privat länk är aktiverat för tillfället, men CI kommer att stödjas i nästa distribution för tjänst expansionen till alla AML-regioner.

## <a name="next-steps"></a>Nästa steg

Mer information om de regioner som Azure Machine Learning är tillgängligt i finns i [produkter efter region](https://azure.microsoft.com/global-infrastructure/services/).
