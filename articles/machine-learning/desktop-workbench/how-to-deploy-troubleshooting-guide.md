---
title: Felsökningsguide för Azure Machine Learning-distribution | Microsoft Docs
description: Felsökningsguide för distribution och skapa tjänster
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/16/2017
ROBOTS: NOINDEX
ms.openlocfilehash: a2867545e454e2b13360d87f5282e684753d6476
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994504"
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>Felsöka distributionen av tjänsten och konfigurera miljön

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Med hjälp av följande information kan du ta reda på orsaken fel när du konfigurerar modellhanteringsmiljö.

## <a name="model-management-environment"></a>Modellhanteringsmiljö
### <a name="contributor-permission-required"></a>Deltagarbehörighet som krävs
Du behöver deltagaråtkomst till prenumerationen eller resursgruppen som ska konfigurera ett kluster för distribution av dina webbtjänster.

### <a name="resource-availability"></a>Resurstillgänglighet
Du måste ha tillräckligt med resurser i din prenumeration så att du kan etablera miljöresurser.

### <a name="subscription-caps"></a>Prenumeration Caps
Din prenumeration kan ha ett tak fakturering som kan hindra dig från att etablera miljöresurser. Ta bort åtkomstprincipen för att aktivera etablering.

### <a name="enable-debug-and-verbose-options"></a>Aktivera felsökning och utförlig alternativ
Använd den `--debug` och `--verbose` flaggor i installationskommandot för att visa information för felsökning och spårning som miljön etableras.

```
az ml env setup -l <location> -n <name> -c --debug --verbose 
```

## <a name="service-deployment"></a>Distributionen av tjänsten
Med hjälp av följande information kan du ta reda på orsaken fel under distributionen eller när du anropar webbtjänsten.

### <a name="service-logs"></a>Tjänstloggar
Den `logs` möjlighet att tjänsten CLI tillhandahåller loggdata från Docker och Kubernetes.

```
az ml service logs realtime -i <web service id>
```

Ytterligare inställningar, använda den `--help` (eller `-h`) alternativet.

```
az ml service logs realtime -h
```

### <a name="debug-and-verbose-options"></a>Alternativ för felsökning och utförlig
Använd den `--debug` flagga för att visa felsökningsloggar som tjänsten distribueras.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

Använd den `--verbose` flagga för att se ytterligare information som tjänsten distribueras.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

### <a name="enable-request-logging-in-app-insights"></a>Aktivera loggning i App Insights begäran
Ange den `-l` -flaggan till true när du skapar en webbtjänst för att aktivera loggning för nivå av begäranden. Begäran-loggarna skrivs till App Insights-instans för din miljö i Azure. Sök efter den här instansen med det namnet i miljön som du använde när du använder den `az ml env setup` kommando.

- Ange `-l` till true när du skapar tjänsten.
- Öppna App Insights i Azure-portalen. Använd miljönamnet på din för att hitta App Insights-instans.
- En gång i App Insights, klickar du på Sök i menyn högst upp om du vill visa resultatet.
- Eller gå till `Analytics`  >  `Exceptions`  >  `exceptions take | 10`.


### <a name="add-error-handling-in-scoring-script"></a>Lägg till felhantering i bedömning skript
Använda undantagshantering i din `scoring.py` skriptets **kör** funktionen för att returnera ett felmeddelande som en del av dina web service-utdata.

Python-exempel:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="other-common-problems"></a>Andra vanliga problem
- Om pip-installation av azure-cli-ml misslyckas med fel `cannot find the path specified` på en Windows-dator kan du behöva aktivera stöd för långa sökvägsnamn. Se https://blogs.msdn.microsoft.com/jeremykuhne/2016/07/30/net-4-6-2-and-long-paths-on-windows-10/. 
- Om den `env setup` kommandot misslyckas med `LocationNotAvailableForResourceType`, använder du förmodligen fel plats (region) för de machine learning-resurser. Kontrollera att platsen med den `-l` parametern är `eastus2`, `westcentralus`, eller `australiaeast`.
- Om den `env setup` kommandot misslyckas med `Resource quota limit exceeded`, se till att du har tillräckligt många kärnor tillgängliga i din prenumeration och dina resurser inte som används upp i andra processer.
- Om den `env setup` kommandot misslyckas med `Invalid environment name. Name must only contain lowercase alphanumeric characters`, tjänstens namn kan inte innehålla versaler, symboler eller understreck (_) (som i *my_environment*).
- Om den `service create` kommandot misslyckas med `Service Name: [service_name] is invalid. The name of a service must consist of lower case alphanumeric characters (etc.)`, kontrollera att tjänstnamnet är mellan 3 och 32 tecken; startar och slutar med gemena alfanumeriska tecken, och innehåller inte versaler, symboler än bindestreck (-) och period ( . ), eller understreck (_) (som i *my_webservice*).
- Försök igen om du får en `502 Bad Gateway` fel vid anrop av webbtjänsten. Det innebär normalt att behållaren inte har distribuerats till klustret ännu.
- Om du får `CrashLoopBackOff` fel när du skapar en tjänst finns i loggarna. Det är vanligtvis resultatet av beroenden som saknas i den **init** funktion.
