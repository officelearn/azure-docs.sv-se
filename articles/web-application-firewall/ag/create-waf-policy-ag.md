---
title: Skapa WAF-principer (Web Application Firewall) för Application Gateway
description: Lär dig hur du skapar brandväggsprinciper för webbprogram för Application Gateway.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: e3738da806ff36cdb7e8d561b88a457a5264eb76
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886933"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Skapa brandväggsprinciper för webbprogram för Application Gateway

Genom att associera en WAF-policy med lyssnare kan flera platser bakom en enda WAF skyddas av olika principer. Om det till exempel finns fem platser bakom DIN WAF kan du ha fem separata WAF-principer (en för varje lyssnare) för att anpassa undantag, anpassade regler och hanterade regeluppsättningar för en plats utan att påverka de andra fyra. Om du vill att en enskild princip ska gälla för alla webbplatser kan du bara associera principen med Application Gateway, i stället för de enskilda lyssnarna, så att den gäller globalt. Principer kan också tillämpas på en sökvägsbaserad routningsregel. 

Du kan skapa så många principer du vill. När du har skapat en princip måste den associeras till en programgateway för att kunna träda i kraft, men den kan associeras med valfri kombination av programgateways och lyssnare. 

Om programgatewayen har en princip tillämpad och du sedan tillämpar en annan princip på en lyssnare på programgatewayen börjar lyssnarens princip gälla, men bara för lyssnarna/lyssnarna som de är tilldelade till. Application Gateway-principen gäller fortfarande för alla andra lyssnare som inte har en specifik princip tilldelad dem. 

   > [!NOTE]
   > Waf-principer per plats och per URI finns i offentlig förhandsversion. Det innebär att den här funktionen omfattas av Microsofts kompletterande användarvillkor. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
   > [!NOTE]
   > När en brandväggsprincip är associerad med en WAF måste det alltid finnas en princip som är associerad med waf. Du kan skriva över den principen, men att ta bort en princip från WAF helt stöds inte. 

Alla nya Web Application Firewall's WAF inställningar (anpassade regler, hanterade rulset konfigurationer, undantag, etc.) lever inuti en WAF-princip. Om du har en befintlig WAF kan dessa inställningar fortfarande finnas kvar i DIN WAF-konfiguration. Steg om hur du flyttar till den nya WAF-principen finns i [Migrera din WAF Config till en WAF-princip](#migrate) senare i den här artikeln. 

## <a name="create-a-policy"></a>Skapa en princip

Skapa först en grundläggande WAF-princip med en hanterad standardregeluppsättning (DRS) med Hjälp av Azure-portalen.

1. Välj **Skapa en resurs**längst upp till vänster i portalen . Sök efter **WAF,** välj **Brandvägg för webbprogram**och välj sedan **Skapa**.
2. På fliken Skapa en **Basics** **WAF-princip** anger eller väljer du följande information, accepterar standardinställningarna för de återstående inställningarna och väljer sedan Granska + **skapa:**

   |Inställning  |Värde  |
   |---------|---------|
   |Policy för     |Regional WAF (Application Gateway)|
   |Prenumeration     |Välj ditt prenumerationsnamn|
   |Resursgrupp     |Välj din resursgrupp|
   |Principnamn     |Skriv ett unikt namn för WAF-principen.|
3. Ange en av följande inställningar på fliken **Association** och välj sedan **Lägg till:**

   |Inställning  |Värde  |
   |---------|---------|
   |Associera programgateway     |Välj ditt profilnamn för Application Gateway.|
   |Associera lyssnare     |Välj namnet på programgatewaylyssnaren och välj sedan **Lägg till**.|

   > [!NOTE]
   > Om du tilldelar en princip till programgatewayen (eller lyssnaren) som redan har en princip på plats skrivs den ursprungliga principen över och ersätts av den nya principen.
4. Välj **Granska + skapa**och välj sedan **Skapa**.

   ![Grunderna i WAF-policy](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>Konfigurera WAF-regler (valfritt)

När du skapar en WAF-princip är den som standard i *identifieringsläge.* I identifieringsläge blockerar WAF inga begäranden. I stället loggas de matchande WAF-reglerna i WAF-loggarna. Om du vill se WAF i aktion kan du ändra lägesinställningarna till *Förebyggande*. I förebyggande läge blockeras och/eller loggas matchningsregler som definieras i den CRS-regeluppsättning du valde.

## <a name="managed-rules"></a>Hanterade regler

Azure-hanterade OWASP-regler är aktiverade som standard. Om du vill inaktivera en enskild regel i en regelgrupp expanderar du reglerna i den regelgruppen, markerar kryssrutan framför regelnumret och väljer **Inaktivera** på fliken ovan.

[![Hanterade](../media/create-waf-policy-ag/managed-rules.png) regler](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>Anpassade regler

Om du vill skapa en anpassad regel väljer du **Lägg till anpassad regel** under fliken Anpassade **regler.** Då öppnas den anpassade regelkonfigurationssidan. Följande skärmbild visar en anpassad exempelregel som konfigurerats för att blockera en begäran om frågesträngen innehåller *textblocket*.

[![Redigera anpassad](../media/create-waf-policy-ag/edit-custom-rule.png) regel](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>Migrera din WAF Config till en WAF-policy

Om du har en befintlig WAF kanske du har märkt vissa ändringar i portalen. Först måste du identifiera vilken typ av policy du har aktiverat på din WAF. Det finns tre potentiella stater:

- Ingen WAF-policy
- Princip endast anpassade regler
- WAF-policy

Du kan se vilket tillstånd din WAF är i genom att titta på den i portalen. Om WAF-inställningarna är synliga och kan ändras inifrån vyn Application Gateway är din WAF i tillstånd 1.

[![WAF-konfiguration](../media/create-waf-policy-ag/waf-configure.png)](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

Om du väljer **Brandvägg för webbprogram** och den visar en associerad princip är WAF i tillstånd 2 eller tillstånd 3. När du har navigerat till principen, om den **bara** visar anpassade regler och associerade programgateways, är det en princip endast för anpassade regler.

![Anpassade WAF-regler](../media/create-waf-policy-ag/waf-custom-rules.png)

Om det också visas principinställningar och hanterade regler är det en fullständig brandväggsprincip för webbprogram. 

![INSTÄLLNINGAR för WAF-princip](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>Migrera till WAF-princip

Om du har en standard-STANDARD-princip för anpassade regler kanske du vill gå över till den nya WAF-principen. Framöver stöder brandväggsprincipen WAF-principinställningar, hanterade regeluppsättningar, undantag och inaktiverade regelgrupper. I huvudsak alla WAF-konfigurationer som tidigare gjordes inuti Application Gateway görs nu via WAF-principen. 

Redigeringar av den anpassade regeln är endast WAF-principen inaktiverad. Om du vill redigera waf-inställningar, till exempel inaktivera regler, lägga till undantag osv.

Det gör du genom att skapa en *brandväggsprincip för webbprogram* och koppla den till valfri programgateway och lyssnare. Den här nya principen **måste** vara exakt samma som den nuvarande WAF-konfigurationen, vilket innebär att varje anpassad regel, uteslutning, inaktiverad regel osv. När du har en princip kopplad till programgatewayen kan du fortsätta att göra ändringar i dina WAF-regler och inställningar. Du kan också göra detta med Azure PowerShell. Mer information finns i [Associera en WAF-princip med en befintlig Programgateway](associate-waf-policy-existing-gateway.md).

Du kan också använda ett migreringsskript för att migrera till en WAF-princip. Mer information finns i [Principer för Migrera brandväggsprogram för webbprogram med Azure PowerShell](migrate-policy.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om [CRS-regelgrupper och regler för brandvägg för webbprogram.](application-gateway-crs-rulegroups-rules.md)
