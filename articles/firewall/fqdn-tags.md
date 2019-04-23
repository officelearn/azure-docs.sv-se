---
title: FQDN taggar översikt för Azure-brandväggen
description: Lär dig mer om FQDN-taggar i Azure-brandväggen
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/22/2019
ms.author: victorh
ms.openlocfilehash: 740b0ac505edfff1f703c2831ec5608e72851610
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149775"
---
# <a name="fqdn-tags-overview"></a>Översikt över FQDN-taggar

Ett FQDN-taggen representerar en grupp med fullständigt kvalificerade domännamn (FQDN) som är associerade med välkända Microsoft-tjänster. Du kan använda en FQDN-tagg i regler för program så att de nödvändiga utgående nätverkstrafiken via brandväggen.

Till exempel för att tillåta Windows Update-nätverkstrafik via brandväggen manuellt måste du skapa flera regler per Microsoft-dokumentationen. Med FQDN taggar kan du skapa en regel för programmet, innehåller den **Windows-uppdateringar** tagga och nu nätverkstrafik till Microsoft Windows Update-slutpunkter kan flöda via brandväggen.

Du kan inte skapa dina egna FQDN-taggar och inte heller kan du ange vilka FQDN: er ingår i en tagg. Microsoft hanterar FQDN: er som omfattas av taggen FQDN tjänsttaggen och uppdaterar taggen som FQDN ändringen. 

<!--- screenshot of application rule with a FQDN tag.-->

I följande tabell visas de aktuella FQDN-taggar som du kan använda. Microsoft underhåller dessa taggar och du kan förvänta dig ytterligare taggar som ska läggas till med jämna mellanrum.

## <a name="current-fqdn-tags"></a>Aktuella FQDN-taggar

|FQDN-tagg  |Beskrivning  |
|---------|---------|
|Windows Update     |Tillåter utgående åtkomst till Microsoft Update enligt beskrivningen i [hur du konfigurerar en brandvägg för programuppdateringar](https://technet.microsoft.com/library/bb693717.aspx).|
|Windows-diagnostik|Tillåt utgående åtkomst till alla [Windows diagnostik slutpunkter](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|MAPS (Microsoft Active Protection Service)|Tillåter utgående åtkomst till [MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|App Service Environment (ASE)|Tillåter utgående åtkomst till ASE plattform-trafik. Den här taggen täcker inte kundspecifika-lagring och SQL-slutpunkter som skapats av ASE. Dessa ska aktiveras [tjänstslutpunkter](../virtual-network/tutorial-restrict-network-access-to-resources.md) eller läggs till manuellt.<br><br>Mer information om hur du integrerar Azure-brandvägg med ASE finns i [låsa en App Service Environment](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Tillåter utgående åtkomst till tjänsterna Azure Backup.|
|Azure HDInsight<br>(Förhandsversion)|Tillåter utgående åtkomst för trafik för HDInsight-plattformen. Den här taggen täcker inte kundspecifika lagrings- eller SQL-trafik från HDInsight. De aktiveras [tjänstslutpunkter](../virtual-network/tutorial-restrict-network-access-to-resources.md) eller läggs till manuellt.|

> [!NOTE]
> När du väljer FQDN-tagg i en regel för programmet, fältet protocol: port måste anges till **https**.

## <a name="next-steps"></a>Nästa steg

Läs hur du distribuerar en Azure-brandvägg i [självstudien: Distribuera och konfigurera Azure-brandväggen med hjälp av Azure-portalen](tutorial-firewall-deploy-portal.md).