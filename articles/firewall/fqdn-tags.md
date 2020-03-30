---
title: Översikt över FQDN-taggar för Azure-brandväggen
description: En FQDN-tagg representerar en grupp fullständigt kvalificerade domännamn (FQDN) som är associerade med välkända Microsoft-tjänster.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 6396f8292a4c54f7fce237439f37c3e8156d59e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74169038"
---
# <a name="fqdn-tags-overview"></a>Översikt över FQDN-taggar

En FQDN-tagg representerar en grupp fullständigt kvalificerade domännamn (FQDN) som är associerade med välkända Microsoft-tjänster. Du kan använda en FQDN-tagg i programregler för att tillåta den utgående nätverkstrafiken genom brandväggen.

Om du till exempel manuellt vill tillåta Nätverkstrafik i Windows Update genom brandväggen måste du skapa flera programregler enligt Microsoft-dokumentationen. Med FQDN-taggar kan du skapa en programregel, inkludera taggen **Windows-uppdateringar** och nu kan nätverkstrafik till Microsoft Windows Update-slutpunkter flöda genom brandväggen.

Du kan inte skapa egna FQDN-taggar och du kan inte heller ange vilka FQDN:er som ska ingå i en tagg. Microsoft hanterar FQDN som omfattas av FQDN-taggen och uppdaterar taggen när FQDN ändras. 

<!--- screenshot of application rule with a FQDN tag.-->

I följande tabell visas de aktuella FQDN-taggar som du kan använda. Microsoft underhåller dessa taggar och du kan förvänta dig att ytterligare taggar läggs till med jämna mellanrum.

## <a name="current-fqdn-tags"></a>Aktuella FQDN-taggar

|FQDN-tagg  |Beskrivning  |
|---------|---------|
|Windows Update     |Tillåt utgående åtkomst till Microsoft Update enligt beskrivningen i [Så här konfigurerar du en brandvägg för programuppdateringar](https://technet.microsoft.com/library/bb693717.aspx).|
|Windows-diagnostik|Tillåt utgående åtkomst till alla [Windows Diagnostics-slutpunkter](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Microsoft Active Protection Service (MAPS)|Tillåt utgående åtkomst till [MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|App Service Miljö (ASE)|Tillåter utgående åtkomst till ASE-plattformstrafik. Den här taggen täcker inte kundspecifika lagrings- och SQL-slutpunkter som skapats av ASE. Dessa bör aktiveras via [tjänstslutpunkter](../virtual-network/tutorial-restrict-network-access-to-resources.md) eller läggas till manuellt.<br><br>Mer information om hur du integrerar Azure-brandväggen med ASE finns i [Låsa en apptjänstmiljö](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Tillåter utgående åtkomst till Azure Backup-tjänsterna.|
|Azure HDInsight|Tillåter utgående åtkomst för HDInsight-plattformstrafik. Den här taggen täcker inte kundspecifik lagring eller SQL-trafik från HDInsight. Aktivera dessa med hjälp av [tjänstslutpunkter](../virtual-network/tutorial-restrict-network-access-to-resources.md) eller lägg till dem manuellt.|

> [!NOTE]
> När du väljer FQDN-tagg i en programregel måste fältet protocol:port anges till **https**.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du distribuerar en Azure-brandvägg finns i [Självstudiekurs: Distribuera och konfigurera Azure-brandväggen med Azure-portalen](tutorial-firewall-deploy-portal.md).
