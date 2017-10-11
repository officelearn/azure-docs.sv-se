---
title: "Distribuera din första app till molnet Foundry på Microsoft Azure | Microsoft Docs"
description: "Distribuera program till molnet Foundry på Azure"
services: virtual-machines-linux
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 8fa04a58-56ad-4e6c-bef4-d02c80d4b60f
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: b617127fc0a3f8dcae293e356ea669edcfa5deff
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Distribuera din första app till molnet Foundry på Microsoft Azure

[Molnet Foundry](http://cloudfoundry.org) är en programplattform för populära öppen källkod tillgängliga på Microsoft Azure. I den här artikeln visar vi hur du distribuerar och hanterar ett program på molnet Foundry i en Azure-miljö.

## <a name="create-a-cloud-foundry-environment"></a>Skapa en moln Foundry-miljö

Det finns flera alternativ för att skapa en miljö med molnet Foundry i Azure:

- Använd den [spela en central molnet Foundry erbjudande] [ pcf-azuremarketplace] i Azure Marketplace för att skapa en standard miljö som innehåller PCF Ops Manager och Azure Service Broker. Du kan hitta [fullständiga] [ pcf-azuremarketplace-pivotaldocs] för att distribuera marketplace erbjuder i spela en central-dokumentationen.
- Skapa en anpassad miljö genom [manuellt distribuera spela en central molnet Foundry][pcf-custom].
- [Distribuera paket för öppen källkod molnet Foundry direkt] [ oss-cf-bosh] genom att ställa in en [BOSH](http://bosh.io) chef, en virtuell dator som samordnar distributionen av molnet Foundry-miljö.

> [!IMPORTANT] 
> Om du distribuerar PCF från Azure Marketplace, notera SYSTEMDOMAINURL och administratörsautentiseringsuppgifter för att komma åt hanteraren spela en central appar som beskrivs i distributionsguiden marketplace. De behövs för att slutföra den här självstudiekursen. Marketplace-distributioner är SYSTEMDOMAINURL i formuläret https://system. *ip-adress*. cf.pcfazure.com.

## <a name="connect-to-the-cloud-controller"></a>Ansluta till molnet-styrenhet

Molnet domänkontrollanten är den primära startpunkten till en molnet Foundry miljö för att distribuera och hantera program. Core molnet Controller API (CCAPI) är en REST-API, men den är tillgänglig via olika verktyg. I det här fallet vi interagerar med det via den [moln Foundry CLI][cf-cli]. Du kan installera CLI på Linux, MacOS eller Windows, men om du inte vill installera den på alla, den är tillgänglig förinstallerat i den [Azure Cloud Shell][cloudshell-docs].

För att logga in lägga `api` till SYSTEMDOMAINURL som du fick från marketplace-distribution. Eftersom standard-distributionen använder ett självsignerat certifikat, bör du också inkludera den `skip-ssl-validation` växla.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Du uppmanas att logga in på molnet-styrenhet. Använd administratörsautentiseringsuppgifter för kontot som du har köpt från marketplace-distributionsstegen.

Molnet Foundry ger *organisationer* och *blanksteg* som namnområden att isolera team och miljöer i en delad distribution. PCF marketplace distributionen omfattar standard *system* org och en uppsättning blanksteg som innehåller de grundläggande komponenterna som tjänsten autoskalning och Azure service broker. Nu välja de *system* utrymme.


## <a name="create-an-org-and-space"></a>Skapa en organisations och utrymme

Om du skriver `cf apps`, visas en uppsättning systemprogram som har distribuerats i området system inom organisationen system. 

Du bör tänka på *system* org som reserverats för systemprogram, så du måste skapa en organisations och utrymme för våra exempelprogrammet.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Använd Målkommandot för att växla till den nya org och utrymme:

```bash
cf target -o testorg -s dev
```

Nu när du distribuerar ett program kan skapas det automatiskt i nya org och blanksteg. För att bekräfta att det finns för närvarande inga appar i det nya org/utrymmet, Skriv `cf apps` igen.

> [!NOTE] 
> Mer information om organisationer blanksteg och hur de kan användas för rollbaserad åtkomstkontroll (RBAC) finns på [moln Foundry dokumentationen][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Distribuera ett program

Vi använder ett exempelprogram för molntjänster Foundry kallas Hello källan moln, vilket är skriven i Java och baserat på de [Vårversionen Framework](http://spring.io) och [Vårversionen Start](http://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Klona lagringsplatsen Hello källan moln

Exempelprogrammet Hello källan molnet finns på GitHub. Klona den i din miljö och ändra till den nya katalogen:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Skapa programmet

Skapa en app med hjälp av [Apache Maven](http://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Distribuera programmet med cf push

Du kan distribuera de flesta program till molnet Foundry med hjälp av den `push` kommando:

```bash
cf push
```

När du *push* ett program, molnet Foundry identifierar typ av program (i det här fallet en Java-app) och identifierar dess beroenden (i det här fallet källan framework). Den sedan paket allt som krävs för att köra din kod i en fristående behållaren avbildningen som kallas en *droplet*. Slutligen molnet Foundry schemalägger program på en av de tillgängliga datorerna i din miljö och skapar en URL där du kan nå den, som är tillgängliga i resultatet av kommandot.

![Utdata från cf push-kommando][cf-push-output]

Öppna den angivna Webbadressen i webbläsaren om du vill se programmet hello källan moln:

![Standardgränssnitt för Hello källan moln][hello-spring-cloud-basic]

> [!NOTE] 
> Mer information om vad som händer under `cf push`, se [hur program mellanlagras] [ cf-push-docs] i molnet Foundry-dokumentationen.

## <a name="view-application-logs"></a>Visa programloggar

Du kan använda molnet Foundry CLI för att visa loggar för ett program med namnet:

```bash
cf logs hello-spring-cloud
```

Som standard loggar kommandot använder *pilslut*, vilket visar nya loggar som de är skrivna. Uppdatera hello-källan-cloud-app i webbläsaren om du vill se nya loggar visas.

Lägg till om du vill visa loggar som redan har skrivits av `recent` växel:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Skala programmet

Som standard `cf push` skapar endast en instans av programmet. För att säkerställa hög tillgänglighet och aktivera skala ut för högre genomströmning, vill du vanligtvis köra fler än en instans av dina program. Du kan enkelt skala ut redan distribuerade program med hjälp av den `scale` kommando:

```bash
cf scale -i 2 hello-spring-cloud
```

Kör den `cf app` kommandot på programmet visar att molnet Foundry skapar en annan instans av programmet. När programmet har börjat startar automatiskt moln Foundry belastningsutjämning trafik till den.


## <a name="next-steps"></a>Nästa steg

- [Läs i dokumentationen för molnet Foundry][cloudfoundry-docs]
- [Konfigurera Visual Studio Team Services plugin-programmet för molnet Foundry][vsts-plugin]
- [Konfigurera Microsoft Log Analytics munstycket för molnet Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: https://docs.microsoft.com/azure/cloud-shell/overview
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: http://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png