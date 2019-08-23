---
title: Företagssäkerhet
titleSuffix: Azure Machine Learning service
description: 'Använd Azure Machine Learning tjänst på ett säkert sätt: autentisering, auktorisering, nätverks säkerhet, data kryptering och övervakning.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/07/2019
ms.openlocfilehash: 510f58cc0b71fb75ac6f5e15fc883c3caf4a8f9a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897898"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Företags säkerhet för Azure Machine Learning-tjänsten

I den här artikeln får du lära dig om säkerhetsfunktioner som är tillgängliga i Azure Machine Learning-tjänsten.

När du använder en moln tjänst är det bästa praxis att begränsa åtkomsten till de användare som behöver den. Detta börjar med att förstå autentiserings-och auktoriserings modellen som används av tjänsten. Du kanske också vill begränsa nätverks åtkomsten eller på ett säkert sätt ansluta resurser i ditt lokala nätverk till molnet. Data kryptering är också viktigt, både i vila och medan data flyttas mellan tjänster. Slutligen måste du kunna övervaka tjänsten och skapa en Gransknings logg för all aktivitet.

## <a name="authentication"></a>Authentication

Multi Factor Authentication stöds om Azure Active Directory (Azure AD) har kon figurer ATS för samma.

* Klient loggar in i Azure AD och hämtar Azure Resource Manager-token.  Användare och tjänstens huvud namn stöds fullt ut.
* Klienten presenterar token för att Azure Resource Manager & alla Azure Machine Learning tjänster
* Azure Machine Learning tjänsten tillhandahåller en Azure Machine Learning token för användar beräkningen. Till exempel Machine Learning-beräkning. Denna token används av användar beräkning för att anropa i Azure Machine Learning-tjänsten (gränser omfång till arbets yta) när körningen har slutförts.

[![Skärm bild som visar hur autentisering fungerar i Azure Machine Learning-tjänsten](./media/enterprise-readiness/authentication.png)](./media/enterprise-readiness/authentication-expanded.png)

### <a name="authentication-for-web-service-deployment"></a>Autentisering för webb tjänst distribution

Azure Machine Learning stöder två typer av autentisering för webb tjänster, nyckel och token. Varje WebService kan bara aktivera en form av autentisering i taget.

|Autentiseringsmetod|ACI|AKS|
|---|---|---|
|Nyckel|Inaktiverat som standard| Aktive rad som standard|
|Token| Saknas| Inaktiverat som standard |

#### <a name="authentication-with-keys"></a>Autentisering med nycklar

När du aktiverar nyckel autentisering för en distribution skapar du automatiskt nycklar för autentisering.

* Autentisering aktive ras som standard när du distribuerar till Azure Kubernetes-tjänsten.
* Autentisering inaktive ras som standard när du distribuerar till Azure Container Instances.

Om du vill aktivera nyckel autentisering använder `auth_enabled` du parametern när du skapar eller uppdaterar en distribution.

Om du har aktiverat autentisering av `get_keys` nycklar kan du använda-metoden för att hämta en primär nyckel och en sekundär autentiseringsnyckel:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Om du behöver återskapa en nyckel använder du[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

#### <a name="authentication-with-tokens"></a>Autentisering med token

När du aktiverar token-autentisering för en webb tjänst måste en användare ange en Azure Machine Learning JSON Web Token till webb tjänsten för att få åtkomst till den.

* Token-autentisering inaktive ras som standard när du distribuerar till Azure Kubernetes-tjänsten.
* Token-autentisering stöds inte när du distribuerar till Azure Container Instances.

Om du vill kontrol lera token `token_auth_enabled` -autentisering använder du parametern när du skapar eller uppdaterar en distribution.

Om token-autentisering har Aktiver ATS kan `get_token` du använda metoden för att hämta en JWT-token och dess förfallo tid för token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Du måste begära en ny token efter det att token `refresh_by` har uppnåtts.
>
> Vi rekommenderar starkt att du skapar din Azure Machine Learning arbets yta i samma region som ditt Azure Kubernetes service-kluster. För att autentisera med en token kommer webb tjänsten att ringa till den region där din Azure Machine Learning arbets yta skapas. Om arbets ytans region inte är tillgänglig kan du inte hämta en token för din WebService även om klustret finns i en annan region än din arbets yta. Detta leder till att Azure AD-autentisering inte är tillgängligt förrän arbets ytans region är tillgänglig igen. Dessutom ökar avståndet mellan klustrets region och arbets ytans region, desto längre tid tar det att hämta en token.

## <a name="authorization"></a>Authorization

Du kan skapa flera arbetsytor och varje arbetsyta kan delas av flera personer. När du delar en arbets yta kan du kontrol lera åtkomsten till den genom att tilldela följande roller till användare:

* Ägare
* Deltagare
* Läsare

I följande tabell visas några av de viktigaste Azure Machine Learning tjänst åtgärderna och de roller som kan utföra dem:

| Azure Machine Learning tjänst åtgärd | Ägare | Deltagare | Läsare |
| ---- |:----:|:----:|:----:|
| Skapa arbetsyta | ✓ | ✓ | |
| Dela arbets yta | ✓ | |  |
| Skapa beräkning | ✓ | ✓ | |
| Bifoga beräkning | ✓ | ✓ | |
| Anslut data lager | ✓ | ✓ | |
| Köra ett experiment | ✓ | ✓ | |
| Visa körningar/mått | ✓ | ✓ | ✓ |
| Registrera modellen | ✓ | ✓ | |
| Skapa avbildning | ✓ | ✓ | |
| Distribuera webb tjänst | ✓ | ✓ | |
| Visa modeller/bilder | ✓ | ✓ | ✓ |
| Anropa webb tjänst | ✓ | ✓ | ✓ |

Om de inbyggda rollerna inte räcker till för dina behov kan du också skapa anpassade roller. De enda anpassade roller som stöds för åtgärder på arbets ytan och Machine Learning-beräkning. De anpassade rollerna kan ha behörigheterna läsa, skriva eller ta bort på arbets ytan och beräknings resursen på arbets ytan. Du kan göra rollen tillgänglig på en speciell arbets yta, en bestämd resurs grupps nivå eller en speciell prenumerations nivå. Mer information finns i [Hantera användare och roller i en Azure Machine Learning arbets yta](how-to-assign-roles.md)

### <a name="securing-compute-and-data"></a>Skydda data bearbetning och data

Ägare och deltagare kan använda alla beräknings mål och data lager som är kopplade till arbets ytan.  
Varje arbets yta har också en associerad systemtilldelad hanterad identitet (med samma namn som arbets ytan) med följande behörigheter för anslutna resurser som används i arbets ytan:

Mer information om hanterade identiteter finns i [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| Resource | Behörigheter |
| ----- | ----- |
| Arbetsyta | Deltagare |
| Lagringskonto | Storage Blob Data-deltagare |
| Key Vault | Åtkomst till alla nycklar, hemligheter, certifikat |
| Azure Container Registry | Deltagare |
| Resurs grupp som innehåller arbets ytan | Deltagare |
| Resurs grupp som innehåller Key Vault (om en annan än den som innehåller arbets ytan) | Deltagare |

Det rekommenderas att administratörer inte återkallar åtkomsten av den hanterade identiteten till de resurser som anges ovan. Åtkomst kan återställas med åtgärden synkronisera om nycklar.

Azure Machine Learning tjänsten skapar ett ytterligare program (namnet börjar med `aml-`) med deltagar nivån åtkomst i din prenumeration för varje region för arbets yta. För t. ex. Om du har en arbets yta i östra USA och en annan arbets yta i Nord Europa i samma prenumeration visas två sådana program. Detta krävs för att Azure Machine Learnings tjänsten ska kunna hantera beräknings resurser.

## <a name="network-security"></a>Nätverkssäkerhet

Tjänsten Azure Machine Learning använder andra Azure-tjänster för beräknings resurser. Beräknings resurser (beräknings mål) används för att träna och distribuera modeller. Dessa beräknings mål kan skapas i ett virtuellt nätverk. Du kan till exempel använda Microsoft Data Science Virtual Machine för att träna en modell och sedan distribuera modellen till Azure Kubernetes service (AKS).  

Mer information finns i [så här kör du experiment och härledning i ett virtuellt nätverk](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Datakryptering

### <a name="encryption-at-rest"></a>Vilande kryptering

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning tjänst lagrar ögonblicks bilder, utdata och loggar i Azure Blob Storage-kontot som är kopplat till den Azure Machine Learning service arbets ytan och finns i användarens prenumeration. Alla data som lagras i Azure Blob Storage krypteras i vila med hjälp av Microsoft-hanterade nycklar.

Mer information om hur du hämtar dina egna nycklar för de data som lagras i Azure Blob Storage finns i [kryptering för lagringstjänst använda Kundhanterade nycklar i Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Tränings data lagras vanligt vis i Azure Blob Storage så att de är tillgängliga för utbildning. Den här lagringen hanteras inte av Azure Machine Learning men monteras för att beräkna ett fjärrstyrt fil system.

Information om hur du återskapar åtkomst nycklar för Azure Storage-konton som används med din arbets yta finns i artikeln [Återskapa lagrings åtkomst nycklar](how-to-change-storage-access-key.md) .

#### <a name="cosmos-db"></a>Cosmos DB

Azure Machine Learning tjänst lagrar mått och metadata till den Cosmos DB som finns i en Microsoft-prenumeration som hanteras av Azure Machine Learning-tjänsten. Alla data som lagras i Cosmos DB krypteras i vila med hjälp av Microsoft-hanterade nycklar.

#### <a name="azure-container-registry-acr"></a>Azure Container Registry (ACR)

Alla behållar avbildningar i registret (ACR) är krypterade i vila. Azure krypterar automatiskt en avbildning innan den lagras och dekrypterar den direkt när Azure Machine Learning tjänsten tar emot avbildningen.

#### <a name="machine-learning-compute"></a>Machine Learning-beräkning

Operativ system disken för varje Compute-nod lagras i Azure Storage krypteras med hjälp av Microsoft-hanterade nycklar i Azure Machine Learning tjänst lagrings konton. Detta beräknings mål är tillfälligt och kluster skalas vanligt vis ned när det inte finns några sekvenser i kön. Den underliggande virtuella datorn har tagits bort och OS-disken har tagits bort. Det finns inte stöd för Azure Disk Encryption för OS-disken.
Varje virtuell dator har också en lokal temporär disk för OS-åtgärder. Disken kan också användas för att mellanlagra tränings data. Disken är inte krypterad.
Mer information om hur kryptering i vila fungerar i Azure finns i [Azure Data Encryption-at-rest](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

### <a name="encryption-in-transit"></a>Kryptering under överföring

Både intern kommunikation mellan olika Azure Machine Learning Micro-tjänster och extern kommunikation med att anropa bedömnings slut punkten stöds med hjälp av SSL. Alla Azure Storage åtkomst är också över en säker kanal.
Mer information finns i [skydda Azure Machine Learning-webbtjänster med hjälp av SSL](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Använda Azure Key Vault

Key Vault instans som är associerad med arbets ytan används av Azure Machine Learning tjänst för att lagra autentiseringsuppgifter av olika typer:

* Den associerade anslutnings strängen för lagrings kontot
* Lösen ord till Azure Container databas instanser
* Anslutnings strängar till data lager.

SSH-lösenord och nycklar för beräknings mål som HDI HDInsight och VM lagras i en separat Key Vault som är associerad med Microsoft-prenumerationen. Azure Machine Learning tjänsten lagrar inte lösen ord eller nycklar som tillhandahålls av användaren i stället skapas, auktoriseras och lagras egna SSH-nycklar för att ansluta till VM/HDInsight för att köra experimenten.
Varje arbets yta har en associerad systemtilldelad hanterad identitet (med samma namn som arbets ytan) som har åtkomst till alla nycklar, hemligheter och certifikat i Key Vault.

## <a name="monitoring"></a>Övervakning

### <a name="metrics"></a>Mått

Azure Monitor Mät värden kan användas för att visa och övervaka mått för arbets ytan för Azure Machine Learnings tjänsten. Från [Azure Portal](https://portal.azure.com)väljer du din arbets yta och använder sedan länken __mått__ .

[![Skärm bild som visar exempel mått för en arbets yta](./media/enterprise-readiness/workspace-metrics.png)](./media/enterprise-readiness/workspace-metrics-expanded.png)

Mått innehåller information om körningar, distributioner och registreringar.

Mer information finns i [mått i Azure Monitor](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Aktivitetslogg

Du kan se aktivitets loggen under arbets ytan för att se olika åtgärder som utförts på arbets ytan och hämta grundläggande information, till exempel åtgärds namn, händelse som initieras av, tidsstämpel osv.

Följande skärm bild visar aktivitets loggen för en arbets yta:

[![Skärm bild som visar aktivitets loggen under en arbets yta](./media/enterprise-readiness/workspace-activity-log.png)](./media/enterprise-readiness/workspace-activity-log-expanded.png)

Information om bedömnings förfrågningar lagras i program insikter, som skapas i användarens prenumeration när arbets ytan skapas. Loggad information innehåller fält som HTTPMethod, UserAgent, ComputeType, RequestUrl, StatusCode, RequestId, varaktighet osv.

> [!IMPORTANT]
> Vissa åtgärder i Azure Machine Learning-arbetsytan loggar inte information i aktivitets loggen. Du kan till exempel starta en utbildning för att köra eller registrera en modell.
>
> Några av de här åtgärderna visas i området __aktiviteter__ i din arbets yta, men de visar inte vem som initierade aktiviteten.

## <a name="data-flow-diagram"></a>Data flödes diagram

### <a name="create-workspace"></a>Skapa arbetsyta

I följande diagram visas arbets ytan skapa arbets yta.
Användaren loggar in på Azure AD från någon av de Azure Machine Learning tjänst klienter som stöds (CLI, python SDK, Azure Portal) och begär rätt Azure Resource Manager-token. Användaren anropar sedan Azure Resource Manager för att skapa arbets ytan.  Azure Resource Manager kontaktar Azure Machine Learning tjänst resurs leverantör för att etablera arbets ytan.  Ytterligare resurser skapas i kundens prenumeration när arbets ytan skapas:

* Nyckel valv (för att lagra hemligheter)
* Ett Azure Storage konto (inklusive BLOB & FileShare)
* Azure Container Registry (för att lagra Docker-avbildningar för härledning/poängsättning och experimentering)
* Application Insights (för att lagra telemetri)

Andra beräkningar som är kopplade till en arbets yta (Azure Kubernetes service, VM osv.) kan också tillhandahållas av kunder vid behov.

[![Skärm bild som visar arbets ytan skapa arbets yta](./media/enterprise-readiness/create-workspace.png)](./media/enterprise-readiness/create-workspace-expanded.png)

### <a name="save-source-code-training-scripts"></a>Spara käll kod (tränings skript)

Följande diagram visar arbets flödet för kod ögonblicks bilder.
Kopplade till en Azure Machine Learning service-arbetsyta är kataloger (experiment) som innehåller käll koden (utbildnings skript).  Dessa skript lagras på kundens lokala dator och i molnet (i Azure-Blob Storage under kund prenumerationen). Kod ögonblicks bilderna används för körning eller inspektion för historisk granskning.

[![Skärm bild som visar arbets ytan skapa arbets yta](./media/enterprise-readiness/code-snapshot.png)](./media/enterprise-readiness/code-snapshot-expanded.png)

### <a name="training"></a>Utbildning

I följande diagram visas arbets flödet för utbildning.

* Azure Machine Learning-tjänsten anropas med ögonblicks bild-ID: t för kod ögonblicks bilden som sparats ovan
* Azure Machine Learnings tjänsten skapar körnings-ID (valfritt) & Azure Machine Learning-tjänstetoken som senare används av beräknings målen som Machine Learning-beräkning/VM för att tala tillbaka till Azure Machine Learning-tjänsten
* Du kan välja antingen en hanterad beräkning (t. ex. Machine Learning-beräkning) eller ohanterad beräkning (t. ex. VM) för att köra dina utbildnings jobb. Data flödet förklaras för båda scenarierna nedan:
* (VM/HDInsight – nås med SSH-autentiseringsuppgifter i Key Vault i Microsoft-prenumerationen) Azure Machine Learnings tjänsten kör hanterings kod på Compute Target som:

   1. Förbereder miljön. (Docker är ett alternativ för virtuell dator och även lokalt. Se följande steg för att Machine Learning-beräkning förstå hur du kan experimentera med Docker-behållaren.)
   1. Laddar ned koden.
   1. Ställer in miljövariabler och konfigurationer.
   1. Kör användar skript (kod ögonblicks bild som nämns ovan).

* (Machine Learning-beräkning – nås med hjälp av arbets ytans hanterad identitet) Eftersom Machine Learning-beräkning är en hanterad beräkning som är, hanteras den av Microsoft, vilket leder till att den körs under Microsoft-prenumerationen.

   1. Fjärrdockans konstruktion har inaktiverats, om det behövs.
   1. Skriver hanterings kod till användar-Azure-FileShare.
   1. Startar behållaren med ett första kommando, det vill säga hanterings kod enligt beskrivningen i föregående steg.

#### <a name="querying-runs-and-metrics"></a>Fråga körningar och mått

Det här steget visas i flödet där inlärnings beräkning skriver tillbaka *körnings måtten* till den Azure Machine Learning tjänsten varifrån den lagras i Cosmos dB. Klienter kan anropa Azure Machine Learning tjänst som i sin tur tar emot pull-mått från Cosmos DB och tillbaka tillbaka till klienten.

[![Skärm bild som visar arbets ytan skapa arbets yta](./media/enterprise-readiness/training-and-metrics.png)](./media/enterprise-readiness/training-and-metrics-expanded.png)

### <a name="creating-web-services"></a>Skapar webb tjänster

I följande diagram visas ett arbets flöde för störningar. Härlednings-eller modell poängsättning är den fas där den distribuerade modellen används för förutsägelse, oftast på produktions data.
Se information nedan:

* Användaren registrerar en modell med en klient som Azure ML SDK
* Användaren skapar avbildning med hjälp av modell, resultat fil och andra modell beroenden
* Docker-avbildningen skapas och lagras i ACR
* Webservice distribueras till Compute Target (ACI/AKS) med avbildningen som skapats ovan
* Information om bedömnings förfrågningar lagras i program insikter, som finns i användarens prenumeration
* Telemetri skickas vidare till Microsoft/Azure-prenumerationen

[![Skärm bild som visar arbets ytan skapa arbets yta](./media/enterprise-readiness/inferencing.png)](./media/enterprise-readiness/inferencing-expanded.png)

## <a name="next-steps"></a>Nästa steg

* [Skydda Azure Machine Learning-webbtjänster med SSL](how-to-secure-web-service.md)
* [Använd en ML-modell som distribueras som en webbtjänst](how-to-consume-web-service.md)
* [Hur du kör batch-förutsägelser](how-to-run-batch-predictions.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Azure Machine Learning tjänst-SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Använda Azure Machine Learning-tjänsten med virtuella Azure-nätverk](how-to-enable-virtual-network.md)
* [Metod tips för att skapa rekommendations system](https://github.com/Microsoft/Recommenders)
* [Bygg en API för rekommendationer i real tid i Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
