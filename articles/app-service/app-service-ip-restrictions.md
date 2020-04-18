---
title: Åtkomstbegränsningar för Azure App Service
description: Lär dig hur du skyddar din app i Azure App Service genom att ange åtkomstbegränsningar.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 298555da2056bc4c16d4d7b16615604f9798b91b
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639271"
---
# <a name="azure-app-service-access-restrictions"></a>Åtkomstbegränsningar för Azure App Service

Med åtkomstbegränsningar kan du definiera en prioriterad lista för tillåt/neka som styr nätverksåtkomsten till din app. Listan kan innehålla IP-adresser eller Azure Virtual Network-undernät. När det finns en eller flera poster finns det sedan en implicit "förneka alla" som finns i slutet av listan.

Åtkomstbegränsningsfunktionen fungerar med alla värdbaserade arbetsbelastningar i App Service, inklusive; webbappar, API-appar, Linux-appar, Linux-behållarappar och funktioner.

När en begäran görs till din app utvärderas FROM-adressen mot IP-adressreglerna i listan över åtkomstbegränsningar. Om FROM-adressen finns i ett undernät som har konfigurerats med tjänstslutpunkter till Microsoft.Web jämförs källundernätet mot de virtuella nätverksreglerna i listan över åtkomstbegränsningar. Om adressen inte tillåts åtkomst baserat på reglerna i listan svarar tjänsten med en [HTTP 403-statuskod.](https://en.wikipedia.org/wiki/HTTP_403)

Åtkomstbegränsningsfunktionen implementeras i apptjänstens frontend-roller, som är uppströms till arbetarvärdarna där koden körs. Därför är åtkomstbegränsningar effektivt nätverks-ACL:er.

Möjligheten att begränsa åtkomsten till din webbapp från ett virtuellt Azure-nätverk (VNet) kallas [tjänstslutpunkter][serviceendpoints]. Med tjänstslutpunkter kan du begränsa åtkomsten till en tjänst med flera innehavare från valda undernät. Den måste vara aktiverad både på nätverkssidan och den tjänst som den aktiveras med. Det fungerar inte att begränsa trafiken till appar som finns i en App Service-miljö. Om du befinner dig i en apptjänstmiljö kan du styra åtkomsten till din app med IP-adressregler.

![flödet för åtkomstbegränsningar](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Lägga till och redigera åtkomstbegränsningsregler i portalen ##

Om du vill lägga till en regel för åtkomstbegränsning i appen använder du menyn för att öppna begränsningar för>**nätverksåtkomst** och klickar på **Konfigurera åtkomstbegränsningar** **Network**

![Alternativ för App Service-nätverk](media/app-service-ip-restrictions/access-restrictions.png)  

I användargränssnittet för åtkomstbegränsningar kan du granska listan över åtkomstbegränsningsregler som definierats för din app.

![begränsningar för liståtkomst](media/app-service-ip-restrictions/access-restrictions-browse.png)

Listan visar alla aktuella begränsningar som finns i din app. Om du har en VNet-begränsning för din app visas tabellen om tjänstslutpunkter är aktiverade för Microsoft.Web. När det inte finns några definierade begränsningar för din app är appen tillgänglig var som helst.  

## <a name="adding-ip-address-rules"></a>Lägga till IP-adressregler

Du kan klicka på **[+] Lägg till regel** för att lägga till en ny regel för åtkomstbegränsning. När du lägger till en regel träder den i kraft omedelbart. Regler tillämpas i prioritetsordning från det lägsta antalet och går upp. Det finns en implicit förneka allt som är i kraft när du lägger till ens en enda regel.

När du skapar en regel måste du välja tillåt/neka och även typen av regel. Du måste också ange prioritetsvärdet och vad du begränsar åtkomsten till.  Du kan också lägga till ett namn och en beskrivning i regeln.  

![lägga till en IP-åtkomstbegränsningsregel](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Om du vill ange en IP-adressbaserad regel väljer du en typ av IPv4 eller IPv6. IP-adress notation måste anges i CIDR-notation för både IPv4- och IPv6-adresser. Om du vill ange en exakt adress kan du använda ungefär 1.2.3.4/32 där de första fyra oktetterna representerar din IP-adress och /32 är masken. IPv4 CIDR-notationen för alla adresser är 0.0.0.0/0. Om du vill veta mer om CIDR-notation kan du läsa [Klasslös routning mellan domäner](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="service-endpoints"></a>Tjänstslutpunkter

Med tjänstslutpunkter kan du begränsa åtkomsten till valda Azure-undernät för virtuella nätverk. Om du vill begränsa åtkomsten till ett visst undernät skapar du en begränsningsregel med en typ av virtuellt nätverk. Du kan välja prenumeration, VNet och undernät som du vill tillåta eller neka åtkomst med. Om tjänstslutpunkter inte redan är aktiverade med Microsoft.Web för det undernät som du har markerat aktiveras det automatiskt för dig om du inte markerar rutan och ber om att inte göra det. Situationen där du vill aktivera den i appen men inte undernätet är till stor del relaterad till om du har behörighet att aktivera tjänstslutpunkter i undernätet eller inte. Om du behöver skaffa någon annan för att aktivera tjänstslutpunkter i undernätet kan du markera rutan och konfigurera appen för tjänstslutpunkter i väntan på att den aktiveras senare i undernätet. 

![lägga till en regel för begränsning av VNet-åtkomst](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Tjänstslutpunkter kan inte användas för att begränsa åtkomsten till appar som körs i en App Service-miljö. När appen är i en apptjänstmiljö kan du styra åtkomsten till din app med IP-åtkomstregler. 

Med tjänstslutpunkter kan du konfigurera appen med Application Gateways eller andra WAF-enheter. Du kan också konfigurera program på flera nivåer med säkra serverdelar. Mer information om några av möjligheterna finns i [Nätverksfunktioner och App Service-](networking-features.md) och [Application Gateway-integrering med tjänstslutpunkter](networking/app-gateway-with-service-endpoints.md).

## <a name="managing-access-restriction-rules"></a>Hantera regler för åtkomstbegränsning

Du kan klicka på valfri rad för att redigera en befintlig regel för åtkomstbegränsning. Redigeringar träder i kraft omedelbart, inklusive ändringar i prioritetsordning.

![redigera en regel för åtkomstbegränsning](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

När du redigerar en regel kan du inte ändra typen mellan en IP-adressregel och en regel för virtuellt nätverk. 

![redigera en regel för åtkomstbegränsning](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Om du vill ta bort en regel klickar du på **...** på regeln och sedan på **Ta bort**.

![regel för borttagning av åtkomstbegränsning](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Blockera en enda IP-adress ##

När du lägger till din första IP-begränsningsregel lägger tjänsten till en explicit regel **neka alla** med prioriteten 2147483647. I praktiken kommer regeln **Neka alla** att köras senast och blockera åtkomst till alla IP-adresser som inte uttryckligen tillåts med hjälp av en **Tillåt-regel.**

I scenariot där användare uttryckligen vill blockera en enda IP-adress eller IP-adressblock, men tillåta allt annat åtkomst, är det nödvändigt att lägga till en explicit **Tillåt alla-regel.**

![blockera en enda IP-adress](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>SCM-webbplats 

Förutom att kunna styra åtkomsten till din app kan du också begränsa åtkomsten till scm-webbplatsen som används av appen. SCM-webbplatsen är slutpunkten för webbut distribuera och även Kudu-konsolen. Du kan separat tilldela åtkomstbegränsningar till scm-webbplatsen från appen eller använda samma uppsättning för både appen och scm-webbplatsen. När du markerar rutan för att ha samma begränsningar som din app är allt utelämnat. Om du avmarkerar rutan tillämpas de inställningar du hade tidigare på scm-webbplatsen. 

![begränsningar för liståtkomst](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Programmatisk manipulering av regler för tillträdesbegränsning ##

[Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/access-restriction?view=azure-cli-latest) och [Azure PowerShell](https://docs.microsoft.com/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0) har stöd för redigering av åtkomstbegränsningar. Exempel på att lägga till en åtkomstbegränsning med Azure CLI:

```azurecli-interactive
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```
Exempel på hur du lägger till en åtkomstbegränsning med Azure PowerShell:

```azurepowershell-interactive
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

Värden kan också ställas in manuellt med en [Azure REST API](https://docs.microsoft.com/rest/api/azure/) PUT-åtgärd på appkonfigurationen i Resource Manager eller med hjälp av en Azure Resource Manager-mall. Som ett exempel kan du använda resources.azure.com och redigera ipSecurityRestrictions-blocket för att lägga till den nödvändiga JSON.

Platsen för den här informationen i Resource Manager är:

management.azure.com/subscriptions/**prenumerations-ID**/resourceGroups/ resursgrupper/providers/Microsoft.Web/sites/**webbappnamn**/config/web?api-version=2018-02-01**resource groups**

JSON-syntaxen för det tidigare exemplet är:
```json
{
  "properties": {
    "ipSecurityRestrictions": [
      {
        "ipAddress": "122.133.144.0/24",
        "action": "Allow",
        "priority": 100,
        "name": "IP example rule"
      }
    ]
  }
}
```

## <a name="azure-functions-access-restrictions"></a>Åtkomstbegränsningar för Azure Functions

Åtkomstbegränsningar är också tillgängliga för funktionsappar med samma funktioner som App Service-abonnemang. Om du aktiverar åtkomstbegränsningar inaktiveras portalkodredigeraren för otillåtna IP-adresser.

## <a name="next-steps"></a>Nästa steg
[Åtkomstbegränsningar för Azure-funktioner](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)

[Integrering av programgateway med tjänstslutpunkter](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
