---
title: "Azure Machine Learning deployment guide för felsökning | Microsoft Docs"
description: "Felsökningsguide för distribution och skapa en tjänst"
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/09/2017
ms.openlocfilehash: b9287c7151c96aaccbcda81c111cfe36ead5ab38
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2017
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>Felsöka service-distributionen och miljö
Följande information kan hjälpa dig att fastställa orsaken till fel när du ställer in hanteringsmiljö modell.

## <a name="model-management-environment"></a>Modellen hanteringsmiljö
### <a name="owner-permission-required"></a>Ägaren behörigheten som krävs
Du måste ha behörighet för ägare på Azure-prenumeration att registrera Compute för Machine Learning.

Du måste också ägare behörighet att konfigurera ett kluster för distribution av web services.

### <a name="resource-availability"></a>Resurstillgänglighet
Du måste ha tillräckligt med resurser i din prenumeration så att du kan etablera miljö resurser.

### <a name="subscription-caps"></a>Prenumerationen Caps
Din prenumeration kan ha ett tak på fakturering som kan förhindra att du etablerar resurser för miljön. Removet som cap för att aktivera etablering.

### <a name="enable-debug-and-verbose-options"></a>Aktivera felsökning och utförlig alternativ
Använd den `--debug` och `--verbose` flaggor i installationskommandot att visa information för felsökning och spårning enligt miljön etableras.

```
az ml env setup -l <loation> -n <name> -c --debug --verbose 
```

## <a name="service-deployment"></a>Service-distributionen
Följande information kan hjälpa dig att fastställa orsaken till fel under distributionen eller när du anropar webbtjänsten.

### <a name="service-logs"></a>tjänstloggar
Den `logs` alternativet för tjänsten CLI innehåller loggdata från Docker och Kubernetes.

```
az ml service logs realtime -i <web service id>
```

Ytterligare inställningar, använder den `--help` (eller `-h`) alternativet.

```
az ml service logs realtime -h
```

### <a name="debug-and-verbose-options"></a>Alternativ för felsökning och utförlig
Använd den `--debug` flagga för att visa felsökningsloggar när tjänsten distribueras.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

Använd den `--verbose` flagga för att se ytterligare information som tjänsten distribueras.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

### <a name="enable-request-logging-in-app-insights"></a>Aktivera loggning i appen insikter begäran
Ange den `-l` -flaggan till true när du skapar en webbtjänst för att aktivera loggning av begäranden nivå. Begäran loggarna skrivs till App Insights-instans för din miljö i Azure. Sök efter den här instansen med hjälp av miljö namnet som du använde när du använder den `az ml env setup` kommando.

- Ange `-l` till true om skapandet av tjänsten.
- Öppna appen insikter i Azure-portalen. Använd namnet på din miljö för att hitta App Insights-instans.
- En gång App Insights, klicka på Sök i den översta menyn Visa resultatet.
- Eller gå till `Analytics`  >  `Exceptions`  >  `exceptions take | 10`.


### <a name="add-error-handling-in-scoring-script"></a>Lägg till felhantering i bedömningen skript
Använd undantagshantering i din `scoring.py` skriptets **kör** funktionen för att returnera ett felmeddelande som en del av din web service-utdata.

Python-exempel:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="other-common-problems"></a>Andra vanliga problem
- Om den `env setup` kommandot misslyckas, kontrollera att du har tillräckligt med kärnor i prenumerationen.
- Använd inte understreck (_) i namnet på webben (som i *my_webservice*).
- Försök igen om du får en **502 felaktig Gateway** fel vid anrop av webbtjänsten. Det innebär normalt behållaren inte har distribuerats till klustret ännu.
- Om du får **CrashLoopBackOff** fel när du skapar en tjänst i loggarna. Det är vanligtvis resultatet av beroenden som saknas på den **init** funktion.
