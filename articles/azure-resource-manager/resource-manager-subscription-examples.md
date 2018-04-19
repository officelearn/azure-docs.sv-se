---
title: Scenarier och exempel för prenumerationen styrningen | Microsoft Docs
description: Ger exempel på hur du implementerar styrning av Azure-prenumeration för vanliga scenarier.
services: azure-resource-manager
documentationcenter: na
author: rdendtler
manager: timlt
editor: tysonn
ms.assetid: e8fbeeb8-d7a1-48af-804f-6fe1a6024bcb
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: rodend;karlku;tomfitz
ms.openlocfilehash: 6bd4e9f6bbc5bba73b2c169b7f3c5931f30029e6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="examples-of-implementing-azure-enterprise-scaffold"></a>Exempel på att implementera kodskelett Azure enterprise
Det här avsnittet innehåller exempel på hur ett företag kan implementera rekommendationer för en [Azure enterprise kodskelett](resource-manager-subscription-governance.md). Fiktiva företaget Contoso används för att illustrera Metodtips för vanliga scenarier.

## <a name="background"></a>Bakgrund
Contoso är ett globalt företag som tillhandahåller ange kedjan lösningar för kunder i allt från en modell för ”programvara som tjänst” till en paketerad modell distribueras lokalt.  De utvecklar programvara världen med betydande Utvecklingscenter i Indien, USA och Kanada.

ISV-delen av företaget är indelade i flera oberoende affärsenheter som hanterar produkter i ett betydande företag. Varje affärsenhet har egna utvecklare, projektledare och arkitekter.

Enterprise teknik Services ETS affärsenhet ger centraliserade IT och hanterar flera datacenter där affärsenheter värd sina program. Tillsammans med hantering av datacenter, ETS organisationen ger och hanterar centraliserad samarbete (till exempel e-post och webbplatser) och nätverk/telefoni tjänster. De kan också hantera kund-riktade arbetsbelastningar för mindre enheter som inte har operativa personal.

Följande personer som används i det här avsnittet:

* Dave är ETS Azure-administratör.
* Alice har Contosos Director för utveckling i affärsenheten ange kedjan.

Contoso måste skapa en line-of-business-app och en kund-riktade app. Beslutade att köra apparna på Azure. Dave läser den [normativ prenumeration styrning](resource-manager-subscription-governance.md) avsnittet, och är nu redo att implementera rekommendationerna.

## <a name="scenario-1-line-of-business-application"></a>Scenario 1: line-of-business-program
Contoso skapar ett hanteringssystem för källa kod (BitBucket) som ska användas av utvecklare över hela världen.  Programmet använder infrastruktur som en tjänst (IaaS) som värd för och består av webbservrar och en databasserver. Utvecklare får åtkomst till servrar i utvecklingsmiljöer, men de behöver inte åtkomst till servrarna i Azure. Contoso ETS vill tillåta program ägare och team för att hantera programmet. Programmet är endast tillgängligt när Contosos företagsnätverk. Dave måste ställa in prenumerationen för det här programmet. Prenumerationen kommer också vara värd för andra developer-relaterade program i framtiden.  

### <a name="naming-standards--resource-groups"></a>Namnge standards & resursgrupper
Dave skapar en prenumeration för att stödja utvecklingsverktyg som är gemensamma för alla affärsenheter. Han måste skapa beskrivande namn för prenumeration och resurs grupper (för programmet och nätverk). Han skapar följande prenumeration och resurs grupper:

| Objekt | Namn | Beskrivning |
| --- | --- | --- |
| Prenumeration |Contoso ETS DeveloperTools produktion |Stöder common utvecklingsverktygen |
| Resursgrupp |bitbucket-produktprenumeration-rg |Innehåller program-webbservern och databasserver |
| Resursgrupp |corenetworks-produktprenumeration-rg |Innehåller de virtuella nätverk och gateway för plats-till-plats-anslutning |

### <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll
När du har skapat sin prenumeration vill Dave se till att rätt team och programägarna kan komma åt sina resurser. Dave identifierar att varje grupp har olika krav. Han använder de grupper som har synkroniserats från Contosos lokala Active Directory (AD) till Azure Active Directory och ger åtkomst till team rätt nivå.

Dave tilldelar följande roller för prenumerationen:

| Roll | Tilldelat till | Beskrivning |
| --- | --- | --- |
| [Ägare](../role-based-access-control/built-in-roles.md#owner) |Hanterade ID från Contosos AD |Detta ID kontrolleras med precis tid JIT-åtkomst via Contosos Identitetshantering verktyg och säkerställer att granskas fullständigt prenumeration ägare åtkomst |
| [Säkerhetshantering](../role-based-access-control/built-in-roles.md#security-manager) |Avdelningen för informationssäkerhet och riskhantering management |Den här rollen kan du titta i Azure Security Center och status för resurser |
| [Nätverksdeltagare](../role-based-access-control/built-in-roles.md#network-contributor) |Nätverk-teamet |Den här rollen kan Contosos nätverksteam för att hantera plats-till-plats-VPN och virtuella nätverk |
| *Anpassad roll* |Programmet ägare |Dave skapar en roll som ger möjlighet att ändra resurser i resursgruppen. Mer information finns i [anpassade roller i Azure RBAC](../role-based-access-control/custom-roles.md) |

### <a name="policies"></a>Principer
Dave har följande krav för att hantera resurser i prenumerationen:

* Eftersom utvecklingsverktyg stöder utvecklare över hela världen, vill inte han hindra användare från att skapa resurser i en region. Dock behöver han veta där resurserna skapas.
* Han är berörda med kostnader. Därför kan vill han förhindra att ägare i onödan dyra virtuella datorer skapas.  
* Eftersom det här programmet har utvecklare på flera enheter, vill han tagga varje resurs med företagsägaren enhet och program. Med hjälp av dessa taggar debiterar ETS rätt team.

Han skapar följande [Azure principer](../azure-policy/azure-policy-introduction.md):

| Fält | Verkan | Beskrivning |
| --- | --- | --- |
| location |Granska |Gransknings-och skapa resurser i en region |
| typ |avvisa |Neka G-serien virtuella datorer med skapas |
| tags |avvisa |Kräv program ägare tagg |
| tags |avvisa |Kräv kostnaden center tagg |
| tags |Lägg till |Lägga till taggnamnet på **affärsenheten** och Taggvärde **ETS** till alla resurser |

### <a name="resource-tags"></a>Resurstaggar
Dave förstår att han måste ha specifik information om faktura för att identifiera kostnadsställe för BitBucket-implementering. Dessutom vill Dave veta alla resurser som ETS äger.

Han lägger till följande [taggar](resource-group-using-tags.md) till resursgrupper och resurser.

| Taggnamn | Taggvärdet |
| --- | --- |
| ApplicationOwner |Namnet på den person som hanterar det här programmet |
| Kostnadsställe |Kostnadsställe på gruppen som betalar för Azure-förbrukningen |
| Affärsenheten |**ETS** (affärsenhet som är associerade med prenumerationen) |

### <a name="core-network"></a>Kärnnätverket
Contoso ETS information informationssäkerhet och riskhantering ledningen granskar Daves föreslagna planen för att flytta programmet till Azure. De vill se till att programmet inte exponerad mot internet.  Dave har också developer-appar som i framtiden kommer att flyttas till Azure. Dessa appar kräver offentliga gränssnitt.  Han ger både interna och externa virtuella nätverk och en säkerhetsgrupp för nätverk för att begränsa åtkomsten för att uppfylla kraven.

Han skapar följande resurser:

| Resurstyp | Namn | Beskrivning |
| --- | --- | --- |
| Virtual Network |interna virtuella nätverk |Används med BitBucket-programmet och är ansluten via ExpressRoute till Contosos företagsnätverk.  Ett undernät (`bitbucket`) ger programmet med en specifik IP-adressutrymme |
| Virtual Network |externa-vnet |Tillgänglig för framtida program som kräver offentliga slutpunkter |
| Nätverkssäkerhetsgrupp |bitbucket nsg |Garanterar att minimeras risken för angrepp på arbetsbelastningen genom att tillåta anslutningar endast på port 443 för undernätet där programmet finns (`bitbucket`) |

### <a name="resource-locks"></a>Resurslås
Dave identifierar att anslutningen mellan Contosos företagsnätverket och det interna virtuella nätverket måste skyddas från wayward skript eller oavsiktlig borttagning.

Han skapar följande [resurslås](resource-group-lock-resources.md):

| Låstyp | Resurs | Beskrivning |
| --- | --- | --- |
| **CanNotDelete** |interna virtuella nätverk |Förhindrar att användare tar bort virtuellt nätverk eller undernät, men förhindrar inte att lägga till nya undernät |

### <a name="azure-automation"></a>Azure Automation
Dave har inget att automatisera för det här programmet. Även om han skapade ett Azure Automation-konto använda inte han den först.

### <a name="azure-security-center"></a>Azure Security Center
Contoso IT-tjänsthantering behöver snabbt identifiera och hantera hot. De vill också att förstå vilka problem kan finnas.  

För att uppfylla dessa datahanteringskrav Dave gör den [Azure Security Center](../security-center/security-center-intro.md), och ger åtkomst till rollen Security Manager.

## <a name="scenario-2-customer-facing-app"></a>Scenario 2: kund-riktade app
Företag position inom affärsenheten ange kedja har identifierats av olika möjligheter att öka användarnas interaktion med Contosos kunder med en kortet. Alice team måste skapa det här programmet och beslutar att Azure ökar de kan uppfylla företagets behov. Alice fungerar med Dave från ETS så här konfigurerar du två prenumerationer för utveckling och drift av det här programmet.

### <a name="azure-subscriptions"></a>Azure-prenumerationer
Dave loggar in på Azure Enterprise Portal och ser att ange kedjan avdelning redan finns.  Det här projektet är det första utvecklingsprojektet för leverans kedjan team i Azure, identifierar Dave behovet av att ett nytt konto för Alice Utvecklingsteamet.  Han skapar kontot ”R & D” för sitt team och tilldelar åtkomst till Alice. Alice loggar in via Azure portal och skapar två prenumerationer: ett för utveckling-servrar och ett för produktionsservrarna.  Hon följer de tidigare upprättad namngivning standarderna när du skapar följande prenumerationer:

| Använd prenumeration | Namn |
| --- | --- |
| Utveckling |Contoso SupplyChain ResearchDevelopment LoyaltyCard utveckling |
| Produktion |Contoso SupplyChain Operations LoyaltyCard produktion |

### <a name="policies"></a>Principer
Dave och Alice diskutera programmet och identifiera att det här programmet endast hanterar kunder i Nordamerika region.  Alice och hennes team du tänker använda Azures program-miljön och Azure SQL för att skapa programmet. De kan behöva skapa virtuella datorer under utvecklingen.  Alice vill se till att hennes utvecklare har de resurser som de behöver för att utforska och undersöka problem utan dra i ETS.

För den **development prenumeration**, de skapar följande princip:

| Fält | Verkan | Beskrivning |
| --- | --- | --- |
| location |Granska |Gransknings-och skapa resurser i en region |

De begränsa inte typ av sku som en användare kan skapa under utveckling och de behöver inte taggar för alla resursgrupper eller resurser.

För den **produktion prenumeration**, de skapar följande principer:

| Fält | Verkan | Beskrivning |
| --- | --- | --- |
| location |avvisa |Neka skapandet av resurser utanför USA Datacenter |
| tags |avvisa |Kräv program ägare tagg |
| tags |avvisa |Kräv avdelning tagg |
| tags |Lägg till |Lägg till tagg i varje resursgrupp som anger produktionsmiljö |

De begränsar inte typ av sku som en användare kan skapa i produktion.

### <a name="resource-tags"></a>Resurstaggar
Dave förstår att han måste ha specifik information för att identifiera rätt affärsgrupper för fakturering och ägare. Han definierar resurstaggar för resursgrupper och resurser.

| Taggnamn | Taggvärdet |
| --- | --- |
| ApplicationOwner |Namnet på den person som hanterar det här programmet |
| Avdelning |Kostnadsställe på gruppen som betalar för Azure-förbrukningen |
| EnvironmentType |**Produktion** (trots att prenumerationen innehåller **produktion** i namnet, inklusive den här taggen möjliggör enkel identifiering när du tittar på resurser i portalen eller på växeln) |

### <a name="core-networks"></a>Core nätverk
Contoso ETS information informationssäkerhet och riskhantering ledningen granskar Daves föreslagna planen för att flytta programmet till Azure. De vill säkerställa att programmet kortet korrekt isoleras och skyddas i ett perimeternätverk.  För att uppfylla detta krav Dave och Alice att skapa ett externt virtuellt nätverk och en säkerhetsgrupp för nätverk för att isolera kortet program från företagsnätverket Contoso.  

För den **development prenumeration**, de skapar:

| Resurstyp | Namn | Beskrivning |
| --- | --- | --- |
| Virtual Network |interna virtuella nätverk |Utvecklingsmiljö Contoso kortet fungerar och är ansluten via ExpressRoute till Contosos företagsnätverk |

För den **produktion prenumeration**, de skapar:

| Resurstyp | Namn | Beskrivning |
| --- | --- | --- |
| Virtual Network |externa-vnet |Värd för kortet programmet och inte är anslutet direkt till Contosos ExpressRoute. Koden skickas via deras källkoden system direkt till PaaS-tjänster |
| Nätverkssäkerhetsgrupp |loyaltycard nsg |Garanterar att minimeras risken för angrepp på arbetsbelastningen genom att bara tillåta inkommande kommunikation på TCP 443.  Contoso undersöker också med hjälp av en brandvägg för webbaserade program ger ytterligare skydd |

### <a name="resource-locks"></a>Resurslås
Dave och Alice ger och väljer att lägga till resurslås på vissa viktiga resurser i miljön för att förhindra oavsiktlig borttagning under ett hänga kod push.

De skapar följande låset:

| Låstyp | Resurs | Beskrivning |
| --- | --- | --- |
| **CanNotDelete** |externa-vnet |Förhindra att användare tar bort virtuellt nätverk eller undernät. Låset förhindrar inte att lägga till nya undernät |

### <a name="azure-automation"></a>Azure Automation
Alice och hennes Utvecklingsteamet har omfattande runbooks för att hantera miljön för det här programmet. Runbooks tillåter för tillägg/borttagning av noder för programmet och andra DevOps-uppgifter.

Om du vill använda dessa runbooks kan [Automation](../automation/automation-intro.md).

### <a name="azure-security-center"></a>Azure Security Center
Contoso IT-tjänsthantering behöver snabbt identifiera och hantera hot. De vill också att förstå vilka problem kan finnas.  

För att uppfylla kraven kan Dave Azure Security Center. Han säkerställer att Azure Security Center övervakning av resurser och ger åtkomst till team DevOps och säkerhet.

## <a name="next-steps"></a>Nästa steg
* Läs om hur du skapar Resource Manager-mallar i [bästa praxis för att skapa mallar för Azure Resource Manager](resource-manager-template-best-practices.md).
