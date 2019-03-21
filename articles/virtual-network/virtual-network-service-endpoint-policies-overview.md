---
title: Principer för tjänstslutpunkter för virtuella Azure-nätverk | Microsoft Docs
description: Lär dig hur du filtrerar virtuell nätverkstrafik till Azure-tjänstresurser med hjälp av tjänstslutpunktsprinciper
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sumeet.mittal
ms.openlocfilehash: 619b9b68a5c4e897642e1f84c25c2822d8291400
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58104496"
---
# <a name="virtual-network-service-endpoint-policies-preview"></a>Principer för tjänstslutpunkter för virtuella nätverk (förhandsversion)

Med principer för tjänstslutpunkter för virtuella nätverk (VNet) kan du filtrera trafik i virtuella nätverk till Azure-tjänster för att endast tillåta specifika Azure-tjänstresurser över tjänstslutpunkter. Slutpunktsprinciper ger detaljerad åtkomstkontroll för trafik i virtuella nätverk till Azure-tjänster.

Den här funktionen är tillgänglig som __förhandsversion__  för följande Azure-tjänster och regioner:

__Azure Storage__: USA, västra centrala, USA, västra 2.

De mest uppdaterade meddelandena för förhandsversionen finns på sidan för [Azure Virtual Network-uppdateringar](https://azure.microsoft.com/updates/?product=virtual-network).

> [!NOTE]  
> Under förhandsversionen har principerna för tjänstslutpunkter för virtuella nätverk kanske inte har samma tillgänglighet och pålitlighet som funktioner som är allmänt tillgängliga. Mer information finns i [de kompletterande villkoren för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-benefits"></a>Viktiga fördelar

Principer för tjänstslutpunkter för virtuella nätverk ger följande fördelar:

- __Förbättrad säkerhet för dina virtuella nätverkstrafik till Azure-tjänster__

  [Azure-tjänsttaggar för nätverkssäkerhetsgrupper](https://aka.ms/servicetags) gör att du kan begränsa utgående trafik i virtuella nätverk till specifika Azure-tjänster. Detta tillåter dock bara trafik till resurser från den Azure-tjänsten. 
  
  Med slutpunktsprinciper kan du nu begränsa utgående åtkomst för virtuella nätverk till endast specifika Azure-resurser. Detta ger mycket mer detaljerad säkerhetskontroll för att skydda data som används i ditt virtuella nätverk. 

- __Skalbara principer med hög tillgänglighet för att filtrera Azure-tjänsttrafik__

   Slutpunktsprinciperna ger en horisontellt skalbar lösning med hög tillgänglighet för att filtrera Azure-tjänsttrafik från virtuella nätverk över tjänstslutpunkter. Det krävs inget ytterligare arbete för att upprätthålla centrala nätverksinstallationer för den här trafiken i ditt virtuella nätverk.

## <a name="configuration"></a>Konfiguration

- Du kan konfigurera slutpunktsprinciper för att begränsa trafik i virtuella nätverk till specifika Azure-tjänstresurser. I förhandsvisningen stöder vi slutpunktsprinciper för Azure Storage. 
- Slutpunktsprinciper konfigureras i ett undernät i ett virtuellt nätverk. Tjänstslutpunkter ska aktiveras på undernätet för att tillämpa principen för alla Azure-tjänster som anges i principen.
- Slutpunktsprincipen gör att du kan godkänna specifika Azure-tjänstresurser med hjälp av resourceID–formatet. Du kan begränsa åtkomsten till alla resurser i en prenumeration eller resursgrupp. Du kan även begränsa åtkomsten till specifika resurser. 
- Om inga principer är kopplade till ett undernät med slutpunkter kan du som standard komma åt alla resurser i tjänsten. När en princip har konfigurerats i undernätet kan endast de resurser som anges i principen nås från beräkningsinstanser i det undernätet. Åtkomst till alla andra resurser för den specifika tjänsten nekas. 
- Du kan filtrera trafik till Azure-tjänstresurser i de regioner där tjänstslutpunkten har konfigurerats. Åtkomst till tjänstresurser i andra regioner tillåts som standard. 

  > [!NOTE]  
  > Om du vill låsa utgående åtkomst för virtuella nätverk till Azure-resurser i tjänstslutpunktsregioner helt behöver du även regler för nätverkssäkerhetsgrupper konfigurerade för att endast tillåta trafik till tjänstslutpunktsregionerna med hjälp av [tjänsttaggar](security-overview.md#service-tags).

- Du kan tillämpa flera principer på ett undernät. När flera principer är kopplade till undernätet för samma tjänst tillåts trafik i virtuella nätverk till resurser som anges i vilken som helst av dessa principer. Åtkomst till alla andra tjänstresurser som inte specificerats i någon av principerna nekas. 

  > [!NOTE]  
  > Principen ger endast åtkomst till angivna tjänstresurser från ett virtuellt nätverk. All annan trafik till tjänsten nekas automatiskt när du lägger till specifika resurser till principen. Kontrollera att alla tjänstresursberoenden för ditt program kan identifieras och anges i principen.

  > [!WARNING]  
  > Azure-tjänster som distribueras till ditt virtuella nätverk, till exempel Azure HDInsight, kommer åt andra Azure-tjänster såsom Azure Storage för infrastrukturkrav. Om slutpunktsprincipen begränsas till specifika resurser kan det stoppa åtkomsten till dessa infrastrukturresurser för tjänster som distribueras i ditt virtuella nätverk. För specifika tjänster kan du läsa [begränsningarna](#limitations). Under förhandsversionen stöds inte tjänstslutpunktsprinciper för hanterade Azure-tjänster som distribueras i det virtuella nätverket.

- För Azure Storage: 
  -  Du kan begränsa åtkomsten genom att ange Azure Resource Manager *resourceId* för lagringskontot. Detta omfattar trafik till blobar, tabeller, köer, filer och Azure Data Lake Storage Gen2.

     Till exempel kan Azure Storage-konton anges i definitionen för slutpunktsprincipen enligt följande:
    
     Så här tillåter du ett specifikt lagringskonto:         
     `subscriptions/subscriptionId/resourceGroups/resourceGroup/providers/Microsoft.Storage/storageAccounts/storageAccountName`
      
     Så här tillåter alla konton i en prenumeration och en resursgrupp: `/subscriptions/subscriptionId/resourceGroups/resourceGroup`
     
     Så här tillåter du alla konton i en prenumeration: `/subscriptions/subscriptionId`
    
- Det är bara lagringskonton som använder Azure Resource Model kan anges i slutpunktsprincipen.  

  > [!NOTE]  
  > Åtkomst till klassiska lagringskonton blockeras med slutpunktsprinciper.

- Den primära platsen för det konto som anges ska vara i geo-parregionerna för tjänstslutpunkten för undernätet. 

  > [!NOTE]  
  > Principer tillåter att tjänstresurser från andra regioner specificeras. Åtkomst för virtuellt nätverk till Azure-tjänsterna filtreras endast för geo-parregioner. Om nätverkssäkerhetsgrupper inte är begränsade till geo-parregioner för Azure Storage kan det virtuella nätverket komma åt alla lagringskonton utanför geo-parregionerna.

- Sekundär RA-GRS-åtkomst tillåts automatiskt om det primära kontot anges. 
- Lagringskonton kan finnas i samma prenumeration, en annan prenumeration eller i Azure Active Directory-klientorganisationen som det virtuella nätverket. 

## <a name="limitations"></a>Begränsningar

- Du kan endast distribuera tjänstslutpunktsprinciper på virtuella nätverk som distribuerats med Azure Resource Manager-distributionsmodellen.
- Virtuella nätverk måste finnas i samma region som tjänstslutpunktsprincipen.
- Du kan bara använda tjänstslutpunktsprincipen i ett undernät om tjänstslutpunkterna har konfigurerats för de Azure-tjänster som anges i principen.
- Du kan inte använda tjänstslutpunktsprinciper för trafik från ditt lokala nätverk till Azure-tjänster.
- Slutpunktsprinciper ska inte tillämpas på undernät med hanterade Azure-tjänster med beroende av Azure-tjänster för infrastrukturkrav. 

  > [!WARNING]  
  > Azure-tjänster som distribueras till ditt virtuella nätverk, till exempel Azure HDInsight, kommer åt andra Azure-tjänster såsom Azure Storage för infrastrukturkrav. Om slutpunktsprincipen begränsas till specifika resurser kan det stoppa åtkomsten till dessa infrastrukturresurser för de Azure-tjänster som distribueras i ditt virtuella nätverk.
  
  - Vissa Azure-tjänster kan distribueras i undernät med andra beräkningsinstanser. Se till att slutpunktsprinciperna inte tillämpas på undernätet om de hanterade tjänster som anges nedan distribueras till undernätet.
   
    - Azure HDInsight
    - Azure Batch (Azure Resource Manager)
    - Azure Active Directory Domain Services (Azure Resource Manager)
    - Azure Application Gateway (Azure Resource Manager)
    - Azure VPN Gateway (Azure Resource Manager)
    - Azure Firewall

  - Vissa Azure-tjänster distribueras till dedikerade undernät. Slutpunktsprinciper blockeras i alla sådana tjänster som anges nedan under förhandsperioden. 

     - Azure App Service Environment
     - Azure Rediscache
     - Azure API Management
     - Hanterad Azure SQL-instans
     - Azure Active Directory Domain Services
     - Azure Application Gateway (Classic)
     - Azure VPN Gateway (Classic)

- Azure Storage: Klassiska lagringskonton stöds inte i slutpunktsprinciper. Principer nekar åtkomst till alla klassiska lagringskonton som standard. Om ditt program behöver åtkomst till Azure Resource Manager och klassiska lagringskonton bör slutpunktsprinciper inte användas för den här trafiken. 

## <a name="nsgs-with-service-endpoint-policies"></a>NSG:er med tjänstslutpunktsprinciper
- Som standard tillåter NSG:er utgående Internettrafik, inklusive trafik i virtuella nätverk, till Azure-tjänster.
- Om du vill neka all utgående Internettrafik och endast tillåta trafik till specifika Azure-tjänstresurser: 

  Steg 1: Konfigurera NSG:er för att endast tillåta utgående trafik till Azure-tjänster i slutpunktsregioner med hjälp av *Azure-tjänsttaggar*. Mer information finns i [tjänsttaggar för NSG:er](https://aka.ms/servicetags)
      
  Till exempel ser regler för nätverkssäkerhetsgrupper som begränsar åtkomsten till endast slutpunktsregioner ut som i följande exempel:

  ```
  Allow AzureStorage.WestUS2,
  Allow AzureStorage.WestCentralUS,
  Deny all
  ```

  Steg 2: Tillämpa tjänstslutpunktsprincipen med åtkomst till endast specifika Azure-tjänstresurser.

  > [!WARNING]  
  > Om nätverkssäkerhetsgruppen inte är konfigurerad för att begränsa ett virtuellt nätverks Azure-tjänståtkomst till slutpunktsregioner kan du komma åt tjänstresurser i andra regioner även om tjänstslutpunktsprincipen tillämpas.

## <a name="scenarios"></a>Scenarier

- **Peer-kopplade, anslutna eller flera virtuella nätverk**: Om du vill filtrera trafik i peer-kopplade virtuella nätverk ska slutpunktsprinciper tillämpas individuellt på dessa virtuella nätverk.
- **Filtrering av Internettrafik med nätverksutrustning eller Azure Firewall**: Filtrera Azure-tjänsttrafik med principer över slutpunkter och filtrera resten av Internet- eller Azure-trafiken via utrustning eller Azure Firewall. 
- **Filtrering av trafik på Azure-tjänster som distribueras i virtuella nätverk**: Under förhandsversionen stöds inte tjänstslutpunktsprinciper för hanterade Azure-tjänster som distribueras i det virtuella nätverket. 
 Se [begränsningar](#Limitations) för specifika tjänster.
- **Filtrering av trafik till Azure-tjänster från lokal plats**: Tjänstslutpunktsprinciper tillämpas endast på trafiken från undernät som är associerade med principerna. För att tillåta åtkomst till specifika Azure-tjänstresurser från en lokal plats ska trafik filtreras med hjälp av virtuella nätverksinstallationer eller brandväggar.

## <a name="logging-and-troubleshooting"></a>Loggning och felsökning
Det finns ingen centraliserad loggning för tjänstslutpunktsprinciper. Se [loggning för tjänstslutpunkter](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting) för diagnostikloggar för tjänsten.

### <a name="troubleshooting-scenarios"></a>Felsökningsscenarier
- Åtkomst tillåts till lagringskonton som inte anges i slutpunktsprinciperna
  - Nätverkssäkerhetsgrupper kan tillåta åtkomst till Internet eller Azure Storage-konton i andra regioner.
  - Nätverkssäkerhetsgrupper bör konfigureras för att neka all utgående Internettrafik och endast tillåta trafik till specifika Azure Storage-regioner. Mer information finns i Nätverkssäkerhetsgrupper.
- Åtkomst nekas för konton som anges i slutpunktsprinciperna
  - Nätverkssäkerhetsgrupper och brandväggsfiltrering blockerar kanske åtkomst
  - Om borttagning/återtillämpning av principen leder till anslutningsförlust:
    - Kontrollera huruvida Azure-tjänsten är konfigurerad för att tillåta åtkomst från det virtuella nätverket över slutpunkter eller om standardprincipen för resursen är inställd på *Tillåt alla*.
      > [!NOTE]      
      > Tjänstresurser behöver inte säkras mot virtuella nätverk för att få åtkomst via slutpunktsprinciper. Som bästa praxis för säkerhet rekommenderar vi dock att tjänstresurser säkras mot dina betrodda nätverk, till exempel dina virtuella Azure-nätverk, via tjänstslutpunkter, och körs lokalt via en IP-brandvägg.
  
    - Verifiera att tjänstdiagnostiken visar trafiken över slutpunkter.
    - Kontrollera om flödesloggarna för nätverkssäkerhetsgruppen visar åtkomsten och att lagringsloggarna visar åtkomsten som förväntat över tjänstslutpunkter.
    - Kontakta Azure-supporten.
- Åtkomst nekas för konton som inte anges i tjänstslutpunktsprinciperna
  - Nätverkssäkerhetsgrupper och brandväggsfiltrering blockerar kanske åtkomst. Se till att *Azure Storage*-tjänsttaggen tillåts för slutpunktsregionerna. Se [begränsningarna](#limitations) för principbegränsningar.
  Till exempel nekas klassiska lagringskonton åtkomst om en princip tillämpas.
  - Kontrollera huruvida Azure-tjänsten är konfigurerad för att tillåta åtkomst från det virtuella nätverket över slutpunkter eller om standardprincipen för resursen är inställd på *Tillåt alla*.

## <a name="provisioning"></a>Etablering

Tjänstslutpunktsprinciper kan konfigureras i virtuella nätverk av en användare med skrivbehörighet för ett virtuellt nätverk. Lär dig mer om [inbyggda roller](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i Azure och att tilldela specifika behörigheter till [anpassade roller](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuella nätverk och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer eller Azure Active Directory-klientorganisationer. 

## <a name="pricing-and-limits"></a>Priser och begränsningar

Det tillkommer inga extra avgifter för att använda tjänstslutpunktsprinciper. Den aktuella prismodellen för Azure-tjänster (till exempel Azure Storage) tillämpas som den gäller i dag över tjänstslutpunkter.

Följande begränsningar tillämpas på tjänstslutpunktsprinciper: 

 |Resurs | Standardgräns |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du konfigurerar tjänstslutpunktsprinciper i virtuella nätverk](virtual-network-service-endpoint-policies-portal.md)
- Läs mer om [tjänstslutpunkter för virtuella nätverk](virtual-network-service-endpoints-overview.md)

