---
title: Hur Cloud Foundry är integrerat med Azure | Microsoft Docs
description: Beskriver hur Cloud Foundry kan utnyttja Azure-tjänster för att förbättra upplevelsen Enterprice
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
ms.openlocfilehash: 908b7e40c0509d7034b86985ac0775635726a6b9
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329811"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integrera Cloud Foundry med Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) är en PaaS-plattform som körs ovanpå molnleverantörer IaaS-plattformen. Den erbjuder enhetlig tillämpning distributionsupplevelse över molnleverantörer. Dessutom kan den också integreras med olika Azure-tjänster, med företagsklass hög tillgänglighet, skalbarhet och kostnadsbesparingar.
Det finns [6 delsystem i Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/), som kan vara ett flexibelt sätt skala online, inklusive: Routning, autentisering, livscykelhantering för program, servicehantering, meddelanden och övervakning. För varje delsystem kan du konfigurera Cloud Foundry för att använda motsvarande Azure-tjänsten. 

![Cloud Foundry på Azure-Integreringsarkitektur](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Hög tillgänglighet och skalbarhet
### <a name="managed-disk"></a>Managed Disk
Bosh använder Azure CPI (Cloud Provider Interface) för att skapa för disken och ta bort rutiner. Som standard används ohanterade diskar. Det kräver att kunden att manuellt skapa storage-konton och sedan konfigurerar konton i CF manifestfiler. Detta beror på begränsningen av antalet diskar per lagringskonto.
Nu [Managed Disk](https://azure.microsoft.com/services/managed-disks/) är tillgänglig, erbjuder hanterade diskar för säker och tillförlitlig lagring för virtuella datorer. Kunden behöver inte längre behöver bry dig om storage-konto för skalbarhet och hög tillgänglighet. Azure ordnar diskar automatiskt. Om det är en ny eller en befintlig distribution kan hanterar Azure-CPI generering och migrering av den hantera disken under en CF-distribution. Det går med PCF 1.11. Du kan även utforska öppen källkod Cloud Foundry [Managed Disk vägledning](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) referens. 
### <a name="availability-zone-"></a>Tillgänglighetszon *
Som en molnbaserade programplattform Cloud Foundry är utformad med [fyra nivå för hög tillgänglighet](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Även om de första tre nivåerna av programfel kan hanteras av själva CF systemet, tillhandahåller plattform feltolerans molnleverantörer. Huvudkomponenterna CF bör skyddas med en molnleverantör plattform lösning för hög tillgänglighet. Detta inkluderar GoRouters, Diego hjärnan, CF databasen och paneler. Som standard [Azure-Tillgänglighetsuppsättning](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) används för feltolerans mellan kluster i ett datacenter.
Bra ny är [Azure Tillgänglighetszon](https://docs.microsoft.com/azure/availability-zones/az-overview ) släpps nu föra feltolerans till nästa nivå med låg latens redundans över datacenter.
Azure Tillgänglighetszoner ger hög tillgänglighet genom att placera en uppsättning virtuella datorer i 2 + datacenter, varje uppsättning virtuella datorer är redundant för andra uppsättningar. Om en zon är nere kan de andra som är fortfarande live isolerade från haveriet.
> [!NOTE] 
> Azure-Tillgänglighetszon erbjuds inte ännu för alla regioner, kontrollera senast [meddelande för listan över regioner som stöds](https://docs.microsoft.com/azure/availability-zones/az-overview). Öppna källa Cloud Foundry, kontrollera [Azure Tillgänglighetszon råd om öppen Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. Nätverksroutning
Som standard används Azure belastningsutjämnare för inkommande CF API/apps-begäranden, vidarebefordrar dem till Gorouters. CF-komponenter som Diego hjärna, MySQL, ERT kan också använda belastningsutjämnaren för att belastningsutjämna trafiken för hög tillgänglighet. Azure tillhandahåller också en uppsättning med helt hanterad nätverkslösningar för belastningsutjämning. Om du behöver för TLS-avslutning (”SSL-avlastning”) eller per HTTP/HTTPS-begäran application layer bearbetning, kan du överväga att Application Gateway. Överväg att standardbelastningsutjämnare för hög tillgänglighet och skalbarhet för belastningsutjämning på layer 4.
### <a name="azure-application-gateway-"></a>Azure Application Gateway *
[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) erbjuder olika layer 7 belastningsutjämning, inklusive SSL-avlastning, från slutpunkt till slutpunkt SSL, Brandvägg för webbaserade program, Cookiebaserad sessionstillhörighet och mycket mer. Du kan [konfigurera Application Gateway i öppen källkod Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). PCF kan kontrollera den [PCF 2.1 viktig](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) för POC-test.

### <a name="azure-standard-load-balancer-"></a>Azure Standard Load Balancer *
Azure Load Balancer är en Layer 4-belastningsutjämnare. Används för att distribuera trafik mellan instanser av tjänster i en belastningsutjämnad uppsättning. Standardversionen ger [avancerade funktioner](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) över grundläggande version. Till exempel 1. Maxgränsen för backend-poolen utlöses från 100 till 1000 virtuella datorer.  2. Slutpunkterna har nu stöd för flera tillgänglighetsuppsättningar i stället för en enskild tillgänglighetsuppsättning.  3. Ytterligare funktioner som högtillgänglighetsportar, rikare övervakningsdata osv. Om du flyttar till Azure-Tillgänglighetszon krävs standardbelastningsutjämnare. För en ny distribution rekommenderar vi att du börjar med Azure Standard Load Balancer. 

## <a name="3-authentication"></a>3. Autentisering 
[Cloud Foundry-användarkonto och autentisering](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) är centrala identity management-tjänsten för CF och dess olika komponenter. [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är Microsofts flera innehavare, molnbaserad katalog- och identity management-tjänsten. Som standard används UAA för Cloud Foundry-autentisering. Som ett avancerat alternativ stöd UAA också för Azure AD som en extern användare butik. Azure AD-användare kan komma åt Cloud Foundry med sina LDAP-identitet, utan ett Cloud Foundry-konto. Följ dessa steg för att [konfigurera Azure AD för UAA i PCF](http://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Datalagring för Cloud Foundry Runtime System
Cloud Foundry erbjuder bra utökningsbarhet för att använda Azure blobstore eller Azure MySQL/PostgreSQL-tjänster för Programlagring runtime system.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Azure Blobstore för Cloud Foundry molnet Controller blobstore
Molnet Controller blobstore är ett kritiskt datalager för buildpacks, dropletar, paket och resurspooler. Som standard används NFS-server för molnet Controller blobstore. Undvik felpunkt genom att använda Azure Blob Storage som extern lagring. Kolla in den [Cloud Foundry-dokumentation](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) bakgrundsinformation och [alternativen i Pivotal Cloud Foundry](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL som Cloud Foundry elastiska kör tid databas *
CF elastiska Runtime kräver två större systemdatabaser:
#### <a name="ccdb"></a>CCDB 
Molnet Controller-databasen.  Molnet Controller har REST API-slutpunkter för klienter för att komma åt systemet. CCDB lagrar tabeller för organisationer, blanksteg, tjänster, användarroller och mer för molnet controller.
#### <a name="uaadb"></a>UAADB 
Databasen för användarkontot och autentisering. Den lagrar användarautentisering relaterade data, till exempel krypterade användarnamn och lösenord.

Som standard kan du använda en lokal databas (MySQL). För hög tillgänglighet och för att skala, utnyttjar Azure hanterade MySQL eller PostgreSQL-tjänster. Här följer instruktioner [aktiverar Azure MySQL/PostgreSQL för CCDB UAADB systemdatabaser, och andra med öppen källkod Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Öppna Service Broker
Azure service broker erbjuder enhetligt gränssnitt för att hantera programåtkomst till Azure-tjänster. Den nya [Open Service Broker for Azure-projekt](https://github.com/Azure/open-service-broker-azure) tillhandahåller en enkel och enkelt sätt att erbjuda tjänster till program i Cloud Foundry, OpenShift och Kubernetes. Se den [Azure Open Service Broker för PCF panel](https://network.pivotal.io/products/azure-open-service-broker-pcf/) anvisningar för distribution på PCF.

## <a name="6-metrics-and-logging"></a>6. Mått och loggning
Azure Log Analytics Nozzle är en Cloud Foundry-komponent som vidarebefordrar mått från den [Cloud Foundry loggregator firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) till [Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/). Du kan använda Nozzle för att samla in, visa och analysera dina CF systemmått hälsa och prestanda mellan flera distributioner.
Klicka på [här](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) att lära dig hur du distribuerar Azure Log Analytics Nozzle för både öppen källkod och Pivotal Cloud Foundry-miljön och komma åt data från Azure Log Analytics-konsolen. 
> [!NOTE]
> PCF 2.0 BOSH hälsomått för virtuella datorer vidarebefordras till Loggregator Firehose som standard och är integrerade i Azure Log Analytics-konsolen.

## <a name="7-cost-saving"></a>7. Kostnadsbesparande
### <a name="cost-saving-for-devtest-environments"></a>Kostnadsbesparingar i miljöer för utveckling/testning
#### <a name="b-series-"></a>B-serien: *
Även om F och virtuella datorer i D-serien har ofta rekommenderas för Pivotal Cloud Foundry-produktionsmiljö, den nya ”anpassningsbara” [B-serien](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) ger nya alternativ. Virtuella datorer med burst B-serien är idealiska för arbetsbelastningar som inte behöver fullständig prestanda för Processorn kontinuerligt, som webbservrar, små databaser och utveckling och testmiljöer. Dessa arbetsbelastningar har normalt anpassningsbara prestandakrav. Det är $ 0,012/timme (B1) jämfört med $0.05/ timme (F1), se en fullständig lista över [VM-storlekar](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) och [priserna](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) mer information. 
#### <a name="managed-standard-disk"></a>Hanterade standarddiskar: 
Premium-diskar rekommenderades för pålitlig prestanda i produktion.  Med [Managed Disk](https://azure.microsoft.com/services/managed-disks/), standard-lagring kan också leverera liknande tillförlitlighet med olika prestanda. För arbetsbelastningar som inte är resultatdrivna, som utveckling och testning eller icke-kritiska miljö, erbjuder hanterade standarddiskar ett alternativ till lägre kostnad.  
### <a name="cost-saving-in-general"></a>Kostnadsbesparande i allmänhet 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Betydande VM kostnaden spara med Azure reservationer: 
Idag alla CF virtuella datorer debiteras enligt ”på begäran” prissättning, även om miljöer vanligtvis vara igång på obestämd tid. Du kan nu reserverad kapacitet för virtuell dator på en term för 1 eller 3 år och få rabatter på 45-65%. Rabatter i faktureringssystem, utan ändringar i din miljö. Mer information finns i [hur Azure reservationer fungerar](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Hanterade Premiumdiskar med mindre storlekar: 
Hanterade diskar support mindre diskstorlekar, till exempel P4(32 GB) och P6(64 GB) för både premium och standard-diskar. Om du har liten arbetsbelastningar kan minska du kostnaderna när du migrerar från standard premium-diskar till hanterade premiumdiskar.
#### <a name="utilizing-azure-first-party-services"></a>Använda Azure-tjänster som första part: 
Dra nytta av Azures tjänst från första part sänker långsiktig administrationen kostnad förutom hög tillgänglighet och tillförlitlighet som nämns i senare avsnitt. 

Pivotal har lanserat en [små utrymmeskrav ERT](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) för PCF kunder komponenterna är samordnad i bara 4 virtuella datorer som kör upp till 2 500 programinstanser. Utvärderingsversionen är nu tillgängligt via [Azure-marknadsplatsen](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Nästa steg
Integrering med Azure-funktioner finns först med [öppen källkod Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), innan den är tillgänglig på Pivotal Cloud Foundry. Funktioner som markerats med * är fortfarande inte tillgängliga via PCF. Cloud Foundry-integrering med Azure Stack ingår inte i det här dokumentet antingen.
För PCF-supporten om funktionerna som markerats med *, eller Cloud Foundry-integrering med Azure Stack, kontakta din Kontoansvariga Pivotal och Microsoft för senaste status. 

