---
title: Principer för tjänstslutpunkter för virtuellt nätverk i Azure | Microsoft Docs
description: Lär dig hur du filtrerar virtuell nätverkstrafik till Azure-tjänstresurser med hjälp av tjänstslutpunktsprinciper
services: virtual-network
documentationcenter: na
author: RDhillon
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: 926da07ffaf0c61ca2a7fd02351ef3635ec4d73b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651310"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>Slutpunktsprinciper för virtuella nätverkstjänster för Azure Storage

Med VNet-tjänstslutpunktsprinciper (Virtual Network) kan du filtrera utgående virtuell nätverkstrafik till Azure Storage-konton via tjänstslutpunkten och tillåta dataexfiltration till endast specifika Azure Storage-konton. Slutpunktsprinciper ger detaljerad åtkomstkontroll för virtuell nätverkstrafik till Azure Storage när du ansluter över tjänstens slutpunkt.

![Skydda utgående fjärrtrafik i virtuellt nätverk till Azure Storage-konton](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

Den här funktionen är vanligtvis tillgänglig för __Azure Storage__ i alla __globala Azure-regioner__.

## <a name="key-benefits"></a>Viktiga fördelar

Principer för tjänstslutpunkt för virtuellt nätverk ger följande fördelar:

- __Förbättrad säkerhet för din virtuella nätverkstrafik till Azure Storage__

  [Azure-tjänsttaggar för nätverkssäkerhetsgrupper](https://aka.ms/servicetags) gör att du kan begränsa utgående trafik i virtuella nätverk till specifika Azure Storage-regioner. Detta möjliggör dock trafik till alla konton inom vald Azure Storage-region.
  
  Med slutpunktsprinciper kan du ange Azure Storage-konton som tillåts utgående åtkomst till virtuellt nätverk och begränsar åtkomsten till alla andra lagringskonton. Detta ger mycket mer detaljerad säkerhetskontroll för att skydda dataexfiltration från ditt virtuella nätverk.

- __Skalbara principer med hög tillgänglighet för att filtrera Azure-tjänsttrafik__

   Slutpunktsprinciperna ger en horisontellt skalbar lösning med hög tillgänglighet för att filtrera Azure-tjänsttrafik från virtuella nätverk över tjänstslutpunkter. Det krävs inget ytterligare arbete för att upprätthålla centrala nätverksinstallationer för den här trafiken i ditt virtuella nätverk.

## <a name="json-object-for-service-endpoint-policies"></a>JSON-objekt för tjänstslutpunktsprinciper
Låt oss ta en snabb titt på serviceslutpunktsprincipobjektet.

```json
"serviceEndpointPolicyDefinitions": [
    {
            "description": null,
            "name": "MySEP-Definition",
            "resourceGroup": "MySEPDeployment",
            "service": "Microsoft.Storage",
            "serviceResources": [ 
                    "/subscriptions/subscriptionID/resourceGroups/MySEPDeployment/providers/Microsoft.Storage/storageAccounts/mystgacc"
            ],
            "type": "Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions"
    }
]
```

## <a name="configuration"></a>Konfiguration

-   Du kan konfigurera slutpunktsprinciperna för att begränsa den virtuella nätverkstrafiken till specifika Azure Storage-konton.
-   Slutpunktsprinciper konfigureras i ett undernät i ett virtuellt nätverk. Tjänstslutpunkter för Azure Storage bör aktiveras i undernätet för att tillämpa principen.
-   Med slutpunktsprincipen kan du lägga till specifika Azure Storage-konton för att tillåta lista med hjälp av resourceID-formatet. Du kan begränsa åtkomsten till
    - alla lagringskonton i en prenumeration<br>
      `E.g. /subscriptions/subscriptionId`

    - alla lagringskonton i en resursgrupp<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - ett enskilt lagringskonto genom att ange motsvarande Azure Resource Manager-resourceId. Detta omfattar trafik till blobar, tabeller, köer, filer och Azure Data Lake Storage Gen2. <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   Om inga principer är kopplade till ett undernät med slutpunkter kan du som standard komma åt alla lagringskonton i tjänsten. När en princip har konfigurerats i undernätet kan endast de resurser som anges i principen nås från beräkningsinstanser i det undernätet. Åtkomst till alla andra lagringskonton nekas.
-   När du använder tjänstslutpunktsprinciper i ett undernät uppgraderas Azure Storage *Service Endpoint-scopet* från regional till **global**. Det innebär att all trafik till Azure Storage skyddas via tjänstslutpunkten därefter. Tjänstslutpunktsprinciperna är också tillämpliga globalt, så alla lagringskonton, som inte uttryckligen tillåts, nekas åtkomst. 
-   Du kan tillämpa flera principer på ett undernät. När flera principer är associerade till undernätet tillåts virtuell nätverkstrafik till resurser som anges i någon av dessa principer. Åtkomst till alla andra tjänstresurser som inte specificerats i någon av principerna nekas.

    > [!NOTE]  
    > Tjänstslutpunktsprinciper är **tillåta principer,** så bortsett från de angivna resurserna är alla andra resurser begränsade. Kontrollera att alla tjänstresursberoenden för dina program identifieras och visas i principen.

- Det är bara lagringskonton som använder Azure Resource Model kan anges i slutpunktsprincipen. Dina klassiska Azure Storage-konton stöder inte Azure Service Endpoint-principer.
- Sekundär RA-GRS-åtkomst tillåts automatiskt om det primära kontot anges.
- Lagringskonton kan finnas i samma prenumeration, en annan prenumeration eller i Azure Active Directory-klientorganisationen som det virtuella nätverket.

## <a name="scenarios"></a>Scenarier

- **Peer-kopplade, anslutna eller flera virtuella nätverk**: Om du vill filtrera trafik i peer-kopplade virtuella nätverk ska slutpunktsprinciper tillämpas individuellt på dessa virtuella nätverk.
- **Filtrera Internet-trafik med nätverksinstallationer eller Azure-brandvägg:** Filtrera Azure-tjänsttrafik med principer, över tjänstslutpunkter och filtrera resten av Internet- eller Azure-trafiken via enheter eller Azure-brandväggen.
- **Filtrering av trafik på Azure-tjänster som distribueras till virtuella nätverk:** Just nu stöds inte Azure Service Endpoint-principer för alla hanterade Azure-tjänster som distribueras till ditt virtuella nätverk. 
- **Filtrera trafik till Azure-tjänster från lokala**: tjänstslutpunktsprinciper tillämpas endast på trafiken från undernät som är associerade med principerna. För att tillåta åtkomst till specifika Azure-tjänstresurser från en lokal plats ska trafik filtreras med hjälp av virtuella nätverksinstallationer eller brandväggar.

## <a name="logging-and-troubleshooting"></a>Loggning och felsökning
Det finns ingen centraliserad loggning för tjänstslutpunktsprinciper. Se [loggning för tjänstslutpunkter](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting) för diagnostikloggar för tjänsten.

### <a name="troubleshooting-scenarios"></a>Felsökningsscenarier
- Åtkomst nekad till lagringskonton som fungerade i förhandsversion (inte i geografiskt parkopplade regioner)
  - Med Azure Storage-uppgradering för att använda globala tjänsttaggar är omfattningen av tjänstens slutpunkt och därmed tjänstslutpunktsprinciper nu globala. Så all trafik till Azure Storage krypteras över tjänstslutpunkter och endast lagringskonton som uttryckligen anges i principen tillåts åtkomst.
  - Tillåt uttryckligen lista alla nödvändiga lagringskonton för att återställa åtkomsten.  
  - Kontakta Azure-supporten.
- Åtkomst nekas för konton som anges i slutpunktsprinciperna
  - Nätverkssäkerhetsgrupper och brandväggsfiltrering blockerar kanske åtkomst
  - Om borttagning/återtillämpning av principen leder till anslutningsförlust:
    - Verifiera om Azure-tjänsten är konfigurerad för att tillåta åtkomst från det virtuella nätverket över slutpunkter, eller att standardprincipen för resursen är inställd *på Tillåt alla*.
    - Verifiera att tjänstdiagnostiken visar trafiken över slutpunkter.
    - Kontrollera om flödesloggarna för nätverkssäkerhetsgruppen visar åtkomsten och att lagringsloggarna visar åtkomsten som förväntat över tjänstslutpunkter.
    - Kontakta Azure-supporten.
- Åtkomst nekas för konton som inte anges i tjänstslutpunktsprinciperna
  - Verifiera om Azure Storage är konfigurerat för att tillåta åtkomst från det virtuella nätverket över slutpunkter, eller om standardprincipen för resursen är inställd *på Tillåt alla*.
  - Kontrollera att kontona inte är **klassiska lagringskonton** med tjänstslutpunktsprinciper i undernätet.
- En hanterad Azure-tjänst slutade fungera efter att en tjänstslutpunktsprincip har tillämpats över undernätet
  - Hanterade tjänster stöds inte med tjänstslutpunktsprinciper just nu. *Titta på det här utrymmet för uppdateringar*.

## <a name="provisioning"></a>Etablering

Tjänstslutpunktsprinciper kan konfigureras i virtuella nätverk av en användare med skrivbehörighet för ett virtuellt nätverk. Lär dig mer om [inbyggda roller](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i Azure och att tilldela specifika behörigheter till [anpassade roller](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuella nätverk och Azure Storage-konton kan finnas i samma eller olika prenumerationer, eller Azure Active Directory-klienter.

## <a name="limitations"></a>Begränsningar

- Du kan endast distribuera tjänstslutpunktsprinciper på virtuella nätverk som distribuerats med Azure Resource Manager-distributionsmodellen.
- Virtuella nätverk måste finnas i samma region som tjänstslutpunktsprincipen.
- Du kan bara använda tjänstslutpunktsprincipen i ett undernät om tjänstslutpunkterna har konfigurerats för de Azure-tjänster som anges i principen.
- Du kan inte använda tjänstslutpunktsprinciper för trafik från ditt lokala nätverk till Azure-tjänster.
- Azure-hanterade tjänster stöder för närvarande inte slutpunktsprinciper. Detta inkluderar hanterade tjänster som distribueras till delade undernät *Azure App Service Environment, Azure Redis Cache, Azure API Management, Azure SQL MI, classic managed services*(t.ex. *Azure HDInsight, Azure Batch, Azure ADDS, Azure APplication Gateway, Azure VPN gateway, Azure Firewall*

 > [!WARNING]
 > Azure-tjänster som distribueras till ditt virtuella nätverk, till exempel Azure HDInsight, kommer åt andra Azure-tjänster såsom Azure Storage för infrastrukturkrav. Om slutpunktsprincipen begränsas till specifika resurser kan det stoppa åtkomsten till dessa infrastrukturresurser för de Azure-tjänster som distribueras i ditt virtuella nätverk.

- Klassiska lagringskonton stöds inte i slutpunktsprinciper. Principer nekar åtkomst till alla klassiska lagringskonton som standard. Om ditt program behöver åtkomst till Azure Resource Manager och klassiska lagringskonton bör slutpunktsprinciper inte användas för den här trafiken.

## <a name="pricing-and-limits"></a>Priser och begränsningar

Det tillkommer inga extra avgifter för att använda tjänstslutpunktsprinciper. Den aktuella prismodellen för Azure-tjänster (till exempel Azure Storage) tillämpas som den gäller i dag över tjänstslutpunkter.

Följande begränsningar tillämpas på tjänstslutpunktsprinciper: 

 |Resurs | Standardgräns |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Efterföljande moment

- Lär dig [hur du konfigurerar principer för tjänstslutpunkt för virtuellt nätverk](virtual-network-service-endpoint-policies-portal.md)
- Läs mer om [slutpunkter](virtual-network-service-endpoints-overview.md) för tjänsten För virtuella nätverk
