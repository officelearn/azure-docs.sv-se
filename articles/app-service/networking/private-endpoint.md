---
title: Anslut privat till en webbapp med hjälp av privat Azure-slutpunkt
description: Anslut privat till en webbapp med hjälp av privat Azure-slutpunkt
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 05/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 6a95c021153a458a4e3f804e64724b73ea1f1937
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198823"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Använda privata slut punkter för Azure Web App (för hands version)

> [!Note]
> Förhands granskningen är tillgänglig i regionerna USA och USA, västra 2, för alla PremiumV2 Windows-och Linux-Web Apps och elastiska Premium-funktioner. 

Du kan använda privat slut punkt för din Azure-webbapp för att tillåta klienter som finns i ditt privata nätverk att säkert komma åt appen via privat länk. Den privata slut punkten använder en IP-adress från ditt Azure VNet-adressutrymme. Nätverks trafik mellan en klient i ditt privata nätverk och webbappen går igenom VNet och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet.

Med hjälp av privat slut punkt för din webbapp kan du:

- Skydda din webbapp genom att konfigurera den privata slut punkten och eliminera offentlig exponering.
- Anslut säkert till en webbapp från lokala nätverk som ansluter till VNet med en VPN-eller ExpressRoute-peering.

Om du bara behöver en säker anslutning mellan ditt VNet och din webbapp är en tjänst slut punkt den enklaste lösningen. Om du också behöver komma åt webbappen från lokala platser via en Azure-Gateway, ett regionalt, interaktivt VNet eller ett globalt peer-baserat VNet är den privata slut punkten lösningen.  

Mer information finns i [tjänst slut punkter][serviceendpoint].

## <a name="conceptual-overview"></a>Konceptuell översikt

En privat slut punkt är ett särskilt nätverks gränssnitt (NIC) för din Azure-webbapp i ett undernät i din Virtual Network (VNet).
När du skapar en privat slut punkt för din webbapp ger den säker anslutning mellan klienter i ditt privata nätverk och din webbapp. Den privata slut punkten tilldelas en IP-adress från det virtuella nätverkets IP-adressintervall.
Anslutningen mellan den privata slut punkten och webb programmet använder en säker [privat länk][privatelink]. Privat slut punkt används endast för inkommande flöden till din webbapp. Utgående flöden använder inte den här privata slut punkten, men du kan mata in utgående flöden till nätverket i ett annat undernät med [funktionen för VNet-integrering][vnetintegrationfeature].

Under nätet där du ansluter den privata slut punkten kan ha andra resurser, du behöver inte ett dedikerat tomt undernät.
Du kan också distribuera den privata slut punkten i en annan region än webbappen. 

> [!Note]
>Funktionen VNet-integrering kan inte använda samma undernät än privat slut punkt, detta är en begränsning för funktionen VNet-integrering.

Från ett säkerhets perspektiv:

- När du aktiverar privata slut punkter till din webbapp inaktiverar du all offentlig åtkomst.
- Du kan aktivera flera privata slut punkter i andra virtuella nätverk och undernät, inklusive virtuella nätverk i andra regioner.
- IP-adressen för det privata slut punkts NÄTVERKSKORTet måste vara dynamisk, men den är densamma tills du tar bort den privata slut punkten.
- NÄTVERKSKORTet för den privata slut punkten kan inte ha en associerad NSG.
- Under nätet som är värd för den privata slut punkten kan ha en NSG associerad, men du måste inaktivera principerna för nätverks principer för den privata slut punkten: se [inaktivera nätverks principer för privata slut punkter][disablesecuritype]. Därför kan du inte filtrera efter NSG åtkomst till din privata slut punkt.
- När du aktiverar privat slut punkt till din webbapp utvärderas inte konfigurationen av [åtkomst begränsningar][accessrestrictions] för webbappen.
- Du kan minska risken för data exfiltrering från VNet genom att ta bort alla NSG-regler där målet är tagga Internet eller Azure-tjänster. Men om du lägger till en privat slut punkt för webb program i ditt undernät kan du komma åt alla webbappar som finns i samma distributions stämpel och exponeras för Internet.

I webb-HTTP-loggfilerna för din webbapp hittar du klientens käll-IP. Detta implementeras med hjälp av TCP-proxy-protokollet och vidarebefordrar klientens IP-egenskap till webbappen. Mer information finns i [Hämta anslutnings information med hjälp av TCP proxy v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Global översikt över webb programs privata slut punkt](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Eftersom den här funktionen är i för hands version ändrar vi inte DNS-posten under för hands versionen. Du måste hantera DNS-posten i din privata DNS-server eller Azure DNS privat zon själv.
Om du behöver använda ett anpassat DNS-namn måste du lägga till det anpassade namnet i din webbapp. Under för hands versionen måste det anpassade namnet verifieras som valfritt anpassat namn med hjälp av offentlig DNS-matchning. Mer information finns i [anpassad DNS-validering][dnsvalidation].

Om du behöver använda kudu-konsolen eller kudu REST API (distribution med Azure DevOps-anpassade agenter till exempel), måste du skapa två poster i din Azure DNS privata zon eller på din anpassade DNS-server. 
- PrivateEndpointIP yourwebappname.azurewebsites.net 
- PrivateEndpointIP yourwebappname.scm.azurewebsites.net 

## <a name="pricing"></a>Prissättning

Pris information finns i [priser för privata Azure-länkar][pricing].

## <a name="limitations"></a>Begränsningar

När du använder Azure Function i elastisk Premium-plan med privat slut punkt, för att köra eller köra funktionen i Azure-webbportalen, måste du ha direkt åtkomst till nätverket eller så får du ett HTTP 403-fel. I andra ord måste webbläsaren kunna komma åt den privata slut punkten för att köra funktionen från Azure-webbportalen. 

Under för hands versionen exponeras bara produktions platsen bakom den privata slut punkten, och andra platser kan bara användas av en offentlig slut punkt.

Vi förbättrar den privata länk funktionen och den privata slut punkten regelbundet. Läs [den här artikeln][pllimitations] för uppdaterad information om begränsningar.

## <a name="next-steps"></a>Nästa steg

Så här distribuerar du en privat slut punkt för din webbapp via portalen se [så här ansluter du privat till en webbapp][howtoguide]




<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
