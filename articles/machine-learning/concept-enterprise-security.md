---
title: Företagssäkerhet
titleSuffix: Azure Machine Learning
description: 'Använd Azure Machine Learning på ett säkert sätt: autentisering, auktorisering, nätverkssäkerhet, datakryptering och övervakning.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/13/2020
ms.openlocfilehash: 359fd7fc787db5710deca75dd562215d25ed9148
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437495"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Företagssäkerhet för Azure Machine Learning

I den här artikeln får du lära dig mer om tillgängliga säkerhetsfunktioner för Azure Machine Learning.

När du använder en molntjänst är en metod för bästa praxis att begränsa åtkomsten till endast de användare som behöver den. Börja med att förstå autentiserings- och auktoriseringsmodellen som används av tjänsten. Du kanske också vill begränsa nätverksåtkomst eller ansluta resurser på ett säkert sätt i det lokala nätverket med molnet. Datakryptering är också viktigt, både i vila och medan data flyttas mellan tjänster. Slutligen måste du kunna övervaka tjänsten och skapa en granskningslogg över all aktivitet.

> [!NOTE]
> Informationen i den här artikeln fungerar med Azure Machine Learning Python SDK version 1.0.83.1 eller senare.

## <a name="authentication"></a>Autentisering

Multifaktorautentisering stöds om Azure Active Directory (Azure AD) är konfigurerat för att använda den. Här är autentiseringsprocessen:

1. Klienten loggar in på Azure AD och hämtar en Azure Resource Manager-token.  Användare och tjänsthuvudnamn stöds fullt ut.
1. Klienten presenterar token till Azure Resource Manager och för alla Azure Machine Learning.
1. Machine Learning-tjänsten tillhandahåller en machine learning-tjänsttoken till användarberäkningsmålet (till exempel Machine Learning Compute). Den här token används av användarens beräkningsmål för att ringa tillbaka till machine learning-tjänsten när körningen är klar. Scopet är begränsat till arbetsytan.

[![Autentisering i Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

Mer information finns i [Konfigurera autentisering för Azure Machine Learning-resurser och arbetsflöden](how-to-setup-authentication.md). Den här artikeln innehåller information och exempel på autentisering, inklusive användning av tjänsthuvudnamn och automatiserade arbetsflöden.

### <a name="authentication-for-web-service-deployment"></a>Autentisering för distribution av webbtjänster

Azure Machine Learning stöder två former av autentisering för webbtjänster: nyckel och token. Varje webbtjänst kan bara aktivera en form av autentisering åt gången.

|Autentiseringsmetod|Beskrivning|Azure Container Instances|AKS|
|---|---|---|---|
|Nyckel|Tangenterna är statiska och behöver inte uppdateras. Nycklar kan återskapas manuellt.|Inaktiverad som standard| Aktiverat som standard|
|Token|Token upphör att gälla efter en angiven tidsperiod och måste uppdateras.| Inte tillgängligt| Inaktiverad som standard |

Kodexempel finns i [avsnittet webbtjänstautentisering](how-to-setup-authentication.md#web-service-authentication).

## <a name="authorization"></a>Auktorisering

Du kan skapa flera arbetsytor och varje arbetsyta kan delas av flera personer. När du delar en arbetsyta kan du styra åtkomsten till den genom att tilldela dessa roller till användare:

* Ägare
* Deltagare
* Läsare

I följande tabell visas några av de större Azure Machine Learning-åtgärderna och de roller som kan utföra dem:

| Azure Machine Learning-åtgärd | Ägare | Deltagare | Läsare |
| ---- |:----:|:----:|:----:|
| Skapa arbetsyta | ✓ | ✓ | |
| Dela arbetsyta | ✓ | |  |
| Uppgradera arbetsytan till Enterprise-utgåvan | ✓ | |
| Skapa beräkningsmål | ✓ | ✓ | |
| Koppla beräkningsmål | ✓ | ✓ | |
| Bifoga datalager | ✓ | ✓ | |
| Kör experiment | ✓ | ✓ | |
| Visa körningar/mått | ✓ | ✓ | ✓ |
| Registrera modellen | ✓ | ✓ | |
| Skapa bild | ✓ | ✓ | |
| Distribuera webbtjänst | ✓ | ✓ | |
| Visa modeller/bilder | ✓ | ✓ | ✓ |
| Ring webbtjänst | ✓ | ✓ | ✓ |

Om de inbyggda rollerna inte uppfyller dina behov kan du skapa anpassade roller. Anpassade roller stöds endast för åtgärder på arbetsytan och Machine Learning Compute. Anpassade roller kan ha läs-, skriv- eller borttagningsbehörigheter på arbetsytan och på beräkningsresursen på arbetsytan. Du kan göra rollen tillgänglig på en viss arbetsytasnivå, en viss resursgruppsnivå eller en viss prenumerationsnivå. Mer information finns [i Hantera användare och roller på en Azure Machine Learning-arbetsyta](how-to-assign-roles.md).

> [!WARNING]
> Azure Machine Learning stöds för närvarande inte med Azure Active Directory business-to-business-samarbete.

### <a name="securing-compute-targets-and-data"></a>Skydda beräkningsmål och data

Ägare och deltagare kan använda alla beräkningsmål och datalager som är kopplade till arbetsytan.  

Varje arbetsyta har också en associerad systemtilldelad hanterad identitet som har samma namn som arbetsytan. Den hanterade identiteten har följande behörigheter för kopplade resurser som används på arbetsytan.

Mer information om hanterade identiteter finns i [Hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Resurs | Behörigheter |
| ----- | ----- |
| Arbetsyta | Deltagare |
| Lagringskonto | Storage Blob Data Contributor |
| Nyckelvalv | Tillgång till alla nycklar, hemligheter, certifikat |
| Azure Container Registry | Deltagare |
| Resursgrupp som innehåller arbetsytan | Deltagare |
| Resursgrupp som innehåller nyckelvalvet (om det skiljer sig från det som innehåller arbetsytan) | Deltagare |

Vi rekommenderar inte att administratörer återkallar åtkomsten för den hanterade identiteten till de resurser som nämns i tabellen föregående. Du kan återställa åtkomsten med hjälp av åtgärden resync-nycklar.

Azure Machine Learning skapar ytterligare ett program `aml-` `Microsoft-AzureML-Support-App-`(namnet börjar med eller ) med åtkomst på deltagarnivå i din prenumeration för varje arbetsyta. Om du till exempel har en arbetsyta i östra USA och en i Norra Europa i samma prenumeration visas två av dessa program. Dessa program gör det möjligt för Azure Machine Learning som hjälper dig att hantera beräkningsresurser.

## <a name="network-security"></a>Nätverkssäkerhet

Azure Machine Learning är beroende av andra Azure-tjänster för beräkningsresurser. Beräkningsresurser (beräkningsmål) används för att träna och distribuera modeller. Du kan skapa dessa beräkningsmål i ett virtuellt nätverk. Du kan till exempel använda Virtuell Azure Data Science-dator för att träna en modell och sedan distribuera modellen till AKS.  

Mer information finns i Så här kör du [experiment och slutsatser i ett virtuellt nätverk](how-to-enable-virtual-network.md).

Du kan också aktivera Azure Private Link för din arbetsyta. Med Private Link kan du begränsa kommunikationen till din arbetsyta från ett virtuellt Azure-nätverk. Mer information finns i [Så här konfigurerar du Privat länk](how-to-configure-private-link.md).

> [!TIP]
> Du kan kombinera virtuella nätverk och privat länk tillsammans för att skydda kommunikationen mellan din arbetsyta och andra Azure-resurser. Vissa kombinationer kräver dock en arbetsyta för Enterprise Edition. Använd följande tabell för att förstå vilka scenarier som kräver Enterprise Edition:
>
> | Scenario | Enterprise</br>Edition | Basic</br>Edition |
> | ----- |:-----:|:-----:| 
> | Inget virtuellt nätverk eller privat länk | ✔ | ✔ |
> | Arbetsyta utan privat länk. Andra resurser (utom Azure Container Registry) i ett virtuellt nätverk | ✔ | ✔ |
> | Arbetsyta utan privat länk. Andra resurser med Private Link | ✔ | |
> | Arbetsyta med privat länk. Andra resurser (utom Azure Container Registry) i ett virtuellt nätverk | ✔ | ✔ |
> | Arbetsyta och andra resurser med Privat länk | ✔ | |
> | Arbetsyta med privat länk. Andra resurser utan Privat länk eller virtuellt nätverk | ✔ | ✔ |
> | Azure Container-registret i ett virtuellt nätverk | ✔ | |
> | Hanterade kundnycklar för arbetsyta | ✔ | |
> 

> [!WARNING]
> Förhandsversionen av Azure Machine Learning-beräkningsinstanser stöds inte på en arbetsyta där Private Link är aktiverad.
> 
> Azure Machine Learning stöder inte användning av en Azure Kubernetes-tjänst som har privat länk aktiverad. I stället kan du använda Azure Kubernetes Service i ett virtuellt nätverk. Mer information finns [i Secure Azure ML-experiment och inferensjobb i ett Virtuellt Azure-nätverk](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Datakryptering

### <a name="encryption-at-rest"></a>Vilande kryptering

> [!IMPORTANT]
> Om arbetsytan innehåller känsliga data rekommenderar vi att du ställer in [flaggan hbi_workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) när arbetsytan skapas. Detta styr mängden data som Microsoft samlar in för diagnostiska ändamål och möjliggör ytterligare kryptering i Microsofts hanterade miljöer.

Mer information om hur kryptering i vila fungerar i Azure finns i [Azure-datakryptering](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)i vila .

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning lagrar ögonblicksbilder, utdata och loggar i Azure Blob-lagringskontot som är kopplat till Arbetsytan Azure Machine Learning och din prenumeration. Alla data som lagras i Azure Blob-lagring krypteras i vila med Microsoft-hanterade nycklar.

Information om hur du använder dina egna nycklar för data som lagras i Azure Blob-lagring finns i [Azure Storage-kryptering med kundhanterade nycklar i Azure Key Vault](../storage/common/storage-encryption-keys-portal.md).

Utbildningsdata lagras vanligtvis också i Azure Blob-lagring så att de är tillgängliga för beräkningsmål för utbildning. Den här lagringen hanteras inte av Azure Machine Learning men är monterad för att beräkna mål som ett fjärrfilsystem.

Om du behöver __rotera eller återkalla__ nyckeln kan du göra det när som helst. När du roterar en nyckel börjar lagringskontot använda den nya nyckeln (den senaste versionen) för att kryptera data i vila. När du återkallar (inaktiverar) en nyckel tar lagringskontot hand om misslyckade begäranden. Det tar vanligtvis en timme för rotation eller återkallande att vara effektiv.

Information om hur du återskapar åtkomstnycklarna finns i [Återskapa lagringsåtkomstnycklar](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning lagrar mått och metadata i en Azure Cosmos DB-instans. Den här instansen är associerad med en Microsoft-prenumeration som hanteras av Azure Machine Learning. Alla data som lagras i Azure Cosmos DB krypteras i vila med Microsoft-hanterade nycklar.

Om du vill använda dina egna (kundhanterade) nycklar för att kryptera Azure Cosmos DB-instansen kan du skapa en dedikerad Cosmos DB-instans som kan användas med arbetsytan. Vi rekommenderar den här metoden om du vill lagra dina data, till exempel körhistorikinformation, utanför den Cosmos DB-instans med flera innehavare som finns i vår Microsoft-prenumeration. 

Så här aktiverar du etablering av en Cosmos DB-instans i din prenumeration med kundhanterade nycklar:

* Aktivera kundhanterade nyckelfunktioner för Cosmos DB. För närvarande måste du begära åtkomst för att kunna använda den här funktionen. För att göra [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com)det, vänligen kontakta .

* Registrera Azure Machine Learning och Azure Cosmos DB-resursleverantörer i din prenumeration, om de inte redan görs.

* Auktorisera Machine Learning App (i Identitets- och åtkomsthantering) med deltagarbehörighet för din prenumeration.

    ![Auktorisera Azure Machine Learning App i Identitets- och åtkomsthantering i portalen](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* Använd följande parametrar när du skapar arbetsytan Azure Machine Learning. Båda parametrarna är obligatoriska och stöds i SDK-, CLI-, REST-API:er och Resource Manager-mallar.

    * `resource_cmk_uri`: Den här parametern är den fullständiga resurs-URI-historiken för den kundhanterade nyckeln i nyckelvalvet, inklusive [versionsinformationen för nyckeln](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: Den här parametern är resurs-ID för nyckelvalvet i prenumerationen. Det här nyckelvalvet måste finnas i samma region och prenumeration som du ska använda för arbetsytan Azure Machine Learning. 
    
        > [!NOTE]
        > Den här nyckelvalvsinstansen kan skilja sig från nyckelvalvet som skapas av Azure Machine Learning när du etablerar arbetsytan. Om du vill använda samma nyckelvalvsinstans för arbetsytan skickar du samma nyckelvalv medan arbetsytan etableras med [parametern key_vault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

Den här Cosmos DB-instansen skapas i en Microsoft-hanterad resursgrupp i din prenumeration. 

> [!IMPORTANT]
> * Om du behöver ta bort den här Cosmos DB-instansen måste du ta bort arbetsytan Azure Machine Learning som använder den. 
> * Standardenheterna för [__begäranden__](../cosmos-db/request-units.md) för det här Cosmos DB-kontot är __8000__. Det går inte att ändra det här värdet. 

Om du behöver __rotera eller återkalla__ nyckeln kan du göra det när som helst. När du roterar en nyckel börjar Cosmos DB använda den nya nyckeln (den senaste versionen) för att kryptera data i vila. När cosmos DB återkallas (inaktiverar) en nyckel tar du hand om misslyckade begäranden. Det tar vanligtvis en timme för rotation eller återkallande att vara effektiv.

Mer information om kundhanterade nycklar med Cosmos DB finns i [Konfigurera kundhanterade nycklar för ditt Azure Cosmos DB-konto](../cosmos-db/how-to-setup-cmk.md).

#### <a name="azure-container-registry"></a>Azure Container Registry

Alla behållaravbildningar i registret (Azure Container Registry) krypteras i vila. Azure krypterar automatiskt en avbildning innan du lagrar den och dekrypterar den när Azure Machine Learning hämtar avbildningen.

Om du vill använda dina egna (kundhanterade) nycklar för att kryptera ditt Azure-behållarregister måste du skapa en egen ACR och bifoga den när du etablerar arbetsytan eller krypterar standardinstansen som skapas vid etableringen av arbetsytan.

Ett exempel på hur du skapar en arbetsyta med hjälp av ett befintligt Azure-behållarregister finns i följande artiklar:

* [Skapa en arbetsyta för Azure Machine Learning med Azure CLI](how-to-manage-workspace-cli.md).
* [Använda en Azure Resource Manager-mall för att skapa en arbetsyta för Azure Machine Learning](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Azure Container-instans

Du kan kryptera en distribuerad Azure Container Instance (ACI) resurs med hjälp av kundhanterade nycklar. Den kundhanterade nyckeln som används för ACI kan lagras i Azure Key Vault för din arbetsyta. Information om hur du genererar en nyckel finns i [Kryptera data med en kundhanterad nyckel](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Om du vill använda nyckeln när du distribuerar en modell `AciWebservice.deploy_configuration()`till Azure Container Instance skapar du en ny distributionskonfiguration med . Ange viktig information med hjälp av följande parametrar:

* `cmk_vault_base_url`: URL:en för nyckelvalvet som innehåller nyckeln.
* `cmk_key_name`: Namnet på nyckeln.
* `cmk_key_version`: Versionen av nyckeln.

Mer information om hur du skapar och använder en distributionskonfiguration finns i följande artiklar:

* [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) referens
* [Var och hur du distribuerar](how-to-deploy-and-where.md)
* [Distribuera en modell till Azure Container Instances](how-to-deploy-azure-container-instance.md)

Mer information om hur du använder en kundhanterad nyckel med ACI finns i [Kryptera data med en kundhanterad nyckel](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Du kan kryptera en distribuerad Azure Kubernetes Service-resurs med hjälp av kundhanterade nycklar när som helst. Mer information finns i [Ta med egna nycklar med Azure Kubernetes Service](../aks/azure-disk-customer-managed-keys.md). 

Med den här processen kan du kryptera både data- och OS-disken för de distribuerade virtuella datorerna i Kubernetes-klustret.

> [!IMPORTANT]
> Den här processen fungerar bara med AKS K8s version 1.17 eller senare. Azure Machine Learning har lagt till stöd för AKS 1.17 den 13 januari 2020.

#### <a name="machine-learning-compute"></a>Beräkning av maskininlärning

OS-disken för varje beräkningsnod som lagras i Azure Storage krypteras med Microsoft-hanterade nycklar i Azure Machine Learning-lagringskonton. Det här beräkningsmålet är efemärt och kluster skalas vanligtvis ned när inga körningar köas. Den underliggande virtuella datorn avetablerades och OS-disken tas bort. Azure Disk Encryption stöds inte för OS-disken.

Varje virtuell dator har också en lokal tillfällig disk för OS-åtgärder. Om du vill kan du använda disken för att arrangera träningsdata. Disken krypteras som standard för arbetsytor med parametern `hbi_workspace` inställd på `TRUE`. Den här miljön är kortlivad endast under hela körningen och krypteringsstödet är begränsat till systemhanterade nycklar.

#### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks kan användas i Azure Machine Learning-pipelines. Som standard krypteras Databricks File System (DBFS) som används av Azure Databricks med en Microsoft-hanterad nyckel. Information om hur du konfigurerar Azure Databricks för att använda kundhanterade nycklar finns i [Konfigurera kundhanterade nycklar på standard (root) DBFS](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="encryption-in-transit"></a>Kryptering under överföring

Du kan använda TLS för att skydda intern kommunikation mellan Azure Machine Learning-mikrotjänster och för att skydda externa anrop till bedömningsslutpunkten. All Azure Storage-åtkomst sker också via en säker kanal.

Mer information finns i [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Använda Azure Key Vault

Azure Machine Learning använder Azure Key Vault-instansen som är associerad med arbetsytan för att lagra autentiseringsuppgifter av olika slag:

* Anslutningssträngen för associerat lagringskonto
* Lösenord till Azure Container Repository-instanser
* Anslutningssträngar till datalager

SSH-lösenord och nycklar för att beräkna mål som Azure HDInsight och virtuella datorer lagras i ett separat nyckelvalv som är associerat med Microsoft-prenumerationen. Azure Machine Learning lagrar inga lösenord eller nycklar som tillhandahålls av användare. I stället genererar, auktoriserar och lagrar de egna SSH-nycklarna för att ansluta till virtuella datorer och HDInsight för att köra experimenten.

Varje arbetsyta har en associerad systemtilldelad hanterad identitet som har samma namn som arbetsytan. Den här hanterade identiteten har åtkomst till alla nycklar, hemligheter och certifikat i nyckelvalvet.

## <a name="data-collection-and-handling"></a>Insamling och hantering av data

### <a name="microsoft-collected-data"></a>Microsoft samlade in data

Microsoft kan samla in identifierande information som inte är användaren, till exempel datauppsättningsnamnet eller maskininlärningsexperimentnamnet) eller jobbmiljövariabler för diagnostikändamål. Alla sådana data lagras med hjälp av Microsoft-hanterade nycklar i lagring som finns i Microsofts ägda prenumerationer och följer [Microsofts standard sekretesspolicy och datahanteringsstandarder](https://privacy.microsoft.com/privacystatement).

Microsoft rekommenderar också att känslig information (t.ex. kontonyckelhemligheter) inte lagras i miljövariabler. Miljövariabler loggas, krypteras och lagras av oss. På samma sätt när du namnger [runid](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)bör du undvika att inkludera känslig information som användarnamn eller hemliga projektnamn. Den här informationen kan visas i telemetriloggar som är tillgängliga för Microsoft Support-tekniker.

Du kan välja bort diagnostikdata som `hbi_workspace` samlas `TRUE` in genom att ställa in parametern till när du etablerar arbetsytan. Den här funktionen stöds när du använder AzureML Python SDK-, CLI-, REST-API:er eller Azure Resource Manager-mallarna.

### <a name="microsoft-generated-data"></a>Microsoft-genererade data

När du använder tjänster som Automatiserad maskininlärning kan Microsoft generera en tillfällig, förbehandlad data för utbildning av flera modeller. Dessa data lagras i ett datalager på arbetsytan, vilket gör att du kan tillämpa åtkomstkontroller och kryptering på rätt sätt.

Du kanske också vill kryptera [diagnostikinformation som loggats från din distribuerade slutpunkt](how-to-enable-app-insights.md) till din Azure Application Insights-instans.

## <a name="monitoring"></a>Övervakning

### <a name="metrics"></a>Mått

Du kan använda Azure Monitor-mått för att visa och övervaka mått för din Azure Machine Learning-arbetsyta. I [Azure-portalen](https://portal.azure.com)väljer du arbetsytan och väljer sedan **Mått:**

[![Skärmbild som visar exempelmått för en arbetsyta](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

Måtten innehåller information om körningar, distributioner och registreringar.

Mer information finns [i Mått i Azure Monitor](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Aktivitetslogg

Du kan visa aktivitetsloggen för en arbetsyta om du vill visa olika åtgärder som utförs på arbetsytan. Loggen innehåller grundläggande information som operationsnamn, händelseinitierare och tidsstämpel.

Den här skärmbilden visar aktivitetsloggen för en arbetsyta:

[![Skärmbild som visar aktivitetsloggen för en arbetsyta](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Information om bedömningsbegäran lagras i Application Insights. Application Insights skapas i din prenumeration när du skapar en arbetsyta. Loggad information omfattar fält som:

* HTTPMethod
* Useragent
* ComputeType (Beräkningstyp)
* RequestUrl (förfråganUrl)
* Statuskod
* Id
* Varaktighet

> [!IMPORTANT]
> Vissa åtgärder på arbetsytan Azure Machine Learning loggar inte information till aktivitetsloggen. Till exempel loggas inte början av en träningskörning och registrering av en modell.
>
> Vissa av dessa åtgärder visas i området **Aktiviteter** på arbetsytan, men dessa meddelanden anger inte vem som initierade aktiviteten.

## <a name="data-flow-diagrams"></a>Dataflödesdiagram

### <a name="create-workspace"></a>Skapa arbetsyta

I följande diagram visas arbetsflödet för att skapa arbetsytan.

* Du loggar in på Azure AD från en av de Azure Machine Learning-klienter som stöds (Azure CLI, Python SDK, Azure-portal) och begär lämplig Azure Resource Manager-token.
* Du anropar Azure Resource Manager för att skapa arbetsytan. 
* Azure Resource Manager kontaktar Azure Machine Learning-resursprovidern för att etablera arbetsytan.

Ytterligare resurser skapas i användarens prenumeration när arbetsytan skapas:

* Key Vault (för att lagra hemligheter)
* Ett Azure-lagringskonto (inklusive blob och filresurs)
* Azure Container Registry (för att lagra Docker-avbildningar för slutledning/bedömning och experiment)
* Application Insights (för att lagra telemetri)

Användaren kan också etablera andra beräkningsmål som är kopplade till en arbetsyta (som Azure Kubernetes Service eller virtuella datorer) efter behov.

[![Skapa arbetsflöde för arbetsyta](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Spara källkod (utbildningsskript)

I följande diagram visas arbetsflödet för ögonblicksbild av kod.

Associerad med en Azure Machine Learning-arbetsyta är kataloger (experiment) som innehåller källkoden (utbildningsskript). Dessa skript lagras på din lokala dator och i molnet (i Azure Blob-lagring för din prenumeration). Kodögonblicksbilderna används för körning eller inspektion för historisk granskning.

[![Arbetsflöde för ögonblicksbild av kod](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>Utbildning

I följande diagram visas träningsarbetsflödet.

* Azure Machine Learning anropas med ögonblicksbild-ID för kodögonblicksbilden som sparats i föregående avsnitt.
* Azure Machine Learning skapar ett körnings-ID (valfritt) och en Machine Learning-tjänsttoken, som senare används av beräkningsmål som Machine Learning Compute/VMs för att kommunicera med machine learning-tjänsten.
* Du kan välja antingen ett hanterat beräkningsmål (som Machine Learning Compute) eller ett ohanterat beräkningsmål (som virtuella datorer) för att köra utbildningsjobb. Här är dataflödena för båda scenarierna:
   * Virtuella datorer/HDInsight, som nås av SSH-autentiseringsuppgifter i ett nyckelvalv i Microsoft-prenumerationen. Azure Machine Learning kör hanteringskod för beräkningsmålet som:

   1. Förbereder miljön. (Docker är ett alternativ för virtuella datorer och lokala datorer. Se följande steg för Machine Learning Compute för att förstå hur experiment på Docker-behållare fungerar.)
   1. Hämtar koden.
   1. Ställer in miljövariabler och konfigurationer.
   1. Kör användarskript (kodögonblicksbilden som nämns i föregående avsnitt).

   * Machine Learning Compute, nås via en arbetsytehanterad identitet.
Eftersom Machine Learning Compute är ett hanterat beräkningsmål (det vill ha det hanterat av Microsoft) körs det under din Microsoft-prenumeration.

   1. Remote Docker konstruktion sparkas igång, om det behövs.
   1. Hanteringskod skrivs till användarens Azure Files-resurs.
   1. Behållaren startas med ett första kommando. Det vill än, hanteringskod som beskrivs i föregående steg.

#### <a name="querying-runs-and-metrics"></a>Fråga körningar och mått

I flödesdiagrammet nedan inträffar det här steget när träningsberäkningsmålet skriver tillbaka körningsmåtten till Azure Machine Learning från lagring i Cosmos DB-databasen. Klienter kan anropa Azure Machine Learning. Machine Learning hämtar i sin tur mått från Cosmos DB-databasen och returnerar dem tillbaka till klienten.

[![Arbetsflöde för utbildning](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>Skapa webbtjänster

I följande diagram visas inferensarbetsflödet. Inferens, eller modellbedömning, är den fas där den distribuerade modellen används för förutsägelse, oftast på produktionsdata.

Här är detaljerna:

* Användaren registrerar en modell med hjälp av en klient som Azure Machine Learning SDK.
* Användaren skapar en bild med hjälp av en modell, en poängfil och andra modellberoenden.
* Docker-avbildningen skapas och lagras i Azure Container Registry.
* Webbtjänsten distribueras till beräkningsmålet (behållarinstanser/AKS) med hjälp av avbildningen som skapades i föregående steg.
* Information om bedömningsbegäran lagras i Application Insights, som finns i användarens prenumeration.
* Telemetri överförs också till Microsoft/Azure-prenumerationen.

[![Inferensarbetsflöde](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>Nästa steg

* [Säkra Azure Machine Learning-webbtjänster med TLS](how-to-secure-web-service.md)
* [Använda en machine learning-modell som distribueras som en webbtjänst](how-to-consume-web-service.md)
* [Så här kör du batchprognoser](how-to-use-parallel-run-step.md)
* [Övervaka dina Azure Machine Learning-modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Använda Azure Machine Learning med Virtuella Azure-nätverk](how-to-enable-virtual-network.md)
* [Bästa praxis för system för byggrekommendation](https://github.com/Microsoft/Recommenders)
* [Skapa ett API för rekommendation i realtid på Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
