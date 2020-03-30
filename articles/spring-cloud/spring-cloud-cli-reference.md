---
title: az våren moln
description: Hantera Azure Spring Cloud med Azure CLI
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.openlocfilehash: 33d13d2d4fa9003ef041c4c96be83a69ac595a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298783"
---
# <a name="az-spring-cloud"></a>az våren-moln

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Hantera Azure Spring Cloud med Azure CLI

>[!Note]
> Azure Spring Cloud är för närvarande i förhandsversion.  Dessa kommandon kan ändras eller tas bort i en framtida version.

| az våren-moln |  |
|------|------:|
| [az våren-moln skapa](#az-spring-cloud-create) | Skapa en Azure Spring Cloud-instans. |
| [az våren-moln ta bort](#az-spring-cloud-delete) | Ta bort en Azure Spring Cloud-instans. |
| [az vår-moln lista](#az-spring-cloud-list) | Lista alla Azure Spring Cloud-instanser i den angivna resursgruppen, annars lista prenumerations-ID: er. |
| [az våren-moln show](#az-spring-cloud-show) | Visa information för ett Azure Spring Cloud. |

| az fjäder-moln app | Kommandon för att hantera appar i Azure Spring Cloud.  |
| ---- | ----: |
| [az våren-moln app skapa](#az-spring-cloud-app-create) | Skapa en ny app med en standarddistribution i Azure Spring Cloud. |
| [az våren-moln app ta bort](#az-spring-cloud-app-delete) | Ta bort en app i Azure Spring Cloud. |
| [az fjäder-moln app distribuera](#az-spring-cloud-app-deploy) | Distribuera från källkod eller en fördefinierad binär till en app och uppdatera relaterade konfigurationer. |
| [az vår-moln app lista](#az-spring-cloud-app-list) | Lista alla appar i Azure Spring Cloud. |
| [az våren-moln app omstart](#az-spring-cloud-app-restart) | Starta om instanser av appen med standardvärden för produktionsdistribution. |
| [az vår-moln app skala](#az-spring-cloud-app-scale) | Skala en app eller dess distributioner manuellt. |
| [az fjäder-moln app set-distribution](#az-spring-cloud-app-set-deployment) | Ange produktionsdistribution av en app. |
| [az våren-moln app show](#az-spring-cloud-app-show) | Visa information om en app i Azure Spring Cloud. |
| [az fjäder-moln app visa-distribuera-logg](#az-spring-cloud-app-show-deploy-log) | Visa byggloggar för den senaste distributionen från källan. Standard för produktionsdistribution. |
| [az våren-moln app start](#az-spring-cloud-app-start) | Starta instanser av appen med standardvärden för produktionsdistribution. |
| [az fjäder-moln app stopp](#az-spring-cloud-app-stop) | Stoppa instanser av appen med hjälp av standardvärden för produktionsdistribution. |
| [az våren-moln app uppdatering](#az-spring-cloud-app-update) | Uppdatera den angivna appens konfiguration. |

| az fjäder-moln app bindning | Kommandon för att hantera bindningar med Azure Data Services.  Appen måste startas om innan dessa inställningar börjar gälla. |
| --- | ---: |
| [az fjäder-moln app bindning lista](#az-spring-cloud-app-binding-list) | Lista alla tjänstbindningar i en app. |
| [az fjäder-moln app bindning ta bort](#az-spring-cloud-app-binding-remove) | Ta bort en tjänstbindning från appen. |
| [az våren-moln app bindande show](#az-spring-cloud-app-binding-show) | Visa information om en tjänstbindning. |
| [az våren-moln app bindande kosmos lägga till](#az-spring-cloud-app-binding-cosmos-add) | Bind en Azure CosmosDB med appen. |
| [az våren-moln app bindande kosmos uppdatering](#az-spring-cloud-app-binding-cosmos-update) | Uppdatera en Azure CosmosDB-tjänstbindning. |
| [az våren-moln app bindande mysql lägga](#az-spring-cloud-app-binding-mysql-add) | Bind en Azure-databas för MySQL med appen. |
| [az våren-moln app bindande mysql uppdatering](#az-spring-cloud-app-binding-mysql-update) | Uppdatera en Azure-databas för MySQL-tjänstbindning. |
| [az spring-cloud app bindande redis lägga](#az-spring-cloud-app-binding-redis-add) | Bind en Azure-cache för Redis med appen. |
| [az våren-moln app bindande redis uppdatering](#az-spring-cloud-app-binding-redis-update) | Uppdatera en Azure-cache för Redis-tjänstbindning. |

| az fjäder-moln app distribution | Kommandon för att hantera distributionslivscykeln för en app i Azure Spring Cloud. |
| --- | ---: |
| [az våren-moln app distribution skapa](#az-spring-cloud-app-deployment-create) | Skapa en mellanlagringsdistribution för appen. |
| [az våren-moln app distribution ta bort](#az-spring-cloud-app-deployment-delete) | Ta bort en distribution av appen. |
| [az våren-moln app distributionslista](#az-spring-cloud-app-deployment-list) | Lista alla distributioner i en app. |
| [az våren-moln app distribution visa](#az-spring-cloud-app-deployment-show) | Visa information om distributionen. |

| az fjäder-moln config-server | Kommandon för att hantera Azure Spring Cloud Config Server. |
| --- | ---: |
| [az fjäder-moln config-server klar](#az-spring-cloud-config-server-clear) | Radera alla inställningar i Config-servern. |
| [az fjäder-moln config-server uppsättning](#az-spring-cloud-config-server-set) | Definiera Config Server från en YAML-fil. |
| [az våren-moln config-server show](#az-spring-cloud-config-server-show) | Visa konfigurationen av Config Server. |
| [az fjäder-moln config server git uppsättning](#az-spring-cloud-config-server-git-set) | Definiera git-egenskaper för Config-servern.  Tidigare värden skrivs över. |
| [az fjäder-moln config server git repo add](#az-spring-cloud-config-server-git-repo-add) | Lägg till en ny git-databaskonfiguration i Config-servern. |
| [az våren-moln config server git repo lista](#az-spring-cloud-config-server-git-repo-list) | Lista alla git-databaskonfigurationer för Config-servern. |
| [az fjäder-moln config server git repo ta bort](#az-spring-cloud-config-server-git-repo-remove) | Ta bort den angivna git-databasen från Config-servern. |

| az fjäder-moln test-slutpunkt | Kommandon för att hantera slutpunktstestning i Azure Spring Cloud |
| --- | ---: |
| [az fjäder-moln test-slutpunkt inaktivera](#az-spring-cloud-test-endpoint-disable) | Inaktivera testslutpunkten. |
| [az fjäder-moln test-slutpunkt aktivera](#az-spring-cloud-test-endpoint-enable) | Aktivera testslutpunkten. |
| [az fjäder-moln test-slutpunkt lista](#az-spring-cloud-test-endpoint-list) | Lista testslutpunktsnycklar. |
| [az fjäder-moln test-slutpunkt förnya-nyckel](#az-spring-cloud-test-endpoint-renew-key) | Återskapa en testslutpunktsnyckel. |

## <a name="az-spring-cloud-create"></a>az våren-moln skapa

Skapa en ny app med en standarddistribution i Azure Spring Cloud.

```azurecli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn -n | Namn för den här Azure Spring Cloud-instansen. |
| --resurs-grupp -g | Anger resursgruppen för den här appen.  Konfigurera standardgruppen med`az configure --defaults group=<name>` |

| Valfria parametrar | |
| --- | ---: |
| --plats -l | Anger serverplats för den här appen.  Hitta giltiga platser med`az account list-locations` |
| --ingen-vänta | Gör inte för tidskrävande åtgärder för att slutföra.

### <a name="examples"></a>Exempel

Skapa ett nytt Azure Spring Cloud i WestUS

```azurecli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az våren-moln ta bort

Ta bort en Azure Spring Cloud-instans.

```azurecli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn -n | Namnet på azure spring cloud-instansen som ska tas bort. |
| --resurs-grupp -g | Namn på den resursgrupp som Azure Spring Cloud tillhör. |

| Valfria parametrar | |
| --- | ---: |
| -vänta inte. | Vänta inte på att tidskrävande åtgärder ska slutföras. |

### <a name="example"></a>Exempel

Ta bort en Azure Spring Cloud-instans som kallas "MyService" från "MyResourceGroup".

```azurecli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>az vår-moln lista

Lista alla Azure Spring Cloud-instanser som är associerade med den angivna resursgruppen. Om ingen resursgrupp har angetts anger du prenumerations-ID:na.

```azurecli
az spring-cloud list --resource-group -g
```

| Obligatoriska parametrar | |
| --- | ---: |
| --resurs-grupp -g | Resursgruppens namn. |

## <a name="az-spring-cloud-show"></a>az våren-moln show

Visa information för den angivna Azure Spring Cloud-instansen.

```azurecli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn -n | Namn på Azure Spring Cloud-instansen. |
| --resurs-grupp -g | Namnet på den resursgrupp som Azure Spring Cloud-instansen tillhör.

## <a name="az-spring-cloud-app-create"></a>az våren-moln app skapa

Skapa en ny app i ett Azure Spring Cloud.

```azurecli
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
| --namn -n | Namnet på appen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --cpu | Antal virtuella kärnor per instans.  Standard: 1. |
| --enable-persistent-storage --enable-persistent-storage --enable-enable-persistent-storage | Booleskt värde.  Om värdet är true monteras en 50 GB-disk med standardsökväg. |
| --instans-antal | Antal instanser.  Standard: 1. |
| --är-offentlig | Booleskt värde.  Om värdet är true tilldelar du en allmän domän. |
| --minne | Antal GB minne per instans.  Standard: 1. |

### <a name="examples"></a>Exempel

Skapa en app med standardkonfigurationen.

```azurecli
az spring-cloud app create -n MyApp -s MyService
```

Skapa en tillgänglig app med tre instanser.  Varje instans har 3 GB minne och 2 CPU-kärnor.

```azurecli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>az våren-moln app ta bort

Tar bort en app i Azure Spring Cloud.

```azurecli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn -n | Namnet på appen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deploy"></a>az fjäder-moln app distribuera

Distribuera en app till Azure Spring Cloud från källkoden eller en fördefinierad binär och uppdatera relaterade konfigurationer.

```azurecli
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
| --namn -n | Namnet på appen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --cpu | Antal virtuella KPI-kärnor per instans. |
| --deployment -d | Namn på en befintlig appdistribution.  Standard till produktionsdistributionen om det inte anges. |
| --env | Utrymmesavgränsade miljövariabler i formatet nyckel[=värde]. |
| --instans-antal | Antal instanser. |
| --jar-path | Om det finns, distribuera burk från given sökväg. Annars distribuerar du aktuell mapp som tjära. |
| --jvm-alternativ | En sträng som innehåller JVM-alternativ.  Använd '=' i stället för ' ' ' för att undvika skaltolkarfel. T.ex. `--jvm-options='-Xms1024m -Xmx2048m` |
| --minne | Antal GB minne per instans. |
| --ingen-vänta | Vänta inte på att tidskrävande åtgärder ska slutföras. |
| --runtime-version | Runtime-version av språket som används i appen.  Tillåtna `Java_11`värden: , `Java_8`. |
| --target-module | Underordnad modul som ska distribueras.  Krävs när flera jar-paket skapas från källkoden. |
| --version | Distributionsversion.  Oförändrad om den inte är inställd. |

### <a name="examples"></a>Exempel

Distribuera källkod till en app. Detta kommer att packa den aktuella katalogen, skapa en binär med hjälp av Pivotal Build Service och sedan distribuera till appen.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService
```

Distribuera en förbyggd burk till en app med JVM-alternativ och miljövariabler.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

Distribuera källkod till en specifik distribution av en app.

```azurecli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>az vår-moln app lista

Lista alla appar i Azure Spring Cloud-instansen.

```azurecli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Obligatoriska parametrar | |
| --- | ---: |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-restart"></a>az våren-moln app omstart

Starta om instanser av appen.  Standard till produktionsdistributionen.

```azurecli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn -n | Namnet på appen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --deployment -d | Namn på den befintliga distributionen av appen.  Standard till produktionsdistributionen om det inte anges. |
| --ingen-vänta | Vänta inte på att tidskrävande åtgärder ska slutföras. |

## <a name="az-spring-cloud-app-scale"></a>az vår-moln app skala

Skala en app eller dess distributioner manuellt.

```azurecli
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
| --namn -n | Namnet på appen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --cpu | Antal virtuella CPU-kärnor per appinstans. |
| --deployment -d | Namn på den befintliga distributionen av appen.  Standard till produktionsdistributionen om det inte anges. |
| --instans-antal | Antal instanser av den här appen. |
| --minne | Antal GB minne per appinstans. |
| --ingen-vänta | Vänta inte på att långvariga åtgärder ska slutföras. |

### <a name="examples"></a>Exempel

Skala upp en app till 4 CPU-kärnor och 8 GB minne per instans.

```azurecli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Skala ut en distribution av appen till 5 instanser.

```azurecli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az fjäder-moln app set-distribution

Ange konfigurationsalternativ för produktionsdistributionen av appen.

```azurecli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| Obligatoriska parametrar | |
| --- | ---: |
| --deployment -d | Namn på en befintlig distribution av appen. |
| --namn -n | Namnet på appen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --ingen-vänta | Vänta inte på att långvariga åtgärder ska slutföras. |

### <a name="examples"></a>Exempel

Byt ut en mellanlagringsdistribution av appen till produktion.

```azurecli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>az våren-moln app show

Visa information om en app i Azure Spring Cloud.

```azurecli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn -n | Namnet på appen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az fjäder-moln app visa-distribuera-logg

Visa byggloggen för den senaste distributionen från källkoden.  Standard för produktionsmiljö.

```azurecli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn -n | Namnet på appen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --deployment -d | Namn på en befintlig distribution av appen.  Standard till produktionsmiljön. |

## <a name="az-spring-cloud-app-start"></a>az våren-moln app start

Startar instanser av appen.  Standard för produktionsmiljö.

```azurecli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn -n | Namnet på appen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --deployment -d | Namn på en befintlig distribution av appen.  Standard till produktionsmiljön. |
| --ingen-vänta | Vänta inte på att långvariga åtgärder ska slutföras. |

## <a name="az-spring-cloud-app-stop"></a>az fjäder-moln app stopp

Stoppa instanser av appen.  Standard till produktionsmiljön.

```azurecli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn -n | Namnet på appen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --deployment -d | Namn på en befintlig distribution av appen.  Standard till produktionsmiljön. |
| --ingen-vänta | Vänta inte på att långvariga åtgärder ska slutföras. |

## <a name="az-spring-cloud-app-update"></a>az våren-moln app uppdatering

Uppdatera den lagrade konfigurationen av en app.

```azurecli
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
| --namn -n | Namnet på appen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --deployment -d | Namn på en befintlig distribution av appen.  Standard till produktionsmiljön. |
| --enable-persistent-storage --enable-persistent-storage --enable-enable-persistent-storage | Boolean.  Om värdet är true monterar du en 50 GB-disk med standardsökvägen. |
| --env | Utrymmesavgränsade miljövariabler i formatet nyckel[=värde]. |
| --är-offentlig | Boolean.  Om värdet är true tilldelar du appen en allmän domän. |
| --jvm-alternativ | En sträng som innehåller JVM-alternativ.  Använd '=' i stället för ' ' ' för att undvika skaltolkarfel. T.ex. `--jvm-options='-Xms1024m -Xmx2048m` |
| --ingen-vänta | Vänta inte på att långvariga åtgärder ska slutföras. |
| --runtime-version | Runtime-version av språket som används i appen.  Tillåtna `Java_11`värden: , `Java_8`. |

### <a name="example"></a>Exempel

Lägg till en miljövariabel för appen.

```azurecli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>az fjäder-moln app bindning lista

Lista alla tjänstbindningar i en app.

```azurecli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-remove"></a>az fjäder-moln app bindning ta bort

Ta bort en tjänstbindning från appen.

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --namn | Namnet på den tjänstbindning som ska tas bort. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-show"></a>az våren-moln app bindande show

Visa information om en tjänstbindning.

```azurecli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --namn | Namnet på tjänstebindningen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az våren-moln app bindande kosmos lägga till

Bind en Azure Cosmos DB med appen.

```azurecli
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

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Obligatoriska parametrar | |
| --- | ---: |
| --api-typ | Ange API-typen med något av följande värden: cassandra, gremlin, mongo, sql, table. |
| --app | Namnet på appen. |
| --namn | Namnet på tjänstebindningen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

|Valfria parametrar | |
| --- | ---: |
| --samling-namn | Samlingens namn.  Krävs vid användning av Gremlin. |
| --databas-namn | Databasens namn.  Krävs när du använder Mongo, SQL och Gremlin. |
| --key-space --key-space --key-space -- | Cassandra nyckel-utrymme.  Krävs vid användning av Cassandra. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>az våren-moln app bindande kosmos uppdatering

```azurecli
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
| --namn | Namnet på tjänstebindningen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

|Valfria parametrar | |
| --- | ---: |
| --samling-namn | Samlingens namn.  Krävs vid användning av Gremlin. |
| --databas-namn | Databasens namn.  Krävs när du använder Mongo, SQL och Gremlin. |
| --key-space --key-space --key-space -- | Cassandra nyckel-utrymme.  Krävs vid användning av Cassandra. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>az våren-moln app bindande mysql lägga

```azurecli
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
| --databas-namn | Databasens namn. |
| --nyckel | API-nyckel för tjänsten. |
| --namn | Namnet på tjänstebindningen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --resurs-id | Azure-resurs-ID för tjänsten att binda med. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |
| --username | Användarnamn för databasåtkomst. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>az våren-moln app bindande mysql uppdatering

Uppdatera tjänstbindningsanslutningen till en Azure-databas för MySQL.

```azurecli
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
| --namn | Namnet på tjänstebindningen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --databas-namn | Databasens namn. |
| --nyckel | API-nyckel för tjänsten. |
| --username | Användarnamn för databasåtkomst. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az spring-cloud app bindande redis lägga

Bind en Azure-cache för Redis med appen.

```azurecli
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
| --namn | Namnet på tjänstebindningen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --resurs-id | Azure-resurs-ID för den tjänst som du vill binda med. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --disable-ssl | Inaktivera TLS. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>az våren-moln app bindande redis uppdatering

Uppdatera en tjänstbindning för Azure Cache för Redis.

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --namn | Namnet på tjänstebindningen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --disable-ssl | Inaktivera TLS. |

## <a name="az-spring-cloud-app-deployment-create"></a>az våren-moln app distribution skapa

Skapa en mellanlagringsdistribution för appen.

Om du vill distribuera kod eller uppdatera `az spring-cloud app deploy --deployment <staging-deployment>` inställningarna till en befintlig distribution <staging deployment>använder du eller az spring-cloud app update --deployment .

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --namn | Namnet på tjänstebindningen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --cpu | Antal virtuella CPU-kärnor per instans.  Standard: 1 |
| --env | Utrymmesavgränsade miljövariabler i formatet nyckel[=värde]. |
| --instans-antal | Antal instanser. Standard: 1. |
| --jar-path | Om det finns, distribuera burken.  Annars distribuerar du aktuell mapp som tjära. |
| --jvm-alternativ | En sträng som innehåller JVM-alternativ.  Använd '=' i stället för ' ' ' för att undvika skaltolkarfel. T.ex. `--jvm-options='-Xms1024m -Xmx2048m` |
| --minne | Antal GB minne per instans. |
| --ingen-vänta | Vänta inte på att tidskrävande åtgärder ska slutföras. |
| --runtime-version | Runtime-version av språket som används i appen.  Tillåtna `Java_11`värden: , `Java_8`. |
| --skip-clone-inställningar | Skapa en mellanlagringsdistribution genom att klona de aktuella produktionsdistributionsinställningarna. |
| --target-module | Underordnad modul som ska distribueras.  Krävs när flera jar-paket skapas från källkoden. |
| --version | Distributionsversion.  Oförändrad om den inte är inställd. |

### <a name="examples"></a>Exempel

Distribuera källkod till en ny distribution av appen.  Detta kommer att packa den aktuella katalogen, skapa en binär med hjälp av Pivotal Build System och sedan distribuera.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

Distribuera en förbyggd burk till en app med JVM-alternativ och miljövariabler.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>az våren-moln app distribution ta bort

Ta bort en distribution av appen.

```azurecli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --namn | Namnet på distributionen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-list"></a>az våren-moln app distributionslista

Lista alla distributioner i en app.

```azurecli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-show"></a>az våren-moln app distribution visa

Visa information om en distribution.

```azurecli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Obligatoriska parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --namn | Namnet på distributionen. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --service -s | Namn på Azure Spring Cloud.  Du kan konfigurera standardtjänsten med `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-config-server-clear"></a>az fjäder-moln config-server klar

Radera alla konfigurationsinställningar i Config-servern.

```azurecli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn | Namn på Azure Spring Cloud. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-set"></a>az fjäder-moln config-server uppsättning

Ange konfigurationsinställningar för Config-servern med hjälp av en YAML-fil.

```azurecli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Obligatoriska parametrar | |
| --- | ---: |
| --config-fil | Filsökväg till ett YAML-manifest för konfigurationen av Config-servern. |
| --namn | Namn på Azure Spring Cloud. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --ingen-vänta | Gör inte för tidskrävande åtgärder för att slutföra.

## <a name="az-spring-cloud-config-server-show"></a>az våren-moln config-server show

Visa config-serverinställningarna.

```azurecli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn | Namn på Azure Spring Cloud. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-git-set"></a>az fjäder-moln config-server git uppsättning

Ange git-egenskaperna för Config-servern.  Detta kommer att skriva över alla befintliga git-egenskaper.

```azurecli
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
| --namn | Namn på Azure Spring Cloud. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --uri | URI för den tillagda konfiten. |

| Valfria parametrar | |
| --- | ---: |
| --skjuta upp | Lagra objektet tillfälligt i den lokala cachen i stället för att skicka till Azure.  Används `az cache` för att visa / rensa. |
| --värd-nyckel | Värdnyckel för den tillagda konfigurationen. |
| --host-key-algoritm | Värdnyckelalgoritm för den tillagda konfigurationen. |
| --etikett | Etikett på den tillagda config. |
| --lösenord | Lösenordet för den tillagda konfigurationen. |
| --privat-nyckel | Privat nyckel till den tillagda konfiten. |
| --sök-sökvägar | Sök sökvägar för den tillagda konfigurationen.  Använd kommaavgränsare för flera sökvägar. |
| --strikt-host-key-kontroll | Aktiverar strikt värdnyckelkontroll av den tillagda konfigurationen. |
| --username | Användarnamn för den tillagda config. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>az fjäder-moln config-server git repo add

```azurecli
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
| --namn | Namn på Azure Spring Cloud. |
| --repo-namn | URI av repo. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --uri | URI för den tillagda konfiten. |

| Valfria parametrar | |
| --- | ---: |
| --skjuta upp | Lagra objektet tillfälligt i den lokala cachen i stället för att skicka till Azure.  Används `az cache` för att visa / rensa. |
| --värd-nyckel | Värdnyckel för den tillagda konfigurationen. |
| --host-key-algoritm | Värdnyckelalgoritm för den tillagda konfigurationen. |
| --etikett | Etikett på den tillagda config. |
| --lösenord | Lösenordet för den tillagda konfigurationen. |
| --mönster | Mönster för reporäntan.  Använd kommaavgränsare för flera sökvägar.|
| --privat-nyckel | Privat nyckel till den tillagda konfiten. |
| --sök-sökvägar | Sök sökvägar för den tillagda konfigurationen.  Använd kommaavgränsare för flera sökvägar. |
| --strikt-host-key-kontroll | Aktiverar strikt värdnyckelkontroll av den tillagda konfigurationen. |
| --username | Användarnamn för den tillagda config. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az fjäder-moln config-server git repo lista

Lista alla git-repos som definierats i Config-servern

```azurecli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn | Namn på Azure Spring Cloud. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --skjuta upp | Lagra objektet tillfälligt i den lokala cachen i stället för att skicka till Azure.  Används `az cache` för att visa / rensa. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az fjäder-moln config-server git repo ta bort

Ta bort en befintlig git-repokonfiguration från Config-servern.

```azurecli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn | Namn på Azure Spring Cloud. |
| --repo-namn | URI av repo. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --skjuta upp | Lagra objektet tillfälligt i den lokala cachen i stället för att skicka till Azure.  Används `az cache` för att visa / rensa. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az fjäder-moln test-slutpunkt inaktivera

Inaktivera testslutpunkten för Azure Spring Cloud

```azurecli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn | Namn på Azure Spring Cloud. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az fjäder-moln test-slutpunkt aktivera

Aktivera testslutpunkt för Azure Spring Cloud. 

```azurecli
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn | Namn på Azure Spring Cloud. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-list"></a>az fjäder-moln test-slutpunkt lista 

Lista tillgängliga testslutpunktsnycklar för Azure Spring Cloud.

```azurecli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn | Namn på Azure Spring Cloud. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |

| Valfria parametrar | |
| --- | ---: |
| --app | Namnet på appen. |
| --deployment -d | Namn på en befintlig distribution av appen.  Standard till produktion om ospecificerat. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az fjäder-moln test-slutpunkt förnya-nyckel

Återskapa en testslutpunktsnyckel för Azure Spring Cloud.

```azurecli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Obligatoriska parametrar | |
| --- | ---: |
| --namn | Namn på Azure Spring Cloud. |
| --resurs-grupp -g | Resursgruppens namn.  Du kan konfigurera standardgruppen med `az configure --defaults group=<name>`. |
| --typ | Typ av testslutpunktsnyckel.  Tillåtna värden: Primär, Sekundär. |
