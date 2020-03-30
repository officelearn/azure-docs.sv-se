---
title: Offentlig slutpunktsanslutning för virtuella Azure-datorer&Standard ILB i SAP HA-scenarier
description: Offentlig slutpunktsanslutning för virtuella datorer med Azure Standard Load Balancer i SAP-scenarier med hög tillgänglighet
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: radeltch
ms.openlocfilehash: 4fd01764c183098a8bd78d502eea7ab173fa22cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293905"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Offentlig slutpunktsanslutning för virtuella datorer med Azure Standard Load Balancer i SAP-scenarier med hög tillgänglighet

Artikelns omfattning är att beskriva konfigurationer, som aktiverar utgående anslutning till offentliga slutpunkter. Konfigurationerna är främst i samband med hög tillgänglighet med Pacemaker för SUSE / RHEL.  

Om du använder Pacemaker med Azure-stängselagent i din lösning med hög tillgänglighet måste de virtuella datorerna ha utgående anslutning till Azure-hanterings-API:et.  
Artikeln innehåller flera alternativ som gör att du kan välja det alternativ som passar bäst för ditt scenario.  

## <a name="overview"></a>Översikt

När du implementerar hög tillgänglighet för SAP-lösningar via klustring är en av de nödvändiga komponenterna [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Azure erbjuder två belastningsutjämna sku:er: standard och grundläggande.

Standard Azure belastningsutjämnare erbjuder vissa fördelar jämfört med Basic belastningsutjämnare. Till exempel fungerar det i Azure Tillgänglighetszoner, det har bättre övervakning och loggning funktioner för enklare felsökning, minskad latens. Funktionen HA-portar omfattar alla portar, det vill än om det inte längre är nödvändigt att lista alla enskilda portar.  

Det finns några viktiga skillnader mellan den grundläggande och standard SKU för Azure belastningsutjämning. En av dem är hanteringen av utgående trafik till den offentliga slutpunkten. För fullständig jämförelse av Basic jämfört med standard SKU-belastningsutjämnare finns i [Jämförelse med belastningsutjämnare SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).  
 
När virtuella datorer utan offentliga IP-adresser placeras i serverdelspoolen för intern (ingen offentlig IP-adress) Standard Azure load balancer, finns det ingen utgående anslutning till offentliga slutpunkter, såvida inte ytterligare konfiguration görs.  

Om en virtuell dator tilldelas en offentlig IP-adress, eller om den virtuella datorn är i serverdelspoolen för en belastningsutjämnare med offentlig IP-adress, kommer den att ha utgående anslutning till offentliga slutpunkter.  

SAP-system innehåller ofta känsliga affärsdata. Det är sällan acceptabelt för virtuella datorer som är värdar för SAP-system att ha offentliga IP-adresser. Samtidigt finns det scenarier, vilket skulle kräva utgående anslutning från den virtuella datorn till offentliga slutpunkter.  

Exempel på scenarier som kräver åtkomst till Offentliga Azure-slutpunkter är:  
- Använda Azure Fence Agent som stängselmekanism i Pacemaker-kluster
- Azure Backup
- Azure Site Recovery  
- Använda offentliga databaser för att korrigera operativsystemet
- SAP-programmets dataflöde kan kräva utgående anslutning till den offentliga slutpunkten

Om din SAP-distribution inte kräver utgående anslutning till offentliga slutpunkter behöver du inte implementera den ytterligare konfigurationen. Det räcker att skapa interna standard SKU Azure Load Balancer för ditt scenario med hög tillgänglighet, förutsatt att det inte heller finns något behov av inkommande anslutning från offentliga slutpunkter.  

> [!Note]
> När virtuella datorer utan offentliga IP-adresser placeras i serverdelspoolen för intern (ingen offentlig IP-adress) Standard Azure load balancer, kommer det inte att finnas någon utgående internetanslutning, såvida inte ytterligare konfiguration utförs för att tillåta routning till offentliga slutpunkter.  
>Om de virtuella datorerna har antingen offentliga IP-adresser eller redan finns i serverdelspoolen för Azure Load Balancer med offentlig IP-adress, har den virtuella datorn redan utgående anslutning till offentliga slutpunkter.


Läs följande uppsatser först:

* Azure Standard belastningsutjämning
  * [Översikt över Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) – omfattande översikt över Azure Standard Load Balancer, viktiga principer, begrepp och självstudier 
  * [Utgående anslutningar i Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios) – scenarier för hur du uppnår utgående anslutning i Azure
  * [Utgående regler för belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)– förklarar begreppen utgående regler för belastningsutjämnare och hur du skapar utgående regler
* Azure Firewall
  * [Översikt över Azure-brandväggen](https://docs.microsoft.com/azure/firewall/overview)– översikt över Azure-brandväggen
  * [Självstudiekurs: Distribuera och konfigurera Azure-brandväggen](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) – instruktioner om hur du konfigurerar Azure-brandväggen via Azure-portalen
* [Virtuella nätverk - Användardefinierade regler](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) - Azure-routningsbegrepp och regler  
* [Servicetaggar för säkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) – så här förenklar du nätverkssäkerhetsgrupper och brandväggskonfiguration med tjänsttaggar

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Ytterligare extern Azure Standard Load Balancer för utgående anslutningar till internet

Ett alternativ för att uppnå utgående anslutning till offentliga slutpunkter, utan att tillåta inkommande anslutning till den virtuella datorn från den offentliga slutpunkten, är att skapa en andra belastningsutjämnare med offentlig IP-adress, lägga till virtuella datorer i serverdelspoolen för den andra belastningsutjämnaren och definiera endast [utgående regler](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).  
Använd [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview) för att styra de offentliga slutpunkterna, som är tillgängliga för utgående samtal från den virtuella datorn.  
Mer information finns i Scenario 2 i dokument [Utgående anslutningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios).  
Konfigurationen skulle se ut så här:  

![Styra anslutningen till offentliga slutpunkter med nätverkssäkerhetsgrupper](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Att tänka på

- Du kan använda ytterligare en offentlig belastningsutjämnare för flera virtuella datorer i samma undernät för att uppnå utgående anslutning till offentlig slutpunkt och optimera kostnaden  
- Använd [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview) för att styra vilka offentliga slutpunkter som är tillgängliga från de virtuella datorerna. Du kan tilldela nätverkssäkerhetsgruppen antingen till undernätet eller till varje virtuell dator. Om möjligt kan du använda [tjänsttaggar](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) för att minska komplexiteten i säkerhetsreglerna.  
- Azure standard belastningsutjämning med offentlig IP-adress och utgående regler tillåter direkt åtkomst till offentlig slutpunkt. Om du har säkerhetskrav för att alla utgående trafik ska passera via centraliserad företagslösning för granskning och loggning kanske du inte kan uppfylla kravet med det här scenariot.  

>[!TIP]
>Använd om möjligt [tjänsttaggar](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) för att minska komplexiteten i nätverkssäkerhetsgruppen . 

### <a name="deployment-steps"></a>Distributionssteg

1. Skapa belastningsutjämnare  
   1. Klicka på Alla resurser i [Azure-portalen,](https://portal.azure.com) Lägg till och sök sedan efter **belastningsutjämnare**  
   1. Klicka på **Skapa** 
   1. Belastningsbalanser Namn **MyPublicILB**  
   1. Välj **Offentlig** som en typ, **Standard** som SKU  
   1. Välj **Skapa offentlig IP-adress** och ange som ett namn **MyPublicILBFrondEndIP**  
   1. Välj **zon redundant** som tillgänglighetszon  
   1. Klicka på Granska och skapa och sedan på Skapa  
2. Skapa Backend pool **MyBackendPoolOfPublicILB** och lägg till virtuella datorer.  
   1. Välj det virtuella nätverket  
   1. Markera de virtuella datorerna och deras IP-adresser och lägg till dem i serverdapoolen  
3. [Skapa utgående regler](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-cli#create-outbound-rule). För närvarande är det inte möjligt att skapa utgående regler från Azure-portalen. Du kan skapa utgående regler med [Azure CLI](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Skapa nätverkssäkerhetsgruppsregler för att begränsa åtkomsten till specifika offentliga slutpunkter. Om det finns en befintlig nätverkssäkerhetsgrupp kan du justera den. Exemplet nedan visar hur du aktiverar åtkomst till Azure-hanterings-API: 
   1. Navigera till nätverkssäkerhetsgruppen
   1. Klicka på Utgående säkerhetsregler
   1. Lägg till en regel för att **neka** all utgående åtkomst till **Internet**.
   1. Lägg till en regel för att **tillåta** åtkomst till **AzureCloud**, med prioritet lägre än prioriteten för regeln att neka all internetåtkomst.


   De utgående säkerhetsreglerna skulle se ut så här: 

   ![Utgående anslutning med andra belastningsutjämnaren med offentlig IP](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Mer information om Azure Network-säkerhetsgrupper finns i [Säkerhetsgrupper ](https://docs.microsoft.com/azure/virtual-network/security-overview). 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>Azure-brandvägg för utgående anslutningar till internet

Ett annat alternativ för att uppnå utgående anslutning till offentliga slutpunkter, utan att tillåta inkommande anslutning till den virtuella datorn från offentliga slutpunkter, är med Azure-brandväggen. Azure Firewall är en hanterad tjänst med inbyggd hög tillgänglighet och den kan sträcka sig över flera tillgänglighetszoner.  
Du måste också distribuera [användardefinierad väg](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes), associerad med undernät där virtuella datorer och Azure-belastningsutjämnaren distribueras, pekar på Azure-brandväggen, för att dirigera trafik genom Azure-brandväggen.  
Mer information om hur du distribuerar Azure-brandväggen finns i [Distribuera och konfigurera Azure-brandväggen](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal).  

Arkitekturen skulle se ut så här:

![Utgående anslutning med Azure-brandväggen](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Att tänka på

- Azure-brandväggen är inbyggd molntjänst, med inbyggd hög tillgänglighet och stöder zondistribution.
- Kräver ytterligare undernät som måste heta AzureFirewallSubnet. 
- Om överföring av stora data anger utgående av det virtuella nätverket där virtuella SAP-datorer finns, till en virtuell dator i ett annat virtuellt nätverk eller till offentlig slutpunkt, kanske det inte är kostnadseffektiv lösning. Ett sådant exempel är att kopiera stora säkerhetskopior över virtuella nätverk. Mer information finns i prissättningen för Azure-brandväggen.  
- Om företagets brandväggslösning inte är Azure-brandväggen och du har säkerhetskrav för att ha alla utgående trafikpass men centraliserad företagslösning, kanske den här lösningen inte är praktisk.  

>[!TIP]
>Om möjligt kan du använda [tjänsttaggar](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) för att minska komplexiteten i Azure-brandväggsreglerna.  

### <a name="deployment-steps"></a>Distributionssteg

1. Distributionsstegen förutsätter att du redan har virtuellt nätverk och undernät definierat för dina virtuella datorer.  
2. Skapa **undernätEt AzureFirewallSubnet** i samma virtuella nätverk, där VMS och Standard Load Balancer distribueras.  
   1. Navigera till det virtuella nätverket i Azure-portalen: Klicka på Alla resurser, Sök efter det virtuella nätverket, Klicka på det virtuella nätverket, Välj undernät.  
   1. Klicka på Lägg till undernät. Ange **AzureFirewallSubnet** som namn. Ange lämpligt adressintervall. Spara.  
3. Skapa Azure-brandväggen.  
   1. I Azure-portalen väljer du Alla resurser, klickar på Lägg till, Brandvägg, Skapa. Välj Resursgrupp (välj samma resursgrupp, där det virtuella nätverket finns).  
   1. Ange namn för Azure Firewall-resursen. Till **exempel, MyAzureFirewall**.  
   1. Välj Region och välj minst två tillgänglighetszoner, i linje med de tillgänglighetszoner där dina virtuella datorer distribueras.  
   1. Välj ditt virtuella nätverk, där virtuella SAP-datorer och Azure Standard Load Balancer distribueras.  
   1. Offentlig IP-adress: Klicka på skapa och ange ett namn. Till exempel **MyFirewallPublicIP**.  
4. Skapa Azure-brandväggsregel för att tillåta utgående anslutning till angivna offentliga slutpunkter. Exemplet visar hur du tillåter åtkomst till den offentliga slutpunkten för Azure Management API.  
   1. Välj Regler, Samling nätverksregel och klicka sedan på Lägg till nätverksregelsamling.  
   1. Namn: **MyOutboundRule**, ange Prioritet, Välj Åtgärd **Tillåt**.  
   1. Tjänst: Namn **ToAzureAPI**.  Protokoll: Välj **valfritt**. Källadress: Ange intervallet för ditt undernät, där de virtuella datorerna och standardbelastningsutjämnaren distribueras till exempel: **11.97.0.0/24**. Målportar: <b>*</b>ange .  
   1. Spara
   1. När du fortfarande är placerad i Azure-brandväggen väljer du Översikt. Anm.down the Private IP Address of the Azure Firewall.  
5. Skapa väg till Azure-brandväggen  
   1. Välj Alla resurser i Azure-portalen och klicka sedan på Lägg till, Rutttabell, Skapa.  
   1. Ange Namn MyRouteTable, välj Prenumeration, Resursgrupp och Plats (som matchar platsen för det virtuella nätverket och brandväggen).  
   1. Spara  

   Brandväggsregeln skulle ![se ut: Utgående anslutning med Azure-brandväggen](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Skapa användardefinierad väg från undernätet för dina virtuella datorer till den privata IP-adressen **för MyAzureFirewall**.
   1. När du är placerad i rutttabellen klickar du på Rutter. Välj Lägg till. 
   1. Ruttnamn: ToMyAzureFirewall, Adressprefix: **0.0.0.0/0**. Nästa hopptyp: Välj Virtuell installation. Nästa hopadress: Ange den privata IP-adressen för brandväggen som du konfigurerat: **11.97.1.4**.  
   1. Spara

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Använda Proxy för Pacemaker-anrop till Azure Management API

Du kan använda proxy för att tillåta Pacemaker-anrop till Azure management API offentliga slutpunkten.  

### <a name="important-considerations"></a>Att tänka på

  - Om det redan finns företagsproxy på plats kan du dirigera utgående samtal till offentliga slutpunkter genom den. Utgående samtal till offentliga slutpunkter går via företagets kontrollpunkt.  
  - Kontrollera att proxykonfigurationen tillåter utgående anslutning till Azure-hanterings-API:`https://management.azure.com`  
  - Kontrollera att det finns en väg från de virtuella datorerna till proxyn  
  - Proxy hanterar endast HTTP/HTTPS-anrop. Om det finns ytterligare behov av att ringa utgående samtal till den offentliga slutpunkten över olika protokoll (som RFC), kommer alternativ lösning att behövas  
  - Proxy-lösningen måste vara mycket tillgänglig för att undvika instabilitet i Pacemaker-klustret  
  - Beroende på var proxyn finns kan den införa ytterligare svarstid i anrop från Azure Fence Agent till Azure Management API. Om din företagsproxy fortfarande är på plats, medan pacemakerklustret finns i Azure, mäter du svarstid och överväger, om den här lösningen är lämplig för dig  
  - Om det inte redan finns högtillgängliga företagsproxy på plats rekommenderar vi inte det här alternativet eftersom kunden skulle ådra sig extra kostnad och komplexitet. Om du ändå väljer att distribuera ytterligare proxylösning, i syfte att tillåta utgående anslutning från Pacemaker till Azure Management offentligt API, se till att proxyn är mycket tillgänglig och svarstiden från de virtuella datorerna till proxyn är låg.  

### <a name="pacemaker-configuration-with-proxy"></a>Pacemaker-konfiguration med proxy 

Det finns många olika Proxy alternativ som finns i branschen. Steg-för-steg-instruktioner för proxydistributionen ligger utanför dokumentets omfattning. I exemplet nedan antar vi att din proxy svarar på **MyProxyService** och lyssnar på porten **MyProxyPort**.  
Så här tillåter du att pacemakern kommunicerar med Azure-hanterings-API:et:  

1. Redigera pacemakerkonfigurationsfilen /etc/sysconfig/pacemaker och lägg till följande rader (alla klusternoder):

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. Starta om pacemakertjänsten på **alla** klusternoder.  
  - SUSE
 
     ```console
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  

     ```console
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du konfigurerar Pacemaker på SUSE i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
* [Lär dig hur du konfigurerar Pacemaker på Red Hat i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)
