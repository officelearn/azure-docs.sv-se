---
title: Distribuera Azure Log Analytics Nozzle för Cloud Foundry övervakning | Microsoft Docs
description: Stegvisa anvisningar för att distribuera Cloud Foundry loggregator Nozzle för Azure Log Analytics. Använd Nozzle för att övervaka den Cloud Foundry systemmått hälsa och prestanda.
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
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: c58c2b255d269aef7e8b3fea62d003ad0c16ef0a
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868134"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Distribuera Azure Log Analytics Nozzle för Cloud Foundry systemövervakning

[Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/) är en tjänst i Azure. Det hjälper dig att samla in och analysera data som genereras från molnet och lokala miljöer.

Log Analytics Nozzle (Nozzle) är en komponent för Cloud Foundry (CF), som vidarebefordrar mått från den [Cloud Foundry loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) firehose till Log Analytics. Du kan använda Nozzle, för att samla in, visa och analysera dina CF hälsotillstånd och prestanda systemmått, mellan flera distributioner.

I det här dokumentet har du lära dig hur du distribuerar Nozzle för CF-miljö och komma åt data från Log Analytics-konsolen.

## <a name="prerequisites"></a>Förutsättningar

Följande är krav för distribution av Nozzle.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Distribuera CF eller Pivotal Cloud Foundry-miljö i Azure

Du kan använda Nozzle med en öppen källkod CF distribution eller en Pivotal Cloud Foundry PCF ()-distribution.

* [Distribuera Cloud Foundry på Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Distribuera Pivotal Cloud Foundry på Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Installera CF kommandoradsverktyg för att distribuera Nozzle

Nozzle körs som ett program i CF miljö. Du behöver CF CLI för att distribuera programmet.

Nozzle måste också behörighet till loggregator firehose och Cloud-styrenhet. För att skapa och konfigurera användaren måste tjänsten användarkonto och autentisering (USA).

* [Installera Cloud Foundry CLI](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Installera Cloud Foundry UAA kommandoradsklient](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Se till att Rubygems har installerats innan du konfigurerar UAA kommandoradsklient.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Skapa en Log Analytics-arbetsyta i Azure

Du kan skapa Log Analytics-arbetsytan manuellt eller genom att använda en mall. Mallen distribuerar en inställning av förkonfigurerade OMS KPI-vyer och aviseringar för OMS-konsolen. 

#### <a name="to-create-the-workspace-manually"></a>Att skapa arbetsytan manuellt:

1. Sök i listan över tjänster på Azure Marketplace i Azure-portalen och välj sedan Log Analytics.
2. Välj **skapa**, och välj sedan alternativ för följande objekt:

   * **OMS-arbetsyta**: Skriv ett namn för din arbetsyta.
   * **Prenumeration**: Om du har flera prenumerationer väljer du det som är samma som din CF-distribution.
   * **Resursgrupp**: du kan skapa en ny resursgrupp eller Använd samma med CF distributionen.
   * **Plats**: Ange platsen.
   * **Prisnivå**: Välj **OK** att slutföra.

Mer information finns i [Kom igång med Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

#### <a name="to-create-the-oms-workspace-through-the-oms-monitoring-template-from-azure-market-place"></a>Skapa OMS-arbetsytan via OMS övervakning mallen från Azure Marketplace:

1. Öppna Azure-portalen.
2. Klicka på tecknet ”+” eller ”skapa en resurs” i det övre vänstra hörnet.
3. Skriv ”Cloud Foundry” i sökfönstret och välj ”OMS Cloud Foundry lösning för övervakning av”.
4. OMS Cloud Foundry som övervakning lösning front mallsidan har lästs in, klicka på ”Skapa” om du vill starta bladet mall.
5. Ange de obligatoriska parametrarna:
    * **Prenumeration**: Välj en Azure-prenumeration för OMS-arbetsyta, vanligtvis samma med Cloud Foundry-distribution.
    * **Resursgrupp**: Välj en befintlig resursgrupp eller skapa en ny nyckel för OMS-arbetsytan.
    * **Plats för resursgruppen**: Välj platsen för resursgruppen.
    * **OMS_Workspace_Name**: Ange ett namn för arbetsytan om arbetsytan inte finns, mallen skapar en ny.
    * **OMS_Workspace_Region**: Välj en plats för arbetsytan.
    * **OMS_Workspace_Pricing_Tier**: välja OMS-arbetsyta SKU. Se den [priser vägledning](https://azure.microsoft.com/pricing/details/log-analytics/) referens.
    * **Juridiska villkor**: Klicka på juridiska villkor, och klicka sedan på ”Skapa” vid accepterande av juridiska villkor.
- När alla parametrar anges, klickar du på ”Skapa” om du vill distribuera mallen. När distributionen är klar visas status på fliken meddelande.


## <a name="deploy-the-nozzle"></a>Distribuera Nozzle

Det finns ett par olika sätt att distribuera Nozzle: som en PCF-panel eller ett program för CF.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Distribuera Nozzle som en panel på PCF Ops Manager

Följ stegen för att [installera och konfigurera Azure Log Analytics Nozzle för PCF](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Det här är förenklad metod, PCF Ops manager panelen automatiskt att konfigurera och skicka nozzle. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Distribuera Nozzle manuellt som ett program för CF

Om du inte använder PCF Ops Manager kan du distribuera Nozzle som ett program. I följande avsnitt beskrivs den här processen.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Logga in på din CF distribution via CF CLI-administratör

Kör följande kommando:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

”SYSTEM_DOMAIN” är CF domännamnet. Du kan hämta den genom att söka ”SYSTEM_DOMAIN” i manifestfilen CF distribution. 

”CF_User” är CF admin-namn. Du kan hämta namn och lösenord genom att söka i avsnittet ”scim”, letar efter namnet och ”cf_admin_password” i manifestfilen CF distribution.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Skapa en CF-användare och tilldela de behörigheter som krävs

Kör följande kommandon:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

”SYSTEM_DOMAIN” är CF domännamnet. Du kan hämta den genom att söka ”SYSTEM_DOMAIN” i manifestfilen CF distribution.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Ladda ned den senaste versionen av Log Analytics Nozzle

Kör följande kommando:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Ange miljövariabler

Du kan nu ange miljövariabler i filen manifest.yml i den aktuella katalogen. Nedan visas appmanifestet för Nozzle. Ersätt värdena med din specifika information för Log Analytics-arbetsytan.

```
OMS_WORKSPACE             : Log Analytics workspace ID: open OMS portal from your Log Analytics workspace, select Settings, and select connected sources.
OMS_KEY                   : OMS key: open OMS portal from your Log Analytics workspace, select Settings, and select connected sources.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Log Analytics. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Log Analytics. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Log Analytics. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Log Analytics as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Log Analytics. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Push-programmet på utvecklingsdatorn

Kontrollera att du är under mappen oms-log-analytics-firehose-nozzle. Kör följande kommando:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Verifiera installationen Nozzle

### <a name="from-apps-manager-for-pcf"></a>Från appar Manager (för PCF)

1. Logga in till Ops Manager och kontrollera att panelen visas på instrumentpanelen för installation.
2. Logga in på appar Manager, kontrollera att det utrymme som du har skapat för Nozzle finns på användningsrapporten och bekräfta att statusen är normalt.

### <a name="from-your-development-computer"></a>Från utvecklingsdatorn

CF CLI-fönstret skriver du:
```
cf apps
```
Kontrollera att OMS Nozzle programmet körs.

## <a name="view-the-data-in-the-oms-portal"></a>Visa data i OMS-portalen

Om du har distribuerat OMS övervakningslösning via Marketplace-mall, gå till Azure portal och finns OMS-lösning. Du hittar lösningen i resursgruppen som du angav i mallen. Klicka på lösningen, bläddra till ”OMS-konsolen”, förkonfigurerade vyer visas i listan med översta Cloud Foundry system KPI: er, programdata, aviseringar och VM-hälsa mått. 

Om du har skapat OMS-arbetsytan manuellt, följer du stegen nedan för att skapa vyer och aviseringar:

### <a name="1-import-the-oms-view"></a>1. Importera OMS-vy

Från OMS-portalen bläddrar du till **Vydesigner** > **Import** > **Bläddra**, och välj en av filerna omsview. Välj exempelvis *molnet Foundry.omsview*, och spara vyn. Nu visas en panel på den **översikt** sidan. Välj den för att se visualiserade mått.

Du kan anpassa dessa vyer eller skapa nya vyer via **Vydesigner**.

Den *”molnet Foundry.omsview”* är en förhandsversion av Cloud Foundry OMS visa mall. Det här är en fullständigt konfigurerad standardmall. Om du har förslag eller feedback om mallen kan du skicka dem till den [utfärda avsnittet](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Skapa aviseringsregler

Du kan [skapa aviseringarna](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts), och anpassa frågor och tröskelvärden efter behov. Följande är rekommenderade aviseringar:

| Sökfråga                                                                  | Generera avisering baserat på | Beskrivning                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Antal resultat < 1   | **BBS. Domain.CF appar** om cf-apps-domän är uppdaterad. Det innebär att CF-App-begäranden från molnet domänkontrollant är synkroniserade till bbs. LRPsDesired (Diego önskade AIs) för körning. Inga data har mottagits innebär cf-apps-domän inte är uppdaterad under den angivna tidsperioden. |
| Typ = CF_ValueMetric_CL Origin_s = rep Name_s = UnhealthyCell Value_d > 1            | Antal resultat > 0   | 0 betyder felfri för Diego celler och 1 innebär feltillstånd. Ange aviseringen om flera felaktiga Diego celler har identifierats under den angivna tidsperioden. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Antal resultat > 0 | 1 innebär att systemet är felfritt och 0 innebär att systemet inte är felfri. |
| Typ = CF_ValueMetric_CL Origin_s = route_emitter Name_s = ConsulDownMode Value_d > 0 | Antal resultat > 0   | Konsuln skickar regelbundet dess hälsostatus. 0 innebär att systemet är felfri och 1 innebär att vägen sändare identifierar att konsuln är igång. |
| Typ = CF_CounterEvent_CL Origin_s = DopplerServer (Name_s="TruncatingBuffer.DroppedMessages” eller Name_s="doppler.shedEnvelopes”) Delta_d > 0 | Antal resultat > 0 | Delta antal meddelanden som avsiktligt utelämnats av Doppler på grund av tryck. |
| Typ = CF_LogMessage_CL SourceType_s = LGR MessageType_s = fel                      | Antal resultat > 0   | Loggregator genererar **LGR** som visar problem med hur loggning. Ett exempel på sådana problem är när meddelandet loggutdata är för hög. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Antal resultat > 0   | När Nozzle tar emot en avisering om långsam konsument från loggregator, skickas den **slowConsumerAlert** ValueMetric till Log Analytics. |
| Typ = CF_CounterEvent_CL Job_s = nozzle Name_s = förlorade händelser Delta_d > 0              | Antal resultat > 0   | Om delta antalet förlorade händelser når ett tröskelvärde, innebär det att Nozzle kan ha problem med att köra. |

## <a name="scale"></a>Skala

Du kan skala Nozzle och loggregator.

### <a name="scale-the-nozzle"></a>Skala Nozzle

Du bör börja med minst två instanser av Nozzle. Firehose distribuerar arbetsbelastningen över alla instanser av Nozzle.
Om du vill se till att Nozzle kan hålla jämna steg med datatrafik från firehose, ställa in den **slowConsumerAlert** avisering (visas i föregående avsnitt, ”skapa Varningsregler”). När du har blivit aviserad, följer du de [vägledning för långsam Nozzle](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) att avgöra om skalning krävs.
Om du vill skala upp Nozzle, använder du appar Manager eller CF CLI för att öka instans siffror eller resurserna som minne eller diskutrymme för Nozzle.

### <a name="scale-the-loggregator"></a>Skala loggregator

Loggregator skickar en **LGR** loggmeddelande som visar problem med hur loggning. Du kan övervaka aviseringen för att avgöra om loggregator måste skalas upp.
Om du vill skala upp loggregator, öka den Doppler buffertstorleken eller lägga till ytterligare Doppler server-instanser i CF manifestet. Mer information finns i [riktlinjer för att skala loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Uppdatering

Om du vill uppdatera Nozzle med en nyare version, ladda ned den nya versionen Nozzle, följer du stegen i det föregående avsnittet ”distribuera Nozzle” och push-programmet igen.

### <a name="remove-the-nozzle-from-ops-manager"></a>Ta bort Nozzle från Ops Manager

1. Logga in till Ops Manager.
2. Leta upp den **Microsoft Azure Log Analytics Nozzle för PCF** panelen.
3. Välj ikonen för skräpinsamling och bekräfta borttagningen.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Ta bort Nozzle från utvecklingsdatorn

Din CF CLI-fönstret skriver du:
```
cf delete <App Name> -r
```

Om du tar bort Nozzle bort data i OMS-portalen inte automatiskt. Det går ut baserat på din Log Analytics-kvarhållningsinställning.

## <a name="support-and-feedback"></a>Support och feedback

Azure Log Analytics Nozzle är öppen källkod. Skicka dina frågor och feedback till den [GitHub avsnittet](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Välj ”virtuell dator som kör Cloud Foundry” som tjänstekategori för att öppna en supportförfrågan för Azure. 

## <a name="next-step"></a>Nästa steg

Från PCF2.0, är VM-prestandamått överförs till Azure log analytics nozzle med System mått vidarebefordrare och integrerade i OMS-arbetsytan. Du behöver inte längre OMS-agenten för VM-prestandamått. Du kan dock fortfarande använda OMS-agenten att samla in Syslog-information. OMS-agenten har installerats som en Bosh tillägg till dina CF virtuella datorer. 

Mer information finns i [distribuera OMS-agent till Cloud Foundry-distributionen](https://github.com/Azure/oms-agent-for-linux-boshrelease).
