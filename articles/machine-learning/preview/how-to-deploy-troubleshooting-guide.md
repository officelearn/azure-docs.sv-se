---
title: "Azure Machine Learning deployment guide för felsökning | Microsoft Docs"
description: "Felsökningsguide för distribution och skapa en tjänst"
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 4cf372f17c3ded5c25a6ebde70b17b2c095a7169
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>Felsöka service-distributionen och miljö
Följande information kan hjälpa dig att fastställa orsaken till fel när du ställer in hanteringsmiljö modell.

## <a name="model-management-environment"></a>Modellen hanteringsmiljö
### <a name="contributor-permission-required"></a>Deltagare behörigheten som krävs
Du behöver deltagare åtkomst till prenumerationen eller resursgrupp att konfigurera ett kluster för distribution av web services.

### <a name="resource-availability"></a>Resurstillgänglighet
Du måste ha tillräckligt med resurser i din prenumeration så att du kan etablera miljö resurser.

### <a name="subscription-caps"></a>Prenumerationen Caps
Din prenumeration kan ha ett tak på fakturering som kan förhindra att du etablerar resurser för miljön. Ta bort åtkomstprincipen för att aktivera etablering.

### <a name="enable-debug-and-verbose-options"></a>Aktivera felsökning och utförlig alternativ
Använd den `--debug` och `--verbose` flaggor i installationskommandot att visa information för felsökning och spårning enligt miljön etableras.

```
az ml env setup -l <location> -n <name> -c --debug --verbose 
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
- Om pip-installation av azure cli ml misslyckas med felmeddelandet `cannot find the path specified` på en Windows-dator måste du aktivera stöd för lång sökväg. Se https://blogs.msdn.microsoft.com/jeremykuhne/2016/07/30/net-4-6-2-and-long-paths-on-windows-10/. 
- Om den `env setup` kommandot misslyckas med `LocationNotAvailableForResourceType`, använder du troligtvis fel plats (region) för den datorn utbildningsresurser. Kontrollera att platsen med den `-l` parametern är `eastus2`, `westcentralus`, eller `australiaeast`.
- Om den `env setup` kommandot misslyckas med `Resource quota limit exceeded`, kontrollera att du har tillräckligt med tillgängliga i prenumerationen kärnor och att dina resurser inte används upp i andra processer.
- Om den `env setup` kommandot misslyckas med `Invalid environment name. Name must only contain lowercase alphanumeric characters`, tjänstens namn kan inte innehålla versaler, symboler eller understreck (_) (som i *my_environment*).
- Om den `service create` kommandot misslyckas med `Service Name: [service_name] is invalid. The name of a service must consist of lower case alphanumeric characters (etc.)`, kontrollera tjänstens namn är mellan 3 och 32 tecken; startar och slutar med gemena alfanumeriska tecken, och inte innehålla versaler, symboler än bindestreck (-) och period ( . ), eller understreck (_) (som i *my_webservice*).
- Försök igen om du får en `502 Bad Gateway` fel vid anrop av webbtjänsten. Det innebär normalt behållaren inte har distribuerats till klustret ännu.
- Om du får `CrashLoopBackOff` fel när du skapar en tjänst i loggarna. Det är vanligtvis resultatet av beroenden som saknas på den **init** funktion.
