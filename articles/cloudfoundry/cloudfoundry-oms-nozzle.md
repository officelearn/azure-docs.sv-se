---
title: Distribuera Azure Log Analytics munstycket för övervakning av molnet Foundry | Microsoft Docs
description: Stegvisa anvisningar om hur du distribuerar Cloud Foundry loggregator munstycket för Azure logganalys. Använd munstycket för att övervaka system molnet Foundry mått hälsotillstånd och prestanda.
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
ms.openlocfilehash: 687356b60ad0bbc469d67e071ce3bccc8b61ebd7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34609009"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Distribuera Azure Log Analytics munstycket för molnet Foundry systemövervakning

[Azure logganalys](https://azure.microsoft.com/services/log-analytics/) är en tjänst i Azure. Det hjälper dig att samla in och analysera data som genereras från molnet och lokala miljöer.

Log Analytics munstycket (munstycket) är en komponent i molnet Foundry (CF), som vidarebefordrar mått från den [moln Foundry loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) firehose till logganalys. Med munstycket, kan du samla in, visa och analysera dina CF system hälsotillstånd och prestanda statistik, och över flera distributioner.

Lär dig hur du distribuerar munstycket till CF-miljön och komma åt data från konsolen logganalys i det här dokumentet.

## <a name="prerequisites"></a>Förutsättningar

Följande är krav för distribution av munstycket.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Distribuera en CF eller spela en central molnet Foundry miljö i Azure

Du kan använda munstycket med en öppen källkod CF distribution eller en spela en central molnet Foundry (PCF)-distribution.

* [Distribuera moln Foundry på Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Distribuera spela en central molnet Foundry på Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Installera CF kommandoradsverktyg för att distribuera munstycket

Munstycket körs som ett program i CF miljö. Du måste CF CLI för att distribuera programmet.

Munstycket måste också behörighet till loggregator firehose och moln-styrenhet. Om du vill skapa och konfigurera användaren, behöver du användarkontot och autentisering (USA)-tjänst.

* [Installera molnet Foundry CLI](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Installera molnet Foundry UAA kommandorads-klient](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Kontrollera att Rubygems är installerade innan du installerar klienten UAA kommandoraden.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Skapa en logganalys-arbetsytan i Azure

Du kan skapa logganalys-arbetsytan manuellt eller genom att använda en mall. Mallen kommer att distribuera en installation av förkonfigurerade OMS KPI-vyer och aviseringar för OMS-konsolen. 

#### <a name="to-create-the-workspace-manually"></a>Skapa arbetsytan manuellt:

1. Sök i listan över tjänster i Azure Marketplace i Azure-portalen och välj sedan logganalys.
2. Välj **skapa**, och välj sedan alternativ för följande objekt:

   * **OMS-arbetsytan**: Ange ett namn för din arbetsyta.
   * **Prenumerationen**: Om du har flera prenumerationer kan välja den som är samma som CF distributionen.
   * **Resursgruppen**: du kan skapa en ny resursgrupp eller Använd samma med CF distributionen.
   * **Plats**: Ange platsen.
   * **Prisnivån**: Välj **OK** ska slutföras.

Mer information finns i [Kom igång med logganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

#### <a name="to-create-the-oms-workspace-through-the-oms-monitoring-template-from-azure-market-place"></a>Skapa OMS-arbetsytan via mallen OMS övervakning från Azure-marknadsplatsen:

1. Öppna Azure-portalen.
2. Klicka på tecknet ”+” eller ”skapa en resurs” i det övre vänstra hörnet.
3. Skriv ”moln Foundry” i fönstret och välj ”OMS Foundry övervakning Molnlösning”.
4. OMS Cloud-Foundry övervakning lösning främre mallsida som har lästs in, klickar du på ”Skapa” för att starta bladet mallen.
5. Ange de obligatoriska parametrarna:
    * **Prenumerationen**: Välj en Azure-prenumeration för OMS-arbetsyta, vanligtvis samma med molnet Foundry distribution.
    * **Resursgruppen**: Välj en befintlig resursgrupp eller skapa en ny för OMS-arbetsytan.
    * **Resursgruppsplats**: Välj platsen för resursgruppen.
    * **OMS_Workspace_Name**: Ange ett Arbetsytenamn på, om inte finns på arbetsytan mallen skapas en ny.
    * **OMS_Workspace_Region**: Välj plats för arbetsytan.
    * **OMS_Workspace_Pricing_Tier**: Välj OMS-arbetsyta SKU. Finns det [priser vägledning](https://azure.microsoft.com/pricing/details/log-analytics/) referens.
    * **Juridiska villkor**: Klicka på juridiska villkor, klicka på ”Skapa” vid accepterande av juridiska villkor.
- Klicka på ”Skapa” för att distribuera mallen när alla parametrar har angetts. När distributionen är klar visas status på fliken meddelande.


## <a name="deploy-the-nozzle"></a>Distribuera munstycket

Det finns ett par olika sätt att distribuera munstycket: som en panel PCF eller som en CF-program.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Distribuera munstycket som en panel PCF Ops Manager

Följ stegen för att [installera och konfigurera Azure Log Analytics munstycket för PCF](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Detta är en förenklad metod, PCF Ops manager panelen automatiskt att konfigurera och push munstycket. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Distribuera manuellt munstycket som ett CF-program

Om du inte använder PCF Ops Manager kan du distribuera munstycket som ett program. I följande avsnitt beskrivs den här processen.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Logga in som administratör via CF CLI CF distributionen

Kör följande kommando:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

”SYSTEM_DOMAIN” är CF domännamnet. Du kan hämta den genom att söka efter ”SYSTEM_DOMAIN” i manifestfilen CF distribution. 

”CF_User” är CF admin-namn. Du kan hämta namn och lösenord genom att söka igenom avsnittet ”scim”, söker efter namn och ”cf_admin_password” i manifestfilen CF distribution.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Skapa en CF användare och bevilja behörigheter som krävs

Kör följande kommandon:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

”SYSTEM_DOMAIN” är CF domännamnet. Du kan hämta den genom att söka efter ”SYSTEM_DOMAIN” i manifestfilen CF distribution.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Hämta den senaste versionen av Log Analytics munstycket

Kör följande kommando:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Ange miljövariabler

Du kan nu ange miljövariabler i filen manifest.yml i den aktuella katalogen. Nedan visas appmanifestet för munstycket. Ersätt värden med din specifika information för logganalys-arbetsytan.

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

Kontrollera att du är under mappen oms-log-analytics-firehose-munstycket. Kör följande kommando:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Verifiera munstycket installationen

### <a name="from-apps-manager-for-pcf"></a>Från appar Manager (för PCF)

1. Logga in till Ops Manager och kontrollera att panelen visas på instrumentpanelen för installation.
2. Logga in på appar Manager, kontrollera det utrymme som du har skapat för munstycket visas i användningsrapporter och bekräfta att status är normal.

### <a name="from-your-development-computer"></a>På utvecklingsdatorn

I fönstret CF CLI skriver du:
```
cf apps
```
Kontrollera att OMS munstycket programmet körs.

## <a name="view-the-data-in-the-oms-portal"></a>Visa data i OMS-portalen

Om du har distribuerat OMS övervakningslösning via mallen marknadsplatsen gå till Azure-portalen och finns OMS-lösning. Du hittar lösningen i den resursgrupp som du angav i mallen. Klicka på lösningen, bläddra till ”OMS-konsolen”, förkonfigurerade vyer visas med högsta molnet Foundry system KPI: er, programdata, aviseringar och VM hälsa mått. 

Om du har skapat OMS-arbetsytan manuellt, följer du stegen nedan för att skapa vyer och aviseringar:

### <a name="1-import-the-oms-view"></a>1. Importera OMS-vy

Bläddra till OMS-portalen **Vydesigner** > **importera** > **Bläddra**, och välj en av filerna omsview. Välj exempelvis *moln Foundry.omsview*, och spara vyn. Nu visas en sida vid sida på den **översikt** sidan. Välj den för att se visualiserade mått.

Du kan anpassa dessa vyer eller skapa nya vyer via **Vydesigner**.

Den *”moln Foundry.omsview”* är en förhandsversion av molnet Foundry OMS visa mall. Det här är en helt konfigurerade standardmall. Om du har förslag eller feedback om mallen skicka dem till den [utfärda avsnittet](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Skapa aviseringsregler

Du kan [skapa aviseringar](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts), och anpassa frågor och tröskelvärden efter behov. Följande rekommenderas aviseringar:

| Sökfråga                                                                  | Generera avisering baserat på | Beskrivning                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Antalet resultat < 1   | **BBS. Domain.CF appar** anger om domänen cf appar är uppdaterad. Det innebär att CF App begäranden från molnet Controller synkroniseras till bbs. LRPsDesired (Diego önskad AIs) för körning. Inga data tas emot innebär cf apps-domän inte är uppdaterad under den angivna tidsperioden. |
| Typ = CF_ValueMetric_CL Origin_s = rep Name_s = UnhealthyCell Value_d > 1            | Antalet resultat > 0   | 0 betyder felfri för Diego celler och 1 innebär feltillstånd. Ange aviseringen om flera ohälsosamt Diego celler identifieras under den angivna tidsperioden. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Antalet resultat > 0 | 1 innebär att systemet är felfri och 0 innebär att systemet inte är felfri. |
| Typ = CF_ValueMetric_CL Origin_s = route_emitter Name_s = ConsulDownMode Value_d > 0 | Antalet resultat > 0   | Konsuln skickar med jämna mellanrum dess hälsostatus. 0 innebär att systemet är felfri och 1 innebär att vägen sändare upptäcker att konsuln ned. |
| Typ = CF_CounterEvent_CL Origin_s = DopplerServer (Name_s="TruncatingBuffer.DroppedMessages” eller Name_s="doppler.shedEnvelopes”) Delta_d > 0 | Antalet resultat > 0 | Delta antal meddelanden som utelämnats avsiktligt av Doppler på grund av ligger. |
| Typ = CF_LogMessage_CL SourceType_s = LGR MessageType_s = fel                      | Antalet resultat > 0   | Loggregator avger **LGR** att indikera problem med hur loggning. Ett exempel på sådana problem är när meddelandet loggutdata är för högt. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Antalet resultat > 0   | När munstycket tar emot en avisering om långsam konsumenten från loggregator, skickas den **slowConsumerAlert** ValueMetric till logganalys. |
| Typ = CF_CounterEvent_CL Job_s = munstycket Name_s = förlorade händelser Delta_d > 0              | Antalet resultat > 0   | Om antalet förlorade händelser delta når ett tröskelvärde, innebär det att munstycket kan ha problem med att köra. |

## <a name="scale"></a>Skala

Du kan skala munstycket och loggregator.

### <a name="scale-the-nozzle"></a>Skala munstycket

Du måste starta med minst två instanser av munstycket. Firehose distribuerar arbetsbelastningen över alla instanser av munstycket.
Kontrollera att munstycket kan klara av datatrafik från firehose genom att ställa in den **slowConsumerAlert** avisering (visas i föregående avsnitt, ”skapa Varningsregler”). När du har fått notifieringar följer den [vägledning för långsam munstycket](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) att avgöra om skalning behövs.
Om du vill skala upp munstycket använda appar Manager eller CF CLI för att öka instans tal eller resurser minne eller diskutrymme för munstycket.

### <a name="scale-the-loggregator"></a>Skala loggregator

Loggregator skickar en **LGR** loggmeddelande att indikera problem med hur loggning. Du kan övervaka aviseringen för att avgöra om loggregator behöver skalas upp.
Om du vill skala upp loggregator, öka buffertstorleken på som Doppler eller lägga till ytterligare Doppler server-instanser i manifestet CF. Mer information finns i [riktlinjer för att skala loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Uppdatering

Om du vill uppdatera munstycket med en nyare version, hämta den nya versionen munstycket, följer du stegen i föregående avsnitt ”distribuera munstycket” och push-programmet igen.

### <a name="remove-the-nozzle-from-ops-manager"></a>Ta bort munstycket från Ops Manager

1. Logga in till Ops Manager.
2. Leta upp den **Microsoft Azure Log Analytics munstycket för PCF** panelen.
3. Välj ikonen skräp och bekräfta borttagningen.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Ta bort munstycket på utvecklingsdatorn

I fönstret CF CLI, skriver du:
```
cf delete <App Name> -r
```

Om du tar bort munstycket är inte data i OMS-portalen bort automatiskt. Det går ut utifrån dina inställningar för kvarhållning av logganalys.

## <a name="support-and-feedback"></a>Support och feedback

Azure Log Analytics munstycket är öppna ursprung. Skicka frågor och feedback till den [GitHub-avsnitt](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Om du vill öppna en Azure-supportbegäran väljer du ”virtuell dator som kör molnet Foundry” som tjänstekategori. 

## <a name="next-step"></a>Nästa steg

Från PCF2.0, är VM prestandamått överförda till Azure log analytics munstycket genom systemet mått vidarebefordrare och integrerade i OMS-arbetsyta. Du inte längre behöver OMS-agent för den Virtuella prestandamåtten. Du kan emellertid fortfarande använda OMS-agenten att samla in information för Syslog. OMS-agent installeras som en Bosh kan läggas till dina CF virtuella datorer. 

Mer information finns i [distribuera OMS-agent till molnet Foundry distributionen](https://github.com/Azure/oms-agent-for-linux-boshrelease).
