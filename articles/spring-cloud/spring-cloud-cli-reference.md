---
title: AZ våren Cloud
description: Hantera Azure våren Cloud med Azure CLI
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.openlocfilehash: 869a47469f8e0429d3726651c28f5a58acfcb856
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279049"
---
# <a name="az-spring-cloud"></a>AZ fjäder – moln

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Hantera Azure våren Cloud med Azure CLI

>[!Note]
> Azure våren Cloud är för närvarande en för hands version.  De här kommandona kan ändras eller tas bort i en framtida version.

| AZ fjäder – moln |  |
|------|------:|
| [AZ våren – Cloud Create](#az-spring-cloud-create) | Skapa en Azure våren-moln instans. |
| [AZ fjäder – moln borttagning](#az-spring-cloud-delete) | Ta bort en Azure våren Cloud-instans. |
| [AZ fjäder – moln lista](#az-spring-cloud-list) | Lista alla Azure våren Cloud-instanser i den aktuella resurs gruppen. i annat fall listas prenumerations-ID: n. |
| [AZ fjäder – Cloud show](#az-spring-cloud-show) | Visa information om ett Azure våren-moln. |

| AZ våren – Cloud App | Kommandon för att hantera appar i Azure våren-molnet.  |
| ---- | ----: |
| [AZ fjäder – Cloud App Create](#az-spring-cloud-app-create) | Skapa en ny app med en standard distribution i Azure våren-molnet. |
| [ta bort AZ våren – Cloud App Delete](#az-spring-cloud-app-delete) | Ta bort en app i Azure våren-molnet. |
| [AZ våren – Cloud App Deploy](#az-spring-cloud-app-deploy) | Distribuera från käll kod eller en fördefinierad binärfil till en app och uppdatera relaterade konfigurationer. |
| [AZ fjäder – Cloud App List](#az-spring-cloud-app-list) | Visa en lista över alla appar i Azure våren-molnet. |
| [AZ fjäder – Cloud Apps restart](#az-spring-cloud-app-restart) | Starta om instanser av appen med standardinställningar för produktions distribution. |
| [AZ fjäder – Cloud App Scale](#az-spring-cloud-app-scale) | Skala en app eller dess distributioner manuellt. |
| [AZ fjäder – Cloud App set-Deployment](#az-spring-cloud-app-set-deployment) | Ange produktions distribution av en app. |
| [AZ fjäder – Cloud App show](#az-spring-cloud-app-show) | Visa information om en app i Azure våren-molnet. |
| [AZ våren – Cloud App show-Deploy-log](#az-spring-cloud-app-show-deploy-log) | Visa build-loggar för den senaste distributionen från källa. Som standard används produktions distributionen. |
| [AZ våren – Cloud App start](#az-spring-cloud-app-start) | Starta instanser av appen med standardinställningar för produktions distribution. |
| [AZ fjäder – Cloud App Stop](#az-spring-cloud-app-stop) | Stoppa instanser av appen med standardinställningar för produktions distribution. |
| [AZ våren – Cloud App Update](#az-spring-cloud-app-update) | Uppdatera den angivna appens konfiguration. |

| AZ fjäder – Cloud App-bindning | Kommandon för att hantera bindningar med Azure Data Services.  Appen måste startas om innan inställningarna börjar gälla. |
| --- | ---: |
| [AZ fjäder – Cloud App binding List](#az-spring-cloud-app-binding-list) | Visa en lista över alla tjänst bindningar i en app. |
| [AZ fjäder – Cloud App binding Remove](#az-spring-cloud-app-binding-remove) | Ta bort en tjänst bindning från appen. |
| [AZ fjäder – Cloud App binding show](#az-spring-cloud-app-binding-show) | Visa information om en tjänst bindning. |
| [AZ fjäder – Cloud App binding Cosmos Add](#az-spring-cloud-app-binding-cosmos-add) | Bind en Azure-CosmosDB med appen. |
| [AZ fjäder – Cloud App binding Cosmos Update](#az-spring-cloud-app-binding-cosmos-update) | Uppdatera en Azure CosmosDB service-bindning. |
| [AZ fjäder – Cloud App binding MySQL Add](#az-spring-cloud-app-binding-mysql-add) | Bind en Azure Database for MySQL med appen. |
| [AZ fjäder – Cloud App binding MySQL Update](#az-spring-cloud-app-binding-mysql-update) | Uppdatera en Azure Database for MySQL tjänst bindning. |
| [AZ fjäder – Cloud App binding Redis Add](#az-spring-cloud-app-binding-redis-add) | Bind en Azure-cache för Redis med appen. |
| [AZ fjäder – Cloud App binding Redis Update](#az-spring-cloud-app-binding-redis-update) | Uppdatera en Azure-cache för Redis-tjänstens bindning. |

| AZ våren – Cloud App Deployment | Kommandon för att hantera distributions livs cykeln för en app i Azure våren Cloud. |
| --- | ---: |
| [AZ fjäder – Cloud App Deployment Create](#az-spring-cloud-app-deployment-create) | Skapa en mellanlagrings distribution för appen. |
| [ta bort AZ våren – Cloud App Deployment](#az-spring-cloud-app-deployment-delete) | Ta bort en distribution av appen. |
| [AZ fjäder – Cloud App Deployment List](#az-spring-cloud-app-deployment-list) | Visa en lista över alla distributioner i en app. |
| [AZ fjäder – Cloud App Deployment show](#az-spring-cloud-app-deployment-show) | Visa information om distributionen. |

| AZ fjäder – moln konfiguration – Server | Kommandon för att hantera konfigurations servern för Azure våren Cloud. |
| --- | ---: |
| [AZ fjäder – moln konfiguration-Rensa Server](#az-spring-cloud-config-server-clear) | Radera alla inställningar på konfigurations servern. |
| [AZ fjäder – moln konfiguration – Server uppsättning](#az-spring-cloud-config-server-set) | Definiera konfigurations Server från en YAML-fil. |
| [AZ fjäder – Cloud config – server show](#az-spring-cloud-config-server-show) | Visa konfiguration av konfigurations Server. |
| [AZ fjäder – moln konfigurations Server git set](#az-spring-cloud-config-server-git-set) | Definiera git-egenskaper för konfigurations servern.  Tidigare värden kommer att skrivas över. |
| [AZ fjäder – Cloud config server git lagrings platsen Lägg till](#az-spring-cloud-config-server-git-repo-add) | Lägg till en ny git-lagringsplats till konfigurations servern. |
| [AZ fjäder – Cloud config server git lagrings platsen List](#az-spring-cloud-config-server-git-repo-list) | Visa en lista med alla git-lagringsplatser för konfigurations servern. |
| [AZ våren – Cloud config server git lagrings platsen Remove](#az-spring-cloud-config-server-git-repo-remove) | Ta bort den angivna git-lagringsplatsen från konfigurations servern. |

| AZ fjäder – Cloud test – slut punkt | Kommandon för att hantera slut punkts testning i Azure våren Cloud |
| --- | ---: |
| [AZ fjäder – moln test – slut punkts inaktive ring](#az-spring-cloud-test-endpoint-disable) | Inaktivera test slut punkt. |
| [AZ fjäder – moln test – slut punkts aktivering](#az-spring-cloud-test-endpoint-enable) | Aktivera test slut punkt. |
| [AZ fjäder – Cloud test – slut punkts lista](#az-spring-cloud-test-endpoint-list) | Lista test slut punkts nycklar. |
| [AZ fjäder – moln test – slut punkts förnyelse – nyckel](#az-spring-cloud-test-endpoint-renew-key) | Återskapa en test-Endpoint-nyckel. |

## <a name="az-spring-cloud-create"></a>AZ våren – Cloud Create

Skapa en ny app med en standard distribution i Azure våren-molnet.

```cli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Obligatoriska parametrar | |
| --- | ---: |
| --Name-n | Namnet på den här Azure våren Cloud-instansen. |
| --resurs-grupp-g | Anger resurs gruppen för den här appen.  Konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>` |

| Valfria parametrar | |
| --- | ---: |
| --plats-l | Anger serverns plats för den här appen.  Hitta giltiga platser med hjälp av `az account list-locations` |
| --No-wait | Utför inte länge drift åtgärder för att slutföras.

### <a name="examples"></a>Exempel

Skapa ett nytt Azure våren-moln i väst

```cli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>AZ fjäder – moln borttagning

Ta bort en Azure våren Cloud-instans.

```cli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Obligatoriska parametrar | |
| --- | ---: |
| --Name-n | Namnet på Azure våren Cloud-instansen som ska tas bort. |
| --resurs-grupp-g | Namnet på resurs gruppen som Azure våren-molnet tillhör. |

| Valfria parametrar | |
| --- | ---: |
| -ingen-wait | Vänta inte tills tids krävande åtgärder har slutförts. |

### <a name="example"></a>Exempel

Ta bort en Azure våren Cloud-instans med namnet "min tjänst" från "MyResourceGroup".

```cli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>AZ fjäder – moln lista

Visa en lista över alla Azure våren Cloud-instanser som är associerade med den aktuella resurs gruppen. Ange prenumerations-ID: n om ingen resurs grupp har angetts.

```cli
az spring-cloud list --resource-group -g
```

| Obligatoriska parametrar | |
| --- | ---: |
| --resurs-grupp-g | Namnet på resurs gruppen. |

## <a name="az-spring-cloud-show"></a>AZ fjäder – Cloud show

Visa information om den angivna Azure våren Cloud-instansen.

```cli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Obligatoriska parametrar | |
| --- | ---: |
| --Name-n | Namnet på Azure våren Cloud-instansen. |
| --resurs-grupp-g | Namnet på resurs gruppen som Azure våren Cloud-instansen tillhör.

## <a name="az-spring-cloud-app-create"></a>AZ fjäder – Cloud App Create

Skapa en ny app i ett Azure våren-moln.

```cli
az spring-cloud app create --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --enable-persistent-storage
                            --instance-count
                            --is-public
                            --memory
```

| Obligatoriska parametrar | |
| --- | ---: |
| --Name-n | Namnet på appen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --CPU | Antal virtuella kärnor per instans.  Standard: 1. |
| --Aktivera-beständig lagring | Booleskt värde.  Om värdet är true monteras en 50 GB disk med standard Sök vägen. |
| --antal instanser | Antal instanser.  Standard: 1. |
| --är-offentlig | Booleskt värde.  Om värdet är true tilldelas en offentlig domän. |
| --minne | Antal GB minne per instans.  Standard: 1. |

### <a name="examples"></a>Exempel

Skapa en app med standard konfigurationen.

```cli
az spring-cloud app create -n MyApp -s MyService
```

Skapa en offentligt tillgänglig app med 3 instanser.  Varje instans har 3 GB minne och 2 processor kärnor.

```cli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>ta bort AZ våren – Cloud App Delete

Tar bort en app i Azure våren-molnet.

```cli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Obligatoriska parametrar | |
| --- | ---: |
| --Name-n | Namnet på appen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deploy"></a>AZ våren – Cloud App Deploy

Distribuera en app till Azure våren Cloud från käll koden eller en fördefinierad binär och uppdatera relaterade konfigurationer.

```cli
az spring cloud app deploy  --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --deployment -d
                            --env
                            --instance-count
                            --jar-path
                            --jvm-options
                            --memory
                            --no-wait
                            --runtime-version
                            --target-module
                            --version
```

| Obligatoriska parametrar | |
| --- | ---: |
| --Name-n | Namnet på appen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --CPU | Antal virtuella CPI-kärnor per instans. |
| --distribution-d | Namnet på en befintlig app-distribution.  Som standard används produktions distributionen om inget anges. |
| --kuvert | Blankstegsavgränsad miljövariabler i formatet nyckel [= värde]. |
| --antal instanser | Antal instanser. |
| --jar-sökväg | Om det anges distribuerar du jar från den angivna sökvägen. Annars distribuerar du den aktuella mappen som en tar. |
| --JVM – alternativ | En sträng som innehåller JVM-alternativ.  Använd ' = ' i stället för ' ' för att undvika gränssnitts tolknings fel. T. ex. `--jvm-options='-Xms1024m -Xmx2048m`. |
| --minne | Antal GB minne per instans. |
| --No-wait | Vänta inte tills tids krävande åtgärder har slutförts. |
| --runtime-version | Körnings version av språket som används i appen.  Tillåtna värden: `Java_11``Java_8`. |
| --mål-modul | Underordnad modul som ska distribueras.  Krävs när flera jar-paket skapas från käll koden. |
| --version | Distributions version.  Oförändrad om inget anges. |

### <a name="examples"></a>Exempel

Distribuera käll koden till en app. Detta packar den aktuella katalogen, skapar en binär med hjälp av Pivoted build-tjänsten och distribuerar sedan till appen.

```cli
az spring-cloud app deploy -n MyApp -s MyService
```

Distribuera en fördefinierad jar-app till en app med hjälp av JVM-alternativ och miljövariabler.

```cli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

Distribuera käll koden till en speciell distribution av en app.

```cli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>AZ fjäder – Cloud App List

Visa en lista över alla appar i Azure våren Cloud-instansen.

```cli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Obligatoriska parametrar | |
| --- | ---: |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-restart"></a>AZ fjäder – Cloud Apps restart

Starta om instanser av appen.  Som standard används produktions distributionen.

```cli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Obligatoriska parametrar | |
| --- | ---: |
| --Name-n | Namnet på appen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --distribution-d | Namnet på den befintliga distributionen av appen.  Som standard används produktions distributionen om inget anges. |
| --No-wait | Vänta inte tills tids krävande åtgärder har slutförts. |

## <a name="az-spring-cloud-app-scale"></a>AZ fjäder – Cloud App Scale

Skala en app eller dess distributioner manuellt.

```cli
az spring-cloud app scale --name -n
                          --resource-group -g
                          --service -s
                          --cpu
                          --deployment -d
                          --instance-count
                          --memory
                          --no-wait
```

| Obligatoriska parametrar | |
| --- | ---: |
| --Name-n | Namnet på appen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --CPU | Antal virtuella processor kärnor per app-instans. |
| --distribution-d | Namnet på den befintliga distributionen av appen.  Som standard används produktions distributionen om inget anges. |
| --antal instanser | Antal instanser av den här appen. |
| --minne | Antal GB minne per app-instans. |
| --No-wait | Vänta inte på att tids krävande åtgärder ska slutföras. |

### <a name="examples"></a>Exempel

Skala upp en app till 4 processor kärnor och 8 GB minne per instans.

```cli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Skala ut en distribution av appen till 5 instanser.

```cli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>AZ fjäder – Cloud App set-Deployment

Ange konfigurations alternativen för produktions distributionen av appen.

```cli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| Obligatoriska parametrar | |
| --- | ---: |
| --distribution-d | Namnet på en befintlig distribution av appen. |
| --Name-n | Namnet på appen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --No-wait | Vänta inte på att tids krävande åtgärder ska slutföras. |

### <a name="examples"></a>Exempel

Växla en mellanlagrings distribution av appen till produktion.

```cli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>AZ fjäder – Cloud App show

Visa information om en app i Azure våren-molnet.

```cli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Obligatoriska parametrar | |
| --- | ---: |
| --Name-n | Namnet på appen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-show-deploy-log"></a>AZ våren – Cloud App show-Deploy-log

Visa build-loggen för den senaste distributionen från käll koden.  Som standard produktions miljö.

```cli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Obligatoriska parametrar | |
| --- | ---: |
| --Name-n | Namnet på appen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --distribution-d | Namnet på en befintlig distribution av appen.  Som standard används produktions miljön. |

## <a name="az-spring-cloud-app-start"></a>AZ våren – Cloud App start

Startar instanser av appen.  Som standard produktions miljö.

```cli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Obligatoriska parametrar | |
| --- | ---: |
| --Name-n | Namnet på appen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --distribution-d | Namnet på en befintlig distribution av appen.  Som standard används produktions miljön. |
| --No-wait | Vänta inte på att tids krävande åtgärder ska slutföras. |

## <a name="az-spring-cloud-app-stop"></a>AZ fjäder – Cloud App Stop

Stoppa instanser av appen.  Som standard används produktions miljön.

```cli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Obligatoriska parametrar | |
| --- | ---: |
| --Name-n | Namnet på appen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --distribution-d | Namnet på en befintlig distribution av appen.  Som standard används produktions miljön. |
| --No-wait | Vänta inte på att tids krävande åtgärder ska slutföras. |

## <a name="az-spring-cloud-app-update"></a>AZ våren – Cloud App Update

Uppdatera den lagrade konfigurationen av en app.

```cli
az spring-cloud app update --name -n
                           --resource-group -g
                           --service -s
                           --deployment -d
                           --enable-persistent-storage
                           --env
                           --is-public
                           --jvm-options
                           --no-wait
                           --runtime-version
```

| Obligatoriska parametrar | |
| --- | ---: |
| --Name-n | Namnet på appen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --distribution-d | Namnet på en befintlig distribution av appen.  Som standard används produktions miljön. |
| --Aktivera-beständig lagring | Booleskt.  Om värdet är true monterar du en 50 GB disk med standard Sök vägen. |
| --kuvert | Blankstegsavgränsad miljövariabler i formatet nyckel [= värde]. |
| --är-offentlig | Booleskt.  Om det här värdet är sant tilldelar du en offentlig domän till appen. |
| --JVM – alternativ | En sträng som innehåller JVM-alternativ.  Använd ' = ' i stället för ' ' för att undvika gränssnitts tolknings fel. T. ex. `--jvm-options='-Xms1024m -Xmx2048m`. |
| --No-wait | Vänta inte på att tids krävande åtgärder ska slutföras. |
| --runtime-version | Körnings version av språket som används i appen.  Tillåtna värden: `Java_11``Java_8`. |

### <a name="example"></a>Exempel

Lägg till en miljö variabel för appen.

```cli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>AZ fjäder – Cloud App binding List

Visa en lista över alla tjänst bindningar i en app.

```cli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-remove"></a>AZ fjäder – Cloud App binding Remove

Ta bort en tjänst bindning från appen.

```cli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --namn | Namnet på den tjänst bindning som ska tas bort. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-show"></a>AZ fjäder – Cloud App binding show

Visa information om en tjänst bindning.

```cli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --namn | Namnet på tjänst bindningen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>AZ fjäder – Cloud App binding Cosmos Add

Bind en Azure Cosmos DB med appen.

```cli
az spring-cloud app binding cosmos add --api-type
                                       --app
                                       --name -n
                                       --resource-group -g
                                       --resource-id
                                       --service -s
                                       --collection-name
                                       --database-name
                                       --key-space
```

```cli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Obligatoriska parametrar | |
| --- | ---: |
| --API-typ | Ange API-typen med något av följande värden: Cassandra, Gremlin, Mongo, SQL, Table. |
| --app | Namnet på appen. |
| --namn | Namnet på tjänst bindningen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

|Valfria parametrar | |
| --- | ---: |
| --samlings namn | Samlingens namn.  Krävs när du använder Gremlin. |
| --databas-namn | Namnet på databasen.  Krävs när du använder Mongo, SQL och Gremlin. |
| --nyckel avstånd | Cassandra nyckel utrymme.  Krävs när du använder Cassandra. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>AZ fjäder – Cloud App binding Cosmos Update

```cli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --namn | Namnet på tjänst bindningen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

|Valfria parametrar | |
| --- | ---: |
| --samlings namn | Samlingens namn.  Krävs när du använder Gremlin. |
| --databas-namn | Namnet på databasen.  Krävs när du använder Mongo, SQL och Gremlin. |
| --nyckel avstånd | Cassandra nyckel utrymme.  Krävs när du använder Cassandra. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>AZ fjäder – Cloud App binding MySQL Add

```cli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --databas-namn | Namnet på databasen. |
| --nyckel | Tjänstens API-nyckel. |
| --namn | Namnet på tjänst bindningen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --resurs-ID | Azure-resurs-ID för den tjänst som ska bindas till. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |
| --username | Användar namn för databas åtkomst. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>AZ fjäder – Cloud App binding MySQL Update

Uppdatera tjänst bindnings anslutningen till appen till en Azure Database for MySQL.

```cli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --namn | Namnet på tjänst bindningen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --databas-namn | Namnet på databasen. |
| --nyckel | Tjänstens API-nyckel. |
| --username | Användar namn för databas åtkomst. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>AZ fjäder – Cloud App binding Redis Add

Bind en Azure-cache för Redis med appen.

```cli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --namn | Namnet på tjänst bindningen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --resurs-ID | Azure-resurs-ID för den tjänst som du vill binda till. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --Disable-SSL | Inaktivera SSL. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>AZ fjäder – Cloud App binding Redis Update

Uppdatera en tjänst bindning för Azure cache för Redis.

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --namn | Namnet på tjänst bindningen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --Disable-SSL | Inaktivera SSL. |

## <a name="az-spring-cloud-app-deployment-create"></a>AZ fjäder – Cloud App Deployment Create

Skapa en mellanlagrings distribution för appen.

Om du vill distribuera kod eller uppdatera inställningarna till en befintlig distribution använder du `az spring-cloud app deploy --deployment <staging-deployment>` eller "AZ våren-Cloud App Update--Deployment <staging deployment>.

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --namn | Namnet på tjänst bindningen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --CPU | Antal virtuella processor kärnor per instans.  Standard: 1 |
| --kuvert | Blankstegsavgränsad miljövariabler i formatet nyckel [= värde]. |
| --antal instanser | Antal instanser. Standard: 1. |
| --jar-sökväg | Distribuera jar om det har tillhandahållits.  Annars distribuerar du den aktuella mappen som en tar. |
| --JVM – alternativ | En sträng som innehåller JVM-alternativ.  Använd ' = ' i stället för ' ' för att undvika gränssnitts tolknings fel. T. ex. `--jvm-options='-Xms1024m -Xmx2048m`. |
| --minne | Antal GB minne per instans. |
| --No-wait | Vänta inte tills tids krävande åtgärder har slutförts. |
| --runtime-version | Körnings version av språket som används i appen.  Tillåtna värden: `Java_11``Java_8`. |
| --Skip-Clone-Settings | Skapa en mellanlagrings distribution genom att klona de aktuella inställningarna för produktions distribution. |
| --mål-modul | Underordnad modul som ska distribueras.  Krävs när flera jar-paket skapas från käll koden. |
| --version | Distributions version.  Oförändrad om inget anges. |

### <a name="examples"></a>Exempel

Distribuera käll koden till en ny distribution av appen.  Detta packar den aktuella katalogen, skapar en binär med hjälp av Pivoted build-systemet och distribuerar sedan.

```cli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

Distribuera en fördefinierad jar-app till en app med JVM-alternativ och miljövariabler.

```cli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>ta bort AZ våren – Cloud App Deployment

Ta bort en distribution av appen.

```cli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --namn | Distributionens namn. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-list"></a>AZ fjäder – Cloud App Deployment List

Visa en lista över alla distributioner i en app.

```cli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-show"></a>AZ fjäder – Cloud App Deployment show

Visa information om en distribution.

```cli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --namn | Distributionens namn. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --tjänst-s | Namnet på Azure våren-molnet.  Du kan konfigurera standard tjänsten med hjälp av `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-config-server-clear"></a>AZ fjäder – moln konfiguration-Rensa Server

Radera alla konfigurations inställningar på konfigurations servern.

```cli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn | Namnet på Azure våren-molnet. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-set"></a>AZ fjäder – moln konfiguration – Server uppsättning

Ange konfigurations inställningar på konfigurations servern med hjälp av en YAML-fil.

```cli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Obligatoriska parametrar | |
| --- | ---: |
| --config-File | Fil Sök väg till ett YAML-manifest för konfiguration av konfigurations servern. |
| --namn | Namnet på Azure våren-molnet. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --No-wait | Utför inte länge drift åtgärder för att slutföras.

## <a name="az-spring-cloud-config-server-show"></a>AZ fjäder – Cloud config – server show

Visa konfigurations Server inställningarna.

```cli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn | Namnet på Azure våren-molnet. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-git-set"></a>AZ fjäder – Cloud config-server git-uppsättning

Ange git-egenskaperna för konfigurations servern.  Alla befintliga git-egenskaper skrivs över.

```cli
az spring-cloud config-server git set --name -n
                                      --resource-group g
                                      --uri
                                      --defer
                                      --host-key
                                      --host-key-algorithm
                                      --label
                                      --password
                                      --private-key
                                      --search-paths
                                      --strict-host-key-checking
                                      --username
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn | Namnet på Azure våren-molnet. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --URI | URI för den tillagda konfigurationen. |

| Valfria parametrar | |
| --- | ---: |
| --Överlåt | Lagra tillfälligt objektet i det lokala cacheminnet i stället för att skicka det till Azure.  Använd `az cache` för att Visa/rensa. |
| --värd nyckel | Värd nyckel för den tillagda konfigurationen. |
| --värd-nyckelalgoritm | Värd nyckelalgoritm för den tillagda konfigurationen. |
| --etikett | Etikett för den tillagda konfigurationen. |
| --lösenord | Lösen ord för den tillagda konfigurationen. |
| --privat nyckel | Privat nyckel för den tillagda konfigurationen. |
| --Sök-sökvägar | Sök sökvägar för den tillagda konfigurationen.  Använd kommatecken avgränsare för flera sökvägar. |
| --strikt-värd nyckel kontroll | Möjliggör sträng kontroll av värd nyckel för den tillagda konfigurationen. |
| --username | Användar namn för den tillagda konfigurationen. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>AZ våren – Cloud config-server git lagrings platsen Lägg till

```cli
az spring-cloud config-server git repo add --name -n
                                           --repo-name
                                           --resource-group -g
                                           --uri
                                           --defer
                                           --host-key
                                           --host-key-algorithm
                                           --label
                                           --password
                                           --pattern
                                           --private-key
                                           --search-paths
                                           --strict-host-key-checking
                                           --username
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn | Namnet på Azure våren-molnet. |
| --lagrings platsen-Name | URI för lagrings platsen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --URI | URI för den tillagda konfigurationen. |

| Valfria parametrar | |
| --- | ---: |
| --Överlåt | Lagra tillfälligt objektet i det lokala cacheminnet i stället för att skicka det till Azure.  Använd `az cache` för att Visa/rensa. |
| --värd nyckel | Värd nyckel för den tillagda konfigurationen. |
| --värd-nyckelalgoritm | Värd nyckelalgoritm för den tillagda konfigurationen. |
| --etikett | Etikett för den tillagda konfigurationen. |
| --lösenord | Lösen ord för den tillagda konfigurationen. |
| --mönster | Mönster för lagrings platsen.  Använd kommatecken avgränsare för flera sökvägar.|
| --privat nyckel | Privat nyckel för den tillagda konfigurationen. |
| --Sök-sökvägar | Sök sökvägar för den tillagda konfigurationen.  Använd kommatecken avgränsare för flera sökvägar. |
| --strikt-värd nyckel kontroll | Möjliggör sträng kontroll av värd nyckel för den tillagda konfigurationen. |
| --username | Användar namn för den tillagda konfigurationen. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>AZ fjäder – Cloud config-server git lagrings platsen-lista

Lista alla git-databaser som definierats i konfigurations servern

```cli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn | Namnet på Azure våren-molnet. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --Överlåt | Lagra tillfälligt objektet i det lokala cacheminnet i stället för att skicka det till Azure.  Använd `az cache` för att Visa/rensa. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>AZ våren – Cloud config-server git lagrings platsen Remove

Ta bort en befintlig git lagrings platsen-konfiguration från konfigurations servern.

```cli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn | Namnet på Azure våren-molnet. |
| --lagrings platsen-Name | URI för lagrings platsen. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --Överlåt | Lagra tillfälligt objektet i det lokala cacheminnet i stället för att skicka det till Azure.  Använd `az cache` för att Visa/rensa. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>AZ fjäder – moln test – slut punkts inaktive ring

Inaktivera test slut punkten för Azure våren-molnet

```cli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn | Namnet på Azure våren-molnet. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-enable"></a>AZ fjäder – moln test – slut punkts aktivering

Aktivera test slut punkten för Azure våren-molnet. 

```cli 
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn | Namnet på Azure våren-molnet. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-list"></a>AZ fjäder – Cloud test – slut punkts lista 

Visa en lista över tillgängliga test slut punkts nycklar för Azure våren-molnet.

```cli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn | Namnet på Azure våren-molnet. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --distribution-d | Namnet på en befintlig distribution av appen.  Standardvärdet för produktion om inget anges. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>AZ fjäder – moln test – slut punkts förnyelse – nyckel

Återskapa en test-Endpoint-nyckel för Azure våren-molnet.

```cli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn | Namnet på Azure våren-molnet. |
| --resurs-grupp-g | Namnet på resurs gruppen.  Du kan konfigurera standard gruppen med hjälp av `az configure --defaults group=<name>`. |
| --typ | Typ av test slut punkt nyckel.  Tillåtna värden: primär, sekundär. |
