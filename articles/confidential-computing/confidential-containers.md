---
title: Konfidentiella behållare på Azure Kubernetes service (AKS)
description: Läs mer om stöd för ej modifierade behållare på konfidentiella behållare.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 9/22/2020
ms.author: amgowda
ms.service: container-service
ms.openlocfilehash: 35518a90ff3db2b951e0310970afd6d78dd25807
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122211"
---
# <a name="confidential-containers"></a>Konfidentiella behållare

## <a name="overview"></a>Översikt

Gör det möjligt för utvecklare att använda ett **befintligt Docker-program (ny eller befintlig)** och köra det på ett säkert sätt på Azure Kubernetes service (AKS) via stöd för konfidentiella databeräknings noder.

Konfidentiella behållare hjälper till att skydda:

- data integritet 
- data sekretess
- kod integritet
- skydd av container kod genom kryptering
- maskin varubaserade garantier
- Tillåt körning av befintliga appar
- skapa förtroende för maskin varu roten

En maskinvarubaserad betrodd körnings miljö (TEE) är en viktig komponent som används för att ge starka garantier genom maskinvaru-och program varu mått från TCB-komponenter (Trusted Computing Base). Kontroller av dessa mått hjälper till att verifiera den förväntade beräkningen och verifiera all manipulering av behållar apparna.

Konfidentiella behållare stöder anpassade program som har utvecklats med **python, Java, Node JS osv. eller paketerade program som nginx, Redis cache, memcache**och så vidare, som ska köras oförändrat på AKS.

Konfidentiella behållare är den snabbaste sökvägen till behållar sekretess, inklusive behållar skyddet genom kryptering, vilket möjliggör ökning och Skift utan några/minimala ändringar i affärs logiken.

![Konvertering av konfidentiell behållare](./media/confidential-containers/conf-con-deploy-process.jpg)


## <a name="confidential-container-enablers"></a>Konfidentiella behållar aktiveringar

Om du vill köra en befintlig Docker-behållare måste program på konfidentiella data behandlings noder kräva ett abstraktions lager eller SGX-programvara för att kunna utnyttja den särskilda CPU-instruktionen. SGX-programvaran gör det också möjligt att skydda känslig program kod och skapa en direkt körning till CPU för att ta bort gäst operativ systemet, värd-OS eller hypervisor. Det här skyddet minskar de övergripande angrepps områdena och sårbarheter med operativ system-eller hypervisor-lager.

Konfidentiella behållare stöds fullt ut på AKS och aktive ras via Azure-partner och OSS-projekt (Open Source Software). Utvecklare kan välja program varu leverantörer baserat på funktioner, integrering med Azure-tjänster och stöd för verktyg.

## <a name="partner-enablers"></a>Partner aktiveringar
> [!NOTE]
> Nedanstående lösningar erbjuds via Azure-partner och kan medföra licens avgifter. Verifiera partner program villkoren oberoende av varandra. 

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) erbjuder utvecklare ett val av en portal och CLI-baserad upplevelse för att ta med sina program i behållare och konvertera dem till SGX-kompatibla konfidentiella behållare utan att behöva ändra eller kompilera om programmet. Fortanix ger flexibiliteten att köra och hantera den bredaste uppsättningen program, inklusive befintliga program, nya enklaven program och förpaketerade program. Användare kan börja med [enklaven Manager](https://em.fortanix.com/) -gränssnittet eller [REST-API: er](https://www.fortanix.com/api/em/) för att skapa konfidentiella behållare genom att följa [Snabbstart](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) guide för Azure Kubernetes-tjänsten.

![Distributions process för Fortanix](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain)

[Scone](https://scontain.com/index.html?lang=en) stöder säkerhets principer som kan generera certifikat, nycklar och hemligheter och säkerställer att de endast visas för bestyrkade tjänster i ett program. På så sätt bekräftar tjänsterna i ett program automatiskt varandra via TLS – utan att behöva ändra programmen eller TLS. Detta beskrivs med hjälp av ett enkelt kolv program här: https://sconedocs.github.io/flask_demo/  

SCONE kan konvertera befintliga binärfiler till program som körs i enclaves utan att behöva ändra programmet eller kompilera om programmet. SCONE skyddar också tolkade språk som python genom att kryptera både datafiler och python-Koda filer. Med hjälp av en SCONE säkerhets princip kan en skydda de krypterade filerna mot obehöriga åtkomster, ändringar och återställningar. Hur du "sconify" ett befintligt python-program beskrivs [här](https://sconedocs.github.io/sconify_image/)

![Scontain-flöde](./media/confidential-containers/scone-workflow.png)

Scone-distributioner på konfidentiella databeräknings noder med AKS stöds helt och hållet. Kom igång med ett exempel program här https://sconedocs.github.io/aks/

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) tillhandahåller SGX-plattforms program vara som gör att du kan köra oförändrade behållare på AKS. Läs mer om funktionerna och kolla in exempel programmen [här](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp).

Kom igång med ett exempel Redis Cache och python-anpassat program [här](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)

![Anjuna process](./media/confidential-containers/anjuna-process-flow.png)

## <a name="oss-enablers"></a>OSS-aktiveringar 
> [!NOTE]
> Nedanstående lösningar erbjuds via projekt med öppen källkod och är inte direkt kopplade till Azures konfidentiella data behandling (ACC) eller Microsoft.  

### <a name="graphene"></a>Graphene

[Graphene](https://grapheneproject.io/) är ett enkelt gäst operativ system som är utformat för att köra ett enda Linux-program med minimala krav på värden. Graphene kan köra program i en isolerad miljö med fördelar som är jämförbara med att köra ett fullständigt operativ system och har stöd för att konvertera befintliga Docker-behållar program till Graphene-avskärmade behållare (generalsekretariatet).

Kom igång med ett exempel program och en distribution på AKS [här](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks)

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) är ett minnes säkert, LibOS (Multi-process Library OS) för Intel SGX. Det gör att äldre program kan köras på SGX med liten till inga ändringar i käll koden. Occlum skyddar mot konfidentialiteten för användar arbets belastningar samtidigt som du kan enkelt lyfta och byta till befintliga Docker-program.

Occlum stöder AKS-distributioner. Följ distributions anvisningarna med olika exempel program [här](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)


## <a name="confidential-containers-demo"></a>Demo om konfidentiella behållare
Visa demon för konfidentiell sjukvård med konfidentiella behållare. Exempel finns [här](https://github.com/Azure-Samples/confidential-container-samples/blob/main/confidential-healthcare-scone-confinf-onnx/README.md). 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Kontakta touch

Har du frågor om implementeringen eller vill bli en aktiverings fråga? Skicka ett e-postmeddelande till acconaks@microsoft.com

## <a name="reference-links"></a>Referens länkar

[Microsoft Azure Attestation](../attestation/overview.md)

[DCsv2 Virtual Machines](virtual-machine-solutions.md)

[Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)
