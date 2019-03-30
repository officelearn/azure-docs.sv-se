---
title: Om nätverk i Azure till Azure-haveriberedskap med hjälp av Azure Site Recovery | Microsoft Docs
description: Ger en översikt över nätverk för replikering av virtuella Azure-datorer med Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/29/2019
ms.author: sujayt
ms.openlocfilehash: 42db22d39a7c87363cf97f874c85955a09cbe653
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651551"
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Om nätverk i Azure till Azure replikering



Den här artikeln innehåller vägledning för nätverk när du replikera och återställa virtuella Azure-datorer från en region till en annan, med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Innan du börjar

Lär dig hur Site Recovery erbjuder haveriberedskap för [det här scenariot](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Vanlig nätverksinfrastruktur

Följande diagram visar en normal Azure miljö för program som körs på Azure Virtual Machines:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Om du använder Azure ExpressRoute eller VPN-anslutning från ditt lokala nätverk till Azure, är miljön enligt följande:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Nätverk är normalt skyddas med brandväggar och nätverkssäkerhetsgrupper (NSG). Brandväggar använda URL eller IP-baserad listan över tillåtna program för att styra nätverksanslutningar. NSG: er tillhandahåller regler som använder IP-adressintervall för att styra nätverksanslutning.

>[!IMPORTANT]
> Med hjälp av en autentiserad proxyserver för att styra nätverksanslutningar stöds inte av Site Recovery och replikering aktiveras inte.


## <a name="outbound-connectivity-for-urls"></a>Utgående anslutning för webbadresser

Om du använder en Webbadressbaserad brandväggsproxy för att styra utgående anslutningar, tillåta dessa Site Recovery-webbadresser:


**URL** | **Detaljer**  
--- | ---
*.blob.core.windows.net | Krävs för att data kan skrivas till cachelagringskontot i källregionen från den virtuella datorn. Om du vet alla cache storage-konton för dina virtuella datorer, kan du lista över tillåtna URL: er för specifika storage-konto (t.ex.: cache1.blob.core.windows.net och cache2.blob.core.windows.net) i stället för *. blob.core.windows.net
login.microsoftonline.com | Krävs för autentisering och auktorisering till Site Recovery-tjänstens webbadresser.
*.hypervrecoverymanager.windowsazure.com | Krävs så att Site Recovery service-kommunikation kan ske från den virtuella datorn. Du kan använda den motsvarande ”Site Recovery IP” om din brandväggsproxy har stöd för IP-adresser.
*.servicebus.windows.net | Krävs så att Site Recovery övervakning och diagnostik data kan skrivas från den virtuella datorn. Du kan använda motsvarande ”Site Recovery övervakning av IP-adress” om din brandväggsproxy har stöd för IP-adresser.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Utgående anslutning för IP-adressintervall

Om du använder en IP-baserad brandväggsproxy eller NSG-regler för att styra utgående anslutningar, måste dessa IP-adressintervall som ska tillåtas.

- Alla IP-adressintervall som motsvarar storage-konton i källregionen
    - Skapa en [Storage tjänsttagg](../virtual-network/security-overview.md#service-tags) baserat NSG-regel för källregionen.
    - Tillåta de här adresserna så att data kan skrivas till cachelagringskontot, från den virtuella datorn.
- Skapa en [Azure Active Directory (AAD) tjänsttagg](../virtual-network/security-overview.md#service-tags) baserat NSG-regel för att tillåta åtkomst till alla IP-adresser för AAD
    - Om nya adresser läggs till Azure Active Directory (AAD) i framtiden, måste du skapa nya NSG-regler.
- Site Recovery service-slutpunkt IP-adresser – tillgänglig i en [XML-fil](https://aka.ms/site-recovery-public-ips) och beror på din målplatsen.
- Vi rekommenderar att du skapa de NSG-reglerna på ett test NSG och kontrollera att det inte finns några problem innan du skapar reglerna på en NSG i produktionsmiljön.


Site Recovery-IP-adressintervall är följande:

   **Mål** | **Site Recovery IP** |  **Site Recovery övervakning av IP**
   --- | --- | ---
   Östasien | 52.175.17.132 | 13.94.47.61
   Sydostasien | 52.187.58.193 | 13.76.179.223
   Indien, centrala | 52.172.187.37 | 104.211.98.185
   Södra Indien | 52.172.46.220 | 104.211.224.190
   Norra centrala USA | 23.96.195.247 | 168.62.249.226
   Norra Europa | 40.69.212.238 | 52.169.18.8
   Västra Europa | 52.166.13.64 | 40.68.93.145
   Östra USA | 13.82.88.226 | 104.45.147.24
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
   Storbritannien, södra 2 | 13.87.37.4| 13.87.34.139
   Storbritannien, norra | 51.142.209.167 | 13.87.102.68
   Sydkorea, centrala | 52.231.28.253 | 52.231.32.85
   Sydkorea, södra | 52.231.298.185 | 52.231.200.144
   Frankrike, centrala | 52.143.138.106 | 52.143.136.55
   Frankrike, södra | 52.136.139.227 |52.136.136.62
   Australien, centrala| 20.36.34.70 | 20.36.46.142
   Australien, centrala 2| 20.36.69.62 | 20.36.74.130
   Sydafrika, västra | 102.133.72.51 | 102.133.26.128
   Sydafrika, norra | 102.133.160.44 | 102.133.154.128
## <a name="example-nsg-configuration"></a>Exempel NSG-konfiguration

Det här exemplet visar hur du konfigurerar NSG-regler för en virtuell dator att replikera.

- Om du använder NSG-regler för att styra utgående anslutningar kan du använda ”Tillåt utgående HTTPS” regler för port: 443 för alla de obligatoriska IP-adressintervall.
- I exemplet förutsätter att källplatsen VM är ”östra USA” och målplatsen är ”USA, centrala”.

### <a name="nsg-rules---east-us"></a>NSG-regler – USA, östra

1. Skapa en utgående Säkerhetsregel för HTTPS (443) för ”Storage.EastUS” på NSG: N som visas i skärmbilden nedan.

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Skapa en utgående Säkerhetsregel för HTTPS (443) för ”AzureActiveDirectory” på NSG: N som visas i skärmbilden nedan.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Skapa regler för utgående HTTPS (443) för Site Recovery IP-adresserna som motsvarar målplatsen:

   **Plats** | **Site Recovery-IP-adress** |  **Site Recovery övervakning IP-adress**
    --- | --- | ---
   Centrala USA | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG-regler – USA, centrala

De här reglerna krävs så att replikeringen kan aktiveras från målregionen till källan region efter en redundansväxling:

1. Skapa en utgående Säkerhetsregel för HTTPS (443) för ”Storage.CentralUS” på NSG: N.

2. Skapa en utgående Säkerhetsregel för HTTPS (443) för ”AzureActiveDirectory” på NSG: N.

3. Skapa regler för utgående HTTPS (443) för Site Recovery IP-adresserna som motsvarar källplats:

   **Plats** | **Site Recovery-IP-adress** |  **Site Recovery övervakning IP-adress**
    --- | --- | ---
   Centrala USA | 13.82.88.226 | 104.45.147.24

## <a name="network-virtual-appliance-configuration"></a>Nätverkskonfiguration för virtuell installation

Om du använder virtuella nätverksinstallationer (Nva) för att styra utgående nätverkstrafik från virtuella datorer, kan installationen begränsas om all replikeringstrafik som passerar genom NVA. Vi rekommenderar att du skapar en tjänstslutpunkt för nätverk i ditt virtuella nätverk för ”Storage” så att replikeringstrafiken inte går att NVA.

### <a name="create-network-service-endpoint-for-storage"></a>Skapa en tjänstslutpunkt nätverk för lagring
Du kan skapa en slutpunkt för nätverk i ditt virtuella nätverk för ”Storage” så att replikeringstrafiken inte lämnar Azure-gränsen.

- Välj Azure-nätverk och klicka på ”Tjänsteslutpunkter”

    ![slutpunkt för lagring](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Klicka på ”Lägg till” och ”Lägg till Tjänsteslutpunkter” fliken öppnas
- Välj ”Microsoft.Storage” under ”tjänst” och de nödvändiga undernät under ”undernät”-fältet och klicka på ”Lägg till”

>[!NOTE]
>Begränsa inte åtkomst till virtuellt nätverk till ditt storage-konton som används för ASR. Du bör tillåta åtkomst från ”alla nätverk”

### <a name="forced-tunneling"></a>Tvingad tunneltrafik

Du kan åsidosätta Azures standardsystemväg för adressprefixet 0.0.0.0/0 med en [anpassad väg](../virtual-network/virtual-networks-udr-overview.md#custom-routes) och använda ett annat VM-trafik till en lokal virtuell nätverksinstallation (NVA), men den här konfigurationen rekommenderas inte för Site Recovery replikering. Om du använder anpassade vägar, bör du [skapa en tjänstslutpunkt för virtuellt nätverk](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) i ditt virtuella nätverk för ”Storage” så att replikeringstrafiken inte lämnar Azure gränsen.

## <a name="next-steps"></a>Nästa steg
- Börja skydda dina arbetsbelastningar genom [replikera virtuella Azure datorer](site-recovery-azure-to-azure.md).
- Läs mer om [IP-adress kvarhållning](site-recovery-retain-ip-azure-vm-failover.md) för Azure VM-redundans.
- Mer information om haveriberedskap för [Azure-datorer med ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
