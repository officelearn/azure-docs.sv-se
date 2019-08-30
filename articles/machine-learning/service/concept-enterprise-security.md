---
title: Företagssäkerhet
titleSuffix: Azure Machine Learning service
description: 'Använd Azure Machine Learning tjänsten på ett säkert sätt: autentisering, auktorisering, nätverks säkerhet, data kryptering och övervakning.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/07/2019
ms.openlocfilehash: 81e8601ac83d43bde0767e38eb387f489d76125b
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165249"
---
# <a name="enterprise-security-for-the-azure-machine-learning-service"></a>Företags säkerhet för tjänsten Azure Machine Learning

I den här artikeln får du lära dig om säkerhetsfunktioner som är tillgängliga för tjänsten Azure Machine Learning.

När du använder en moln tjänst är det bästa sättet att begränsa åtkomsten till de användare som behöver den. Börja med att förstå autentiserings-och auktoriserings modellen som används av tjänsten. Du kanske också vill begränsa nätverks åtkomsten eller på ett säkert sätt ansluta resurser i ditt lokala nätverk till molnet. Data kryptering är också viktigt, både i vila och medan data flyttas mellan tjänster. Slutligen måste du kunna övervaka tjänsten och skapa en Gransknings logg för all aktivitet.

## <a name="authentication"></a>Authentication

Multi-Factor Authentication stöds om Azure Active Directory (Azure AD) har kon figurer ATS för att använda den. Här är autentiseringsprocessen:

1. Klienten loggar in på Azure AD och hämtar en Azure Resource Manager-token.  Användare och tjänstens huvud namn stöds fullt ut.
1. Klienten presenterar token för att Azure Resource Manager och till alla Azure Machine Learning-tjänster.
1. Tjänsten Machine Learning tillhandahåller en Machine Learning tjänst-token för användar beräknings målet (till exempel Machine Learning-beräkning). Denna token används av användar beräknings målet för att anropa till Machine Learning tjänsten när körningen har slutförts. Omfattningen är begränsad till arbets ytan.

[![Autentisering i Azure Machine Learnings tjänsten](./media/enterprise-readiness/authentication.png)](./media/enterprise-readiness/authentication-expanded.png)

### <a name="authentication-for-web-service-deployment"></a>Autentisering för webb tjänst distribution

Azure Machine Learning stöder två typer av autentisering för webb tjänster: nyckel och token. Varje webb tjänst kan bara aktivera en form av autentisering i taget.

|Autentiseringsmetod|Azure Container Instances|AKS|
|---|---|---|
|Nyckel|Inaktiverat som standard| Aktive rad som standard|
|Token| Saknas| Inaktiverat som standard |

#### <a name="authentication-with-keys"></a>Autentisering med nycklar

När du aktiverar nyckel autentisering för en distribution skapar du automatiskt nycklar för autentisering.

* Autentisering aktive ras som standard när du distribuerar till Azure Kubernetes service (AKS).
* Autentisering inaktive ras som standard när du distribuerar till Azure Container Instances.

Om du vill aktivera nyckel autentisering använder `auth_enabled` du parametern när du skapar eller uppdaterar en distribution.

Om du har aktiverat autentisering av `get_keys` nycklar kan du använda-metoden för att hämta en primär nyckel och en sekundär autentiseringsnyckel:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Om du vill återskapa en nyckel kan du använda [ `service.regen_key` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

#### <a name="authentication-with-tokens"></a>Autentisering med token

När du aktiverar token-autentisering för en webb tjänst måste användarna presentera en Azure Machine Learning JSON Web Token till webb tjänsten för att komma åt den.

* Token-autentisering inaktive ras som standard när du distribuerar till Azure Kubernetes-tjänsten.
* Token-autentisering stöds inte när du distribuerar till Azure Container Instances.

Om du vill kontrol lera token `token_auth_enabled` -autentisering använder du parametern när du skapar eller uppdaterar en distribution.

Om token-autentisering har Aktiver ATS kan `get_token` du använda metoden för att hämta en JSON Web token (JWT) och den tokens förfallo tid:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Du måste begära en ny token efter det att token `refresh_by` har uppnåtts.
>
> Vi rekommenderar starkt att du skapar din Azure Machine Learning arbets yta i samma region som ditt Azure Kubernetes service-kluster. 
>
> För att autentisera med en token kommer webb tjänsten att ringa till den region där din Azure Machine Learning arbets yta skapas. Om arbets ytans region inte är tillgänglig kan du inte hämta en token för din webb tjänst, även om klustret finns i en annan region än din arbets yta. Resultatet är att Azure AD-autentisering inte är tillgängligt förrän arbets ytans region är tillgänglig igen. 
>
> Dessutom ökar avståndet mellan klustrets region och arbets ytans region, desto längre tid tar det att hämta en token.

## <a name="authorization"></a>Authorization

Du kan skapa flera arbetsytor och varje arbetsyta kan delas av flera personer. När du delar en arbets yta kan du kontrol lera åtkomsten till den genom att tilldela dessa roller till användare:

* Ägare
* Deltagare
* Läsare

I följande tabell visas några av de viktigaste Azure Machine Learning tjänst åtgärderna och de roller som kan utföra dem:

| Azure Machine Learning tjänst åtgärd | Ägare | Deltagare | Läsare |
| ---- |:----:|:----:|:----:|
| Skapa arbetsyta | ✓ | ✓ | |
| Dela arbets yta | ✓ | |  |
| Skapa beräknings mål | ✓ | ✓ | |
| Koppla beräknings mål | ✓ | ✓ | |
| Bifoga data lager | ✓ | ✓ | |
| Kör experimentet | ✓ | ✓ | |
| Visa körningar/mått | ✓ | ✓ | ✓ |
| Registrera modellen | ✓ | ✓ | |
| Skapa avbildning | ✓ | ✓ | |
| Distribuera webb tjänst | ✓ | ✓ | |
| Visa modeller/bilder | ✓ | ✓ | ✓ |
| Anropa webb tjänst | ✓ | ✓ | ✓ |

Om de inbyggda rollerna inte uppfyller dina behov kan du skapa anpassade roller. Anpassade roller stöds bara för åtgärder på arbets ytan och Machine Learning-beräkning. Anpassade roller kan ha behörigheterna läsa, skriva eller ta bort på arbets ytan och på beräknings resursen på arbets ytan. Du kan göra rollen tillgänglig på en speciell arbets yta, en bestämd resurs grupps nivå eller en speciell prenumerations nivå. Mer information finns i [Hantera användare och roller i en Azure Machine Learning-arbetsyta](how-to-assign-roles.md).

### <a name="securing-compute-targets-and-data"></a>Säkra beräknings mål och data

Ägare och deltagare kan använda alla beräknings mål och data lager som är kopplade till arbets ytan.  

Varje arbets yta har också en associerad systemtilldelad hanterad identitet som har samma namn som arbets ytan. Den hanterade identiteten har följande behörigheter för anslutna resurser som används i arbets ytan.

Mer information om hanterade identiteter finns i [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Resource | Behörigheter |
| ----- | ----- |
| Arbetsyta | Deltagare |
| Lagringskonto | Storage Blob Data-deltagare |
| Nyckelvalv | Åtkomst till alla nycklar, hemligheter, certifikat |
| Azure Container Registry | Deltagare |
| Resurs grupp som innehåller arbets ytan | Deltagare |
| Resurs grupp som innehåller nyckel valvet (om det skiljer sig från det som innehåller arbets ytan) | Deltagare |

Vi rekommenderar inte att administratörer återkallar åtkomsten av den hanterade identiteten till de resurser som anges i tabellen ovan. Du kan återställa åtkomsten med hjälp av åtgärden omsynkronisera nycklar.

Azure Machine learnings tjänsten skapar ett ytterligare program (namnet börjar med `aml-`) med åtkomst på deltagar nivå i din prenumeration för varje region för arbets ytan. Om du till exempel har en arbets yta i östra USA och en annan arbets yta i Nord Europa i samma prenumeration, ser du två av dessa program. De här programmen gör det möjligt för Azure Machine Learnings tjänsten att hjälpa dig att hantera beräknings resurser.

## <a name="network-security"></a>Nätverkssäkerhet

Tjänsten Azure Machine Learning använder andra Azure-tjänster för beräknings resurser. Beräknings resurser (beräknings mål) används för att träna och distribuera modeller. Du kan skapa dessa beräknings mål i ett virtuellt nätverk. Du kan till exempel använda Azure Data Science Virtual Machine för att träna en modell och sedan distribuera modellen till AKS.  

Mer information finns i [så här kör du experiment och härledning i ett virtuellt nätverk](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Datakryptering

### <a name="encryption-at-rest"></a>Vilande kryptering

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Tjänsten Azure Machine Learning lagrar ögonblicks bilder, utdata och loggar i Azure Blob Storage-kontot som är knutet till Azure Machine Learning service-arbetsytan och din prenumeration. Alla data som lagras i Azure Blob Storage krypteras i vila med Microsoft-hanterade nycklar.

Information om hur du använder dina egna nycklar för data som lagras i Azure Blob Storage finns i [Azure Storage kryptering med Kundhanterade nycklar i Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Tränings data lagras vanligt vis i Azure Blob Storage så att det är tillgängligt för att träna beräknings mål. Den här lagringen hanteras inte av Azure Machine Learning men monteras för att beräkna mål som ett fjärrfilsystem.

Information om hur du återskapar åtkomst nycklar för Azure Storage-konton som används med din arbets yta finns i [Återskapa lagrings åtkomst nycklar](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Tjänsten Azure Machine Learning lagrar mått och metadata i Azure Cosmos DB-instansen som associeras med en Microsoft-prenumeration som hanteras av Azure Machine Learnings tjänsten. Alla data som lagras i Azure Cosmos DB krypteras i vila med Microsoft-hanterade nycklar.

#### <a name="azure-container-registry"></a>Azure Container Registry

Alla behållar avbildningar i registret (Azure Container Registry) är krypterade i vila. Azure krypterar automatiskt en avbildning innan den lagras och dekrypterar den direkt när Azure Machine Learning tjänsten hämtar avbildningen.

#### <a name="machine-learning-compute"></a>Machine Learning-beräkning

OS-disken för varje Compute-nod som lagras i Azure Storage krypteras med Microsoft-hanterade nycklar i Azure Machine Learning tjänst lagrings konton. Detta beräknings mål är tillfälligt och kluster skalas vanligt vis ned när inga körningar placeras i kö. Den underliggande virtuella datorn är avetablerad och OS-disken tas bort. Azure Disk Encryption stöds inte för OS-disken.

Varje virtuell dator har också en lokal temporär disk för OS-åtgärder. Om du vill kan du använda disken för att mellanlagra tränings data. Disken är inte krypterad.
Mer information om hur kryptering i vila fungerar i Azure finns i [Azure Data](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)Encryption i vila.

### <a name="encryption-in-transit"></a>Kryptering under överföring

Du kan använda SSL för att skydda intern kommunikation mellan Azure Machine Learning mikrotjänster och säkra externa anrop till bedömnings slut punkten. Alla Azure Storage åtkomst sker också över en säker kanal.

Mer information finns i [använda SSL för att skydda en webb tjänst via Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Använda Azure Key Vault

Den Azure Machine Learning tjänsten använder Azure Key Vault-instansen som är kopplad till arbets ytan för att lagra autentiseringsuppgifter av olika typer:

* Den associerade anslutnings strängen för lagrings kontot
* Lösen ord till Azure Container databas instanser
* Anslutnings strängar till data lager

SSH-lösenord och nycklar för att beräkna mål som Azure HDInsight och virtuella datorer lagras i ett separat nyckel valv som är associerat med Microsoft-prenumerationen. Tjänsten Azure Machine Learning lagrar inte lösen ord eller nycklar som tillhandahålls av användarna. I stället genererar, auktoriserar och lagrar sina egna SSH-nycklar för att ansluta till virtuella datorer och HDInsight för att köra experimenten.

Varje arbets yta har en associerad systemtilldelad hanterad identitet som har samma namn som arbets ytan. Den här hanterade identiteten har åtkomst till alla nycklar, hemligheter och certifikat i nyckel valvet.

## <a name="monitoring"></a>Övervakning

### <a name="metrics"></a>Mått

Du kan använda Azure Monitor mått för att visa och övervaka mått för din Azure Machine Learning service-arbetsyta. I [Azure Portal](https://portal.azure.com)väljer du din arbets yta och väljer sedan **mått**:

[![Skärm bild som visar exempel mått för en arbets yta](./media/enterprise-readiness/workspace-metrics.png)](./media/enterprise-readiness/workspace-metrics-expanded.png)

Måtten innehåller information om körningar, distributioner och registreringar.

Mer information finns i [mått i Azure Monitor](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Aktivitetslogg

Du kan visa aktivitets loggen för en arbets yta för att se olika åtgärder som utförs på arbets ytan. Loggen innehåller grundläggande information, t. ex. åtgärds namn, händelse initierare och tidsstämpel.

Den här skärm bilden visar aktivitets loggen för en arbets yta:

[![Skärm bild som visar aktivitets loggen för en arbets yta](./media/enterprise-readiness/workspace-activity-log.png)](./media/enterprise-readiness/workspace-activity-log-expanded.png)

Information om bedömnings förfrågningar lagras i Application Insights. Application Insights skapas i prenumerationen när du skapar en arbets yta. Loggad information innehåller fält som HTTPMethod, UserAgent, ComputeType, RequestUrl, StatusCode, RequestId och varaktighet.

> [!IMPORTANT]
> Vissa åtgärder i Azure Machine Learning-arbetsytan loggar inte information i aktivitets loggen. Exempelvis loggas inte starten av en utbildnings körning och registreringen av en modell.
>
> Några av dessa åtgärder visas i området **aktiviteter** i din arbets yta, men dessa meddelanden indikerar inte vem som initierade aktiviteten.

## <a name="data-flow-diagrams"></a>Data flödes diagram

### <a name="create-workspace"></a>Skapa arbetsyta

I följande diagram visas arbets ytan skapa arbets yta.

* Användaren loggar in på Azure AD från en av de Azure Machine Learning tjänst klienter som stöds (Azure CLI, python SDK, Azure Portal) och begär rätt Azure Resource Manager-token.
* Användaren anropar Azure Resource Manager för att skapa arbets ytan. 
* Azure Resource Manager kontaktar Azure Machine Learning tjänst resurs leverantör för att etablera arbets ytan.

Ytterligare resurser skapas i användarens prenumeration när arbets ytan skapas:

* Key Vault (för att lagra hemligheter)
* Ett Azure Storage-konto (inklusive blob och fil resurs)
* Azure Container Registry (för att lagra Docker-avbildningar för härledning/poängsättning och experimentering)
* Application Insights (för att lagra telemetri)

Användaren kan också etablera andra beräknings mål som är kopplade till en arbets yta (t. ex. Azure Kubernetes-tjänsten eller virtuella datorer) efter behov.

[![Skapa arbets ytans arbets flöde](./media/enterprise-readiness/create-workspace.png)](./media/enterprise-readiness/create-workspace-expanded.png)

### <a name="save-source-code-training-scripts"></a>Spara käll kod (tränings skript)

Följande diagram visar arbets flödet för kod ögonblicks bilder.

Kopplade till en Azure Machine Learning service-arbetsyta är kataloger (experiment) som innehåller käll koden (utbildnings skript). Dessa skript lagras på din lokala dator och i molnet (i Azure Blob Storage för din prenumeration). Kod ögonblicks bilderna används för körning eller inspektion för historisk granskning.

[![Arbets flöde för kod ögonblicks bild](./media/enterprise-readiness/code-snapshot.png)](./media/enterprise-readiness/code-snapshot-expanded.png)

### <a name="training"></a>Utbildning

I följande diagram visas arbets flödet för utbildning.

* Azure Machine Learnings tjänsten anropas med ögonblicks bild-ID: t för kod ögonblicks bilden som sparades i föregående avsnitt.
* Azure Machine Learnings tjänsten skapar ett körnings-ID (valfritt) och en Machine Learning-tjänstetoken som senare används av beräknings mål som Machine Learning-beräkning/VM: ar för att kommunicera med Machine Learning-tjänsten.
* Du kan välja antingen ett hanterat beräknings mål (till exempel Machine Learning-beräkning) eller ett ohanterat beräknings mål (t. ex. virtuella datorer) för att köra dina utbildnings jobb. Här är data flöden för båda scenarierna:
   * VM/HDInsight, som används av SSH-autentiseringsuppgifter i ett nyckel valv i Microsoft-prenumerationen. Azure Machine Learnings tjänsten kör hanterings kod på Compute-målet som:

   1. Förbereder miljön. (Docker är ett alternativ för virtuella datorer och lokala datorer. Se följande steg för att Machine Learning-beräkning förstå hur du kan köra experiment i Docker-behållare fungerar.)
   1. Laddar ned koden.
   1. Ställer in miljövariabler och konfigurationer.
   1. Kör användar skript (kod ögonblicks bilden som nämns i föregående avsnitt).

   * Machine Learning-beräkning, som nås via en arbets yta-hanterad identitet.
Eftersom Machine Learning-beräkning är ett hanterat beräknings mål (dvs. det hanteras av Microsoft) körs det under din Microsoft-prenumeration.

   1. Fjärrdockans konstruktion har inaktiverats, om det behövs.
   1. Hanterings koden skrivs till användarens Azure Files-resurs.
   1. Behållaren startas med ett inledande kommando. Det vill säga hanterings koden enligt beskrivningen i föregående steg.

#### <a name="querying-runs-and-metrics"></a>Fråga körningar och mått

I flödes diagrammet nedan inträffar det här steget när inlärnings mål målet skriver körnings måtten tillbaka till Azure Machine Learning tjänsten från lagringen i Cosmos DB-databasen. Klienter kan anropa tjänsten Azure Machine Learning. Machine Learning kommer i tur och retur-mått från Cosmos DB-databasen och återställa dem tillbaka till klienten.

[![Arbets flöde för utbildning](./media/enterprise-readiness/training-and-metrics.png)](./media/enterprise-readiness/training-and-metrics-expanded.png)

### <a name="creating-web-services"></a>Skapar webb tjänster

I följande diagram visas ett arbets flöde för störningar. Härlednings-eller modell poängsättning är den fas i vilken den distribuerade modellen används för förutsägelse, oftast på produktions data.

Här är information:

* Användaren registrerar en modell med hjälp av en klient som Azure Machine Learning SDK.
* Användaren skapar en avbildning med hjälp av en modell, en resultat fil och andra modell beroenden.
* Docker-avbildningen skapas och lagras i Azure Container Registry.
* Webb tjänsten distribueras till Compute-målet (Container Instances/AKS) med hjälp av avbildningen som skapades i föregående steg.
* Information om bedömnings förfrågningar lagras i Application Insights, som finns i användarens prenumeration.
* Telemetri skickas också till Microsoft/Azure-prenumerationen.

[![Arbets flöde för störningar](./media/enterprise-readiness/inferencing.png)](./media/enterprise-readiness/inferencing-expanded.png)

## <a name="next-steps"></a>Nästa steg

* [Skydda Azure Machine Learning-webbtjänster med SSL](how-to-secure-web-service.md)
* [Använda en Machine Learning modell som distribueras som en webb tjänst](how-to-consume-web-service.md)
* [Hur du kör batch-förutsägelser](how-to-run-batch-predictions.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Azure Machine Learning tjänst-SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Använd Azure Machine Learning tjänsten med Azure Virtual Network](how-to-enable-virtual-network.md)
* [Metod tips för att skapa rekommendations system](https://github.com/Microsoft/Recommenders)
* [Bygg en API för rekommendationer i real tid i Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
