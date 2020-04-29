---
title: Åtkomst regler för brand vägg
description: Konfigurera regler för åtkomst till ett Azure Container Registry från bakom en brand vägg genom att tillåta åtkomst till ("vit listning") REST API-och lagrings slut punkts domän namn eller tjänstspecifika IP-adressintervall.
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77168025"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Konfigurera regler för åtkomst till ett Azure Container Registry bakom en brand vägg

Den här artikeln förklarar hur du konfigurerar regler i brand väggen för att tillåta åtkomst till ett Azure Container Registry. Till exempel kan en Azure IoT Edge enhet bakom en brand vägg eller proxyserver behöva komma åt ett behållar register för att hämta en behållar avbildning. Eller så kan en låst server i ett lokalt nätverk behöva åtkomst för att skicka en avbildning.

Om du i stället vill konfigurera regler för inkommande nätverks åtkomst i ett behållar register endast inom ett virtuellt Azure-nätverk eller från ett offentligt IP-adressintervall, se [begränsa åtkomsten till ett Azure Container Registry från ett virtuellt nätverk](container-registry-vnet.md).

## <a name="about-registry-endpoints"></a>Om register slut punkter

För att hämta eller skicka avbildningar eller andra artefakter till ett Azure Container Registry måste en klient, till exempel en Docker-daemon, interagera över HTTPS med två distinkta slut punkter.

* **Registrets REST API slut punkt** – autentiserings-och register hanterings åtgärder hanteras via registrets offentliga REST API-slutpunkt. Den här slut punkten är inloggnings Server namnet för registret eller ett associerat IP-adressintervall. 

* **Lagrings slut punkt** – Azure [allokerar blob Storage](container-registry-storage.md) i Azure Storage konton för varje registers räkning för att hantera data för behållar avbildningar och andra artefakter. När en klient ansluter till avbildnings lager i ett Azure Container Registry, begär det begär Anden med hjälp av en slut punkt för lagrings kontot som tillhandahålls av registret.

Om ditt register är [geo-replikerat](container-registry-geo-replication.md)kan en klient behöva INTERAGERA med rest-och lagrings slut punkter i en angiven region eller i flera replikerade regioner.

## <a name="allow-access-to-rest-and-storage-domain-names"></a>Tillåt åtkomst till REST-och lagrings domän namn

* **REST-slutpunkt** – Tillåt åtkomst till det fullständigt kvalificerade registrets inloggnings Server namn, till exempel`myregistry.azurecr.io`
* **Lagring (data) slut punkt** – Tillåt åtkomst till alla Azure Blob Storage-konton med jokertecknet`*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Tillåt åtkomst med IP-adressintervall

Om din organisation har principer som endast tillåter åtkomst till vissa IP-adresser eller adress intervall, laddar du ned [Azure IP-intervall och service märken – offentligt moln](https://www.microsoft.com/download/details.aspx?id=56519).

Du hittar IP-intervall för ACR REST-slutpunkt som du måste tillåta åtkomst genom att söka efter **AzureContainerRegistry** i JSON-filen.

> [!IMPORTANT]
> IP-adressintervall för Azure-tjänster kan ändras och uppdateringar publiceras varje vecka. Ladda ned JSON-filen regelbundet och gör nödvändiga uppdateringar i dina åtkomst regler. Om ditt scenario innefattar konfigurering av regler för nätverks säkerhets grupper i ett virtuellt Azure-nätverk för att få åtkomst till Azure Container Registry, använder du **AzureContainerRegistry** [-tjänst tag gen](#allow-access-by-service-tag) i stället.
>

### <a name="rest-ip-addresses-for-all-regions"></a>REST IP-adresser för alla regioner

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>REST IP-adresser för en angiven region

Sök efter den angivna regionen, till exempel **AzureContainerRegistry. AustraliaEast**.

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>Lagrings-IP-adresser för alla regioner

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>Lagrings-IP-adresser för vissa regioner

Sök efter den angivna regionen, till exempel **Storage. AustraliaCentral**.

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>Tillåt åtkomst per service tag-tagg

I ett virtuellt Azure-nätverk använder du nätverks säkerhets regler för att filtrera trafik från en resurs, till exempel en virtuell dator i ett behållar register. För att förenkla skapandet av reglerna för Azure-nätverk använder du **AzureContainerRegistry** [service tag](../virtual-network/security-overview.md#service-tags). En service-tagg representerar en grupp IP-adressprefix för att få åtkomst till en Azure-tjänst globalt eller per Azure-region. Taggen uppdateras automatiskt när adresserna ändras. 

Du kan till exempel skapa en regel för utgående nätverks säkerhets grupp med mål- **AzureContainerRegistry** för att tillåta trafik till ett Azure Container Registry. Om du bara vill tillåta åtkomst till tjänst tag gen i en angiven region anger du regionen i följande format: **AzureContainerRegistry**. [*regions namn*].

## <a name="configure-client-firewall-rules-for-mcr"></a>Konfigurera brand Väggs regler för MCR

Om du behöver åtkomst till Microsoft Container Registry (MCR) bakom en brand vägg kan du läsa mer i rikt linjerna för att konfigurera [brand Väggs regler för MCR](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md). MCR är det primära registret för alla Microsoft-publicerade Docker-avbildningar, till exempel Windows Server-avbildningar.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Azures metod tips för nätverks säkerhet](../security/fundamentals/network-best-practices.md)

* Lär dig mer om [säkerhets grupper](/azure/virtual-network/security-overview) i ett virtuellt Azure-nätverk



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

