---
title: Om nätverk i Azure till Azure haveri beredskap med Azure Site Recovery | Microsoft Docs
description: Innehåller en översikt över nätverk för replikering av virtuella Azure-datorer med hjälp av Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: c642640d590e1f568fb6f6c5072decd75575ab2d
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543646"
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Om nätverk i Azure till Azure-replikering



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
*.blob.core.windows.net | Krävs så att data kan skrivas till cache-lagrings kontot i käll regionen från den virtuella datorn. Om du känner till alla cache-lagrings konton för dina virtuella datorer kan du vitlista de angivna URL: erna för lagrings kontot (t. ex. cache1.blob.core.windows.net och cache2.blob.core.windows.net) i stället för *. blob.core.windows.net
login.microsoftonline.com | Krävs för auktorisering och autentisering till Site Recovery tjänst-URL: er.
*.hypervrecoverymanager.windowsazure.com | Krävs så att kommunikationen mellan Site Recoverys tjänsten kan ske från den virtuella datorn. Du kan använda motsvarande "Site Recovery IP" om brand Väggs-proxyn stöder IP-adresser.
*.servicebus.windows.net | Krävs så att Site Recovery övervakning och diagnostikdata kan skrivas från den virtuella datorn. Du kan använda motsvarande "Site Recovery övervaknings-IP" om brand Väggs-proxyn har stöd för IP-adresser.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Utgående anslutning för IP-adressintervall

Om du använder en IP-baserad brand Väggs-proxy eller NSG-regler för att kontrol lera utgående anslutningar, måste dessa IP-adressintervall tillåtas.

- Alla IP-adressintervall som motsvarar lagrings kontona i käll regionen
    - Skapa en NSG-regel för [lagrings tjänst](../virtual-network/security-overview.md#service-tags) som är baserad på käll regionen.
    - Tillåt dessa adresser så att data kan skrivas till cache-lagrings kontot från den virtuella datorn.
- Skapa en [Azure Active Directory (AAD) tjänsttagg](../virtual-network/security-overview.md#service-tags) baserat NSG-regel för att tillåta åtkomst till alla IP-adresser för AAD
    - Om nya adresser läggs till Azure Active Directory (AAD) i framtiden, måste du skapa nya NSG-regler.
- Site Recovery tjänst slut punktens IP-adresser – tillgängligt i en [XML-fil](https://aka.ms/site-recovery-public-ips) och är beroende av mål platsen.
- Vi rekommenderar att du skapar de nödvändiga NSG-reglerna på en test-NSG och kontrollerar att det inte finns några problem innan du skapar reglerna på en produktions NSG.


Site Recovery IP-adressintervall är följande:

   **Fokusera** | **Site Recovery IP** |  **Site Recovery övervakning av IP**
   --- | --- | ---
   Östasien | 52.175.17.132 | 13.94.47.61
   Sydostasien | 52.187.58.193 | 13.76.179.223
   Indien, centrala | 52.172.187.37 | 104.211.98.185
   Indien, södra | 52.172.46.220 | 104.211.224.190
   Norra centrala USA | 23.96.195.247 | 168.62.249.226
   Norra Europa | 40.69.212.238 | 52.169.18.8
   Västra Europa | 52.166.13.64 | 40.68.93.145
   East US | 13.82.88.226 | 104.45.147.24
   Västra USA | 40.83.179.48 | 104.40.26.199
   Södra centrala USA | 13.84.148.14 | 104.210.146.250
   Centrala USA | 40.69.144.231 | 52.165.34.144
   USA, östra 2 | 52.184.158.163 | 40.79.44.59
   Östra Japan | 52.185.150.140 | 138.91.1.105
   Västra Japan | 52.175.146.69 | 138.91.17.38
   Södra Brasilien | 191.234.185.172 | 23.97.97.36
   Östra Australien | 104.210.113.114 | 191.239.64.144
   Sydöstra Australien | 13.70.159.158 | 191.239.160.45
   Centrala Kanada | 52.228.36.192 | 40.85.226.62
   Östra Kanada | 52.229.125.98 | 40.86.225.142
   Västra centrala USA | 52.161.20.168 | 13.78.149.209
   Västra USA 2 | 52.183.45.166 | 13.66.228.204
   Storbritannien, västra | 51.141.3.203 | 51.141.14.113
   Storbritannien, södra | 51.140.43.158 | 51.140.189.52
   Södra Storbritannien 2 | 13.87.37.4| 13.87.34.139
   Norra Storbritannien | 51.142.209.167 | 13.87.102.68
   Sydkorea, centrala | 52.231.28.253 | 52.231.32.85
   Sydkorea, södra | 52.231.198.185 | 52.231.200.144
   Frankrike, centrala | 52.143.138.106 | 52.143.136.55
   Frankrike, södra | 52.136.139.227 |52.136.136.62
   Australien, centrala| 20.36.34.70 | 20.36.46.142
   Australien, centrala 2| 20.36.69.62 | 20.36.74.130
   Sydafrika, västra | 102.133.72.51 | 102.133.26.128
   Sydafrika, norra | 102.133.160.44 | 102.133.154.128
   Virginia (USA-förvaltad region) | 52.227.178.114 | 23.97.0.197
   US Gov, Iowa | 13.72.184.23 | 23.97.16.186
   Arizona (USA-förvaltad region) | 52.244.205.45 | 52.244.48.85
   Texas (USA-förvaltad region) | 52.238.119.218 | 52.238.116.60
   US DoD, östra | 52.181.164.103 | 52.181.162.129
   US DoD, centrala | 52.182.95.237 | 52.182.90.133
   Kina, norra | 40.125.202.254 | 42.159.4.151
   Kina, norra 2 | 40.73.35.193 | 40.73.33.230
   Kina, östra | 42.159.205.45 | 42.159.132.40
   Kina, östra 2 | 40.73.118.52| 40.73.100.125
  
## <a name="example-nsg-configuration"></a>Exempel på NSG-konfiguration

Det här exemplet visar hur du konfigurerar NSG-regler för en virtuell dator att replikera.

- Om du använder NSG-regler för att kontrol lera utgående anslutningar använder du reglerna "Tillåt HTTPS utgående" till port: 443 för alla nödvändiga IP-adressintervall.
- Exemplet förutsätter att käll platsen för den virtuella datorn är "USA, östra" och att mål platsen är "Central US".

### <a name="nsg-rules---east-us"></a>NSG-regler – USA, östra

1. Skapa en utgående HTTPS-säkerhetsregel (443) för "Storage. öst" på NSG som visas på skärm bilden nedan.

      ![lagrings tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Skapa en utgående HTTPS (443) säkerhets regel för "AzureActiveDirectory" på NSG som visas på skärm bilden nedan.

      ![AAD-tagg](./media/azure-to-azure-about-networking/aad-tag.png)

3. Skapa regler för utgående HTTPS (443) för de Site Recovery IP-adresser som motsvarar mål platsen:

   **Location** | **Site Recovery IP-adress** |  **Site Recovery övervakning av IP-adress**
    --- | --- | ---
   Centrala USA | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG-regler – centrala USA

De här reglerna krävs för att replikeringen ska kunna aktive ras från mål regionen till käll regionen efter redundans:

1. Skapa en utgående HTTPS-säkerhets regel (443) för "Storage. Central" på NSG.

2. Skapa en utgående HTTPS (443) säkerhets regel för "AzureActiveDirectory" på NSG.

3. Skapa regler för utgående HTTPS (443) för de Site Recovery IP-adresser som motsvarar käll platsen:

   **Location** | **Site Recovery IP-adress** |  **Site Recovery övervakning av IP-adress**
    --- | --- | ---
   Centrala USA | 13.82.88.226 | 104.45.147.24

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

### <a name="forced-tunneling"></a>Tvingad tunneltrafik

Du kan åsidosätta Azures standard system väg för adressprefixet 0.0.0.0/0 med en [anpassad väg och dirigera](../virtual-network/virtual-networks-udr-overview.md#custom-routes) VM-trafik till en lokal virtuell nätverks installation (NVA), men den här konfigurationen rekommenderas inte för Site Recovery replikering. Om du använder anpassade vägar bör du [skapa en tjänst slut punkt för virtuellt nätverk](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) i ditt virtuella nätverk för "lagring", så att replikeringstrafiken inte lämnar Azure-gränser.

## <a name="next-steps"></a>Nästa steg
- Börja skydda dina arbets belastningar genom att [Replikera virtuella Azure-datorer](site-recovery-azure-to-azure.md).
- Läs mer om [IP-kvarhållning](site-recovery-retain-ip-azure-vm-failover.md) av redundans för virtuella Azure-datorer.
- Läs mer om haveri beredskap för [virtuella Azure-datorer med ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
