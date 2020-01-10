---
title: Om nätverk i haveri beredskap för Azure VM med Azure Site Recovery
description: Innehåller en översikt över nätverk för replikering av virtuella Azure-datorer med hjälp av Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 1/8/2020
ms.author: sutalasi
ms.openlocfilehash: 9fe3b4c0b7acc9c1e980d5885043d30503c211c4
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754494"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>Om nätverk i haveri beredskap för virtuella Azure-datorer



Den här artikeln ger nätverks vägledning när du replikerar och återställer virtuella Azure-datorer från en region till en annan med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Innan du börjar

Lär dig hur Site Recovery ger haveri beredskap för [det här scenariot](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Typisk nätverks infrastruktur

Följande diagram illustrerar en typisk Azure-miljö för program som körs på virtuella Azure-datorer:

![kund-miljö](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Om du använder Azure-ExpressRoute eller en VPN-anslutning från ditt lokala nätverk till Azure är miljön följande:

![kund-miljö](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Normalt skyddas nätverk med brand väggar och nätverks säkerhets grupper (NSG: er). Brand väggar använder URL-eller IP-baserade vit listning för att kontrol lera nätverks anslutningen. NSG: er tillhandahåller regler som använder IP-adressintervall för att kontrol lera nätverks anslutningen.

>[!IMPORTANT]
> Att använda en autentiserad proxy för att kontrol lera nätverks anslutningen stöds inte av Site Recovery, och det går inte att aktivera replikering.


## <a name="outbound-connectivity-for-urls"></a>Utgående anslutning för webbadresser

Om du använder en URL-baserad brand Väggs-proxy för att kontrol lera utgående anslutningar kan du tillåta följande Site Recovery webb adresser:


**URL** | **Detaljer**  
--- | ---
*.blob.core.windows.net | Krävs så att data kan skrivas till cache-lagrings kontot i käll regionen från den virtuella datorn. Om du känner till alla cache-lagrings konton för dina virtuella datorer kan du tillåta åtkomst till de angivna URL: erna för lagrings konton (t. ex.: cache1.blob.core.windows.net och cache2.blob.core.windows.net) i stället för *. blob.core.windows.net
login.microsoftonline.com | Krävs för auktorisering och autentisering till Site Recovery tjänst-URL: er.
*.hypervrecoverymanager.windowsazure.com | Krävs så att kommunikationen mellan Site Recoverys tjänsten kan ske från den virtuella datorn. Du kan använda motsvarande "Site Recovery IP" om brand Väggs-proxyn stöder IP-adresser.
*.servicebus.windows.net | Krävs så att Site Recovery övervakning och diagnostikdata kan skrivas från den virtuella datorn. Du kan använda motsvarande "Site Recovery övervaknings-IP" om brand Väggs-proxyn har stöd för IP-adresser.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Utgående anslutning för IP-adressintervall

Om du använder en IP-baserad brand Väggs-proxy eller NSG för att kontrol lera utgående anslutningar, måste dessa IP-intervall vara tillåtna.

- Alla IP-adressintervall som motsvarar lagrings kontona i käll regionen
    - Skapa en NSG-regel för [lagrings tjänst](../virtual-network/security-overview.md#service-tags) som är baserad på käll regionen.
    - Tillåt dessa adresser så att data kan skrivas till cache-lagrings kontot från den virtuella datorn.
- Skapa en [Azure Active Directory (AAD) tjänsttagg](../virtual-network/security-overview.md#service-tags) baserat NSG-regel för att tillåta åtkomst till alla IP-adresser för AAD
    - Om nya adresser läggs till Azure Active Directory (AAD) i framtiden, måste du skapa nya NSG-regler.
- Skapa en EventsHub service tag-baserad NSG-regel för mål regionen, vilket ger åtkomst till Site Recovery övervakning.
- Skapa en AzureSiteRecovery service tag-baserad NSG-regel för att tillåta åtkomst till Site Recovery tjänst i vilken region som helst.
- Vi rekommenderar att du skapar de nödvändiga NSG-reglerna på en test-NSG och kontrollerar att det inte finns några problem innan du skapar reglerna på en produktions NSG.


Om du föredrar att använda Site Recovery IP-adressintervall (rekommenderas inte) kan du läsa tabellen nedan:

   **Mål** | **Site Recovery IP** |  **Site Recovery övervakning av IP**
   --- | --- | ---
   Asien, östra | 52.175.17.132 | 13.94.47.61
   Asien, sydöstra | 52.187.58.193 | 13.76.179.223
   Indien, centrala | 52.172.187.37 | 104.211.98.185
   Indien, södra | 52.172.46.220 | 104.211.224.190
   USA, norra centrala | 23.96.195.247 | 168.62.249.226
   Europa, norra | 40.69.212.238 | 52.169.18.8
   Europa, västra | 52.166.13.64 | 40.68.93.145
   USA, östra | 13.82.88.226 | 104.45.147.24
   USA, västra | 40.83.179.48 | 104.40.26.199
   USA, södra centrala | 13.84.148.14 | 104.210.146.250
   USA, centrala | 40.69.144.231 | 52.165.34.144
   USA, östra 2 | 52.184.158.163 | 40.79.44.59
   Japan, östra | 52.185.150.140 | 138.91.1.105
   Japan, västra | 52.175.146.69 | 138.91.17.38
   Brasilien, södra | 191.234.185.172 | 23.97.97.36
   Australien, östra | 104.210.113.114 | 191.239.64.144
   Australien, sydöstra | 13.70.159.158 | 191.239.160.45
   Kanada, centrala | 52.228.36.192 | 40.85.226.62
   Kanada, östra | 52.229.125.98 | 40.86.225.142
   USA, västra centrala | 52.161.20.168 | 13.78.149.209
   USA, västra 2 | 52.183.45.166 | 13.66.228.204
   Storbritannien, västra | 51.141.3.203 | 51.141.14.113
   Storbritannien, södra | 51.140.43.158 | 51.140.189.52
   Storbritannien, södra 2 | 13.87.37.4| 13.87.34.139
   Storbritannien, norra | 51.142.209.167 | 13.87.102.68
   Sydkorea, centrala | 52.231.28.253 | 52.231.32.85
   Sydkorea, södra | 52.231.198.185 | 52.231.200.144
   Frankrike, centrala | 52.143.138.106 | 52.143.136.55
   Frankrike, södra | 52.136.139.227 |52.136.136.62
   Australien, centrala| 20.36.34.70 | 20.36.46.142
   Australien, centrala 2| 20.36.69.62 | 20.36.74.130
   Sydafrika, västra | 102.133.72.51 | 102.133.26.128
   Sydafrika, norra | 102.133.160.44 | 102.133.154.128
   USA Gov Virginia | 52.227.178.114 | 23.97.0.197
   US Gov, Iowa | 13.72.184.23 | 23.97.16.186
   Arizona (USA-förvaltad region) | 52.244.205.45 | 52.244.48.85
   Texas (USA-förvaltad region) | 52.238.119.218 | 52.238.116.60
   USA DoD, östra | 52.181.164.103 | 52.181.162.129
   US DoD, centrala | 52.182.95.237 | 52.182.90.133
   Kina, norra | 40.125.202.254 | 42.159.4.151
   Kina, norra 2 | 40.73.35.193 | 40.73.33.230
   Kina, östra | 42.159.205.45 | 42.159.132.40
   Kina, östra 2 | 40.73.118.52| 40.73.100.125
   Tyskland, norra| 51.116.208.58| 51.116.58.128
   Tyskland, västra centrala | 51.116.156.176 | 51.116.154.192
   Schweiz, västra | 51.107.231.223| 51.107.154.128
   Schweiz, norra | 51.107.68.31| 51.107.58.128
   Norge, östra | 51.120.100.64| 51.120.98.128
   Norge, västra | 51.120.220.65| 51.120.218.160

## <a name="example-nsg-configuration"></a>Exempel på NSG-konfiguration

Det här exemplet visar hur du konfigurerar NSG-regler för en virtuell dator att replikera.

- Om du använder NSG-regler för att kontrol lera utgående anslutningar använder du reglerna "Tillåt HTTPS utgående" till port: 443 för alla nödvändiga IP-adressintervall.
- Exemplet förutsätter att käll platsen för den virtuella datorn är "USA, östra" och att mål platsen är "Central US".

### <a name="nsg-rules---east-us"></a>NSG-regler – USA, östra

1. Skapa en utgående HTTPS-säkerhetsregel (443) för "Storage. öst" på NSG som visas på skärm bilden nedan.

      ![lagrings tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Skapa en utgående HTTPS (443) säkerhets regel för "AzureActiveDirectory" på NSG som visas på skärm bilden nedan.

      ![AAD-tagg](./media/azure-to-azure-about-networking/aad-tag.png)

3. På samma sätt som säkerhets regler, skapar du en utgående HTTPS (443) säkerhets regel för "EventHub. Central" på den NSG som motsvarar mål platsen. Detta ger åtkomst till Site Recovery övervakning.

4. Skapa en utgående HTTPS (443) säkerhets regel för "AzureSiteRecovery" på NSG. Detta ger till gång till Site Recovery tjänst i vilken region som helst.

### <a name="nsg-rules---central-us"></a>NSG-regler – centrala USA

De här reglerna krävs för att replikeringen ska kunna aktive ras från mål regionen till käll regionen efter redundans:

1. Skapa en utgående HTTPS-säkerhets regel (443) för "Storage. Central" på NSG.

2. Skapa en utgående HTTPS (443) säkerhets regel för "AzureActiveDirectory" på NSG.

3. På samma sätt som säkerhets regler, skapar du en utgående HTTPS (443) säkerhets regel för "EventHub. East" på den NSG som motsvarar käll platsen. Detta ger åtkomst till Site Recovery övervakning.

4. Skapa en utgående HTTPS (443) säkerhets regel för "AzureSiteRecovery" på NSG. Detta ger till gång till Site Recovery tjänst i vilken region som helst.

## <a name="network-virtual-appliance-configuration"></a>Konfiguration av virtuell nätverks installation

Om du använder virtuella nätverks installationer (NVA) för att styra utgående nätverks trafik från virtuella datorer kan installationen få en begränsning om all replikeringstrafik passerar genom NVA. Vi rekommenderar att du skapar en nätverks tjänst slut punkt i ditt virtuella nätverk för "lagring" så att replikeringstrafiken inte går till NVA.

### <a name="create-network-service-endpoint-for-storage"></a>Skapa nätverks tjänstens slut punkt för lagring
Du kan skapa en nätverks tjänst slut punkt i ditt virtuella nätverk för "lagring" så att replikeringstrafiken inte lämnar Azure-gränser.

- Välj ditt virtuella Azure-nätverk och klicka på tjänstens slut punkter

    ![lagring – slut punkt](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Klicka på fliken Lägg till och Lägg till tjänst slut punkter öppnas
- Välj Microsoft. Storage under "tjänst" och de undernät som krävs under fältet "undernät" och klicka på "Lägg till"

>[!NOTE]
>Begränsa inte åtkomst till virtuella nätverk till dina lagrings konton som används för ASR. Du bör tillåta åtkomst från alla nätverk

### <a name="forced-tunneling"></a>Forcerade tunnlar

Du kan åsidosätta Azures standard system väg för adressprefixet 0.0.0.0/0 med en [anpassad väg och dirigera](../virtual-network/virtual-networks-udr-overview.md#custom-routes) VM-trafik till en lokal virtuell nätverks installation (NVA), men den här konfigurationen rekommenderas inte för Site Recovery replikering. Om du använder anpassade vägar bör du [skapa en tjänst slut punkt för virtuellt nätverk](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) i ditt virtuella nätverk för "lagring", så att replikeringstrafiken inte lämnar Azure-gränser.

## <a name="next-steps"></a>Nästa steg
- Börja skydda dina arbets belastningar genom att [Replikera virtuella Azure-datorer](site-recovery-azure-to-azure.md).
- Läs mer om [IP-kvarhållning](site-recovery-retain-ip-azure-vm-failover.md) av redundans för virtuella Azure-datorer.
- Läs mer om haveri beredskap för [virtuella Azure-datorer med ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
