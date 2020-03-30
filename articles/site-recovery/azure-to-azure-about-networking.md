---
title: Om nätverk i Azure VM-haveriberedskap med Azure Site Recovery
description: Ger en översikt över nätverk för replikering av virtuella Azure-datorer med Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/13/2020
ms.author: sutalasi
ms.openlocfilehash: 58348c9aed14a5cc9126be780fe01817274a0b47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283267"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>Om nätverk i Azure VM-haveriberedskap



Den här artikeln innehåller nätverksvägledning när du replikerar och återställer virtuella Azure-datorer från en region till en annan med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Innan du börjar

Lär dig hur Site Recovery ger haveriberedskap för [det här scenariot](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Typisk nätverksinfrastruktur

I följande diagram visas en typisk Azure-miljö för program som körs på virtuella Azure-datorer:

![kund-miljö](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Om du använder Azure ExpressRoute eller en VPN-anslutning från ditt lokala nätverk till Azure är miljön följande:

![kund-miljö](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Vanligtvis skyddas nätverk med brandväggar och nätverkssäkerhetsgrupper (NSG). Brandväggar använder URL eller IP-baserad vitlistning för att styra nätverksanslutningen. NSG:er innehåller regler som använder IP-adressintervall för att styra nätverksanslutningen.

>[!IMPORTANT]
> Att använda en autentrad proxy för att styra nätverksanslutningen stöds inte av Site Recovery och replikering kan inte aktiveras.


## <a name="outbound-connectivity-for-urls"></a>Utgående anslutning för webbadresser

Om du använder en URL-baserad brandväggsproxy för att styra utgående anslutning tillåter du dessa url:er för webbplatsåterställning:


**URL** | **Detaljer**
--- | ---
*.blob.core.windows.net | Krävs så att data kan skrivas till cachelagringskontot i källregionen från den virtuella datorn. Om du känner till alla cachelagringskonton för dina virtuella datorer kan du tillåta åtkomst till de specifika url:erna för lagringskonto (t.ex. cache1.blob.core.windows.net och cache2.blob.core.windows.net) i stället för *.blob.core.windows.net
login.microsoftonline.com | Krävs för auktorisering och autentisering till url:erna för tjänsten Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Krävs så att tjänsten Site Recovery-tjänsten kan ske från den virtuella datorn.
*.servicebus.windows.net | Krävs så att data för övervakning och diagnostik av platsåterställning kan skrivas från den virtuella datorn.
*.vault.azure.net | Ger åtkomst för att aktivera replikering för ADE-aktiverade virtuella datorer via portal
*.automation.ext.azure.com | Gör det möjligt att aktivera automatisk uppgradering av mobilitetsagent för ett replikerat objekt via portal

## <a name="outbound-connectivity-using-service-tags"></a>Utgående anslutning med hjälp av tjänsttaggar

Om du använder en NSG för att styra utgående anslutning måste dessa tjänsttaggar tillåtas.

- För lagringskonton i källregionen:
    - Skapa en [lagringstjänsttagbaserad](../virtual-network/security-overview.md#service-tags) NSG-regel för källregionen.
    - Tillåt dessa adresser så att data kan skrivas till cachelagringskontot från den virtuella datorn.
- Skapa en [Azure Active Directory-tjänsttaggsbaserad](../virtual-network/security-overview.md#service-tags) NSG-regel för att tillåta åtkomst till alla IP-adresser som motsvarar AAD
- Skapa en EventsHub-tjänsttaggbaserad NSG-regel för målregionen, vilket ger åtkomst till övervakning av webbplatsåterställning.
- Skapa en AzureSiteRecovery-tjänsttaggbaserad NSG-regel för att tillåta åtkomst till site recovery-tjänsten i alla regioner.
- Skapa en AzureKeyVault-tjänsttaggbaserad NSG-regel. Detta krävs endast för att aktivera replikering av ADE-aktiverade virtuella datorer via portalen.
- Skapa en GuestAndHybridManagement-tjänsttagg baserad NSG-regel. Detta krävs endast för att aktivera automatisk uppgradering av mobilitetsagent för ett replikerat objekt via portalen.
- Vi rekommenderar att du skapar de nödvändiga NSG-reglerna för ett test NSG och kontrollerar att det inte finns några problem innan du skapar reglerna för en produktions-NSG.

## <a name="example-nsg-configuration"></a>Exempel på NSG-konfiguration

Det här exemplet visar hur du konfigurerar NSG-regler för en virtuell dator som ska replikeras.

- Om du använder NSG-regler för att styra utgående anslutning använder du regler för "Tillåt utgående HTTPS" till port:443 för alla nödvändiga IP-adressintervall.
- Exemplet förutsätter att vm-källplatsen är "Östra USA" och målplatsen är "Central US".

### <a name="nsg-rules---east-us"></a>NSG regler - Östra USA

1. Skapa en utgående HTTPS -säkerhetsregel (443) för "Storage.EastUS" på NSG som visas i skärmbilden nedan.

      ![lagring-tagg](./media/azure-to-azure-about-networking/storage-tag.png)

2. Skapa en utgående HTTPS-säkerhetsregel (443) för "AzureActiveDirectory" på NSG som visas i skärmbilden nedan.

      ![aad-tagg](./media/azure-to-azure-about-networking/aad-tag.png)

3. I likhet med ovanstående säkerhetsregler skapar du utgående HTTPS-säkerhet (443) för "EventHub.CentralUS" på NSG som motsvarar målplatsen. Detta ger åtkomst till övervakning av webbplatsåterställning.

4. Skapa en utgående HTTPS-säkerhet (443) för "AzureSiteRecovery" på NSG. Detta ger åtkomst till Site Recovery Service i alla regioner.

### <a name="nsg-rules---central-us"></a>NSG regler - Centrala USA

Dessa regler krävs så att replikering kan aktiveras från målområdet till källregionen efter redundansen:

1. Skapa en utgående HTTPS-säkerhetsregel (443) för "Storage.CentralUS" på NSG.

2. Skapa en utgående HTTPS-säkerhet (443) för "AzureActiveDirectory" på NSG.

3. I likhet med ovanstående säkerhetsregler skapar du utgående HTTPS-säkerhet (443) för "EventHub.EastUS" på NSG som motsvarar källplatsen. Detta ger åtkomst till övervakning av webbplatsåterställning.

4. Skapa en utgående HTTPS-säkerhet (443) för "AzureSiteRecovery" på NSG. Detta ger åtkomst till Site Recovery Service i alla regioner.

## <a name="network-virtual-appliance-configuration"></a>Konfiguration av virtuell nätverksinstallation

Om du använder virtuella nätverksenheter (NVAs) för att styra utgående nätverkstrafik från virtuella datorer kan installationen begränsas om all replikeringstrafik passerar genom NVA. Vi rekommenderar att du skapar en slutpunkt för nätverkstjänsten i det virtuella nätverket för "Lagring" så att replikeringstrafiken inte går till NVA.

### <a name="create-network-service-endpoint-for-storage"></a>Skapa slutpunkt för nätverkstjänst för lagring
Du kan skapa en slutpunkt för nätverkstjänsten i det virtuella nätverket för "Lagring" så att replikeringstrafiken inte lämnar Azure-gränsen.

- Välj ditt virtuella Azure-nätverk och klicka på "Tjänstslutpunkter"

    ![slutpunkt för lagring](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Klicka på fliken Lägg till och Lägg till tjänstslutpunkter
- Välj "Microsoft.Storage" under "Tjänst" och de nödvändiga undernäten under fältet Undernät och klicka på Lägg till

>[!NOTE]
>Begränsa inte den virtuella nätverksåtkomsten till dina lagringskonton som används för ASR. Du bör tillåta åtkomst från "Alla nätverk"

### <a name="forced-tunneling"></a>Tvingad tunneltrafik

Du kan åsidosätta Azures standardsystemväg för 0.0.0.0/0-adressprefixet med en [anpassad väg](../virtual-network/virtual-networks-udr-overview.md#custom-routes) och vidarekoppla VM-trafik till en lokal virtuell nätverksinstallation (NVA), men den här konfigurationen rekommenderas inte för site recovery-replikering. Om du använder anpassade vägar bör du skapa en slutpunkt för [en virtuell nätverkstjänst](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) i det virtuella nätverket för "Lagring" så att replikeringstrafiken inte lämnar Azure-gränsen.

## <a name="next-steps"></a>Nästa steg
- Börja skydda dina arbetsbelastningar genom [att replikera virtuella Azure-datorer](site-recovery-azure-to-azure.md).
- Läs mer om [IP-adresskvarhållning](site-recovery-retain-ip-azure-vm-failover.md) för redundans för virtuella Azure-datorer.
- Läs mer om haveriberedskap av [virtuella Azure-datorer med ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
