---
title: Distribuera din första app till Cloud Foundry på Microsoft Azure
description: Distribuera ett program till molngjuteri på Azure
author: seanmck
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 793a8f291be4fcca6fad19d486849253dddc089f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294798"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Distribuera din första app till Cloud Foundry på Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) är en populär programplattform med öppen källkod som är tillgänglig på Microsoft Azure. I den här artikeln visar vi hur du distribuerar och hanterar ett program på Cloud Foundry i en Azure-miljö.

## <a name="create-a-cloud-foundry-environment"></a>Skapa en molngjuterimiljö

Det finns flera alternativ för att skapa en molngjuterimiljö på Azure:

- Använd [Erbjudandet pivotala molngjuteri][pcf-azuremarketplace] på Azure Marketplace för att skapa en standardmiljö som innehåller PCF Ops Manager och Azure Service Broker. Du hittar [fullständiga instruktioner][pcf-azuremarketplace-pivotaldocs] för hur du distribuerar marketplace-erbjudandet i pivotaldokumentationen.
- Skapa en anpassad miljö genom [att distribuera Pivotal Cloud Foundry manuellt][pcf-custom].
- [Distribuera molngjuteripaket med öppen källkod direkt][oss-cf-bosh] genom att konfigurera en [BOSH-chef,](https://bosh.io) en virtuell dator som samordnar distributionen av cloud foundry-miljön.

> [!IMPORTANT] 
> Om du distribuerar PCF från Azure Marketplace kan du anteckna SYSTEMDOMAINURL och de administratörsautentiseringsuppgifter som krävs för att komma åt Pivotal Apps Manager, som båda beskrivs i distributionsguiden för marketplace. De behövs för att slutföra den här guiden. För marketplace-distributioner finns SYSTEMDOMAINURL `https://system.*ip-address*.cf.pcfazure.com`i formuläret .

## <a name="connect-to-the-cloud-controller"></a>Anslut till molnkontrollanten

Cloud Controller är den primära startpunkten till en Cloud Foundry-miljö för distribution och hantering av program. Core Cloud Controller API (CCAPI) är ett REST API, men det är tillgängligt via olika verktyg. I det här fallet interagerar vi med den via [Cloud Foundry CLI][cf-cli]. Du kan installera CLI på Linux, macOS eller Windows, men om du föredrar att inte installera det alls, är det tillgängligt förinstallerat i [Azure Cloud Shell][cloudshell-docs].

Om du vill logga `api` in förbereder du till SYSTEMDOMAINURL som du har fått från marketplace-distributionen. Eftersom standarddistributionen använder ett självsignerat certifikat `skip-ssl-validation` bör du också inkludera växeln.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Du uppmanas att logga in på Molnkontrollanten. Använd de administratörskontouppgifter som du har skaffat från distributionsstegen för marketplace.

Cloud Foundry tillhandahåller *organisationer* och *utrymmen* som namnområden för att isolera team och miljöer i en delad distribution. PCF-marketplace-distributionen innehåller *standardsystemorganisationen* och en uppsättning utrymmen som skapats för att innehålla baskomponenterna, till exempel tjänsten för automatisk skalning och Azure-tjänstmäklaren. För nu, välj *systemutrymme.*


## <a name="create-an-org-and-space"></a>Skapa en organisation och ett utrymme

Om du `cf apps`skriver visas en uppsättning systemprogram som har distribuerats i systemutrymmet i systemorganisationen. 

Du bör hålla *systemet* org reserverad för systemapplikationer, så skapa en organisation och utrymme för att hysa vårt urval ansökan.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Använd målkommandot för att växla till den nya organisationen och utrymmet:

```bash
cf target -o testorg -s dev
```

Nu, när du distribuerar ett program, skapas det automatiskt i den nya organisationen och utrymmet. Om du vill bekräfta att det för närvarande `cf apps` inte finns några appar i den nya organisationen/utrymmet skriver du igen.

> [!NOTE] 
> Mer information om orgs och utrymmen och hur de kan användas för rollbaserad åtkomstkontroll (RBAC) finns i [dokumentationen för Molngjuteri][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Distribuera ett program

Låt oss använda ett exempel Cloud Foundry ansökan kallas Hello Spring Cloud, som är skriven i Java och baserat på [Spring Framework](https://spring.io) och [Spring Boot](https://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Klona Hello Spring Cloud-lagringsplatsen

Exempelprogrammet Hello Spring Cloud är tillgängligt på GitHub. Klona den till din miljö och ändra till den nya katalogen:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Skapa programmet

Skapa appen med [Apache Maven](https://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Distribuera programmet med cf push

Du kan distribuera de flesta program `push` till Cloud Foundry med kommandot:

```bash
cf push
```

När du *skickar* ett program identifierar Cloud Foundry typen av program (i det här fallet en Java-app) och identifierar dess beroenden (i det här fallet spring-ramverket). Den paketerar sedan allt som krävs för att köra din kod i en fristående behållaravbildning, känd som en *droppe.* Slutligen schemalägger Cloud Foundry programmet på en av de tillgängliga datorerna i din miljö och skapar en URL där du kan nå den, som är tillgänglig i utdata från kommandot.

![Utdata från cf push-kommando][cf-push-output]

Om du vill se hello-spring-cloud-programmet öppnar du den angivna webbadressen i din webbläsare:

![Standardgränssnitt för Hello Spring Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Mer information om vad `cf push`som händer under finns i [Hur program är iscensatta][cf-push-docs] i dokumentationen för molngjuteri.

## <a name="view-application-logs"></a>Visa programloggar

Du kan använda Cloud Foundry CLI för att visa loggar för ett program med dess namn:

```bash
cf logs hello-spring-cloud
```

Som standard använder kommandot loggar *svans*, som visar nya loggar som de skrivs. Om du vill se nya loggar visas uppdaterar du appen hello-spring-cloud i webbläsaren.

Om du vill visa loggar som `recent` redan har skrivits lägger du till växeln:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Skala programmet

Som standard `cf push` skapar bara en enda instans av ditt program. För att säkerställa hög tillgänglighet och aktivera utskalning för högre dataflöde, vill du i allmänhet köra mer än en instans av dina program. Du kan enkelt skala ut `scale` redan distribuerade program med kommandot:

```bash
cf scale -i 2 hello-spring-cloud
```

Köra `cf app` kommandot på programmet visar att Cloud Foundry skapar en annan instans av programmet. När programmet har startat startar Cloud Foundry automatiskt belastningsutjämningstrafik till det.


## <a name="next-steps"></a>Nästa steg

- [Läs dokumentationen för molngjuteri][cloudfoundry-docs]
- [Konfigurera plugin-programmet Azure DevOps Services för molngjuteri][vsts-plugin]
- [Konfigurera Microsoft Log Analytics-munstycket för molngjuteri][loganalytics-nozzle]

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
