---
title: Skapa en Programgateway - Azure-portalen | Microsoft Docs
description: "Lär dig hur du skapar en Programgateway med hjälp av portalen"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 54dffe95-d802-4f86-9e2e-293f49bd1e06
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 17d09ce98c40717d1db0927f791a7c97ea7835e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-with-the-portal"></a>Skapa en Programgateway med portalen

[Programgateway](application-gateway-introduction.md) är en dedikerad virtuell installation som ger programmet leverans domänkontrollant (ADC) som en tjänst som erbjuder olika layer 7 belastningsutjämning för ditt program. Den här artikeln tar dig igenom stegen för att skapa en Programgateway med Azure-portalen och lägga till en befintlig server som en backend-medlem.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [http://portal.azure.com](http://portal.azure.com)

## <a name="create-application-gateway"></a>Skapa Programgateway

Om du vill skapa en Programgateway instruktionerna som följer. Programgateway kräver sin egen undernät. När du skapar ett virtuellt nätverk, se till att du lämnar tillräckligt med adressutrymme för att du har flera undernät. När programgatewayen har distribuerats till ett undernät, kan andra programgatewayer läggas till.

1. I fönstret Favoriter av portalen klickar du på **ny**
1. Klicka på **Nätverk** i bladet **Nytt**. I den **nätverk** bladet, klickar du på **Programgateway**, enligt följande bild:

    ![Skapa Programgateway][1]

1. I den **grunderna** bladet som visas, ange följande värden, och klicka sedan på **OK**:

   | **Inställning** | **Värde** | **Detaljer**|
   |---|---|---|
   |**Namn**|AdatumAppGateway|Namnet på programgatewayen|
   |**Nivå**|Standard|Tillgängliga värden är Standard och Brandvägg. Besök [Brandvägg för webbaserade program](application-gateway-web-application-firewall-overview.md) för mer information om Brandvägg.|
   |**SKU-storleken**|Medel|Alternativ när du väljer standardnivån är små, medelstora och stora. När du väljer Brandvägg nivå är alternativen medel och stor bara.|
   |**Instansantal**|2|Antal instanser av programgatewayen för hög tillgänglighet. Antalet instanser av 1 bör endast användas för testning.|
   |**Prenumeration**|[Din prenumeration]|Välj den prenumeration där du vill skapa programgatewayen.|
   |**Resursgrupp**|**Skapa en ny:** AdatumAppGatewayRG|Skapa en resursgrupp. Resursgruppens namn måste vara unikt inom den prenumeration du valde. Mer information om resursgrupper finns i [översikten över Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups).|
   |**Plats**|Västra USA||

1. I den **inställningar** bladet som visas under **för virtuella nätverk**, klickar du på **Välj ett virtuellt nätverk**. Den **Välj virtuellt nätverk** blad öppnas.  Klicka på **Skapa nytt** att öppna den **skapa virtuellt nätverk** bladet.

   ![Välj ett virtuellt nätverk][2]

1. På den **skapa virtuellt nätverk-bladet** ange följande värden och klicka sedan på **OK**. Den **skapa virtuellt nätverk** och **Välj virtuellt nätverk** blad stängs. Det här steget fyller i **undernät** på den **inställningar** bladet med undernätet valt.

   | **Inställning** | **Värde** | **Detaljer**|
   |---|---|---|
   |**Namn**|AdatumAppGatewayVNET|Namnet på programmet-gateway|
   |**Adressutrymmet**|10.0.0.0/16|Detta är adressutrymmet för det virtuella nätverket|
   |**Namn på undernät**|AppGatewaySubnet|Namnet på undernätet för Programgateway|
   |**Adressintervall för undernätet**|10.0.0.0/28|Det här undernätet tillåter flera undernät i det virtuella nätverket för medlemmar i serverdelen|

1. På den **inställningar** bladet under **Frontend-IP-konfiguration**, Välj **offentliga** som den **IP-adresstyp**

1. På den **inställningar** bladet under **offentliga IP-adressen** klickar du på **Välj en offentlig IP-adress**, **Välj offentlig IP-adress** blad öppnas Klicka på **Skapa nytt**.

   ![Välj offentlig IP-adress][3]

1. På den **skapa offentlig IP-adress** bladet godkänner du standardvärdet och klicka på **OK**. Bladet stängs och fyller den **offentliga IP-adressen** med offentliga IP-adressen är valt.

1. På den **inställningar** bladet under **Lyssnarkonfigurationen**, klickar du på **HTTP** under **protokollet**. Ange porten som ska användas i den **Port** fältet.

2. Klicka på **OK** på den **inställningar** bladet för att fortsätta.

1. Granska inställningarna på den **sammanfattning** bladet och klicka på **OK** att starta skapandet av programgatewayen. Skapa en Programgateway är en tidskrävande uppgift och tar tid att slutföra.

## <a name="add-servers-to-backend-pools"></a>Lägga till servrar till serverdelspooler

När du har skapat Programgateway system som är värdar för att programmet ska vara belastningsutjämnade fortfarande behöver läggas till i programgatewayen. IP-adresser, FQDN eller nätverkskort av servrarna läggs till backend-adresspool.

### <a name="ip-address-or-fqdn"></a>IP-adress eller FQDN

1. Med Programgateway som skapats i Azure portal **Favoriter** rutan klickar du på **alla resurser**. Klicka på den **AdatumAppGateway** Programgateway i bladet för alla resurser. Om den prenumeration som du har valt redan har flera resurser i den, kan du ange **AdatumAppGateway** i den **filtrera efter namn...** för att enkelt få åtkomst till Application Gateway.

1. Programgateway som du har skapat visas. Klicka på **serverdelspooler**, och välj den aktuella serverdelspoolen **appGatewayBackendPool**, **appGatewayBackendPool** blad öppnas.

   ![Programpooler Gateway backend][4]

1. Klicka på **lägga till målet** att lägga till IP-adresser till FQDN-värden. Välj **IP-adress eller FQDN** som den **typen** och ange din IP-adress eller FQDN i fältet. Upprepa det här steget för ytterligare backend poolmedlemmar. När du är klar klickar du på **spara**.

### <a name="virtual-machine-and-nic"></a>Den virtuella datorn och nätverkskortet

Du kan också lägga till virtuella nätverkskort som medlemmar i serverdelen. Endast virtuella datorer i samma virtuella nätverk som Programgatewayen är tillgängliga via den nedrullningsbara listrutan.

1. Med Programgateway som skapats i Azure portal **Favoriter** rutan klickar du på **alla resurser**. Klicka på den **AdatumAppGateway** Programgateway i bladet för alla resurser. Om den prenumeration som du har valt redan har flera resurser i den, kan du ange **AdatumAppGateway** i den **filtrera efter namn...** för att enkelt få åtkomst till Application Gateway.

1. Programgateway som du har skapat visas. Klicka på **serverdelspooler**, och välj den aktuella serverdelspoolen **appGatewayBackendPool**, **appGatewayBackendPool** blad öppnas.

   ![Programpooler Gateway backend][5]

1. Klicka på **lägga till målet** att lägga till IP-adresser till FQDN-värden. Välj **virtuella** som den **typen** och välj den virtuella datorn och nätverkskortet till att använda. När du är klar klickar du på **spara**

   > [!NOTE]
   > Endast virtuella datorer i samma virtuella nätverk som programgatewayen är tillgängliga i nedrullningsbara listrutan.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen och Programgateway alla relaterade resurser när de inte längre behövs. Om du vill göra det, Välj resursgruppen programmet nätverksgateway-bladet och klicka på **ta bort**.

## <a name="next-steps"></a>Nästa steg

I detta scenario distribueras en Programgateway och lagt till en server till serverdelen. Nästa steg är att konfigurera programgatewayen genom att ändra inställningarna och justera regler i gatewayen. De här stegen finns genom att besöka följande artiklar:

Lär dig hur du skapar anpassade hälsoavsökningar genom att besöka [skapa en anpassad hälsoavsökningen](application-gateway-create-probe-portal.md)

Lär dig hur du konfigurerar SSL-avlastning och ta kostsamma SSL-dekryptering av webbservrar genom att besöka [Konfigurera SSL-avlastning](application-gateway-ssl-portal.md)

Lär dig att skydda dina program med [Brandvägg för webbaserade program](application-gateway-webapplicationfirewall-overview.md) en funktion i Programgateway.

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png
