---
title: "Skapa eller uppdatera en Programgateway med en brandvägg för webbaserade program | Microsoft Docs"
description: "Lär dig hur du skapar en Programgateway med en brandvägg för webbaserade program med hjälp av portalen"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b561a210-ed99-4ab4-be06-b49215e3255a
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: bfc06c1b44974fd17a3794654503d21d6407a917
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-by-using-the-portal"></a>Skapa en Programgateway med en brandvägg för webbaserade program med hjälp av portalen

> [!div class="op_single_selector"]
> * [Azure-portalen](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

Lär dig hur du skapar en brandvägg för webbaserade program (Brandvägg)-aktiverat Programgateway.

Brandvägg i Azure Application Gateway skyddar webbprogram från vanliga webbaserade attacker som SQL injection, webbplatser scripting attacker och sessionen hijacks. En Brandvägg skyddar mot många OWASP översta 10 vanliga web säkerhetsriskerna.

## <a name="scenarios"></a>Scenarier

Den här artikeln beskriver två scenarier. I det första scenariot lär du dig hur du [skapa en Programgateway med en Brandvägg](#create-an-application-gateway-with-web-application-firewall). I det andra scenariot lär du dig hur du [lägga till en Brandvägg i en befintlig Programgateway](#add-web-application-firewall-to-an-existing-application-gateway).

![Exempel på ett scenario][scenario]

> [!NOTE]
> Du kan lägga till anpassade hälsoavsökningar, adresser backend-poolen och ytterligare regler programgatewayen. Dessa program konfigureras när programgatewayen har konfigurerats och inte under första distributionen.

## <a name="before-you-begin"></a>Innan du börjar

 En Programgateway kräver sin egen undernät. När du skapar ett virtuellt nätverk, se till att du lämnar tillräckligt med adressutrymme för att du har flera undernät. När du har distribuerat en Programgateway till ett undernät kan bara ytterligare programgatewayer läggas till undernätet.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Lägg till en brandvägg för webbaserade program i en befintlig Programgateway

Det här exemplet uppdaterar en befintlig Programgateway för att stödja en Brandvägg i **förebyggande** läge.

1. I Azure portal **Favoriter** väljer **alla resurser**. På den **alla resurser** bladet Välj befintliga programgatewayen. Om den prenumeration som du har valt redan har flera resurser i den, anger du namnet på den **filtrera efter namn** rutan att enkelt få åtkomst till DNS-zonen.

   ![Befintliga program gateway markeringen][1]

2. Välj **Brandvägg för webbaserade program**, och uppdatera gatewayen programinställningar. När uppdateringen är klar väljer du **spara**. 

3. Använd följande inställningar för att uppdatera en befintlig Programgateway för att stödja en Brandvägg:

   | **Inställning** | **Värde** | **Detaljer**
   |---|---|---|
   |**Uppgradera till Brandvägg nivå**| Markerad | Det här alternativet anger nivån för Programgateway Brandvägg-nivån.|
   |**Status för brandväggen**| Enabled | Den här inställningen aktiverar brandväggen på Brandvägg.|
   |**Brandväggsläge** | Prevention (Skydd) | Den här inställningen är hur en Brandvägg behandlar skadlig trafik. **Identifiering av** läge endast loggar händelser. **Förebyggande** läge loggar händelser och stoppar skadlig trafik.|
   |**Regeluppsättning**|3.0|Den här inställningen avgör den [core regeluppsättning](application-gateway-web-application-firewall-overview.md#core-rule-sets) som används för att skydda medlemmarna backend-adresspool.|
   |**Konfigurera inaktiverade regler**|Det varierar|För att hindra möjliga falska positiva identifieringar kan du använda den här inställningen för att inaktivera vissa [regler och regelgrupper](application-gateway-crs-rulegroups-rules.md).|

    >[!NOTE]
    > När du uppgraderar en befintlig Programgateway till Brandvägg SKU SKU-storleken ändras till **medel**. När konfigurationen är klar kan du konfigurera om den här inställningen.

    ![Grundläggande inställningar][2-1]

    > [!NOTE]
    > Visa Brandvägg loggar, aktivera diagnostik och välj **ApplicationGatewayFirewallLog**. Välj ett instansantal på **1** endast avsett för testning. Vi rekommenderar inte instansantalet under **2** eftersom det inte omfattas av SLA. Liten gateways är inte tillgängliga när du använder en Brandvägg.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Skapa en Programgateway med en brandvägg för webbaserade program

Det här scenariot kommer:

* Skapa en medelhög Brandvägg Programgateway med två instanser.
* Skapa ett virtuellt nätverk med namnet AdatumAppGatewayVNET med en 10.0.0.0/16 reserverade CIDR-blocket.
* Skapa undernätet Appgatewaysubnet som använder 10.0.0.0/28 som dess CIDR-block.
* Konfigurera ett certifikat för SSL-avlastning.

1. Logga in på [Azure Portal](https://portal.azure.com). Om du inte redan har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/free).

2. I den **Favoriter** rutan på portalen väljer **ny**.

3. På den **ny** bladet väljer **nätverk**. På den **nätverk** bladet väljer **Programgateway**, enligt följande bild:

    ![Skapa för program-gateway][1]

4. På den **grunderna** bladet som visas, ange följande värden och välj sedan **OK**:

   | **Inställning** | **Värde** | **Detaljer**
   |---|---|---|
   |**Namn**|AdatumAppGateway|Namnet på programgatewayen.|
   |**Nivå**|WAF|Tillgängliga värden är Standard och Brandvägg. Mer information om en Brandvägg finns [Brandvägg för webbaserade program](application-gateway-web-application-firewall-overview.md).|
   |**SKU-storleken**|Medel|Standardnivån alternativ är **små**, **medel**, och **stor**. Brandvägg nivå alternativ är **medel** och **stor** endast.|
   |**Instansantal**|2|Antal instanser av programgatewayen för hög tillgänglighet. Använd antalet instanser av 1 endast för testning.|
   |**Prenumeration**|[Din prenumeration]|Välj en prenumeration för att skapa programgatewayen.|
   |**Resursgrupp**|**Skapa en ny:** AdatumAppGatewayRG|Skapa en resursgrupp. Resursgruppens namn måste vara unikt inom den prenumeration du valde. Mer information om resursgrupper finns i [översikten över Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups).|
   |**Plats**|Västra USA||

   ![Inställningar för grundläggande konfiguration][2-2]

5. På den **inställningar** bladet som visas under **för virtuella nätverk**väljer **Välj ett virtuellt nätverk**. På den **Välj virtuellt nätverk** bladet väljer **Skapa nytt**.

   ![Val av virtuellt nätverk][2]

6. På den **skapa virtuellt nätverk-bladet**, ange följande värden och välj sedan **OK**. Den **undernät** på den **inställningar** bladet fylls med undernät som du har valt.

   |**Inställning** | **Värde** | **Detaljer** |
   |---|---|---|
   |**Namn**|AdatumAppGatewayVNET|Namnet på programgatewayen.|
   |**Adressutrymme**|10.0.0.0/16| Det här värdet är adressutrymmet för det virtuella nätverket.|
   |**Namn på undernät**|AppGatewaySubnet|Namnet på undernätet för programgatewayen.|
   |**Adressintervall för undernätet**|10.0.0.0/28 | Det här undernätet tillåter flera undernät i det virtuella nätverket för backend-medlemmar.|

7. På den **inställningar** bladet under **Frontend-IP-konfiguration**väljer **offentliga** som den **IP-adresstypen**.

8. På den **inställningar** bladet under **offentliga IP-adressen**väljer **Välj en offentlig IP-adress**. På den **Välj offentlig IP-adress** bladet väljer **Skapa nytt**.

   ![Val av offentliga IP-adress][3]

9. På den **skapa offentlig IP-adress** bladet godkänner du standardvärdet och välj **OK**. Den **offentliga IP-adressen** fylls med den offentliga IP-adressen som du har valt.

10. På den **inställningar** bladet under **Lyssnarkonfigurationen**väljer **HTTP** under **protokollet**. Ett certifikat krävs för att använda **HTTPS**. Den privata nyckeln för certifikatet som krävs. Ange en PFX-export av certifikatet och ange lösenordet för filen.

11. Konfigurera inställningar för den **Brandvägg**.

   |**Inställning** | **Värde** | **Detaljer** |
   |---|---|---|
   |**Status för brandväggen**| Enabled| Den här inställningen inaktiverar Brandvägg eller inaktivera.|
   |**Brandväggsläge** | Prevention (Skydd)| Den här inställningen avgör vilka åtgärder som Brandvägg tar på sig skadlig trafik. **Identifiering av** läge loggar endast trafik. **Förebyggande** läge loggar och stoppar trafik med ett 403 obehörig svar.|


12. Granska de **sammanfattning** och väljer **OK**. Programgatewayen är nu i kö och skapas.

13. Efter gateway har skapats, gå till den i portalen för att fortsätta konfigurationen av programgatewayen.

    ![Programvy gateway resurs][10]

Dessa steg skapar en basic-Programgateway med standardinställningarna för lyssnare, backend-adresspool, backend-HTTP-inställningar och regler. Efter att etablering är slutförd, kan du ändra dessa inställningar så att de passar din distribution.

> [!NOTE]
> Programgatewayer som skapats med den grundläggande konfigurationen av Brandvägg har konfigurerats med CR 3.0 för skydd.

## <a name="next-steps"></a>Nästa steg

Så här konfigurerar du en anpassad domän alias för den [offentliga IP-adressen](../dns/dns-custom-domain.md#public-ip-address), du kan använda Azure DNS eller en annan DNS-leverantör.

För att konfigurera diagnostik loggning för att logga händelser som upptäckts eller förhindras med Brandvägg, se [Programgateway diagnostik](application-gateway-diagnostics.md).

För att skapa anpassade hälsoavsökningar, se [skapa en anpassad hälsoavsökningen](application-gateway-create-probe-portal.md).

För att konfigurera SSL-avlastning och dra kostsamma SSL prenumerationen av webbservrar, se [Konfigurera SSL-avlastning](application-gateway-ssl-portal.md).

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png
