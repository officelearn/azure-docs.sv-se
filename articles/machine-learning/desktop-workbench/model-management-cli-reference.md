---
title: Referens för Azure Machine Learning-modellhantering-kommandoradsgränssnittet | Microsoft Docs
description: Referens för Azure Machine Learning-modellhantering kommandoradsgränssnittet.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/08/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 6844537c512d10fccb244a18dafabe7521e697b1
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321843"
---
# <a name="model-management-command-line-interface-reference"></a>Referens för objektmodell management kommandoradsgränssnittet

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


## <a name="base-cli-concepts"></a>Grundläggande begrepp för CLI:

    account : Manage model management accounts. 
    env     : Manage compute environments.
    image   : Manage operationalization images.
    manifest: Manage operationalization manifests.
    model   : Manage operationalization models.
    service : Manage operationalized services.

## <a name="account-commands"></a>Kommandon
Ett modellhanteringskonto krävs för att använda tjänsterna, där du kan distribuera och hantera modeller. Använd `az ml account modelmanagement -h` att se listan nedan:

    create: Create a Model Management Account.
    delete: Delete a specified Model Management Account.
    list  : Gets the Model Management Accounts in the current subscriptiong.
    set   : Set the active Model Management Account.
    show  : Show a Model Management Account.
    update: Update an existing Model Management Account.

**Skapa Modellhanteringskonto**

Skapa ett modellhanteringskonto för fakturering med följande kommando:

`az ml account modelmanagement create --location [Azure region e.g. eastus2] --name [new account name] --resource-group [resource group name to store the account in]`

Lokala argument:

    --location -l       [Required]: Resource location.
    --name -n           [Required]: Name of the model management account.
    --resource-group -g [Required]: Resource group to create the model management account in.
    --description -d              : Description of the model management account.
    --sku-instances               : Number of instances of the selected SKU. Must be between 1 and
                                    16 inclusive.  Default: 1.
    --sku-name                    : SKU name. Valid names are S1|S2|S3|DevTest.  Default: S1.
    --tags -t                     : Tags for the model management account.  Default: {}.
    -v                            : Verbosity flag.

## <a name="environment-commands"></a>Miljö-kommandon

    cluster        : Switch the current execution context to 'cluster'.
    delete         : Delete an MLCRP-provisioned resource.
    get-credentials: List the keys for an environment.
    list           : List all environments in the current subscription.
    local          : Switch the current execution context to 'local'.
    set            : Set the active MLC environment.
    setup          : Sets up an MLC environment.
    show           : Show an MLC resource; if resource_group or cluster_name are not provided, shows
                     the active MLC env.

**Konfigurera miljön för distribution**

Installationskommandot kräver att du har deltagarbehörighet för prenumerationen. Om du inte har det, behöver du som minst deltagarbehörighet för resursgruppen som du distribuerar till. Om du vill göra det senare måste du ange resursgruppens namn som en del av installationskommandot med flaggan `-g`. 

Det finns två alternativ för distribution: *lokala* och *kluster*. Ange den `--cluster` (eller `-c`) flaggan gör det möjligt för distribution av kluster som etablerar ett ACS-kluster. Grundinställning syntax är följande:

`az ml env setup [-c] --location [location of environment resources] --name[name of environment]`

Det här kommandot initierar din Azure machine learning-miljö med ett lagringskonto, ACR-registret och App Insights-tjänsten som skapats i din prenumeration. Som standard initieras miljön för lokala distributioner endast (inga ACS) om ingen flagga har angetts. Om du vill skala tjänst kan du ange den `--cluster` (eller `-c`) flagga för att skapa ett ACS-kluster.

Information om kommandot:

    --location -l        [Required]: Location for environment resources; an Azure region, e.g. eastus2.
    --name -n            [Required]: Name of environment to provision.
    --acr -r                       : ARM ID of ACR to associate with this environment.
    --agent-count -z               : Number of agents to provision in the ACS cluster. Default: 3.
    --cert-cname                   : CNAME of certificate.
    --cert-pem                     : Path to .pem file with certificate bytes.
    --cluster -c                   : Flag to provision ACS cluster. Off by default; specify this to force an ACS cluster deployment.
    --key-pem                      : Path to .pem file with certificate key.
    --master-count -m              : Number of master nodes to provision in the ACS cluster. Acceptable values: 1, 3, 5. Default: 1.
    --resource-group -g            : Resource group in which to create compute resource. Is created if it does not exist.
                                     If not provided, resource group is created with 'rg' appended to 'name.'.
    --service-principal-app-id -a  : App ID of service principal to use for configuring ML compute.
    --service-principal-password -p: Password associated with service principal.
    --storage -s                   : ARM ID of storage account to associate with this environment.
    --yes -y                       : Flag to answer 'yes' to any prompts. Command fails if user is not logged in.

Global argument
```
    --debug                        : Increase logging verbosity to show all debug logs.
    --help -h                      : Show this help message and exit.
    --output -o                    : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                        : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                      : Increase logging verbosity. Use --debug for full debug logs.
```
## <a name="model-commands"></a>Modell-kommandon

    list
    register
    show

**Registrera en modell**

Kommando för att registrera modellen.

`az ml model register --model [path to model file] --name [model name]`

Information om kommandot:

    --model -m [Required]: Model to register.
    --name -n  [Required]: Name of model to register.
    --description -d     : Description of the model.
    --tag -t             : Tags for the model. Multiple tags can be specified with additional -t arguments.
    -v                   : Verbosity flag.

Global argument

    --debug              : Increase logging verbosity to show all debug logs.
    --help -h            : Show this help message and exit.
    --output -o          : Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query              : JMESPath query string. See http://jmespath.org/ for more information and
                           examples.
    --verbose            : Increase logging verbosity. Use --debug for full debug logs.

## <a name="manifest-commands"></a>Manifest-kommandon

    create: Create an Operationalization Manifest. This command has two different
            sets of required arguments, depending on if you want to use previously registered
            model/s.
    list
    show

**Skapa manifestet**

Följande kommando skapar en manifestfil för modellen. 

`az ml manifest create --manifest-name [your new manifest name] -f [path to score file] -r [runtime for the image, e.g. spark-py]`

Information om kommandot:

    --manifest-name -n [Required]: Name of the manifest to create.
    -f                 [Required]: The score file to be deployed.
    -r                 [Required]: Runtime of the web service. Valid runtimes are spark-py|python.
    --conda-file -c              : Path to Conda Environment file.
    --dependency -d              : Files and directories required by the service. Multiple
                                   dependencies can be specified with additional -d arguments.
    --manifest-description       : Description of the manifest.
    --schema-file -s             : Schema file to add to the manifest.
    -p                           : A pip requirements.txt file needed by the score file.
    -v                           : Verbosity flag.

Registrerade modellen argument

    --model-id -i                : [Required] Id of previously registered model to add to manifest.
                                   Multiple models can be specified with additional -i arguments.

Avregistrera modellen argument

    --model-file -m              : [Required] Model file to register. If used, must be combined with
                                   model name.

Global argument

    --debug                      : Increase logging verbosity to show all debug logs.
    --help -h                    : Show this help message and exit.
    --output -o                  : Output format.  Allowed values: json, jsonc, table, tsv.
                                   Default: json.
    --query                      : JMESPath query string. See http://jmespath.org/ for more
                                   information and examples.
    --verbose                    : Increase logging verbosity. Use --debug for full debug logs.


## <a name="image-commands"></a>Bildkommandon

    create: Creates a docker image with the model and its dependencies. This command has two different sets of
            required arguments, depending on if you want to use a previously created manifest.
    list
    show
    usage

**Skapa avbildning**

Du kan skapa en avbildning med alternativet för att ha skapat manifestet innan. 

`az ml image create -n [image name] --manifest-id [the manifest ID]`

Eller du kan skapa manifestet och bild med ett enda kommando. 

`az ml image create -n [image name] --model-file [model file or folder path] -f [score file, e.g. the score.py file] -r [the runtime eg.g. spark-py which is the Docker container image base]`

Information om kommandot:

    --image-name -n [Required]: The name of the image being created.
    --image-description       : Description of the image.
    --image-type              : The image type to create. Defaults to "Docker".
    -v                        : Verbosity flag.

Registrerade Manifest argument

    --manifest-id             : [Required] Id of previously registered manifest to use in image creation.

Avregistrera Manifest argument

    --conda-file -c           : Path to Conda Environment file.
    --dependency -d           : Files and directories required by the service. Multiple dependencies can
                                be specified with additional -d arguments.
    --model-file -m           : [Required] Model file to register.
    --schema-file -s          : Schema file to add to the manifest.
    -f                        : [Required] The score file to be deployed.
    -p                        : A pip requirements.txt file needed by the score file.
    -r                        : [Required] Runtime of the web service. Valid runtimes are python|spark-py.


## <a name="service-commands"></a>Tjänsten kommandon
Följande kommandon stöds för tjänsten. Använd alternativet -h parametrarna för varje kommando visas. Till exempel använda `az ml service create realtime -h` att se Skapa information om kommandot.

    create
    delete
    keys
    list
    logs
    run
    show
    update
    usage

**Skapa en tjänst**

Om du vill skapa en tjänst med en tidigare skapad avbildning, använder du följande kommando:

`az ml service create realtime --image-id [image to deploy] -n [service name]`

För att skapa en tjänst, använder manifest och avbildning med ett enda kommando du följande kommando:

`az ml service create realtime --model-file [path to model file(s)] -f [path to model scoring file, e.g. score.py] -n [service name] -r [run time included in the image, e.g. spark-py]`

Information om kommandon:

    -n                                : [Required] Webservice name.
    --autoscale-enabled               : Enable automatic scaling of service replicas based on request demand.
                                        Allowed values: true, false. False if omitted.  Default: false.
    --autoscale-max-replicas          : If autoscale is enabled - sets the maximum number of replicas.
    --autoscale-min-replicas          : If autoscale is enabled - sets the minimum number of replicas.
    --autoscale-refresh-period-seconds: If autoscale is enabled - the interval of evaluating scaling demand.
    --autoscale-target-utilization    : If autoscale is enabled - target utilization of replicas time.
    --collect-model-data              : Enable model data collection. Allowed values: true, false. False if omitted.  Default: false.
    --cpu                             : Reserved number of CPU cores per service replica (can be fraction).
    --enable-app-insights -l          : Enable app insights. Allowed values: true, false. False if omitted.  Default: false.
    --memory                          : Reserved amount of memory per service replica, in M or G. (ex. 1G, 300M).
    --replica-max-concurrent-requests : Maximum number of concurrent requests that can be routed to a service replica.
    -v                                : Verbosity flag.
    -z                                : Number of replicas for a Kubernetes service.  Default: 1.

Registrerade bild argument

    --image-id                        : [Required] Image to deploy to the service.

Avregistrera bild argument

    --conda-file -c                   : Path to Conda Environment file.
    --image-type                      : The image type to create. Defaults to "Docker".
    --model-file -m                   : [Required] The model to be deployed.
    -d                                : Files and directories required by the service. Multiple dependencies can be specified
                                        with additional -d arguments.
    -f                                : [Required] The score file to be deployed.
    -p                                : A pip requirements.txt file of package needed by the score file.
    -r                                : [Required] Runtime of the web service. Valid runtimes are python|spark-py.
    -s                                : Input and output schema of the web service.

Global argument

    --debug                           : Increase logging verbosity to show all debug logs.
    --help -h                         : Show this help message and exit.
    --output -o                       : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                           : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                         : Increase logging verbosity. Use --debug for full debug logs.


Tänk på den `-d` flagga för att bifoga beroenden: Om du skickar namnet på en katalog som inte redan paketeras (zip, tar osv.), katalogen automatiskt hämtar tar'ed och skickas tillsammans sedan automatiskt avskiljs maskinvara på andra sidan. 

Om du skickar i en katalog som redan har paketerats är katalogen behandlas som en fil och skickas med skick. Det är separata automatiskt. du förväntas hantera som i din kod.

**Hämta tjänstinformation**

Få information om tjänsten, inklusive URL: en, användning (inklusive exempeldata om ett schema har skapats).

`az ml service show realtime --name [service name]`

Information om kommandot:

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

Global argument

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

**Köra tjänsten**

`az ml service run realtime -n [service name] -d [input_data]`

Information om kommandot:

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

Global argument

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

Kommando

    az ml service run realtime

Argumenty--id -i: [krävs] tjänst-id kan bedöma mot.
-d: data som ska använda för att anropa webbtjänsten.
-v: detaljnivå flaggan.

Global argument

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.
