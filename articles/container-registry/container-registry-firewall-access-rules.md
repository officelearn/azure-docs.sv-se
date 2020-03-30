---
title: Regler för åtkomst till brandvägg
description: Konfigurera regler för åtkomst till ett Azure-behållarregister bakom en brandvägg, genom att tillåta åtkomst till ("vitlistning") REST API och lagringsslutpunktsdomännamn eller tjänstspecifika IP-adressintervall.
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77168025"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Konfigurera regler för åtkomst till ett Azure-behållarregister bakom en brandvägg

I den här artikeln beskrivs hur du konfigurerar regler i brandväggen för att tillåta åtkomst till ett Azure-behållarregister. En Azure IoT Edge-enhet bakom en brandvägg eller proxyserver kan till exempel behöva komma åt ett behållarregister för att hämta en behållaravbildning. En låst server i ett lokalt nätverk kan också behöva åtkomst för att skicka en avbildning.

Om du i stället vill konfigurera regler för inkommande nätverksåtkomst på ett behållarregister endast i ett virtuellt Azure-nätverk eller från ett offentligt IP-adressintervall läser du [Begränsa åtkomsten till ett Azure-behållarregister från ett virtuellt nätverk](container-registry-vnet.md).

## <a name="about-registry-endpoints"></a>Om registerslutpunkter

Om du vill hämta eller skicka avbildningar eller andra artefakter till ett Azure-behållarregister måste en klient, till exempel en Docker-demon, interagera över HTTPS med två distinkta slutpunkter.

* **Register REST API-slutpunkt** - Autentisering och registerhantering hanteras via registrets offentliga REST API-slutpunkt. Den här slutpunkten är registrets inloggningsservernamn eller ett associerat IP-adressintervall. 

* **Lagringsslutpunkt** - Azure [allokerar bloblagring](container-registry-storage.md) i Azure Storage-konton för varje register för att hantera data för behållaravbildningar och andra artefakter. När en klient kommer åt avbildningslager i ett Azure-behållarregister görs begäranden med hjälp av en slutpunkt för lagringskonto som tillhandahålls av registret.

Om registret är [georeplikerat](container-registry-geo-replication.md)kan en klient behöva interagera med REST- och lagringsslutpunkter i en viss region eller i flera replikerade regioner.

## <a name="allow-access-to-rest-and-storage-domain-names"></a>Tillåt åtkomst till REST- och lagringsdomännamn

* **REST-slutpunkt** - Tillåt åtkomst till det fullständigt kvalificerade registrets inloggningsservernamn, t.ex.`myregistry.azurecr.io`
* **Slutpunkt för lagring (data)** – Tillåt åtkomst till alla Azure blob storage-konton med jokertecknet`*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Tillåt åtkomst per IP-adressintervall

Om din organisation har principer för att endast tillåta åtkomst till specifika IP-adresser eller adressintervall laddar du ned [Azure IP-intervall och tjänsttaggar – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519).

Om du vill hitta IP-intervallen FÖR ACR REST-slutpunkt som du behöver för att tillåta åtkomst för söker du efter **AzureContainerRegistry** i JSON-filen.

> [!IMPORTANT]
> IP-adressintervall för Azure-tjänster kan ändras och uppdateringar publiceras varje vecka. Ladda ner JSON-filen regelbundet och gör nödvändiga uppdateringar i dina åtkomstregler. Om ditt scenario innebär att konfigurera nätverkssäkerhetsgruppsregler i ett virtuellt Azure-nätverk för åtkomst till Azure Container Registry använder du [azurecontainerregistry-tjänsttaggen](#allow-access-by-service-tag) i stället. **AzureContainerRegistry**
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

### <a name="rest-ip-addresses-for-a-specific-region"></a>REST IP-adresser för en viss region

Sök efter den specifika regionen, till exempel **AzureContainerRegistry.AustraliaEast**.

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

### <a name="storage-ip-addresses-for-specific-regions"></a>Lagrings-IP-adresser för specifika regioner

Sök efter den specifika regionen, till exempel **Storage.AustraliaCentral**.

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

## <a name="allow-access-by-service-tag"></a>Tillåt åtkomst via tjänstmärke

I ett virtuellt Azure-nätverk använder du nätverkssäkerhetsregler för att filtrera trafik från en resurs, till exempel en virtuell dator till ett behållarregister. Om du vill förenkla skapandet av Azure-nätverksreglerna använder du [tjänsttaggen](../virtual-network/security-overview.md#service-tags) **AzureContainerRegistry** . Ett tjänsttag representerar en grupp IP-adressprefix för att komma åt en Azure-tjänst globalt eller per Azure-region. Taggen uppdateras automatiskt när adresserna ändras. 

Skapa till exempel en regel för utgående nätverkssäkerhetsgrupp med målet **AzureContainerRegistry** för att tillåta trafik till ett Azure-behållarregister. Om du bara vill tillåta åtkomst till tjänsttaggen i en viss region anger du regionen i följande format: **AzureContainerRegistry**. [*regionnamn*].

## <a name="configure-client-firewall-rules-for-mcr"></a>Konfigurera klientbrandväggsregler för MCR

Om du behöver komma åt MCR (Microsoft Container Registry) bakom en brandvägg läser du anvisningarna för att konfigurera [MCR-klientbrandväggsregler](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md). MCR är det primära registret för alla Microsoft-publicerade dockeravbildningar, till exempel Windows Server-avbildningar.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [azure-metodtips för nätverkssäkerhet](../security/fundamentals/network-best-practices.md)

* Läs mer om [säkerhetsgrupper](/azure/virtual-network/security-overview) i ett virtuellt Azure-nätverk



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

