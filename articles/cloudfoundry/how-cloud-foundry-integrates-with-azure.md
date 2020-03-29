---
title: Så här integrerar cloud foundry med Azure | Microsoft-dokument
description: Beskriver hur Cloud Foundry kan använda Azure-tjänster för att förbättra Enterprise-upplevelsen
services: virtual-machines-linux
documentationcenter: ''
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: 04ef72f7ec70b370305395ae8de8180f4594b43b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277334"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integrera Cloud Foundry med Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) är en PaaS-plattform som körs ovanpå molnleverantörernas IaaS-plattform. Det ger konsekvent programdistributionsupplevelse över molnleverantörer. Den kan också integreras med olika Azure-tjänster, med HA i företagsklass, skalbarhet och kostnadsbesparingar.
Det finns [6 delsystem i Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/), som kan flexibelt skala online, inklusive: Routning, autentisering, program livscykelhantering, tjänsthantering, meddelanden och övervakning. För vart och ett av undersystemen kan du konfigurera Cloud Foundry så att den använder azure-korrespondenttjänsten. 

![Molngjuteri på Azure Integration Architecture](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Hög tillgänglighet och skalbarhet
### <a name="managed-disk"></a>Hanterad disk
Bosh använder Azure CPI (Cloud Provider Interface) för diskskapande och borttagning av rutiner. Som standard används ohanterade diskar. Det kräver att kunden manuellt skapar lagringskonton och sedan konfigurerar kontona i CF-manifestfiler. Detta beror på begränsningen av antalet diskar per lagringskonto.
Nu [hanteras disk](https://azure.microsoft.com/services/managed-disks/) är tillgänglig, erbjuder hanterad säker och tillförlitlig disklagring för virtuella datorer. Kunden behöver inte längre hantera lagringskontot för skala och HA. Azure ordnar diskar automatiskt. Oavsett om det är en ny eller befintlig distribution hanterar Azure-KPI skapandet eller migreringen av den hanterade disken under en CF-distribution. Det stöds med PCF 1,11. Du kan också utforska vägledningen för [molngjuterihanterad disk](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) med öppen källkod som referens. 
### <a name="availability-zone-"></a>Tillgänglighetszon *
Som en molnbaserad programplattform är Cloud Foundry utformad med [fyra nivåer av hög tillgänglighet](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Medan de tre första nivåerna av programvarufel kan hanteras av CF-systemet självt, tillhandahålls plattformsfeltolerans av molnleverantörer. De viktigaste CF-komponenterna bör skyddas med en molnleverantörs plattform HA-lösning. Detta inkluderar GoRouters, Diego Brains, CF databas och service plattor. Som standard används [Azure Availability Set](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) för feltolerans mellan kluster i ett datacenter.
Den nya är [Azure Availability Zone](https://docs.microsoft.com/azure/availability-zones/az-overview ) släpps nu, vilket innebär att feltoleransen till nästa nivå, med låg redundans för svarstid över datacenter.
Azure Availability Zone uppnår HA genom att placera en uppsättning virtuella datorer i 2 + datacenter, varje uppsättning virtuella datorer är överflödiga för andra uppsättningar. Om en zon är nere, är de andra uppsättningarna fortfarande levande, isolerade från katastrofen.
> [!NOTE] 
> Azure Availability Zone erbjuds inte till alla regioner ännu, kontrollera det senaste [meddelandet för listan över regioner som stöds](https://docs.microsoft.com/azure/availability-zones/az-overview). För Molngjutning med öppen källkod kontrollerar du [Azure-tillgänglighetszon för molngjutningsvägledning med öppen källkod](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. Nätverksdirigering
Som standard används Azure basic load balancer för inkommande CF API/apps-begäranden, vidarebefordra dem till Gorouters. CF komponenter som Diego Brain, MySQL, ERT kan också använda lastbalanseraren för att balansera trafiken för HA. Azure tillhandahåller också en uppsättning fullständigt hanterade belastningsutjämningslösningar. Om du letar efter TLS-avslutning ("SSL-avlastning") eller per HTTP/HTTPS-begäran om programlagerbearbetning bör du överväga Application Gateway. För hög tillgänglighet och skalbarhet belastningsutjämning på lager 4, överväga standard belastningsutjämnare.
### <a name="azure-application-gateway-"></a>Azure-programgateway *
[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) erbjuder olika funktioner för belastningsutjämning på lager 7, inklusive SSL-avlastning, end to end SSL, Web Application Firewall, cookie-baserad sessionstillhörighet med mera. Du kan [konfigurera Application Gateway i Molngjuteri med öppen källkod](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). För PCF, kontrollera [PCF 2.1 viktig information](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) för POC test.

### <a name="azure-standard-load-balancer-"></a>Azure Standard belastningsutjämning *
Azure Load Balancer är en layer 4 belastningsutjämnare. Den används för att distribuera trafiken mellan instanser av tjänster i en belastningsbalanserad uppsättning. Standardversionen innehåller [avancerade funktioner](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) ovanpå den grundläggande versionen. Till exempel 1. Maxgränsen för backend-poolen höjs från 100 till 1 000 virtuella datorer.  2. Slutpunkterna stöder nu flera tillgänglighetsuppsättningar i stället för en enda tillgänglighetsuppsättning.  3. Ytterligare funktioner som HA-portar, rikare övervakningsdata och så vidare. Om du flyttar till Azure Availability Zone krävs standardbelastningsutjämning. För en ny distribution rekommenderar vi att du börjar med Azure Standard Load Balancer. 

## <a name="3-authentication"></a>3. Autentisering 
[Cloud Foundry User Account and Authentication](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) är den centrala identitetshanteringstjänsten för CF och dess olika komponenter. [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är Microsofts tjänst för molnbaserad katalog och identitetshantering för flera innehavare. Som standard används UAA för cloud foundry-autentisering. Som ett avancerat alternativ stöder UAA också Azure AD som ett externt användararkiv. Azure AD-användare kan komma åt Cloud Foundry med sin LDAP-identitet, utan ett Cloud Foundry-konto. Följ dessa steg för att [konfigurera Azure AD för UAA i PCF](https://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Datalagring för Cloud Foundry Runtime System
Cloud Foundry erbjuder stor utökningsbarhet för att använda Azure blobstore- eller Azure MySQL/PostgreSQL-tjänster för systemlagring av programkörning.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Azure Blobstore för Cloud Foundry Cloud Controller blobstore
Cloud Controller-blobstore är ett kritiskt datalager för buildpacks, droplets, paket och resurspooler. Som standard används NFS-servern för Cloud Controller blobstore. Använd Azure Blob Storage som externt arkiv för att undvika enstaka felpunkt. Kolla in dokumentationen för [molngjuteri](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) för bakgrund och [alternativ i Pivotal Cloud Foundry](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL som Cloud Foundry Elastic Run Time Database *
CF Elastic Runtime kräver två större systemdatabaser:
#### <a name="ccdb"></a>CCDB (på andra sätt) 
Cloud Controller-databasen.  Cloud Controller tillhandahåller REST API-slutpunkter för klienter att komma åt systemet. CCDB lagrar tabeller för organisationer, utrymmen, tjänster, användarroller med mera för Molnkontrollanten.
#### <a name="uaadb"></a>UAADB (storbritannien) 
Databasen för användarkonto och autentisering. Den lagrar användarautentiseringsrelaterade data, till exempel krypterade användarnamn och lösenord.

Som standard kan en lokal systemdatabas (MySQL) användas. För HA och skala, utnyttja Azure-hanterade MySQL- eller PostgreSQL-tjänster. Här är instruktionen [att aktivera Azure MySQL/PostgreSQL för CCDB, UAADB och andra systemdatabaser med Open Source Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Öppen servicemäklare
Azure-tjänstmäklare erbjuder konsekvent gränssnitt för att hantera programmets åtkomst till Azure-tjänster. Det nya [Open Service Broker for Azure-projektet](https://github.com/Azure/open-service-broker-azure) är ett enkelt och enkelt sätt att leverera tjänster till program i Cloud Foundry, OpenShift och Kubernetes. Se [Azure Open Service Broker för PCF-panel](https://pivotal.io/platform/services-marketplace/data-management/microsoft-azure) för distributionsinstruktioner på PCF.

## <a name="6-metrics-and-logging"></a>6. Mått och loggning
Azure Log Analytics-munstycket är en Cloud Foundry-komponent som vidarebefordrar mått från [Cloud Foundry loggregator firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) till [Azure Monitor-loggar](https://azure.microsoft.com/services/log-analytics/). Med munstycket kan du samla in, visa och analysera hälso- och prestandamått för CF-systemet över flera distributioner.
Klicka [här](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) om du vill lära dig hur du distribuerar Azure Log Analytics-munstycket till både open source- och Pivotal Cloud Foundry-miljön och sedan komma åt data från Azure Monitor-loggkonsolen. 
> [!NOTE]
> Från PCF 2.0 vidarebefordras BOSH-hälsomått för virtuella datorer till Loggregator Firehose som standard och är integrerade i Azure Monitor-loggkonsolen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. Kostnadsbesparande
### <a name="cost-saving-for-devtest-environments"></a>Kostnadsbesparing för utvecklings-/testmiljöer
#### <a name="b-series-"></a>B-serien: *
Medan F- och D VM-serien ofta rekommenderades för Pivotal Cloud Foundry-produktionsmiljön, ger den nya "burstable" [B-serien](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) nya alternativ. B-serien burstable virtuella datorer är idealiska för arbetsbelastningar som inte behöver full prestanda för processorn kontinuerligt, som webbservrar, små databaser och utvecklings- och testmiljöer. Dessa arbetsbelastningar har vanligtvis burstable prestandakrav. Det är $ 0.012/hour (B1) jämfört med $ 0.05/hour (F1), se hela listan över [VM storlekar](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) och [priser](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) för detaljer. 
#### <a name="managed-standard-disk"></a>Hanterad standarddiskett: 
Premium diskar rekommenderades för tillförlitlig prestanda i produktionen.  Med [Managed Disk](https://azure.microsoft.com/services/managed-disks/)kan standardlagring också ge liknande tillförlitlighet, med olika prestanda. För arbetsbelastning som inte är prestandakänslig, till exempel utveckling/test eller icke-kritisk miljö, erbjuder hanterade standarddiskar ett alternativt alternativ med lägre kostnad.  
### <a name="cost-saving-in-general"></a>Kostnadsbesparing i allmänhet 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Betydande vm kostnadsbesparingar med Azure-reservationer: 
Idag faktureras alla virtuella cf-datorer med "on-demand"-priser, även om miljöerna vanligtvis stannar uppe på obestämd tid. Nu kan du reservera VM-kapacitet på en 1 eller 3-årsperiod och få rabatter på 45-65%. Rabatter tillämpas i faktureringssystemet, utan ändringar i din miljö. Mer information finns i [Så här fungerar Azure-reservationer](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Hanterad Premium-disk med mindre storlekar: 
Hanterade diskar stöder mindre diskstorlekar, till exempel P4(32 GB) och P6(64 GB) för både premium- och standarddiskar. Om du har små arbetsbelastningar kan du spara kostnader när du migrerar från premiumdiskar till hanterade premiumdiskar.
#### <a name="use-azure-first-party-services"></a>Använd Azure First Party Services: 
Om du drar nytta av Azures förstapartstjänst minskar den långsiktiga administrationskostnaden, förutom HA och tillförlitlighet som nämns i ovanstående avsnitt. 

Pivotal har lanserat ett [LITET FOTAVTRYCK ERT](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) för PCF-kunder, komponenterna är samlokala till bara 4 virtuella datorer, som kör upp till 2500 programinstanser. Testversionen är nu tillgänglig via [Azure Market place](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Efterföljande moment
Azure-integreringsfunktioner är först tillgängliga med [Open Source Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), innan den är tillgänglig på Pivotal Cloud Foundry. Funktioner som är markerade med * är fortfarande inte tillgängliga via PCF. Integrering av molngjuteri med Azure Stack ingår inte heller i det här dokumentet.
För PCF-stöd för funktioner som är markerade med *, eller Cloud Foundry-integrering med Azure Stack, kontakta din Pivotal och Microsoft-kontohanterare för senaste status. 

