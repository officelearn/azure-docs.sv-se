---
title: Företagssäkerhet och styrning
titleSuffix: Azure Machine Learning
description: 'Använd Azure Machine Learning på ett säkert sätt: autentisering, auktorisering, nätverks säkerhet, data kryptering och övervakning.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/09/2020
ms.openlocfilehash: 19736a37e0da07237f6b112de7da86efe3d8bfe5
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359382"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Företags säkerhet och styrning för Azure Machine Learning

I den här artikeln får du lära dig om säkerhetsfunktioner som är tillgängliga för Azure Machine Learning.

När du använder en moln tjänst är det bästa sättet att begränsa åtkomsten till de användare som behöver den. Börja med att förstå autentiserings-och auktoriserings modellen som används av tjänsten. Du kanske också vill begränsa nätverks åtkomsten eller på ett säkert sätt ansluta resurser i ditt lokala nätverk till molnet. Data kryptering är också viktigt, både i vila och medan data flyttas mellan tjänster. Du kanske också vill skapa principer för att genomdriva vissa konfigurationer eller logg när icke-kompatibla konfigurationer skapas. Slutligen måste du kunna övervaka tjänsten och skapa en Gransknings logg för all aktivitet.

> [!NOTE]
> Informationen i den här artikeln fungerar med Azure Machine Learning python SDK-version 1.0.83.1 eller högre.

## <a name="authentication"></a>Autentisering

Multi-Factor Authentication stöds om Azure Active Directory (Azure AD) har kon figurer ATS för att använda den. Här är autentiseringsprocessen:

1. Klienten loggar in på Azure AD och hämtar en Azure Resource Manager-token.  Användare och tjänstens huvud namn stöds fullt ut.
1. Klienten presenterar token för att Azure Resource Manager och till alla Azure Machine Learning.
1. Tjänsten Machine Learning tillhandahåller en Machine Learning tjänst-token för användar beräknings målet (till exempel Machine Learning-beräkning). Denna token används av användar beräknings målet för att anropa till Machine Learning tjänsten när körningen har slutförts. Omfattningen är begränsad till arbets ytan.

[![Autentisering i Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Mer information finns i [Konfigurera autentisering för Azure Machine Learning resurser och arbets flöden](how-to-setup-authentication.md). Den här artikeln innehåller information och exempel på autentisering, inklusive att använda tjänstens huvud namn och automatiserade arbets flöden.

### <a name="authentication-for-web-service-deployment"></a>Autentisering för webb tjänst distribution

Azure Machine Learning stöder två typer av autentisering för webb tjänster: nyckel och token. Varje webb tjänst kan bara aktivera en form av autentisering i taget.

|Autentiseringsmetod|Description|Azure Container Instances|AKS|
|---|---|---|---|
|Nyckel|Nycklar är statiska och behöver inte uppdateras. Nycklar kan återskapas manuellt.|Inaktiverat som standard| Aktiverat som standard|
|Token|Token upphör att gälla efter en viss tids period och behöver uppdateras.| Inte tillgängligt| Inaktiverat som standard |

Kod exempel finns i [avsnittet Web-Service Authentication](how-to-setup-authentication.md#web-service-authentication).

## <a name="authorization"></a>Auktorisering

Du kan skapa flera arbets ytor och varje arbets yta kan delas av flera personer. När du delar en arbets yta kan du kontrol lera åtkomsten till den genom att tilldela dessa roller till användare:

* Ägare
* Deltagare
* Läsare

I följande tabell visas några av de viktigaste Azure Machine Learning åtgärderna och de roller som kan utföra dem:

| Azure Machine Learning åtgärd | Ägare | Deltagare | Läsare |
| ---- |:----:|:----:|:----:|
| Skapa arbetsyta | ✓ | ✓ | |
| Dela arbets yta | ✓ | |  |
| Skapa beräknings mål | ✓ | ✓ | |
| Koppla beräknings mål | ✓ | ✓ | |
| Bifoga data lager | ✓ | ✓ | |
| Kör experiment | ✓ | ✓ | |
| Visa körningar/mått | ✓ | ✓ | ✓ |
| Registrera modellen | ✓ | ✓ | |
| Skapa avbildning | ✓ | ✓ | |
| Distribuera webb tjänst | ✓ | ✓ | |
| Visa modeller/bilder | ✓ | ✓ | ✓ |
| Anropa webb tjänst | ✓ | ✓ | ✓ |

Om de inbyggda rollerna inte uppfyller dina behov kan du skapa anpassade roller. Anpassade roller stöds för att kontrol lera alla åtgärder i en arbets yta, till exempel skapa en beräkning, skicka in en körning, registrera ett data lager eller distribuera en modell. Anpassade roller kan ha behörigheterna läsa, skriva eller ta bort för de olika resurserna i en arbets yta, till exempel kluster, data lager, modeller och slut punkter. Du kan göra rollen tillgänglig på en speciell arbets yta, en bestämd resurs grupps nivå eller en speciell prenumerations nivå. Mer information finns i [Hantera användare och roller i en Azure Machine Learning-arbetsyta](how-to-assign-roles.md).

> [!WARNING]
> Azure Machine Learning stöds med Azure Active Directory Business-to-Business-samarbete, men stöds för närvarande inte med Azure Active Directory samarbete mellan företag.

### <a name="securing-compute-targets-and-data"></a>Säkra beräknings mål och data

Ägare och deltagare kan använda alla beräknings mål och data lager som är kopplade till arbets ytan.  

Varje arbets yta har också en associerad systemtilldelad hanterad identitet som har samma namn som arbets ytan. Den hanterade identiteten har följande behörigheter för anslutna resurser som används i arbets ytan.

Mer information om hanterade identiteter finns i [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

| Resurs | Behörigheter |
| ----- | ----- |
| Arbetsyta | Deltagare |
| Lagringskonto | Storage Blob Data-deltagare |
| Nyckelvalv | Åtkomst till alla nycklar, hemligheter, certifikat |
| Azure Container Registry | Deltagare |
| Resurs grupp som innehåller arbets ytan | Deltagare |
| Resurs grupp som innehåller nyckel valvet (om det skiljer sig från det som innehåller arbets ytan) | Deltagare |

Vi rekommenderar inte att administratörer återkallar åtkomsten av den hanterade identiteten till de resurser som anges i tabellen ovan. Du kan återställa åtkomsten med hjälp av åtgärden omsynkronisera nycklar.

Azure Machine Learning skapar ett ytterligare program (namnet börjar med `aml-` eller `Microsoft-AzureML-Support-App-` ) med åtkomst på deltagar nivå i din prenumeration för varje region för arbets yta. Om du till exempel har en arbets yta i USA, östra och en i Nord Europa i samma prenumeration, ser du två av dessa program. Med dessa program kan Azure Machine Learning hjälpa dig att hantera beräknings resurser.

## <a name="network-security"></a>Nätverkssäkerhet

Azure Machine Learning använder andra Azure-tjänster för beräknings resurser. Beräknings resurser (beräknings mål) används för att träna och distribuera modeller. Du kan skapa dessa beräknings mål i ett virtuellt nätverk. Du kan till exempel använda Azure Data Science Virtual Machine för att träna en modell och sedan distribuera modellen till AKS.  

Mer information finns i [Översikt över virtuell nätverks isolering och sekretess](how-to-network-security-overview.md).

Du kan också aktivera Azures privata länk för din arbets yta. Med privat länk kan du begränsa kommunikationen till din arbets yta från en Azure-Virtual Network. Mer information finns i [så här konfigurerar du en privat länk](how-to-configure-private-link.md).

## <a name="data-encryption"></a>Datakryptering

> [!IMPORTANT]
> För produktions klass kryptering under __utbildningen__ rekommenderar Microsoft att du använder Azure Machine Learning beräknings kluster. Microsoft rekommenderar att du använder Azure Kubernetes-tjänsten för kryptering av produktions klass under __härledningen__.
>
> Azure Machine Learning beräknings instans är en utvecklings-/test miljö. När du använder den rekommenderar vi att du lagrar dina filer, till exempel antecknings böcker och skript, i en fil resurs. Dina data ska lagras i ett data lager.

### <a name="encryption-at-rest"></a>Kryptering i vila

> [!IMPORTANT]
> Om din arbets yta innehåller känsliga data rekommenderar vi att du ställer in [hbi_workspace flagga](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) när du skapar din arbets yta. Det `hbi_workspace` går bara att ange flaggan när en arbets yta skapas. Den kan inte ändras för en befintlig arbets yta.

`hbi_workspace`Flaggan styr mängden [data som Microsoft samlar in i diagnostiska syfte](#microsoft-collected-data) och möjliggör [ytterligare kryptering i Microsoft-hanterade miljöer](../security/fundamentals/encryption-atrest.md). Dessutom kan du använda följande åtgärder:

* Startar kryptering av den lokala grunden-disken i Azure Machine Learning beräknings kluster förutsatt att du inte har skapat några tidigare kluster i den prenumerationen. Annars måste du skapa ett support ärende för att aktivera kryptering av den virtuella datorns arbets kluster 
* Rensar den lokala disken för tillfällig lagring mellan körningar
* Skickar autentiseringsuppgifter på ett säkert sätt för ditt lagrings konto, behållar registret och SSH-kontot från körnings skiktet till dina beräknings kluster med hjälp av nyckel valvet
* Aktiverar IP-filtrering för att säkerställa att underliggande batch-pooler inte kan anropas av andra externa tjänster än AzureMachineLearningService
* Observera att beräknings instanser inte stöds i HBI-arbetsytan

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning lagrar ögonblicks bilder, utdata och loggar i Azure Blob Storage-kontot som är knutet till Azure Machine Learning arbets ytan och din prenumeration. Alla data som lagras i Azure Blob Storage krypteras i vila med Microsoft-hanterade nycklar.

Information om hur du använder dina egna nycklar för data som lagras i Azure Blob Storage finns i [Azure Storage kryptering med Kundhanterade nycklar i Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md).

Tränings data lagras vanligt vis i Azure Blob Storage så att det är tillgängligt för att träna beräknings mål. Den här lagringen hanteras inte av Azure Machine Learning men monteras för att beräkna mål som ett fjärrfilsystem.

Om du behöver __Rotera eller återkalla__ din nyckel kan du när som helst göra det. När du roterar en nyckel börjar lagrings kontot använda den nya nyckeln (den senaste versionen) för att kryptera data i vila. När du återkallar (inaktiverar) en nyckel tar lagrings kontot hand om misslyckade begär Anden. Det tar vanligt vis en timme för rotationen eller återkallning att vara effektiv.

Information om hur du återskapar åtkomst nycklarna finns i [Återskapa lagrings åtkomst nycklar](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning lagrar mått och metadata i en Azure Cosmos DB-instans. Den här instansen är associerad med en Microsoft-prenumeration som hanteras av Azure Machine Learning. Alla data som lagras i Azure Cosmos DB krypteras i vila med Microsoft-hanterade nycklar.

Om du vill använda egna (Kundhanterade) nycklar för att kryptera Azure Cosmos DB-instansen kan du skapa en dedikerad Cosmos DB-instans för användning med din arbets yta. Vi rekommenderar den här metoden om du vill lagra dina data, t. ex. information om körnings historik, utanför Cosmos DB-instansen för flera innehavare som finns i vår Microsoft-prenumeration. 

Om du vill aktivera etablering av en Cosmos DB instans i din prenumeration med Kundhanterade nycklar utför du följande åtgärder:

* Registrera Microsoft. MachineLearning-och Microsoft.DocumentDB-resurs leverantörerna i din prenumeration, om de inte redan har gjort det.

* Använd följande parametrar när du skapar arbets ytan Azure Machine Learning. Båda parametrarna är obligatoriska och stöds i SDK, CLI, REST API: er och Resource Manager-mallar.

    * `resource_cmk_uri`: Den här parametern är den fullständiga resurs-URI: n för kundens hanterade nyckel i ditt nyckel valv, inklusive [versions informationen för nyckeln](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: Den här parametern är resurs-ID för nyckel valvet i din prenumeration. Det här nyckel valvet måste finnas i samma region och prenumeration som du ska använda för Azure Machine Learning-arbetsytan. 
    
        > [!NOTE]
        > Den här Key Vault-instansen kan vara annorlunda än nyckel valvet som skapas av Azure Machine Learning när du etablerar arbets ytan. Om du vill använda samma Key Vault-instans för arbets ytan skickar du samma nyckel valv medan du konfigurerar arbets ytan med hjälp av [parametern key_vault](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Om du behöver __Rotera eller återkalla__ din nyckel kan du när som helst göra det. När du roterar en nyckel börjar Cosmos DB att använda den nya nyckeln (senaste versionen) för att kryptera data i vila. När du återkallar (inaktiverar) en nyckel tar Cosmos DB hand om misslyckade begär Anden. Det tar vanligt vis en timme för rotationen eller återkallning att vara effektiv.

Mer information om kund hanterade nycklar med Cosmos DB finns i [Konfigurera Kundhanterade nycklar för ditt Azure Cosmos DB-konto](../cosmos-db/how-to-setup-cmk.md).

#### <a name="azure-container-registry"></a>Azure Container Registry

Alla behållar avbildningar i registret (Azure Container Registry) är krypterade i vila. Azure krypterar automatiskt en avbildning innan den lagras och dekrypterar den när Azure Machine Learning hämtar avbildningen.

Om du vill använda dina egna (Kundhanterade) nycklar för att kryptera din Azure Container Registry måste du skapa en egen ACR och koppla den medan du konfigurerar arbets ytan eller kryptera standard instansen som skapas vid tidpunkten för etablering av arbets ytor.

> [!IMPORTANT]
> Azure Machine Learning kräver att administratörs kontot är aktiverat på din Azure Container Registry. Som standard är den här inställningen inaktive rad när du skapar ett behållar register. Information om hur du aktiverar administratörs kontot finns i [administratörs konto](../container-registry/container-registry-authentication.md#admin-account).
>
> När en Azure Container Registry har skapats för en arbets yta ska du inte ta bort den. Om du gör det bryts Azure Machine Learning arbets ytan.

Ett exempel på hur du skapar en arbets yta med en befintlig Azure Container Registry finns i följande artiklar:

* [Skapa en arbets yta för Azure Machine Learning med Azure CLI](how-to-manage-workspace-cli.md).
* [Skapa en arbets yta med python SDK](how-to-manage-workspace.md?tabs=python#create-a-workspace).
* [Använd en Azure Resource Manager mall för att skapa en arbets yta för Azure Machine Learning](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Azure Container-instans

Du kan kryptera en distribuerad Azure Container instance-resurs (ACI) med Kundhanterade nycklar. Den Kundhanterade nyckeln som används för ACI kan lagras i Azure Key Vault för din arbets yta. Information om hur du skapar en nyckel finns i [kryptera data med en kundhanterad nyckel](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Om du vill använda nyckeln när du distribuerar en modell till Azure Container instance skapar du en ny distributions konfiguration med hjälp av `AciWebservice.deploy_configuration()` . Ange viktig information med hjälp av följande parametrar:

* `cmk_vault_base_url`: URL: en för nyckel valvet som innehåller nyckeln.
* `cmk_key_name`: Namnet på nyckeln.
* `cmk_key_version`: Nyckelns version.

Mer information om hur du skapar och använder en distributions konfiguration finns i följande artiklar:

* [AciWebservice.deploy_configuration ()-](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) referens
* [Var och hur du distribuerar](how-to-deploy-and-where.md)
* [Distribuera en modell till Azure Container Instances](how-to-deploy-azure-container-instance.md)

Mer information om hur du använder en kundhanterad nyckel med ACI finns i [kryptera data med en kundhanterad nyckel](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Du kan kryptera en distribuerad Azure Kubernetes service-resurs med hjälp av Kundhanterade nycklar när som helst. Mer information finns i [ta med dina egna nycklar med Azure Kubernetes-tjänsten](../aks/azure-disk-customer-managed-keys.md). 

Med den här processen kan du kryptera både data och OS-disken för de distribuerade virtuella datorerna i Kubernetes-klustret.

> [!IMPORTANT]
> Den här processen fungerar bara med AKS K8s version 1,17 eller senare. Azure Machine Learning lade till stöd för AKS 1,17 den 13 januari 2020.

#### <a name="machine-learning-compute"></a>Machine Learning-beräkning

OS-disken för varje Compute-nod som lagras i Azure Storage krypteras med Microsoft-hanterade nycklar i Azure Machine Learning lagrings konton. Detta beräknings mål är tillfälligt och kluster skalas vanligt vis ned när inga körningar placeras i kö. Den underliggande virtuella datorn är avetablerad och OS-disken tas bort. Azure Disk Encryption stöds inte för OS-disken.

Varje virtuell dator har också en lokal temporär disk för OS-åtgärder. Om du vill kan du använda disken för att mellanlagra tränings data. Disken är krypterad som standard för arbets ytor där `hbi_workspace` parametern har angetts till `TRUE` . Den här miljön är endast kort livs längd under körningen och krypterings stödet är begränsat till endast systemhanterade nycklar.

#### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks kan användas i Azure Machine Learning pipeliner. Som standard krypteras DBFS (Databricks File System) som används av Azure Databricks med hjälp av en Microsoft-hanterad nyckel. Information om hur du konfigurerar Azure Databricks att använda Kundhanterade nycklar finns i [Konfigurera Kundhanterade nycklar på standard-DBFS (root)](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="encryption-in-transit"></a>Kryptering under överföring

Azure Machine Learning använder TLS för att skydda intern kommunikation mellan olika Azure Machine Learning mikrotjänster. Alla Azure Storage åtkomst sker också över en säker kanal.

Azure Machine Learning använder TLS för att skydda externa anrop till poäng slut punkten. Mer information finns i [använda TLS för att skydda en webb tjänst via Azure Machine Learning](./how-to-secure-web-service.md).

### <a name="using-azure-key-vault"></a>Använda Azure Key Vault

Azure Machine Learning använder Azure Key Vault-instansen som är kopplad till arbets ytan för att lagra autentiseringsuppgifter av olika typer:

* Den associerade anslutnings strängen för lagrings kontot
* Lösen ord till Azure Container databas instanser
* Anslutnings strängar till data lager

SSH-lösenord och nycklar för att beräkna mål som Azure HDInsight och virtuella datorer lagras i ett separat nyckel valv som är associerat med Microsoft-prenumerationen. Azure Machine Learning lagrar inte lösen ord eller nycklar som tillhandahålls av användarna. I stället genererar, auktoriserar och lagrar sina egna SSH-nycklar för att ansluta till virtuella datorer och HDInsight för att köra experimenten.

Varje arbets yta har en associerad systemtilldelad hanterad identitet som har samma namn som arbets ytan. Den här hanterade identiteten har åtkomst till alla nycklar, hemligheter och certifikat i nyckel valvet.

## <a name="data-collection-and-handling"></a>Insamling och hantering av data

### <a name="microsoft-collected-data"></a>Microsoft-insamlade data

Microsoft kan samla in information om icke-användare, t. ex. resurs namn (till exempel data uppsättningens namn eller namnet på Machine Learning-experimentet) eller jobbets miljövariabler för diagnostisk användning. Alla sådana data lagras med Microsoft-hanterade nycklar i lagring som finns i Microsofts ägda prenumerationer och följer [Microsofts standard sekretess policy och data hanterings standarder](https://privacy.microsoft.com/privacystatement).

Microsoft rekommenderar även att inte lagra känslig information (till exempel konto nyckel hemligheter) i miljövariabler. Miljövariabler loggas, krypteras och lagras av oss. Undvik att använda känslig information som användar namn eller namn på hemliga projekt på samma sätt som när du namnger [run_id](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py). Den här informationen kan visas i telemetri loggar som är tillgängliga för Microsoft Support tekniker.

Du kan välja att inte använda diagnostikdata som samlas in genom `hbi_workspace` att ange parametern till `TRUE` medan du konfigurerar arbets ytan. Den här funktionen stöds när du använder AzureML python SDK, CLI, REST API: er eller Azure Resource Manager mallar.

### <a name="microsoft-generated-data"></a>Microsoft-genererade data

När du använder tjänster som automatiserade Machine Learning kan Microsoft generera en tillfällig, förbehandlad data för att träna flera modeller. Dessa data lagras i ett data lager i din arbets yta, vilket gör att du kan tillämpa åtkomst kontroller och kryptering på lämpligt sätt.

Du kanske också vill kryptera [diagnostikinformation som loggats från den distribuerade slut punkten](how-to-enable-app-insights.md) till din Azure Application insikter-instans.

## <a name="monitoring"></a>Övervakning

### <a name="metrics"></a>Mått

Du kan använda Azure Monitor mått för att visa och övervaka mått för arbets ytan Azure Machine Learning. I [Azure Portal](https://portal.azure.com)väljer du din arbets yta och väljer sedan **mått** :

[![Skärm bild som visar exempel mått för en arbets yta](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

Måtten innehåller information om körningar, distributioner och registreringar.

Mer information finns i [mått i Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).

### <a name="activity-log"></a>Aktivitetslogg

Du kan visa aktivitets loggen för en arbets yta för att se olika åtgärder som utförs på arbets ytan. Loggen innehåller grundläggande information, t. ex. åtgärds namn, händelse initierare och tidsstämpel.

Den här skärm bilden visar aktivitets loggen för en arbets yta:

[![Skärm bild som visar aktivitets loggen för en arbets yta](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Information om bedömnings förfrågningar lagras i Application Insights. Application Insights skapas i prenumerationen när du skapar en arbets yta. Loggad information innehåller fält som:

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* StatusCode
* RequestId
* Varaktighet

> [!IMPORTANT]
> Vissa åtgärder i Azure Machine Learning-arbetsytan loggar inte information i aktivitets loggen. Exempelvis loggas inte starten av en utbildnings körning och registreringen av en modell.
>
> Några av dessa åtgärder visas i området **aktiviteter** i din arbets yta, men dessa meddelanden indikerar inte vem som initierade aktiviteten.

### <a name="vulnerability-scanning"></a>Sårbarhetsgenomsökning

Azure Security Center erbjuder enhetlig säkerhetshantering och avancerat skydd mot hot i olika hybridmolnarbetsbelastningar. För Azure Machine Learning bör du Aktivera genomsökning av Azure Container Registry resurs-och Azure Kubernetes-tjänsteresurser. Se [Azure Container Registry avbildnings genomsökning av Security Center](../security-center/defender-for-container-registries-introduction.md) och [integrering med Azure Kubernetes Services med Security Center](../security-center/defender-for-kubernetes-introduction.md).

## <a name="data-flow-diagrams"></a>Data flödes diagram

### <a name="create-workspace"></a>Skapa arbetsyta

I följande diagram visas arbets ytan skapa arbets yta.

* Du loggar in på Azure AD från någon av de Azure Machine Learning klienter som stöds (Azure CLI, python SDK, Azure Portal) och begär rätt Azure Resource Manager-token.
* Du anropar Azure Resource Manager för att skapa arbets ytan. 
* Azure Resource Manager kontaktar Azure Machine Learning Resource Provider för att etablera arbets ytan.

Ytterligare resurser skapas i användarens prenumeration när arbets ytan skapas:

* Key Vault (för att lagra hemligheter)
* Ett Azure Storage-konto (inklusive blob och fil resurs)
* Azure Container Registry (för att lagra Docker-avbildningar för härledning/poängsättning och experimentering)
* Application Insights (för att lagra telemetri)

Användaren kan också etablera andra beräknings mål som är kopplade till en arbets yta (t. ex. Azure Kubernetes-tjänsten eller virtuella datorer) efter behov.

[![Skapa arbets ytans arbets flöde](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Spara käll kod (tränings skript)

Följande diagram visar arbets flödet för kod ögonblicks bilder.

Kopplade till en Azure Machine Learning-arbetsyta är kataloger (experiment) som innehåller käll koden (utbildnings skript). Dessa skript lagras på din lokala dator och i molnet (i Azure Blob Storage för din prenumeration). Kod ögonblicks bilderna används för körning eller inspektion för historisk granskning.

[![Arbets flöde för kod ögonblicks bild](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot.png#lightbox)

### <a name="training"></a>Utbildning

I följande diagram visas arbets flödet för utbildning.

* Azure Machine Learning anropas med ögonblicks bild-ID: t för kod ögonblicks bilden som sparades i föregående avsnitt.
* Azure Machine Learning skapar ett körnings-ID (valfritt) och en Machine Learning-nyckeltoken som senare används av beräknings mål som Machine Learning-beräkning/VM: ar för att kommunicera med Machine Learning-tjänsten.
* Du kan välja antingen ett hanterat beräknings mål (till exempel Machine Learning-beräkning) eller ett ohanterat beräknings mål (t. ex. virtuella datorer) för att köra utbildnings jobb. Här är data flöden för båda scenarierna:
   * VM/HDInsight, som används av SSH-autentiseringsuppgifter i ett nyckel valv i Microsoft-prenumerationen. Azure Machine Learning kör hanterings kod på Compute-målet som:

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

I flödes diagrammet nedan inträffar det här steget när träning Compute Target skriver körnings måtten tillbaka till Azure Machine Learning från lagringen i Cosmos DB-databasen. Klienter kan anropa Azure Machine Learning. Machine Learning kommer i tur och retur-mått från Cosmos DB-databasen och återställa dem tillbaka till klienten.

[![Arbets flöde för utbildning](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics.png#lightbox)

### <a name="creating-web-services"></a>Skapar webb tjänster

I följande diagram visas ett arbets flöde för störningar. Härlednings-eller modell poängsättning är den fas i vilken den distribuerade modellen används för förutsägelse, oftast på produktions data.

Här är information:

* Användaren registrerar en modell med hjälp av en klient som Azure Machine Learning SDK.
* Användaren skapar en avbildning med hjälp av en modell, en resultat fil och andra modell beroenden.
* Docker-avbildningen skapas och lagras i Azure Container Registry.
* Webb tjänsten distribueras till Compute-målet (Container Instances/AKS) med hjälp av avbildningen som skapades i föregående steg.
* Information om bedömnings förfrågningar lagras i Application Insights, som finns i användarens prenumeration.
* Telemetri skickas också till Microsoft/Azure-prenumerationen.

[![Arbets flöde för störningar](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing.png#lightbox)

## <a name="audit-and-manage-compliance"></a>Granska och hantera efterlevnad

[Azure policy](../governance/policy/index.yml) är ett styrnings verktyg som gör att du kan se till att Azure-resurserna är kompatibla med dina principer. Med Azure Machine Learning kan du tilldela följande principer:

* **Kundhanterad nyckel** : granska eller Använd om arbets ytor måste använda en kundhanterad nyckel.
* **Privat länk** : granska om arbets ytor använder en privat slut punkt för att kommunicera med ett virtuellt nätverk.

Mer information om Azure Policy finns i Azure Policy- [dokumentationen](../governance/policy/overview.md).

Mer information om principerna som är speciella för Azure Machine Learning finns i [Granska och hantera efterlevnad med Azure policy](how-to-integrate-azure-policy.md).

## <a name="resource-locks"></a>Resurslås

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Nästa steg

* [Skydda Azure Machine Learning webb tjänster med TLS](how-to-secure-web-service.md)
* [Använda en Machine Learning modell som distribueras som en webb tjänst](how-to-consume-web-service.md)
* [Använda Azure Machine Learning med Azure-brandvägg](how-to-access-azureml-behind-firewall.md)
* [Använda Azure Machine Learning med Azure Virtual Network](how-to-network-security-overview.md)
* [Metod tips för att skapa rekommendations system](https://github.com/Microsoft/Recommenders)
* [Bygg en API för rekommendationer i real tid i Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)
