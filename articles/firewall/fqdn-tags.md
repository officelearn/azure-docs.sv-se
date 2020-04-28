---
title: Översikt över FQDN-taggar för Azure-brandvägg
description: En FQDN-tagg representerar en grupp med fullständigt kvalificerade domän namn (FQDN) som är kopplade till välkända Microsoft-tjänster.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 6396f8292a4c54f7fce237439f37c3e8156d59e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74169038"
---
# <a name="fqdn-tags-overview"></a>Översikt över FQDN-Taggar

En FQDN-tagg representerar en grupp med fullständigt kvalificerade domän namn (FQDN) som är kopplade till välkända Microsoft-tjänster. Du kan använda en FQDN-tagg i program regler för att tillåta nödvändig utgående nätverks trafik genom brand väggen.

Om du till exempel vill tillåta Windows Update nätverks trafik manuellt genom brand väggen måste du skapa flera program regler per Microsoft-dokumentationen. Med hjälp av FQDN-taggar kan du skapa en program regel, inkludera taggen **Windows updates** och nu kan nätverks trafik till Microsoft Windows Update-slutpunkter flöda genom brand väggen.

Du kan inte skapa egna FQDN-taggar, eller så kan du inte ange vilka FQDN: er som ingår i en tagg. Microsoft hanterar FQDN: er som omfattas av FQDN-taggen och uppdaterar taggen som FQDN-ändringar. 

<!--- screenshot of application rule with a FQDN tag.-->

I följande tabell visas de aktuella FQDN-taggar som du kan använda. Microsoft underhåller dessa taggar och du kan vänta på att ytterligare Taggar läggs till regelbundet.

## <a name="current-fqdn-tags"></a>Aktuella FQDN-Taggar

|FQDN-tagg  |Beskrivning  |
|---------|---------|
|Windows Update     |Tillåt utgående åtkomst till Microsoft Update enligt beskrivningen i [så här konfigurerar du en brand vägg för program uppdateringar](https://technet.microsoft.com/library/bb693717.aspx).|
|Windows-diagnostik|Tillåt utgående åtkomst till alla [slut punkter för Windows-diagnostik](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Microsoft Active Protection Service (MAPS)|Tillåt utgående åtkomst till [Maps](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|App Service-miljön (ASE)|Tillåter utgående åtkomst till ASE-plattforms trafik. Den här taggen avser inte kundspecifik lagring och SQL-slutpunkter som skapats av ASE. De bör aktive ras via [tjänst slut punkter](../virtual-network/tutorial-restrict-network-access-to-resources.md) eller läggas till manuellt.<br><br>Mer information om hur du integrerar Azure-brandväggen med ASE finns i [låsa en app service-miljön](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Tillåter utgående åtkomst till Azure Backup-tjänsterna.|
|Azure HDInsight|Tillåter utgående åtkomst för HDInsight-plattforms trafik. Den här taggen avser inte kundspecifik lagring eller SQL-trafik från HDInsight. Aktivera dessa med hjälp av [tjänst slut punkter](../virtual-network/tutorial-restrict-network-access-to-resources.md) eller Lägg till dem manuellt.|

> [!NOTE]
> När du väljer FQDN-tagg i en program regel måste fältet protokoll: port anges till **https**.

## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar en Azure-brandvägg finns i [Självstudier: Distribuera och konfigurera Azure-brandväggen med hjälp av Azure Portal](tutorial-firewall-deploy-portal.md).
