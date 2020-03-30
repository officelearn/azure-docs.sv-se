---
title: Översikt över Azure Web Application Firewall (WAF)
description: Den här artikeln är en översikt över WAF-principer (Web Application Firewall) globala, per plats och per URI.This article is a overview of Web Application Firewall (WAF) global, per-site, and per-URI policies.
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: fff4fb5707c07098fd7fac261a36909224365cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060274"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Översikt över Azure Web Application Firewall (WAF)

Brandväggsprinciper för webbprogram innehåller alla WAF-inställningar och konfigurationer. Detta inkluderar undantag, anpassade regler, hanterade regler och så vidare. Dessa principer associeras sedan till en programgateway (global), en lyssnare (per plats) eller en sökvägsbaserad regel (per URI) för att de ska börja gälla.

> [!NOTE]
> Waf-principer (Azure Web Application Firewall) per plats och per URI finns i offentlig förhandsversion.
> 
> Den allmänt tillgängliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Det finns ingen gräns för hur många principer du kan skapa. När du skapar en princip måste den associeras till en programgateway för att börja gälla. Den kan associeras med valfri kombination av programgateways, lyssnare och sökvägsbaserade regler.

## <a name="global-waf-policy"></a>Global WAF-policy

När du associerar en WAF-princip globalt skyddas alla webbplatser bakom din Application Gateway WAF med samma hanterade regler, anpassade regler, undantag och alla andra konfigurerade inställningar.

Om du vill att en enskild princip ska gälla för alla platser kan du associera principen med programgatewayen. Mer information finns i [Skapa principer för brandvägg för webbprogram för Application Gateway för](create-waf-policy-ag.md) att skapa och tillämpa en WAF-princip med Azure-portalen. 

## <a name="per-site-waf-policy"></a>WAF-princip per plats

Med WAF-principer per plats kan du skydda flera platser med olika säkerhetsbehov bakom en enda WAF med hjälp av principer per plats. Om det till exempel finns fem platser bakom DIN WAF kan du ha fem separata WAF-principer (en för varje lyssnare) för att anpassa undantag, anpassade regler, hanterade regeluppsättningar och alla andra WAF-inställningar för varje webbplats.

Anta att programgatewayen har en global princip som tillämpas på den. Sedan kan du tillämpa en annan princip på en lyssnare på den programgatewayen. Lyssnarens politik träder nu i kraft för just den lyssnaren. Programgatewayens globala princip gäller fortfarande för alla andra lyssnare och sökvägsbaserade regler som inte har en specifik princip tilldelad dem.

## <a name="per-uri-policy"></a>Per-URI-policy

Om du vill ha ännu mer anpassning ned till URI-nivån kan du associera en WAF-princip med en sökvägsbaserad regel. Om det finns vissa sidor på en enda webbplats som kräver olika principer kan du göra ändringar i WAF-principen som bara påverkar en viss URI. Detta kan gälla för en betalnings- eller inloggningssida, eller andra URI:er som behöver en ännu mer specifik WAF-policy än de andra webbplatserna bakom din WAF.

Precis som med WAF-principer per plats åsidosätter mer specifika principer mindre specifika principer. Det innebär att en princip per URI på en URL-sökvägskarta åsidosätter alla per plats eller globala WAF-princip ovanför den.

## <a name="example"></a>Exempel

Anta att du har tre platser: contoso.com, fabrikam.com och adatum.com alla bakom samma programgateway. Du vill ha en WAF som tillämpas på alla tre webbplatserna, men du behöver ökad säkerhet med adatum.com eftersom det är där kunderna besöker, bläddrar och köper produkter.

Du kan tillämpa en global princip på WAF, med vissa grundläggande inställningar, undantag eller anpassade regler om det behövs för att stoppa vissa falska positiva identifieringar från att blockera trafik. I det här fallet finns det ingen anledning att ha globala SQL-insprutningsregler som körs eftersom fabrikam.com och contoso.com är statiska sidor utan SQL-serverning. Så du kan inaktivera dessa regler i den globala principen.

Denna globala policy är lämplig för contoso.com och fabrikam.com, men du måste vara mer försiktig med adatum.com där inloggningsinformation och betalningar hanteras. Du kan använda en princip per plats på adatum-lyssnaren och låta SQL-reglerna köras. Anta också att det finns en cookie som blockerar viss trafik, så att du kan skapa ett undantag för den cookien för att stoppa det falska positiva. 

Den adatum.com/payments URI är där du måste vara försiktig. Så tillämpa en annan princip på att URI och lämna alla regler aktiverade, och även ta bort alla undantag.

I det här exemplet har du en global princip som gäller för två webbplatser. Du har en policy per plats som gäller för en plats och sedan en per-URI-princip som gäller för en specifik sökvägsbaserad regel. Se (infoga länk här när den finns) hur du skapar per plats och per URI-principer för motsvarande PowerShell för det här exemplet.

## <a name="existing-waf-configurations"></a>Befintliga WAF-konfigurationer

Alla nya WAF-inställningar för webbprogram brandväggen (anpassade regler, hanterade regeluppsättningskonfigurationer, undantag och så vidare.) finns i en WAF-princip. Om du har en befintlig WAF kan dessa inställningar fortfarande finnas kvar i WAF-konfigurationen. Om du vill ha mer information om hur du flyttar till den nya WAF-principen [migrerar du WAF Config till en WAF-princip](https://docs.microsoft.com/azure/web-application-firewall/ag/migrate-policy). 


## <a name="next-steps"></a>Nästa steg

Skapa principer per plats och per URI med Azure PowerShell.
