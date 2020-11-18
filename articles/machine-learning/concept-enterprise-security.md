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
ms.openlocfilehash: a9ad018980784a1f809ad28a77dacf9f0328fffa
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94873904"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Företags säkerhet och styrning för Azure Machine Learning

I den här artikeln får du lära dig om säkerhetsfunktioner som är tillgängliga för Azure Machine Learning.

När du använder en moln tjänst är det bästa sättet att begränsa åtkomsten till de användare som behöver den. Börja med att förstå autentiserings-och auktoriserings modellen som används av tjänsten. Du kanske också vill begränsa nätverks åtkomsten eller på ett säkert sätt ansluta resurser i ditt lokala nätverk till molnet. Data kryptering är också viktigt, både i vila och medan data flyttas mellan tjänster. Du kanske också vill skapa principer för att genomdriva vissa konfigurationer eller logg när icke-kompatibla konfigurationer skapas. Slutligen måste du kunna övervaka tjänsten och skapa en Gransknings logg för all aktivitet.

> [!NOTE]
> Informationen i den här artikeln fungerar med Azure Machine Learning python SDK-version 1.0.83.1 eller högre.

## <a name="authentication--authorization"></a>Autentisering & auktorisering

Mest autentisering för att Azure Machine Learning resurser använder Azure Active Directory (Azure AD) för autentisering och rollbaserad åtkomst kontroll (Azure RBAC) för auktorisering. Undantagen till detta är:

* __SSH__: du kan aktivera SSH-åtkomst till vissa beräknings resurser, till exempel Azure Machine Learning beräknings instans. SSH-åtkomst använder nyckelbaserad autentisering. Mer information om hur du skapar SSH-nycklar finns i [skapa och hantera SSH-nycklar](../virtual-machines/linux/create-ssh-keys-detailed.md). Information om hur du aktiverar SSH-åtkomst finns i [skapa och hantera Azure Machine Learning beräknings instans](how-to-create-manage-compute-instance.md).
* __Modeller som distribueras som webb tjänster__: distributioner av webb tjänster kan använda __nyckel__ -eller __tokenbaserad__ åtkomst kontroll. Nycklar är statiska strängar. Tokens hämtas med hjälp av ett Azure AD-konto. Mer information finns i [Konfigurera autentisering för modeller som distribueras som en webb tjänst](how-to-authenticate-web-service.md).

Specifika tjänster som Azure Machine Learning är beroende av, t. ex. Azure Data Storage Services, har sina egna metoder för autentisering och auktorisering. Mer information om lagrings tjänsternas autentisering finns i [Anslut till lagrings tjänster](how-to-access-data.md).

### <a name="azure-ad-authentication"></a>Azure AD-autentisering

Multi-Factor Authentication stöds om Azure Active Directory (Azure AD) har kon figurer ATS för att använda den. Här är autentiseringsprocessen:

1. Klienten loggar in på Azure AD och hämtar en Azure Resource Manager-token.  Användare och tjänstens huvud namn stöds fullt ut.
1. Klienten presenterar token för att Azure Resource Manager och till alla Azure Machine Learning.
1. Tjänsten Machine Learning tillhandahåller en Machine Learning tjänst-token för användar beräknings målet (till exempel Machine Learning-beräkning). Denna token används av användar beräknings målet för att anropa till Machine Learning tjänsten när körningen har slutförts. Omfattningen är begränsad till arbets ytan.

[![Autentisering i Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Mer information finns i [autentisering för Azure Machine Learning-arbetsyta](how-to-setup-authentication.md).

### <a name="azure-rbac"></a>Azure RBAC

Du kan skapa flera arbets ytor och varje arbets yta kan delas av flera personer. Du kan styra vilka funktioner eller åtgärder för arbets ytans användare som har åtkomst genom att tilldela deras Azure AD-konto till Azure RBAC-roller. Följande är de inbyggda rollerna:

* Ägare
* Deltagare
* Läsare

Ägare och deltagare kan använda alla beräknings mål och data lager som är kopplade till arbets ytan.  

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

Om de inbyggda rollerna inte uppfyller dina behov kan du skapa anpassade roller. Anpassade roller styr alla åtgärder inom en arbets yta, till exempel att skapa en beräkning, skicka in en körning, registrera ett data lager eller distribuera en modell. Anpassade roller kan ha behörigheterna läsa, skriva eller ta bort för de olika resurserna i en arbets yta, till exempel kluster, data lager, modeller och slut punkter. Du kan göra rollen tillgänglig på en speciell arbets yta, en bestämd resurs grupps nivå eller en speciell prenumerations nivå. Mer information finns i [Hantera användare och roller i en Azure Machine Learning-arbetsyta](how-to-assign-roles.md).

Mer information om hur du använder RBAC med Kubernetes finns i [Azure Role-Based Access Control för auktorisering av Kubernetes](../aks/manage-azure-rbac.md).

> [!IMPORTANT]
> Azure Machine Learning är beroende av andra Azure-tjänster som Azure Blob Storage och Azure Kubernetes Services. Varje Azure-tjänst har sina egna Azure RBAC-konfigurationer. För att uppnå önskad nivå av åtkomst kontroll kan du behöva använda båda Azure RBAC-konfigurationerna för Azure Machine Learning och för de tjänster som används med Azure Machine Learning.

> [!WARNING]
> Azure Machine Learning stöds med Azure Active Directory Business-to-Business-samarbete, men stöds för närvarande inte med Azure Active Directory samarbete mellan företag.

### <a name="managed-identities"></a>Hanterade identiteter

Varje arbets yta har också en associerad systemtilldelad [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) som har samma namn som arbets ytan. Den hanterade identiteten används för att på ett säkert sätt få åtkomst till resurser som används av arbets ytan. Den har följande behörigheter för anslutna resurser:

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

Alternativt kan du konfigurera dina egna hanterade identiteter för användning med Azure Virtual Machines-och Azure Machine Learning Compute-kluster. Med en virtuell dator kan den hanterade identiteten användas för att komma åt din arbets yta från SDK, i stället för den enskilda användarens Azure AD-konto. Med ett beräknings kluster används den hanterade identiteten för att få åtkomst till resurser som skyddade data lager som användaren som kör utbildnings jobbet kanske inte har åtkomst till. Mer information finns i [autentisering för Azure Machine Learning-arbetsyta](how-to-setup-authentication.md).

## <a name="network-security-and-isolation"></a>Nätverks säkerhet och isolering

Om du vill begränsa fysisk åtkomst till Azure Machine Learning resurser kan du använda Azure Virtual Network (VNet). Med virtuella nätverk kan du skapa nätverks miljöer som är delvis eller helt isolerade från det offentliga Internet. Detta minskar angrepps ytan för din lösning, samt risken för data exfiltrering.

Mer information finns i följande dokument:

* [Översikt över virtuella nätverks isolering och sekretess](how-to-network-security-overview.md)
* [Skydda arbetsyteresurser](how-to-secure-workspace-vnet.md)
* [Skydda träningsmiljöer](how-to-secure-training-vnet.md)
* [Säker miljö för störningar](how-to-secure-inferencing-vnet.md)
* [Använda Studio i ett säkert virtuellt nätverk](how-to-enable-studio-virtual-network.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Datakryptering

Azure Machine Learning använder en mängd olika beräknings resurser och data lager. Mer information om hur var och en av dessa stöder data kryptering i vila och under överföring finns i [data kryptering med Azure Machine Learning](concept-data-encryption.md).

### <a name="microsoft-generated-data"></a>Microsoft-genererade data

När du använder tjänster som automatiserade Machine Learning kan Microsoft generera en tillfällig, förbehandlad data för att träna flera modeller. Dessa data lagras i ett data lager i din arbets yta, vilket gör att du kan tillämpa åtkomst kontroller och kryptering på lämpligt sätt.

Du kanske också vill kryptera [diagnostikinformation som loggats från den distribuerade slut punkten](how-to-enable-app-insights.md) till din Azure Application insikter-instans.

## <a name="monitoring"></a>Övervakning

Det finns flera övervaknings scenarier med Azure Machine Learning, beroende på roll och vad som övervakas.

| Roll | Övervakning som ska användas | Beskrivning |
| ---- | ----- | ----- |
| Admin, DevOps, MLOps | [Azure Monitor Mät värden](#azure-monitor), [aktivitets loggen](#activity-log), [sårbarhets sökning](#vulnerability-scanning) | Information om service nivå |
| Data expert, MLOps | [Övervaka körningar](#monitor-runs) | Information som loggats under utbildning körs |
| MLOps | [Samla in modell data](how-to-enable-data-collection.md), [övervaka med Application Insights](how-to-enable-app-insights.md) | Information som loggats av modeller som distribuerats som webb tjänster eller IoT Edge moduler|

### <a name="monitor-runs"></a>Övervaka körningar

Du kan övervaka experiment körningar i Azure Machine Learning, inklusive loggnings information i dina utbildnings skript. Den här informationen kan visas via SDK, Azure CLI och Studio. Mer information finns i följande artiklar:

* [Starta, övervaka och avbryta inlärnings körningar](how-to-manage-runs.md)
* [Aktivera loggar](how-to-track-experiments.md)
* [Visa loggar](how-to-monitor-view-training-logs.md)
* [Visualisera körningar med TensorBoard](how-to-monitor-tensorboard.md)

### <a name="azure-monitor"></a>Azure Monitor

Du kan använda Azure Monitor mått för att visa och övervaka mått för arbets ytan Azure Machine Learning. I [Azure Portal](https://portal.azure.com)väljer du din arbets yta och väljer sedan **mått**:

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

## <a name="audit-and-manage-compliance"></a>Granska och hantera efterlevnad

[Azure policy](../governance/policy/index.yml) är ett styrnings verktyg som gör att du kan se till att Azure-resurserna är kompatibla med dina principer. Med Azure Machine Learning kan du tilldela följande principer:

* **Kundhanterad nyckel**: granska eller Använd om arbets ytor måste använda en kundhanterad nyckel.
* **Privat länk**: granska om arbets ytor använder en privat slut punkt för att kommunicera med ett virtuellt nätverk.

Mer information om Azure Policy finns i Azure Policy- [dokumentationen](../governance/policy/overview.md).

Mer information om principerna som är speciella för Azure Machine Learning finns i [Granska och hantera efterlevnad med Azure policy](how-to-integrate-azure-policy.md).

## <a name="resource-locks"></a>Resurslås

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Nästa steg

* [Skydda Azure Machine Learning webb tjänster med TLS](how-to-secure-web-service.md)
* [Använda en Machine Learning modell som distribueras som en webb tjänst](how-to-consume-web-service.md)
* [Använda Azure Machine Learning med Azure-brandvägg](how-to-access-azureml-behind-firewall.md)
* [Använda Azure Machine Learning med Azure Virtual Network](how-to-network-security-overview.md)
* [Data kryptering i vila och under överföring](concept-data-encryption.md)
* [Bygg en API för rekommendationer i real tid i Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)
