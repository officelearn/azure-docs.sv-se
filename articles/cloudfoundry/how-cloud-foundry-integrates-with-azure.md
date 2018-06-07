---
title: Hur molnet Foundry kan integreras med Azure | Microsoft Docs
description: Beskriver hur molnet Foundry kan utlize Azure services för att förbättra Enterprice
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: jeconnoc
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: 1f4afbe1849210c55c392d014449224f2fe97b04
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655466"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integrera Foundry moln med Azure

[Molnet Foundry](https://docs.cloudfoundry.org/) är en PaaS-plattform som körs på molnet providers IaaS-plattformen. Den erbjuder enhetlig tillämpning distributionsupplevelse över molntjänstleverantörer. Dessutom kan den också integreras med olika Azure-tjänster, med enterprise-klass hög tillgänglighet, skalbarhet och besparingar.
Det finns [6 undersystem för molnet](https://docs.cloudfoundry.org/concepts/architecture/), som kan vara ett flexibelt sätt skala online, inklusive: routning, autentisering, livscykel programhantering, Service-hantering, övervakning och meddelanden. Du kan konfigurera molnet Foundry för att använda motsvarande Azure-tjänst för varje delsystem. 

![Molnet Foundry på Azure-integrering-arkitektur](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Hög tillgänglighet och skalbarhet
### <a name="managed-disk"></a>Hanterade diskar
Bosh använder Azure CPI (moln Provider Interface) för att skapa disken och tar bort rutiner. Ohanterad diskar används som standard. Det kräver att kunden att manuellt skapa storage-konton och sedan konfigurera konton i manifest CF-filer. Detta beror på begränsningar för antalet diskar per lagringskonto.
Nu [hanteras Disk](https://azure.microsoft.com/services/managed-disks/) är tillgänglig, erbjuder hanterade säkert och tillförlitligt disklagring för virtuella datorer. Kunden behöver inte längre hantera lagringskontot för skalbarhet och hög tillgänglighet. Azure ordnar diskar automatiskt. Om det är en ny eller en befintlig distribution, kommer att hantera Azure-CPI generering och migrering av den hantera disken under en CF-distribution. Det går med PCF 1.11. Du kan även utforska öppen källkod molnet Foundry [hanteras Disk vägledning](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) referens. 
### <a name="availability-zone-"></a>Tillgänglighet zonen *
Som en moln-intern programmet plattform molnet Foundry är utformad med [fyra nivå med hög tillgänglighet](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). När de första tre nivåerna av programfel kan hanteras av själva CF systemet som plattform feltolerans molntjänstleverantörer. Nyckelkomponenterna CF bör skyddas med en molntjänstleverantör plattform lösning för hög tillgänglighet. Detta inkluderar GoRouters, Diego hjärna, CF databasen och paneler. Som standard [Azure Tillgänglighetsuppsättning](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) används för feltolerans mellan kluster i ett datacenter.
Bra nya är [Azure tillgänglighet zonen](https://docs.microsoft.com/azure/availability-zones/az-overview ) släpps nu ta feltolerans till nästa nivå med låg latens redundans mellan datacenter.
Azure tillgänglighet zonen ger hög tillgänglighet genom att placera en uppsättning virtuella datorer i 2 + datacenter, varje uppsättning virtuella datorer är redundant till andra uppsättningar. Om en zon är nere, de andra som är fortfarande aktiv, isolerade från haveriet.
> [!NOTE] 
> Azure tillgänglighet zonen erbjuds inte till alla regioner ännu, kontrollera senast [meddelande för listan över regioner som stöds](https://docs.microsoft.com/azure/availability-zones/az-overview). Öppna källa molnet Foundry Kontrollera [Azure tillgänglighet zon för öppen källkod molnet Foundry vägledning](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. Nätverksroutning
Azure basic belastningsutjämnare används som standard för inkommande begäranden i CF API/appar, vidarebefordrar dem till Gorouters. CF-komponenter som Diego hjärna, MySQL, infoga kan också använda belastningsutjämnaren för att utjämna trafiken för hög tillgänglighet. Azure tillhandahåller dessutom en uppsättning helt hanterad nätverkslösningar för belastningsutjämning. Om du behöver för TLS-avslutning (”SSL avlastning”) eller per HTTP/HTTPS-begäran application layer bearbetning, Överväg Application Gateway. Överväg att standard belastningsutjämning för hög tillgänglighet och skalbarhet för belastningsutjämning på lager 4.
### <a name="azure-application-gateway-"></a>Azure Application Gateway *
[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) erbjuder olika layer 7 belastningsutjämning funktioner, inklusive SSL-avlastning, slutpunkt till slutpunkt SSL, Brandvägg för webbaserade program, cookie-baserad session tillhörighet och mer. Du kan [konfigurera Programgateway i öppen källa molnet Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). PCF, kontrollera den [PCF 2.1 viktig information](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) för Konceptbeviset.

### <a name="azure-standard-load-balancer-"></a>Standard Azure belastningsutjämnare *
Azure belastningsutjämnare är Layer 4 belastningsutjämning. Den används för att distribuera trafik mellan instanser av tjänster i en belastningsutjämnad uppsättning. Standardversionen ger [avancerade funktioner](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) över grundläggande version. Till exempel 1. Maxgränsen för backend-adresspool utlöses från 100 till 1000 virtuella datorer.  2. Slutpunkterna stöder nu flera tillgänglighetsuppsättningar i stället för samma tillgänglighetsuppsättning.  3. Ytterligare funktioner som hög tillgänglighet portar, bättre övervakningsdata osv. Om du flyttar till Azure tillgänglighet zonen krävs standard belastningsutjämnare. För en ny distribution rekommenderar vi att du börjar med Azure Standard belastningsutjämnaren. 

## <a name="3-authentication"></a>3. Autentisering 
[Molnet Foundry användarkontot och autentisering](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) är central identity management-tjänsten för CF och dess olika komponenter. [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är Microsofts flera innehavare, molnbaserad katalog och identity management-tjänsten. Som standard används UAA för molnet Foundry autentisering. Som ett avancerat alternativ stöd UAA också för Azure AD som en extern användare butik. Azure AD-användare kan komma åt molnet Foundry med sin identitet för LDAP, utan ett moln Foundry-konto. Följ dessa steg för att [konfigurera Azure AD för UAA i PCF](http://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Datalagring för molnet Foundry Runtime-systemet
Molnet Foundry erbjuder bra utökningsbarhet för att använda Azure blobstore eller Azure MySQL/PostgreSQL-tjänster för Programlagring runtime system.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Azure Blobstore för molnet Foundry moln Controller blobstore
Molnet Controller blobstore är ett kritiskt datalager för buildpacks, dropletar, paket och resurspooler. Som standard används NFS-servern för molnet Controller blobstore. Undvik felpunkt genom att använda Azure Blob Storage som extern butik. Checka ut den [moln Foundry dokumentationen](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) för bakgrunden, och [alternativ i spela en central molnet Foundry](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL som molntjänster Foundry elastisk köra tid databasen *
CF elastisk Runtime kräver två huvudsakliga systemdatabaser:
#### <a name="ccdb"></a>CCDB 
Molnet Controller-databasen.  Molnet styrenhet ger REST API-slutpunkter för klienter för att komma åt systemet. CCDB lagrar tabeller för organisationer, blanksteg, tjänster, användarroller och fler moln-styrenhet.
#### <a name="uaadb"></a>UAADB 
Databasen för användarkontot och autentisering. Den lagrar användarautentisering relaterade data, till exempel krypteras användarnamn och lösenord.

Som standard kan du använda en lokal databas (MySQL). För hög tillgänglighet och skala, använda Azure hanterade MySQL eller PostgreSQL-tjänster. Här följer instruktioner [aktiverar Azure MySQL/PostgreSQL för CCDB, UAADB och andra systemdatabaser med öppen källa molnet Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Öppna Service Broker
Azure service broker erbjuder ett konsekvent gränssnitt för att hantera programåtkomst till Azure-tjänster. Den nya [öppna Service Broker för Azure-projekt](https://github.com/Azure/open-service-broker-azure) ger en enkel och enkelt sätt att leverera tjänsterna till program över molnet Foundry, OpenShift och Kubernetes. Finns det [Azure öppna Service Broker för PCF panelen](https://network.pivotal.io/products/azure-open-service-broker-pcf/) anvisningar för distribution på PCF.

## <a name="6-metrics-and-logging"></a>6. Mått och loggning
Azure Log Analytics munstycket är en Cloud Foundry-komponent som vidarebefordrar mått från den [moln Foundry loggregator firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) till [Azure logganalys](https://azure.microsoft.com/services/log-analytics/). Med munstycket, kan du samla in, visa och analysera dina CF system hälso- och prestandamått över flera distributioner.
Klicka på [här](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) att lära dig hur du distribuerar Azure Log Analytics munstycket både öppen källkod och spela en central molnet Foundry miljö och komma åt data från Azure logganalys OMS-konsolen. 
> [!NOTE]
> BOSH hälsotillstånd mätvärden för virtuella datorer från PCF 2.0 vidarebefordras till Loggregator Firehose som standard och är integrerade i Azure logganalys OMS-konsolen.

## <a name="7-cost-saving"></a>7. Kostnad sparades
### <a name="cost-saving-for-devtest-environments"></a>Kostnadsbesparingar för utveckling och testning miljöer
#### <a name="b-series-"></a>B-serien: *
Medan F och virtuell dator D-serien har ofta rekommenderas för spela en central molnet Foundry produktionsmiljön, den nya ”burstable” [B-serien](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) ger nya alternativ. B-serien burstable VMs lämpar sig för arbetsbelastningar som inte behöver fullständig prestanda för Processorn kontinuerligt, som webbservrar, små databaser och utveckling och test miljöer. Dessa arbetsbelastningar har vanligtvis burstable prestandakrav. Det är $0,012 per timme (B1) jämfört med $0,05 per timme (F1), se en fullständig lista över [VM-storlekar](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/sizes-general) och [priser](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) mer information. 
#### <a name="managed-standard-disk"></a>Hanterade Standard Disk: 
Premiumdiskar rekommenderades för tillförlitlig prestanda i produktion.  Med [hanteras Disk](https://azure.microsoft.com/services/managed-disks/), standardlagring kan också tillhandahålla en liknande tillförlitlighet, med olika prestanda. För arbetsbelastning som inte är känslig för prestanda, t.ex. dev/Test- eller icke-kritiska miljön erbjuda hanterade standarddiskar ett alternativ med lägre kostnad.  
### <a name="cost-saving-in-general"></a>Kostnad sparas i allmänhet 
#### <a name="significant-vm-cost-saving-with-reserved-instances"></a>VM betydande kostnadsbesparingar med reserverade instanser: 
Idag debiteras alla CF virtuella datorer med hjälp av ”på begäran” prissättning, även om miljöerna vanligtvis fortsätta under obestämd tid. Du kan nu reserverad kapacitet för virtuell dator på en term som 1 eller 3 år och få rabatt på 45 65%. Rabatter i faktureringssystem utan ändringar av miljön. Mer information finns i [hur reserverade instanser fungerar](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Hanterade Premium Disk med mindre: 
Hanterade diskar stöd mindre diskstorlekar, till exempel P4(32 GB) och P6(64 GB) för premium- och standarddiskar. Om du har liten arbetsbelastningar kan spara du kostnader när du migrerar från standard premiumdiskar till hanterade premiumdiskar.
#### <a name="utilizing-azure-first-party-services"></a>Använda Azure första part Services: 
Dra nytta av Azures första part tjänsten sänker långsiktig administration kostnad, förutom hög tillgänglighet och tillförlitlighet i ovannämnda avsnitt. 

Spela en central har startas en [små storleken infoga](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) för PCF kunder komponenterna är samordnad till bara 4 virtuella datorer som kör upp till 2 500 programinstanser. Testversionen är nu tillgänglig genom [Azure-marknadsplatsen](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Nästa steg
Azure-integreringsfunktionerna först är tillgängliga med [öppen källa molnet Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/)innan den är tillgänglig på Spela en central Foundry i molnet. Funktioner som markerats med * är fortfarande inte tillgängliga via PCF. Molnet Foundry integrering med Azure-stacken ingår inte i det här dokumentet antingen.
För PCF stöd för funktioner som markerats med *, eller molnet Foundry integrering med Azure-stacken, kontakta din Kontoansvariga Pivotal och Microsoft för senaste status. 

