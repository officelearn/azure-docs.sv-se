---
title: "Vanliga frågor och svar om Azure Container Service | Microsoft Docs"
description: "Få svar på vanliga frågor om tjänsten Azure Container Service som gör det enklare att skapa, konfigurera och hantera ett kluster av virtuella datorer som kör Docker-behållarappar."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, Containers, Micro-services, Mesos, Azure, Kubernetes
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 2a381431acb6436ddd8e13c69b05423a33cd4fa6
ms.openlocfilehash: c28391b752c071161796421aee63402899d2a0a4


---
# <a name="frequently-asked-questions-azure-container-service"></a>Vanliga frågor och svar: Azure Container Service


## <a name="orchestrators"></a>Dirigeringsverktyg

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Vilka behållardirigeringsverktyg stöder Azure Container Service? 

Det finns stöd för lösningar som är baserade på öppen källkod, som DC/OS, Docker Swarm och Kubernetes. Mer information finns i [Översikt](container-service-intro.md).
 
### <a name="do-you-support-docker-swarm-mode"></a>Finns det stöd för Docker Swarm-läge? 

Swarm-läge stöds inte för närvarande, men finns med i planerna för tjänsten. 

### <a name="does-azure-container-service-support-windows-containers"></a>Har Azure Container Service stöd för Windows-behållare?  

För närvarande finns stöd för Linux-behållare med alla dirigeringsverktyg. Stöd för Windows-behållare med Kubernetes är tillgängligt i förhandsversionen.

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>Rekommenderar ni något specifikt dirigeringsverktyg i Azure Container Service? 
Vanligtvis rekommenderar vi inte något specifikt dirigeringsverktyg. Om du har erfarenheter av något av de dirigeringsverktyg som stöds kan du använda dig av det i Azure Container Service. Man kan dock säga att DC/OS är produktionstestat för stordata och IoT-arbetsbelastningar, att Kubernetes passar för molnautentiska arbetsbelastningar och att Docker Swarm är känt för sin integrering med Docker-verktyg och korta inlärningskurva.

Beroende på ditt scenario kan du också skapa och hantera anpassade behållarlösningar med andra Azure-tjänster. Till exempel med [Virtual Machines](../virtual-machines/virtual-machines-linux-azure-overview.md), [Service Fabric](../service-fabric/service-fabric-overview.md), [Web Apps](../app-service-web/app-service-web-overview.md) och [Batch](../batch/batch-technical-overview.md).  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Vad är skillnaden mellan Azure Container Service och ACS Engine? 
Azure Container Service är en SLA-reglerad Azure-tjänst med funktioner i Azure Portal, Azure kommandoradsverktyg och Azure API: er. Med tjänsten kan du snabbt implementera och hantera kluster som kör standardverktyg för behållardirigering med relativt få konfigurationsalternativ. 

[ACS Engine](http://github.com/Azure/acs-engine) är ett projekt baserat på öppen källkod som gör det möjligt för privilegierade användare att anpassa klusterkonfigurationen på varje nivå. Möjligheten att ändra konfigurationen för både infrastruktur och programvara innebär att vi inte erbjuder något serviceavtal för ACS Engine. Support hanteras via projektet på GitHub i stället för via de officiella Microsoft-kanalerna. 

## <a name="cluster-management"></a>Klusterhantering

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>Hur skapar jag SSH-nycklar för mitt kluster?

Du kan använda standardverktygen i ditt operativsystem för att skapa en privat och en offentlig SSH-RSA-nyckel för autentisering mot virtuella Linux-datorer för ditt kluster. Anvisningar finns i guiden för [OS X och Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) eller [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). 

Om du använder [Azure CLI 2.0-kommandon](container-service-create-acs-cluster-cli.md) för att distribuera ett kluster för behållartjänsten kan SSH-nycklar genereras automatiskt för klustret.

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Hur skapar jag ett huvudnamn för mitt Kubernetes-kluster?

Ett ID och lösenord krävs också för Azure Active Directory-tjänstens huvudnamn när du skapar ett Kubernetes-kluster i Azure Container Service. Mer information finns i [Om tjänstens huvudnamn för ett Kubernetes-kluster](container-service-kubernetes-service-principal.md).


Om du använder [Azure CLI 2.0-kommandon](container-service-create-acs-cluster-cli.md) för att distribuera ett Kubernetes-kluster kan autentiseringsuppgifter för tjänstens huvudnamn genereras automatiskt för klustret.


### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>Hur ökar jag antalet huvudservrar efter att ett kluster har skapats? 
När klustret har skapats går det inte att ändra antalet huvudservrar. När du skapar klustret bör du helst välja flera huvudservrar för att uppnå hög tillgänglighet.


### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>Hur ökar jag antalet agenter efter att ett kluster har skapats? 
Du kan skala antalet agenter i klustret med hjälp av Azure Portal eller kommandoradsverktygen. Se [Skala ett Azure Container Service-kluster](container-service-scale.md).


### <a name="what-are-the-urls-of-my-masters-and-agents"></a>Vilka URL: er har mina huvudservrar och agenter? 
URL:erna för klusterresurserna i Azure Container Service baseras på DNS-prefixet du anger och namnet på den Azure-region du väljer för distribution. Det fullständiga domännamnet för den överordnade noden har till exempel det här formatet:

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

Du hittar vanliga URL:er för ditt kluster i Azure Portal, Azure Resource Explorer eller andra Azure-verktyg.
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>Var hittar jag SSH-anslutningssträngen till mitt kluster?

Du hittar anslutningssträngen i Azure Portal eller med hjälp av Azure-kommandoradsverktygen. 

1. Navigera till resursgruppen för klusterdistributionen i portalen.  

2. Klicka på **Översikt** och på länken för **Distributioner** under **Essentials**. 

3. På bladet **Distributionshistorik** klickar du på den distribution som har ett namn som börjar med **microsoft-acs** följt av ett distributionsdatum. Till exempel: microsoft-acs-201701310000.  

4. Det finns flera klusterlänkar <provided></provided> under **Utdata** på sidan **Sammanfattning**. **SSHMaster0** ger en SSH-anslutningssträng till den första huvudserver i klustret. 

Som vi nämnde tidigare kan du också använda Azure-verktyg för att hitta det fullständiga domännamnet för huvudservern. Skapa en SSH-anslutning till huvudservern med det fullständiga domännamnet för huvudservern och det användarnamn som du angav när klustret skapades. Exempel:

```bash
ssh userName@masterFQDN –A –p 22 
```

Mer information finns i [Ansluta till ett Azure Container Service-kluster](container-service-connect.md).




## <a name="next-steps"></a>Nästa steg

* [Läs mer](container-service-intro.md) om Azure Container Service.
* Distribuera ett behållartjänstkluster med hjälp av [portalen](container-service-deployment.md) eller [Azure CLI 2.0](container-service-create-acs-cluster-cli.md).


<!--HONumber=Feb17_HO4-->


