---
title: Distribuera Azure Log Analytics-munstycke för övervakning av molngjuteri
description: Steg-för-steg-vägledning om distribution av Cloud Foundry loggregator Nozzle för Azure Log Analytics. Använd munstycket för att övervaka hälso- och prestandamåtten för cloud foundry-systemet.
services: virtual-machines-linux
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: bf6691310ec964a1d6293f3a60c151e3d6f8e641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277365"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Distribuera Azure Log Analytics-munstycke för övervakning av molngjuterisystem

[Azure Monitor](https://azure.microsoft.com/services/log-analytics/) är en tjänst i Azure. Det hjälper dig att samla in och analysera data som genereras från ditt moln och lokala miljöer.

Log Analytics-munstycket (munstycket) är en CF-komponent (Cloud Foundry), som vidarebefordrar mått från [Cloud Foundry loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) firehose till Azure Monitor-loggar. Med munstycket kan du samla in, visa och analysera hälso- och prestandamått för CF-systemet över flera distributioner.

I det här dokumentet får du lära dig hur du distribuerar munstycket till din CF-miljö och sedan kommer åt data från Azure Monitor-loggkonsolen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Krav

Följande steg är förutsättningar för distribution av munstycket.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Distribuera en CF- eller Pivotal Cloud Foundry-miljö i Azure

Du kan använda munstycket med antingen en CF-distribution med öppen källkod eller en PCF-distribution (Pivotal Cloud Foundry).

* [Distribuera Cloud Foundry på Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Distribuera pivotala molngjuteri på Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Installera CF-kommandoradsverktygen för distribution av munstycket

Munstycket körs som ett program i CF-miljö. Du behöver CF CLI för att distribuera programmet.

Munstycket behöver också åtkomstbehörighet till loggregatorsbrandhose och Cloud Controller. Om du vill skapa och konfigurera användaren behöver du tjänsten Användarkonto och autentisering (UAA).

* [Installera Cloud Foundry CLI](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Installera kommandoradsklient för molngjuteri UAA](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Innan du konfigurerar UAA-kommandoradsklienten ska du se till att RubyGems är installerat.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Skapa en log Analytics-arbetsyta i Azure

Du kan skapa log analytics-arbetsytan manuellt eller med hjälp av en mall. Mallen distribuerar en installation av förkonfigurerade KPI-vyer och aviseringar för Azure Monitor-loggkonsolen. 

#### <a name="to-create-the-workspace-manually"></a>Så här skapar du arbetsytan manuellt:

1. I Azure-portalen söker du i listan över tjänster på Azure Marketplace och väljer sedan Log Analytics-arbetsytor.
2. Välj **Skapa**och välj sedan alternativ för följande objekt:

   * **Log Analytics-arbetsyta:** Skriv ett namn på arbetsytan.
   * **Prenumeration:** Om du har flera prenumerationer väljer du den som är samma som din CF-distribution.
   * **Resursgrupp**: Du kan skapa en ny resursgrupp eller använda samma med din CF-distribution.
   * **Plats**: Ange platsen.
   * **Prisnivå**: Välj **OK** för att slutföra.

Mer information finns i [Komma igång med Azure Monitor-loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Så här skapar du log analytics-arbetsytan via övervakningsmallen från Azure-marknadsplatsen:

1. Öppna Azure-portalen.
1. Klicka på tecknet "+" eller "Skapa en resurs" i det övre vänstra hörnet.
1. Skriv "Cloud Foundry" i sökfönstret, välj "Cloud Foundry Monitoring Solution".
1. Cloud Foundry övervakning lösningsmallen förstasidan laddas, klicka på "Skapa" för att starta mallbladet.
1. Ange de parametrar som krävs:
    * **Prenumeration:** Välj en Azure-prenumeration för Log Analytics-arbetsytan, vanligtvis samma sak med Cloud Foundry-distribution.
    * **Resursgrupp**: Välj en befintlig resursgrupp eller skapa en ny för log analytics-arbetsytan.
    * **Plats för resursgrupp:** Välj plats för resursgruppen.
    * **OMS_Workspace_Name:** Ange ett arbetsytenamn, om arbetsytan inte finns, kommer mallen att skapa ett nytt.
    * **OMS_Workspace_Region**: Välj plats för arbetsytan.
    * **OMS_Workspace_Pricing_Tier**: Välj log analytics-arbetsytan SKU. Se [prisvägledningen](https://azure.microsoft.com/pricing/details/log-analytics/) som referens.
    * **Juridiska termer**: Klicka på Juridiska termer och klicka sedan på "Skapa" för att acceptera den juridiska termen.
1. När alla parametrar har angetts klickar du på "Skapa" för att distribuera mallen. När distributionen är klar visas statusen på meddelandefliken.


## <a name="deploy-the-nozzle"></a>Distribuera munstycket

Det finns ett par olika sätt att distribuera munstycket: som en PCF-panel eller som ett CF-program.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Distribuera munstycket som en PCF Ops Manager-panel

Följ stegen för att [installera och konfigurera Azure Log Analytics-munstycket för PCF](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Detta är den förenklade metoden, PCF Ops manager kakel kommer automatiskt att konfigurera och driva munstycket. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Distribuera munstycket manuellt som ett CF-program

Om du inte använder PCF Ops Manager distribuerar du munstycket som ett program. I följande avsnitt beskrivs den här processen.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Logga in på din CF-distribution som administratör via CF CLI

Kör följande kommando:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" är ditt CF-domännamn. Du kan hämta den genom att söka i "SYSTEM_DOMAIN" i cf-distributionsmanifestfilen. 

"CF_User" är CF admin namn. Du kan hämta namnet och lösenordet genom att söka i avsnittet "scim" och leta efter namnet och "cf_admin_password" i cf-distributionsmanifestfilen.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Skapa en CF-användare och bevilja nödvändiga privilegier

Kör följande kommandon:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" är ditt CF-domännamn. Du kan hämta den genom att söka i "SYSTEM_DOMAIN" i cf-distributionsmanifestfilen.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Ladda ned den senaste versionen av Log Analytics-munstycket

Kör följande kommando:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Ange miljövariabler

Nu kan du ställa in miljövariabler i filen manifest.yml i den aktuella katalogen. Följande visar appmanifestet för munstycket. Ersätt värden med din specifika logganalysarbetsytainformation.

```
OMS_WORKSPACE             : Log Analytics workspace ID: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_KEY                   : OMS key: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Azure Monitor logs. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Azure Monitor logs. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Azure Monitor logs. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Azure Monitor logs as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Azure Monitor logs. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Skicka programmet från din utvecklingsdator

Se till att du är under mappen oms-log-analytics-firehose-munstycke. Kör följande kommando:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Validera installationen av munstycket

### <a name="from-apps-manager-for-pcf"></a>Från Apps Manager (för PCF)

1. Logga in på Ops Manager och kontrollera att panelen visas på installationsinstrumentpanelen.
2. Logga in på Apps Manager, kontrollera att det utrymme du har skapat för munstycket visas i användningsrapporten och bekräfta att statusen är normal.

### <a name="from-your-development-computer"></a>Från din utvecklingsdator

Skriv i CF CLI-fönstret:
```
cf apps
```
Kontrollera att OMS-munstyckesprogrammet körs.

## <a name="view-the-data-in-the-azure-portal"></a>Visa data i Azure-portalen

Om du har distribuerat övervakningslösningen via marknadsplatsmallen går du till Azure-portalen och letar reda på lösningen. Du hittar lösningen i resursgruppen som du angav i mallen. Klicka på lösningen, bläddra till "log analytics console", de förkonfigurerade vyerna visas, med de bästa Cloud Foundry-system-KPI:erna, programdata, aviseringar och hälsomått för virtuella datorer. 

Om du har skapat arbetsytan Log Analytics manuellt följer du stegen nedan för att skapa vyer och aviseringar:

### <a name="1-import-the-oms-view"></a>1. Importera OMS-vyn

Bläddra till Visa > **designerimport** > **bläddra**på OMS-portalen och välj en av omsview-filerna. **View Designer** Välj till exempel *Cloud Foundry.omsview*och spara vyn. Nu visas en panel på sidan **Översikt.** Markera den om du vill visa visualiserade mått.

Du kan anpassa vyerna eller skapa nya vyer via **View Designer**.

*"Cloud Foundry.omsview"* är en förhandsversion av cloud foundry OMS-vymallen. Detta är en fullständigt konfigurerad standardmall. Om du har förslag eller feedback om mallen skickar du dem till [ärendeavsnittet](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Skapa varningsregler

Du kan [skapa aviseringarna](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)och anpassa frågorna och tröskelvärdena efter behov. Följande är rekommenderade varningar:

| Sökfråga                                                                  | Generera avisering baserat på | Beskrivning                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domän.cf-appar"                   | Antal resultat < 1   | **bbs. Domain.cf-appar** anger om cf-apps-domänen är uppdaterad. Det innebär att CF App-begäranden från Cloud Controller synkroniseras med bbs. LRPsDesired (Diego-önskade AIs) för utförande. Inga mottagna data innebär att cf-apps-domänen inte är uppdaterad i det angivna tidsfönstret. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=FelfrittCell Value_d>1            | Antal resultat > 0   | För Diego celler, 0 betyder friska, och 1 betyder ohälsosamt. Ange aviseringen om flera felaktiga Diego-celler upptäcks i det angivna tidsfönstret. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Antal resultat > 0 | 1 innebär att systemet är friskt, och 0 betyder att systemet inte är hälsosamt. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Antal resultat > 0   | Konsuln avger sin hälsostatus med jämna mellanrum. 0 betyder att systemet är friskt, och 1 innebär att utsändaren upptäcker att konsuln är nere. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" eller Name_s="doppler.shedEnvelopes") Delta_d>0 | Antal resultat > 0 | Deltan numrerar av meddelanden som tappas avsiktligt av Doppler tack vare mot pressar. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Antal resultat > 0   | Loggregator avger **LGR** för att indikera problem med loggningsprocessen. Ett exempel på ett sådant problem är när loggmeddelandets utdata är för hög. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Antal resultat > 0   | När munstycket tar emot en långsam konsumentavisering från loggregator skickar det **slowConsumerAlert** ValueMetric till Azure Monitor-loggar. |
| Type=CF_CounterEvent_CL Job_s=munstycke Name_s=eventsLost Delta_d>0              | Antal resultat > 0   | Om deltaantalet förlorade händelser når ett tröskelvärde betyder det att munstycket kan ha problem med att köras. |

## <a name="scale"></a>Skalning

Du kan skala munstycket och loggregatorn.

### <a name="scale-the-nozzle"></a>Skala munstycket

Du bör börja med minst två instanser av munstycket. Firehose distribuerar arbetsbelastningen över alla instanser av munstycket.
Om du vill vara säker på att munstycket kan hålla jämna steg med datatrafiken från brandsysten ställer du in den **slowConsumerAlert-aviseringen** (listad i föregående avsnitt, "Skapa varningsregler"). När du har fått ett meddelande följer du [anvisningarna för långsamt munstycke för](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) att avgöra om skalning behövs.
Om du vill skala upp munstycket använder du Apps Manager eller CF CLI för att öka instansnumren eller minnes- eller diskresurserna för munstycket.

### <a name="scale-the-loggregator"></a>Skala loggregatorn

Loggregator skickar ett **LGR-loggmeddelande** för att indikera problem med loggningsprocessen. Du kan övervaka aviseringen för att avgöra om loggregatorn behöver skalas upp.
Om du vill skala upp loggregatorn ökar du antingen Doppler-buffertstorleken eller lägger till ytterligare Doppler-serverinstanser i CF-manifestet. Mer information finns [i vägledningen för skalning av loggregatorn](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Uppdatering

Om du vill uppdatera munstycket med en nyare version hämtar du den nya munstyckesutlösningen, följer stegen i avsnittet "Distribuera munstycket" och skickar programmet igen.

### <a name="remove-the-nozzle-from-ops-manager"></a>Ta bort munstycket från Ops Manager

1. Logga in på Ops Manager.
2. Leta reda på **Microsoft Azure Log Analytics-munstycket för PCF-panelen.**
3. Markera skräpikonen och bekräfta borttagningen.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Ta bort munstycket från utvecklingsdatorn

Skriv:
```
cf delete <App Name> -r
```

Om du tar bort munstycket tas inte data i OMS-portalen bort automatiskt. Den upphör att gälla baserat på bevarandeinställningen för Azure Monitor-loggar.

## <a name="support-and-feedback"></a>Support och feedback

Azure Log Analytics-munstycket är öppen källkod. Skicka dina frågor och feedback till [avsnittet GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Om du vill öppna en Azure-supportbegäran väljer du "Virtuell dator som kör Cloud Foundry" som tjänstkategori. 

## <a name="next-step"></a>Nästa steg

Från PCF2.0 överförs vm-prestandamått till Azure Log Analytics-munstycket av System Metrics Forwarder och integreras i log analytics-arbetsytan. Du behöver inte längre Log Analytics-agenten för prestandamåtten för virtuella datorer. Du kan dock fortfarande använda Log Analytics-agenten för att samla in Syslog-information. Log Analytics-agenten installeras som ett Bosh-tillägg till dina virtuella cf-datorer. 

Mer information finns i [Distribuera Logganalysagent till din Cloud Foundry-distribution](https://github.com/Azure/oms-agent-for-linux-boshrelease).
