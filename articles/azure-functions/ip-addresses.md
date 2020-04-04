---
title: IP-adresser i Azure-funktioner
description: Lär dig hur du hittar inkommande och utgående IP-adresser för funktionsappar och vad som gör att de ändras.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: bfd2d573e0a1c78d0ef4c68be224f92e8f689f62
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656774"
---
# <a name="ip-addresses-in-azure-functions"></a>IP-adresser i Azure-funktioner

I den här artikeln beskrivs följande avsnitt som rör IP-adresser till funktionsappar:

* Så här hittar du de IP-adresser som för närvarande används av en funktionsapp.
* Vad som gör att en funktionsapps IP-adresser ändras.
* Så här begränsar du IP-adresser som kan komma åt en funktionsapp.
* Så här skaffar du dedikerade IP-adresser för en funktionsapp.

IP-adresser är associerade med funktionsappar, inte med enskilda funktioner. Inkommande HTTP-begäranden kan inte använda den inkommande IP-adressen för att anropa enskilda funktioner. De måste använda standarddomännamnet (functionappname.azurewebsites.net) eller ett anpassat domännamn.

## <a name="function-app-inbound-ip-address"></a>Funktionsapp inkommande IP-adress

Varje funktionsapp har en enda inkommande IP-adress. Så här hittar du IP-adressen:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till funktionsappen.
3. Välj **Plattformsfunktioner**.
4. Välj **Egenskaper**och den inkommande IP-adressen visas under **Virtuell IP-adress**.

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>Utgående IP-adresser för funktionsapp

Varje funktionsapp har en uppsättning tillgängliga utgående IP-adresser. Alla utgående anslutningar från en funktion, till exempel till en backend-databas, använder en av de tillgängliga utgående IP-adresserna som ursprungs-IP-adress. Du kan inte veta i förväg vilken IP-adress en viss anslutning kommer att använda. Därför måste backend-tjänsten öppna brandväggen för alla funktionsappens utgående IP-adresser.

Så här tar du reda på de utgående IP-adresser som är tillgängliga för en funktionsapp:

1. Logga in på [Azure Resource Explorer](https://resources.azure.com).
2. Välj **prenumerationer > {din prenumeration} > leverantörer > Microsoft.Web > webbplatser**.
3. Leta reda på webbplatsen med en `id` egenskap som slutar i namnet på funktionsappen på JSON-panelen.
4. Se `outboundIpAddresses` `possibleOutboundIpAddresses`och . 

Uppsättningen `outboundIpAddresses` är för närvarande tillgänglig för funktionsappen. Uppsättningen innehåller `possibleOutboundIpAddresses` IP-adresser som endast är tillgängliga om [funktionsappen skalas till andra prisnivåer](#outbound-ip-address-changes).

Ett alternativt sätt att hitta tillgängliga utgående IP-adresser är att använda [Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```
> [!NOTE]
> När en funktionsapp som körs på [förbrukningsplanen](functions-scale.md#consumption-plan) skalas kan ett nytt intervall med utgående IP-adresser tilldelas. När du kör på förbrukningsplanen kan du behöva vitlista hela datacentret.

## <a name="data-center-outbound-ip-addresses"></a>Utgående IP-adresser för datacenter

Om du behöver vitlista utgående IP-adresser som används av dina funktionsappar är ett annat alternativ att vitlista funktionsapparnas datacenter (Azure-region). Du kan [hämta en JSON-fil som visar IP-adresser för alla Azure-datacenter](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Leta sedan reda på det JSON-fragment som gäller för den region som din funktionsapp körs i.

Till exempel är detta vad Västeuropa JSON fragment kan se ut:

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 Om du vill veta mer om när filen uppdateras och när IP-adresserna ändras expanderar du avsnittet **Information** på [sidan Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>Ändringar av inkommande IP-adress

Den inkommande IP-adressen **kan** ändras när du:

- Ta bort en funktionsapp och återskapa den i en annan resursgrupp.
- Ta bort den sista funktionsappen i en resursgrupp och regionkombination och återskapa den.
- Ta bort en TLS-bindning, till exempel under [certifikatförnyelse](../app-service/configure-ssl-certificate.md#renew-certificate).

När funktionsappen körs i en [förbrukningsplan](functions-scale.md#consumption-plan)kan den inkommande IP-adressen också ändras även när du inte har vidtagit några åtgärder, till exempel de som [anges ovan](#inbound-ip-address-changes).

## <a name="outbound-ip-address-changes"></a>Ändringar av utgående IP-adress

Uppsättningen med tillgängliga utgående IP-adresser för en funktionsapp kan ändras när du:

* Vidta alla åtgärder som kan ändra den inkommande IP-adressen.
* Ändra prisnivån för appserviceplanen. Listan över alla möjliga utgående IP-adresser som appen kan använda `possibleOutboundIPAddresses` för alla prisnivåer finns i egenskapen. Se [Hitta utgående IPs](#find-outbound-ip-addresses).

När funktionsappen körs i en [förbrukningsplan](functions-scale.md#consumption-plan)kan den utgående IP-adressen också ändras även när du inte har vidtagit några åtgärder, till exempel de som [anges ovan](#inbound-ip-address-changes).

Så här tvingar du avsiktligt en utgående IP-adressändring:

1. Skala apptjänstplanen uppåt eller nedåt mellan standard- och Premium v2-prisnivåer.
2. Vänta 10 minuter.
3. Skala tillbaka till där du började.

## <a name="ip-address-restrictions"></a>IP-adressbegränsningar

Du kan konfigurera en lista över IP-adresser som du vill tillåta eller neka åtkomst till en funktionsapp. Mer information finns i [Statiska IP-begränsningar för Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Dedikerade IP-adresser

Om du behöver statiska, dedikerade IP-adresser rekommenderar vi [App Service Environments](../app-service/environment/intro.md) (den [isolerade nivån för](https://azure.microsoft.com/pricing/details/app-service/) App Service-abonnemang). Mer information finns i [IP-adresser](../app-service/environment/network-info.md#ase-ip-addresses) för App Service Environment och [Så här styr du inkommande trafik till en App Service-miljö](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

Så här tar du reda på om din funktionsapp körs i en apptjänstmiljö:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till funktionsappen.
3. Välj fliken **Översikt**.
4. App service-plannivån visas under **App Service-plan/prisnivå**. Prisnivån App Service Environment är **isolerad**.
 
Som ett alternativ kan du använda [Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

App servicemiljön `sku` `Isolated`är .

## <a name="next-steps"></a>Nästa steg

En vanlig orsak till IP-ändringar är funktionsappskalaändringar. [Läs mer om skalning av funktionsappar](functions-scale.md).
