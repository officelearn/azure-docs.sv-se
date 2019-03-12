---
title: Företagssäkerhet
titleSuffix: Azure Machine Learning service
description: 'Använda Azure Machine Learning-tjänsten: autentisering, auktorisering, nätverkssäkerhet, datakryptering och övervakning.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/10/2019
ms.openlocfilehash: b950e7d38235d089c6236c76136d8ec2fc7a1f74
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731325"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Företagssäkerhet för Azure Machine Learning-tjänsten

I den här artikeln lär du dig att säkerhetsfunktioner som är tillgängliga med de Azure-Machine learning-tjänsten.

När du använder en molnbaserad tjänst, är det en bra idé att begränsa åtkomst till de användare som behöver den. Detta startar genom att förstå autentisering och auktorisering modellen som används av tjänsten. Du kanske också vill begränsa nätverksåtkomst eller på ett säkert sätt ansluta till resurser i det lokala nätverket med de som finns i molnet. Kryptering är också viktigt, både i vila och vid informationen flyttas mellan tjänster. Slutligen måste kunna övervaka tjänsten och skapar en granskningslogg för all aktivitet.

## <a name="authentication"></a>Authentication
Multi-Factor-autentisering stöds om Azure Active Directory (Azure AD) är konfigurerad för samma.
* Klienten loggar in på Azure AD och hämtar Azure Resource Manager-token.  Användare och tjänsthuvudnamn stöds fullt ut.
* Klienten presenterar token till Azure Resource Manager och alla Azure Machine Learning-tjänster
* Azure Machine Learning-tjänsten tillhandahåller en Azure Machine Learning-token till användaren beräkning. Till exempel beräkning av Machine Learning. Den här token används av användaren i Azure Machine Learning compute återuppringning i Azure Machine Learning-tjänsten (begränsar omfattningen till arbetsytan) när körningen är klar.

![Skärmbild som visar hur autentisering fungerar i Azure Machine Learning-tjänsten](./media/enterprise-readiness/authentication.png)

### <a name="authentication-keys-for-web-service-deployment"></a>Nycklar för autentisering för distribution av webbtjänster

När du aktiverar autentisering för en distribution kan skapa du automatiskt autentiseringsnycklar.

* Autentisering är aktiverad som standard när du distribuerar till Azure Kubernetes Service.
* Autentisering är inaktiverad som standard när du distribuerar till Azure Container Instances.

För att styra autentisering, använder de `auth_enabled` parameter när du skapar eller uppdaterar en distribution.

Om autentisering har aktiverats, kan du använda den `get_keys` metod för att hämta en primära och sekundära autentiseringsnyckel:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Om du vill återskapa en nyckel kan du använda [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)


## <a name="authorization"></a>Auktorisering

Du kan skapa flera arbetsytor och varje arbetsyta kan delas av flera personer. När du delar en arbetsyta, kan du styra åtkomsten till den genom att tilldela användarna följande roller:
* Ägare
* Deltagare
* Läsare
    
I följande tabell visas några av logg för Azure Machine Learning-tjänsten och de roller som kan utföra dem:

| Azure Machine Learning-tjänsten igen | Ägare | Deltagare | Läsare |
| ---- |:----:|:----:|:----:|
| Skapa arbetsyta | ✓ | ✓ | |
| Dela arbetsyta | ✓ | |  |
| Skapa beräkning | ✓ | ✓ | |
| Bifoga beräkning | ✓ | ✓ | |
| Bifoga datalager | ✓ | ✓ | |
| Kör ett Experiment | ✓ | ✓ | |
| Visa körningar/mått | ✓ | ✓ | ✓ |
| Registrera modellen | ✓ | ✓ | |
| Skapa avbildning | ✓ | ✓ | |
| Distribuera webbtjänst | ✓ | ✓ | |
| Visa modeller/bilder | ✓ | ✓ | ✓ |
| Anropa webbtjänst | ✓ | ✓ | ✓ |

Om de inbyggda rollerna är tillräckligt för dina behov kan skapa du också anpassade roller. Observera att endast anpassade roller som vi har stöd för åtgärder på arbetsytan och beräkning av Machine Learning. Anpassade roller kan ha läsa, skriva eller ta bort behörigheter för arbetsytan och beräkningsresursen på arbetsytan. Du kan göra rollen på en viss arbetsyta-nivå, en specifik resursgruppsnivå eller en specifik prenumerationsnivå. Mer information finns i [hantera användare och roller i en Azure Machine Learning-arbetsyta](how-to-assign-roles.md)

### <a name="securing-compute-and-data"></a>Att säkra beräknings- och data
Ägare och deltagare kan använda alla beräkning mål och datalager som är kopplade till arbetsytan.  
Varje arbetsyta har också associerade systemtilldelade hanterade identiteter (med samma namn som arbetsytan) med följande behörigheter på anslutna resurser som används i arbetsytan:

Mer information om hanterade identiteter finns [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| Resurs | Behörigheter |
| ----- | ----- |
| Arbetsyta | Deltagare | 
| Lagringskonto | Storage Blob Data-deltagare | 
| Key Vault | Åtkomst till alla nycklar, hemligheter, certifikat | 
| Azure Container Registry | Deltagare | 
| Resursgruppen som innehåller arbetsytan | Deltagare | 
| Resursgruppen som innehåller Nyckelvalvet (om det är annorlunda än den som innehåller arbetsytan) | Deltagare | 

Vi rekommenderar att administratörer inte återkalla åtkomst till den hanterade identitet till de resurser som nämns ovan. Åtkomst kan återställas med åtgärden synkronisera om nycklar.

Azure Machine Learning-tjänsten skapar en tilläggsapplikation (namn börjar med aml-) med deltagare administratörsnivå i din prenumeration för varje arbetsyteregion. För t.ex. Om du har en arbetsyta i östra USA och en annan arbetsyta i Norra Europa i samma prenumeration visas 2 sådana program. Detta krävs så att Azure Machine Learning-tjänsten kan hantera beräkningsresurser.


## <a name="network-security"></a>Nätverkssäkerhet

Azure Machine Learning-tjänsten är beroende av andra Azure-tjänster för compute-resurser. Beräkningsresurser (beräkningsmål) används för att träna och distribuera modeller. Dessa beräkningsalternativ mål kan skapas i ett virtuellt nätverk. Du kan till exempel använda Microsoft Data Science Virtual Machine för att träna en modell och distribuerar sedan modellen till Azure Kubernetes Service (AKS).  

Mer information finns i [hur du kör experiment och inferensjobb i ett virtuellt nätverk](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Datakryptering

### <a name="encryption-at-rest"></a>Vilande kryptering
#### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure Machine Learning-tjänsten lagrar ögonblicksbilder, utdata och loggar i Azure Blob Storage-konto som är kopplat till arbetsytan Azure Machine Learning-tjänsten och finns i användarens prenumeration. Alla data som lagras i Azure Blob Storage krypteras i viloläge med hjälp av Microsoft-Managed nycklar.

Mer information om hur du hanterar dina egna nycklar för data som lagras i Azure Blob Storage finns i [kryptering av lagringstjänst med Kundhanterade nycklar i Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Finjusteringsdata också lagras vanligtvis i Azure Blob storage så att den är tillgänglig för utbildning-beräkning. Den här lagringen är inte hanteras av Azure Machine Learning men monterad för att beräkna som en fjärrfil-system.

#### <a name="cosmos-db"></a>Cosmos DB
Azure Machine Learning-tjänsten lagrar mått och metadata till Cosmos DB som finns i en Microsoft-prenumeration som hanteras av Azure Machine Learning-tjänsten. Alla data som lagras i Cosmos DB krypteras i vila med hjälp av Microsoft-hanterade nycklar.

#### <a name="azure-container-registry-acr"></a>Azure Container Registry (ACR)
Alla avbildningar i registret (ACR) krypteras i vila. Azure krypterar en avbildning innan de lagras och dekrypterar dem på direkt när Azure Machine Learning-tjänsten hämtar avbildningen automatiskt.

#### <a name="machine-learning-compute"></a>Machine Learning-beräkning
OS-disken krypteras för varje beräkningsnod lagras i Azure Storage med Microsoft-hanterade nycklar i Azure Machine Learning-tjänsten storage-konton. Den här beräkningen är tillfälliga och kluster vanligtvis skalas när det finns inga körningar i kö. Den underliggande virtuella datorn enhetstiden och OS-disken tas bort. Azure-diskkryptering stöds inte för OS-disken.
Varje virtuell dator har också en lokal temporär disk för OS-åtgärder. Den här disken kan också användas om du vill att steget träningsdata. Den här disken krypteras inte. Mer information om så här fungerar kryptering i vila i Azure finns i [Azure Data kryptering vid vila](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest). 

### <a name="encryption-in-transit"></a>Kryptering under överföring
Både intern kommunikation mellan olika mikrotjänster i Azure Machine Learning och extern kommunikation för att anropa bedömnings slutpunkten stöds med hjälp av SSL. All åtkomst i Azure Storage är också över en säker kanal. Mer information finns i [säkra Azure Machine Learning-webbtjänster som använder SSL](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Med Azure Key Vault
Key Vault-instansen som är kopplade till arbetsytan används av Azure Machine Learning-tjänsten för att lagra autentiseringsuppgifter för olika typer:
* Associerade lagringskontots anslutningssträng
* Lösenord till lagringsplatsen för Azure Container instances
* Anslutningen strängar till data butiker. 

SSH-lösenord och nycklar för att beräkna mål, till exempel virtuella datorer och HDI HDInsight lagras i ett separat Nyckelvalv som är associerad med Microsoft-prenumeration. Azure Machine Learning-tjänsten lagrar eventuella lösenord eller nycklar anges av användaren i stället det genererar, auktoriserar och lagrar sin egen SSH-nycklar för att ansluta till VM/HDInsight för att köra experiment. Varje arbetsyta har en associerad systemtilldelade hanterad identitet (med samma namn som arbetsytan) som har åtkomst till alla nycklar, hemligheter och certifikat i Key Vault.

 
## <a name="monitoring"></a>Övervakning

Användarna kan se aktivitetsloggen under arbetsytan för att se hur olika åtgärder som utförs på arbetsytan och få grundläggande information som åtgärdens namn, händelsen startades av, tidsstämpel osv.

Följande skärmbild visar aktivitetsloggen för en arbetsyta:

![Skärmbild som visar aktivitetsloggen under en arbetsyta](./media/enterprise-readiness/workspace-activity-log.png)


Bedömnings Frågedetaljer lagras i AppInsights som skapats i användarens prenumeration när du skapar arbetsytan. Detta inkluderar fält som HTTPMethod, UserAgent, ComputeType, RequestUrl, StatusCode, RequestId, varaktighet osv.


## <a name="data-flow-diagram"></a>Dataflödesdiagram

### <a name="create-workspace"></a>Skapa arbetsyta
Följande diagram visar arbetsflödet skapa arbetsytan.
Användaren loggar in på Azure AD från någon av de Azure Machine Learning-tjänsten klienterna som stöds (CLI, Python SDK, Azure portal) och begär lämplig Azure Resource Manager-token.  Användaren anropar sedan Azure Resource Manager för att skapa arbetsytan.  Azure Resource Manager-kontakter Azure Machine Learning-tjänsten Resource Provider för att etablera arbetsytan.  Ytterligare resurser skapas i kundens prenumeration när arbetsytan skapas:
* KeyVault (för att lagra hemligheter)
* Azure Storage-kontot (inklusive Blob & filresurs)
* Azure Container Registry (för att lagra docker-avbildningar för inferensjobb och experimentering)
* Application Insights (för att lagra telemetri)

Andra beräkningarna som är kopplade till en arbetsyta (Azure Kubernetes Service, VM etc.) kan också etableras av kunder efter behov. 

![Skärmbild som visar skapa arbetsytan arbetsflöde](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>Spara källkoden (utbildningsskript)
Följande diagram visar kod ögonblicksbild arbetsflödet.
Som är associerade med en Azure Machine Learning-tjänstens arbetsyta är kataloger (försök), som innehåller källkod (utbildningsskript).  De lagras på kundens lokala datorn och i molnet (i Azure Blob Storage under kundens prenumeration). De här ögonblicksbilderna kod används för körning eller inspektion för historiska granskning.

![Skärmbild som visar skapa arbetsytan arbetsflöde](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>Utbildning
Följande diagram visar arbetsflödet för utbildning.
* Azure Machine Learning-tjänsten anropas med ögonblicksbild-ID för kod ögonblicksbilden sparade ovan
* Azure Machine Learning-tjänsten skapar kör ID (valfritt) och Azure Machine Learning-tjänsttoken, som senare används av beräkningsmål som Machine Learning beräkning/VM tala tillbaka till Azure Machine Learning-tjänsten
* Du kan välja antingen en hanterad beräkning (ex.) Machine Learning-beräkning) eller ohanterade beräkning (ex.) VM) för att köra dina utbildningsjobb. Dataflödet förklaras för båda scenarierna nedan:
* (Virtuell dator/HDInsight/lokal – nås med hjälp av SSH-inloggningsuppgifter i Key Vault i Microsoft-prenumeration) Azure Machine Learning-tjänsten körs hanteringskod på beräkningsmål som:
    1.  Förbereder miljön (Observera: Docker är också ett alternativ för VM/lokal. Se steg för beräkning av Machine Learning nedan att förstå hur pågående experiment på docker container fungerar)
    2.  Hämtar koden
    3.  Ställer in miljön variabler/konfigurationer
    4.  Kör användarskript (kod snapshot som nämns ovan)
* (Machine Learning beräkning – nås med hjälp av arbetsytan hanterad identitet) Observera att eftersom beräkning av Machine Learning är en hanterad beräkning det vill säga den hanteras av Microsoft, därmed den körs i Microsoft-prenumeration.
    1.  Fjärransluten Docker-konstruktion har startats om det behövs
    2.  Skriver management kod till användarens Azure-filresursen
    3.  Startar behållaren med inledande kommandot det vill säga hanteringskod i steget ovan


#### <a name="querying-runs--metrics"></a>Frågor körs och statistik
Det här steget visas i flödet där utbildning compute skrivningar i *kör mått* tillbaka till Azure Machine Learning-tjänsten från där den lagras i Cosmos DB. Klienter kan anropa Azure Machine Learning-tjänsten som i sin tur använder mått från Cosmos-DB och returnera den tillbaka till klienten.

![Skärmbild som visar skapa arbetsytan arbetsflöde](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>Skapa webbtjänster
Följande diagram visar inferensjobb arbetsflödet där modellen har distribuerats som en webbtjänst.
Se information nedan:
* Användare registrerar en modell som använder en klient som Azure ML-SDK
* Användare skapar avbildning med hjälp av modellen, poäng fil och andra modell-beroenden
* Docker-avbildningen skapas och lagras i ACR
* Webbtjänsten har distribuerats till beräkningsmål (ACI/AKS) med den avbildning som skapades ovan
* Bedömnings Frågedetaljer lagras i AppInsights som finns i användarens prenumeration
* Telemetri skickas även till Microsoft/Azure-prenumeration

![Skärmbild som visar skapa arbetsytan arbetsflöde](./media/enterprise-readiness/inferencing.png)

## <a name="next-steps"></a>Nästa steg

* [Skydda Azure Machine Learning-webbtjänster med SSL](how-to-secure-web-service.md)
* [Använd en ML-modell som distribueras som en webbtjänst](how-to-consume-web-service.md)
* [Hur du kör batch-förutsägelser](how-to-run-batch-predictions.md)
* [Övervaka dina Azure Machine Learning-modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Azure Machine Learning service SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Använda Azure Machine Learning-tjänsten med Azure-nätverk](how-to-enable-virtual-network.md)
* [Metodtips för att bygga rekommendationssystem](https://github.com/Microsoft/Recommenders)
* [Skapa en i realtid rekommendation API på Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
