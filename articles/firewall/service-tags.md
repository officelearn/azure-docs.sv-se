---
title: Översikt över brandväggen för Azure-tjänsttaggar
description: Den här artikeln är en översikt av de Azure-brandvägg.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 2/25/2019
ms.author: victorh
ms.openlocfilehash: 1d03d896de947fcc938619c52a3690962a0d2d6c
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805628"
---
# <a name="azure-firewall-service-tags"></a>Azure-tjänsttaggar för brandvägg

En tjänsttagg representerar en grupp IP-adressprefix och används i syfte att minska komplexiteten vid skapande av säkerhetsregler. Du kan inte skapa egna tjänsttaggar, och du kan inte heller ange vilka IP-adresser som ska finnas i en tagg. Microsoft hanterar adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

Azure-tjänsttaggar för brandväggen kan användas i fältet mål nätverk. Du kan använda dem i stället för specifika IP-adresser.

## <a name="supported-service-tags"></a>Tjänsttaggar som stöds

Följande tjänsttaggar är tillgängliga för användning i Azure brandväggsregler för nätverk:

* **AzureCloud** (endast Resource Manager): Den här taggen anger IP-adressutrymmet för Azure, inklusive alla [offentliga IP-adresser för datacentret](https://www.microsoft.com/download/details.aspx?id=41653). Om du anger *AzureCloud* som värde tillåts eller nekas trafik till offentliga Azure IP-adresser. Om du bara vill tillåta åtkomst till AzureCloud i en viss [region](https://azure.microsoft.com/regions) kan du ange regionen. Om du till exempel endast vill tillåta åtkomst till Azure AzureCloud i regionen USA, östra så anger *AzureCloud.EastUS* som en tjänsttagg. 
* **AzureTrafficManager** (endast Resource Manager): Den här taggen anger IP-adressutrymmet för IP-adresserna för avsökning i Azure Traffic Manager. Mer information om IP-adresser för avsökning i Traffic Manager finns i [Vanliga frågor och svar om Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). 
* **Storage** (endast Resource Manager): Den här taggen anger IP-adressutrymmet för tjänsten Azure Storage. Om du anger *Storage* som värde tillåts eller nekas trafik till lagringen. Om du bara vill tillåta åtkomst till lagring i en viss [region](https://azure.microsoft.com/regions) anger du regionen. Om du till exempel bara vill tillåta åtkomst till Azure Storage i regionen östra USA kan du ange *Storage.EastUS* som tjänsttagg. Taggen representerar tjänsten, men inte specifika instanser av tjänsten. Taggen kan till exempel representera tjänsten Azure Storage, men inte ett specifikt Azure Storage-konto.
* **Sql** (endast Resource Manager): Den här taggen anger adressprefix för tjänsterna Azure SQL Database och Azure SQL Data Warehouse. Om du anger *Sql* som värde tillåts eller nekas trafik till Sql. Om du bara vill tillåta åtkomst till Sql i en viss [region](https://azure.microsoft.com/regions) anger du regionen. Om du till exempel vill tillåta åtkomst endast till Azure SQL Database i regionen östra USA anger du *Sql.EastUS* som tjänsttagg. Taggen representerar tjänsten, men inte specifika instanser av tjänsten. Taggen kan till exempel representera tjänsten Azure SQL Database, men inte en specifik SQL-databas eller -server.
* **AzureCosmosDB** (endast Resource Manager): Den här taggen anger adressprefix för tjänsten Azure Cosmos Database. Om du anger *Azure Cosmos DB* som värde tillåts eller nekas trafik till Azure Cosmos DB. Om du bara vill tillåta åtkomst till AzureCosmosDB i en viss [region](https://azure.microsoft.com/regions), du kan ange den region som är i formatet AzureCosmosDB. [ Regionsnamn].
* **AzureKeyVault** (endast Resource Manager): Den här taggen anger adressprefix för tjänsten Azure KeyVault. Om du anger *AzureKeyVault* som värde tillåts eller nekas trafik till AzureKeyVault. Om du bara vill tillåta åtkomst till AzureKeyVault i en viss [region](https://azure.microsoft.com/regions), du kan ange den region som är i formatet AzureKeyVault. [ Regionsnamn].
* **EventHub** (endast Resource Manager): Den här taggen anger adressprefix för tjänsten Azure EventHub. Om du anger *EventHub* som värde tillåts eller nekas trafik till EventHub. Om du bara vill tillåta åtkomst till EventHub i en viss [region](https://azure.microsoft.com/regions), du kan ange den region som är i formatet EventHub. [ Regionsnamn]. 
* **ServiceBus** (endast Resource Manager): Den här taggen anger adressprefix för tjänsten Azure ServiceBus. Om du anger *ServiceBus* som värde tillåts eller nekas trafik till ServiceBus. Om du bara vill tillåta åtkomst till ServiceBus i en viss [region](https://azure.microsoft.com/regions), du kan ange den region som är i formatet ServiceBus. [ Regionsnamn].
* **MicrosoftContainerRegistry** (endast Resource Manager): Den här taggen anger adressprefix för tjänsten Microsoft Container Registry. Om du anger *MicrosoftContainerRegistry* som värde så tillåts eller nekas trafik till MicrosoftContainerRegistry. Om du bara vill tillåta åtkomst till MicrosoftContainerRegistry i en viss [region](https://azure.microsoft.com/regions), du kan ange den region som är i formatet MicrosoftContainerRegistry. [ Regionsnamn].
* **AzureContainerRegistry** (endast Resource Manager): Den här taggen anger adressprefix för tjänsten Azure Container Registry. Om du anger *AzureContainerRegistry* som värde så tillåts eller nekas trafik till AzureContainerRegistry. Om du bara vill tillåta åtkomst till AzureContainerRegistry i en viss [region](https://azure.microsoft.com/regions), du kan ange den region som är i formatet AzureContainerRegistry. [ Regionsnamn]. 
* **AppService** (endast Resource Manager): Den här taggen anger adressprefix för tjänsten Azure AppService. Om du anger *AppService* som värde tillåts eller nekas trafik till AppService. Om du bara vill tillåta åtkomst till App Service i en specifik [region](https://azure.microsoft.com/regions), du kan ange den region som är i formatet AppService. [ Regionsnamn]. 
* **AppServiceManagement** (endast Resource Manager): Den här taggen anger adressprefix för tjänsten Azure AppService Management. Om du anger *AppServiceManagement* för värdet så tillåts eller nekas trafik till AppServiceManagement. 
* **ApiManagement** (endast Resource Manager): Den här taggen anger adressprefix för Azure API Management-tjänsten. Om du anger *ApiManagement* för värdet så tillåts eller nekas trafik till ApiManagement.  
* **AzureConnectors** (endast Resource Manager): Den här taggen anger adressprefix för tjänsten Azure Connectors. Om du anger *AzureConnectors* som värde tillåts eller nekas trafik till AzureConnectors. Om du bara vill tillåta åtkomst till AzureConnectors i en viss [region](https://azure.microsoft.com/regions), du kan ange den region som är i formatet AzureConnectors. [ Regionsnamn].
* **AzureDataLake** (endast Resource Manager): Den här taggen anger adressprefix för tjänsten Azure Data Lake. Om du anger *AzureDataLake* som värde tillåts eller nekas trafik till AzureDataLake.
* **AzureActiveDirectory** (endast Resource Manager): Den här taggen anger adressprefix för tjänsten Azure Active Directory. Om du anger *AzureActiveDirectory* som värde tillåts eller nekas trafik till AzureActiveDirectory.
* **AzureMonitor** (endast Resource Manager): Den här taggen anger adressprefix för tjänsten AzureMonitor. Om du anger *AzureMonitor* för, trafik tillåts eller nekas till AzureMonitor.
* **Service fabric** (endast Resource Manager): Den här taggen anger adressprefix för Service fabric-tjänsten. Om du anger *ServiceFabric* för, trafik tillåts eller nekas åtkomst till Service fabric.
* **AzureMachineLearning** (endast Resource Manager): Den här taggen anger adressprefix för tjänsten AzureMachineLearning. Om du anger *AzureMachineLearning* för, trafik tillåts eller nekas till AzureMachineLearning.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure-brandväggsregler i [Azure brandväggsregel bearbetningslogiken](rule-processing.md).