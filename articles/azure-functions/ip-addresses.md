---
title: IP-adresser i Azure Functions
description: Lär dig hur du hittar inkommande och utgående IP-adresser för funktionsappar och vad som orsakar användarna att ändra.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: glenga
ms.openlocfilehash: 83e5a15d8a7f9c01f6a180ebceb715600b8a39db
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849487"
---
# <a name="ip-addresses-in-azure-functions"></a>IP-adresser i Azure Functions

Den här artikeln förklaras i följande avsnitt relaterade till IP-adresser för funktionsappar:

* Så här att hitta IP-adresser som för närvarande används av en funktionsapp.
* Vad som orsakar en funktion appens IP-adresser som ändras.
* Hur du begränsar de IP-adresser som har åtkomst till en funktionsapp.
* Så här att hämta dedikerade IP-adresser för en funktionsapp.

IP-adresser som är associerade med funktionsappar, inte enskilda funktioner. Inkommande HTTP-begäranden kan inte använda den inkommande IP-adressen för att anropa enskilda funktioner. de måste använda standarddomännamnet (functionappname.azurewebsites.net) eller ett anpassat domännamn.

## <a name="function-app-inbound-ip-address"></a>Funktionsapp inkommande IP-adress

Varje funktionsapp har en inkommande IP-adress. Hitta IP-adress:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Gå till funktionsappen.
3. Välj **plattformsfunktioner**.
4. Välj **egenskaper**, och den inkommande IP-adressen visas under **virtuell IP-adress**.

## <a name="find-outbound-ip-addresses"></a>Funktionen app utgående IP-adresser

Varje funktionsapp har en uppsättning tillgängliga utgående IP-adresser. Alla utgående anslutning från en funktion, till exempel en backend-databas, använder en av de tillgängliga utgående IP-adresserna som ursprunglig IP-adress. Du kan inte vet i förväg vilka IP-adressen en viss anslutning kommer att använda. Därför måste din serverdelstjänst öppna dess brandväggen till att alla function-appens utgående IP-adresser.

Hitta de utgående IP-adresserna till en funktionsapp:

1. Logga in på den [Azure Resource Explorer](https://resources.azure.com).
2. Välj **prenumerationer > {din prenumeration} > providers > Microsoft.Web > platser**.
3. På panelen JSON hitta platsen med en `id` egenskapen som slutar namnet på din funktionsapp.
4. Se `outboundIpAddresses` och `possibleOutboundIpAddresses`. 

Uppsättningen `outboundIpAddresses` är nu tillgänglig för funktionsappen. Uppsättningen `possibleOutboundIpAddresses` innehåller IP-adresser som är tillgängliga bara om funktionsappen [kan skalas till andra prisnivåerna](#outbound-ip-address-changes).

Ett annat sätt att hitta tillgängliga utgående IP-adresser är med hjälp av den [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```
> [!NOTE]
> När en funktionsapp som körs på den [förbrukningsplan](functions-scale.md#consumption-plan) är skalas, en ny mängd utgående IP-adresser kan tilldelas. När du kör på förbrukningsplanen kan behöva du godkänna hela datacentret.

## <a name="data-center-outbound-ip-addresses"></a>Utgående IP-adresser i datacentret

Om du vill lista över tillåtna utgående IP-adresser som används av dina funktionsappar, ett annat alternativ är att vitlista i funktionsappar datacenter (Azure-region). Du kan [hämta en JSON-fil som visar en lista över IP-adresser för alla Azure-Datacenter](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Hitta den JSON-fragment som gäller för den region som funktionsappen som körs i.

Det här är till exempel hur Västeuropa JSON-fragment kan se ut:

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

 Information om när den här filen uppdateras och när IP-adresserna ändras, expandera den **information** delen av den [Download Center-sidan](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

## <a name="inbound-ip-address-changes"></a>Inkommande IP-adress ändras

Inkommande IP-adressen **kan** ändra när du:

- Ta bort en funktionsapp och återskapar den i en annan resursgrupp.
- Ta bort den sista funktionsappen i en kombination av resurs grupp och region och skapa den igen.
- Ta bort en SSL-bindning som under [förnyelse av certifikat](../app-service/app-service-web-tutorial-custom-ssl.md#renew-certificates)).

När appen körs i en [förbrukningsplan](functions-scale.md#consumption-plan), inkommande IP-adressen ändras också när du inte har vidtagit några åtgärder som som visas.

## <a name="outbound-ip-address-changes"></a>Utgående IP-adress ändras

Uppsättningen med tillgängliga utgående IP-adresser för en funktionsapp kan ändras när du:

* Vidta några åtgärder som kan ändra den inkommande IP-adressen.
* Ändra prisnivån App Service-planen. Lista över alla möjliga utgående IP-adresser din app kan använda för alla prisnivåer finns i den `possibleOutboundIPAddresses` egenskapen. Se [hitta utgående IP-adresser](#find-outbound-ip-addresses).

När appen körs i en [förbrukningsplan](functions-scale.md#consumption-plan), utgående IP-adressen ändras också när du inte har vidtagit några åtgärder som som visas.

Att tvinga en utgående IP-adressändring:

1. Skala din App Service-plan upp eller ned mellan Standard och Premium v2-prisnivåer.
2. Vänta i 10 minuter.
3. Skala tillbaka till där du startade.

## <a name="ip-address-restrictions"></a>IP-adressbegränsningar

Du kan konfigurera en lista över IP-adresser som du vill tillåta eller neka åtkomst till en funktionsapp. Mer information finns i [Azure App Service statiska IP-begränsningar](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Särskilda IP-adresser

Om du behöver statiska IP-adresser, rekommenderar vi [App Service-miljöer](../app-service/environment/intro.md) (den [isolerade nivån](https://azure.microsoft.com/pricing/details/app-service/) App Service-planer). Mer information finns i [App Service Environment IP-adresser](../app-service/environment/network-info.md#ase-ip-addresses) och [hur du kontrollerar inkommande trafik till en App Service Environment](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

Att ta reda på om din funktionsapp körs i en App Service Environment:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Gå till funktionsappen.
3. Välj den **översikt** fliken.
4. App Service-plannivån visas **App Service plan/prisnivå**. App Service Environment prisnivån är **isolerad**.
 
Alternativt kan du använda den [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

App Service Environment `sku` är `Isolated`.

## <a name="next-steps"></a>Nästa steg

En vanlig orsak till IP-ändringar är funktionen appändringar. [Mer information om funktionen app skalning](functions-scale.md).
