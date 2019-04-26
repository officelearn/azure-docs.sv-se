---
title: Distribuera din första app till Cloud Foundry på Microsoft Azure | Microsoft Docs
description: Distribuera ett program till Cloud Foundry på Azure
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: jeconnoc
editor: ''
tags: ''
keywords: ''
ms.assetid: 8fa04a58-56ad-4e6c-bef4-d02c80d4b60f
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 5a43ce3f09ce9695fa5add58b52271a46e2a271a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60388513"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Distribuera din första app till Cloud Foundry på Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) är en programplattform för populära öppen källkod-tillgängliga på Microsoft Azure. I den här artikeln visar vi hur du distribuerar och hanterar ett program i Cloud Foundry i en Azure-miljö.

## <a name="create-a-cloud-foundry-environment"></a>Skapa en Cloud Foundry-miljö

Det finns flera alternativ för att skapa en miljö med Cloud Foundry på Azure:

- Använd den [Pivotal Cloud Foundry erbjudandet] [ pcf-azuremarketplace] på Azure Marketplace för att skapa en standardmiljö med PCF Ops Manager och Azure Service Broker. Du kan hitta [fullständiga] [ pcf-azuremarketplace-pivotaldocs] för att distribuera marketplace erbjuder i Pivotal-dokumentationen.
- Skapa en anpassad miljö av [manuellt distribuera Pivotal Cloud Foundry][pcf-custom].
- [Distribuera Cloud Foundry-paket för öppen källkod direkt] [ oss-cf-bosh] genom att ställa in en [BOSH](https://bosh.io) director, en virtuell dator som samordnar distributionen av Cloud Foundry-miljö.

> [!IMPORTANT] 
> Om du distribuerar PCF från Azure Marketplace, notera SYSTEMDOMAINURL och autentiseringsuppgifter som administratör krävs för att få åtkomst till den Pivotal appar Manager, som beskrivs i Distributionshandboken för marketplace. De behövs för att slutföra den här kursen. För distributioner för marketplace, SYSTEMDOMAINURL är i formatet https://system. *IP-adress*. cf.pcfazure.com.

## <a name="connect-to-the-cloud-controller"></a>Ansluta till molnet-styrenhet

Cloud-styrenheten är den primära startpunkten till en Cloud Foundry-miljö för att distribuera och hantera program. Core API för Cloud-Controller (CCAPI) är ett REST-API, men den kan nås via olika verktyg. I det här fallet vi interagerar med det via den [Cloud Foundry CLI][cf-cli]. Du kan installera CLI på Linux, MacOS och Windows, men om du inte vill installera den på alla, det finns förinstallerade i den [Azure Cloud Shell][cloudshell-docs].

Logga in genom åtkomstgruppen `api` till SYSTEMDOMAINURL som du fick från marketplace-distributionen. Eftersom standard-distributionen använder ett självsignerat certifikat, bör du även inkludera den `skip-ssl-validation` växla.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Du uppmanas att logga in på molnet kontrollanten. Använd administratörsautentiseringsuppgifter för kontot som du har köpt från marketplace distributionsstegen.

Cloud Foundry ger *organisationer* och *blanksteg* som namnområden att isolera team och miljöer i en delad distribution. PCF marketplace-distributionen omfattar standard *system* org och en uppsättning med blanksteg som innehåller de grundläggande komponenterna som tjänsten för automatisk skalning och Azure service broker. Välj nu den *system* utrymme.


## <a name="create-an-org-and-space"></a>Skapa en organisation och utrymme

Om du skriver `cf apps`, visas en uppsättning systemprogram som har distribuerats i området system inom organisationen system. 

Bör du behålla den *system* org som reserverats för systemprogram, så skapa en organisation och utrymme för vår exempelprogram.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Använd mål-kommando för att växla till den nya org och utrymme:

```bash
cf target -o testorg -s dev
```

Nu när du distribuerar ett program kan skapas det automatiskt i den nya org och utrymme. För att bekräfta att det finns för närvarande inga appar i det nya org/utrymmet, skriver `cf apps` igen.

> [!NOTE] 
> Läs mer om organisationer och blanksteg och hur de kan användas för rollbaserad åtkomstkontroll (RBAC), den [Cloud Foundry-dokumentation][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Distribuera ett program

Nu ska vi använda ett exempelprogram för Cloud Foundry som heter Hello Spring Cloud, som är skrivna i Java och baserat på den [Spring Framework](https://spring.io) och [Spring Boot](https://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Klona databasen för Hello Spring Cloud

Exempelprogrammet Hello Spring Cloud är tillgängliga på GitHub. Klona den till din miljö och ändra till den nya katalogen:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Skapa programmet

Skapa en app med hjälp av [Apache Maven](https://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Distribuera programmet med cf push

Du kan distribuera de flesta program till Cloud Foundry med hjälp av den `push` kommando:

```bash
cf push
```

När du *push* ett program, Cloud Foundry identifierar typ av program (i det här fallet en Java-app) och identifierar dess beroenden (i det här fallet Spring framework). Den sedan paket allt som krävs för att köra din kod i en fristående behållaravbildning som kallas en *droplet*. Slutligen Cloud Foundry schemalägger programmet på en av de tillgängliga datorerna i din miljö och skapar en URL där du kan nå den, som finns i kommandots utdata.

![Utdata från kommandot för cf push][cf-push-output]

Om du vill se programmet hello-spring-moln, öppna den tillhandahållna URL: en i webbläsaren:

![Standardgränssnitt för Hello Spring Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Mer information om vad som händer under `cf push`, se [hur program mellanlagras] [ cf-push-docs] i Cloud Foundry-dokumentationen.

## <a name="view-application-logs"></a>Visa programloggar

Du kan använda Cloud Foundry CLI för att visa loggar för ett program med namnet:

```bash
cf logs hello-spring-cloud
```

Som standard loggar kommandot använder *pilslut*, som visar nya loggar som de är skrivna. Uppdatera hello spring cloud-app i webbläsaren om du vill se nya loggar som visas.

Du kan visa loggar som redan har skrivits genom att lägga till den `recent` växla:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Skala programmet

Som standard `cf push` skapar endast en instans av programmet. Om du vill garantera hög tillgänglighet och aktiverar skala ut för högre dataflöde, vanligtvis du kör fler än en instans av dina program. Du kan enkelt skala ut redan distribuerade program med hjälp av den `scale` kommando:

```bash
cf scale -i 2 hello-spring-cloud
```

Kör den `cf app` -kommando på programmet visar att Cloud Foundry skapar en annan instans av programmet. När programmet har startats startar Cloud Foundry automatiskt belastningsutjämning trafik till den.


## <a name="next-steps"></a>Nästa steg

- [Läs Cloud Foundry-dokumentation][cloudfoundry-docs]
- [Konfigurera plugin-programmet Azure DevOps-tjänsterna för Cloud Foundry][vsts-plugin]
- [Konfigurera Microsoft Log Analytics Nozzle för Cloud Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: https://docs.microsoft.com/azure/cloud-shell/overview
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: https://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png
