---
title: FQDN taggar översikt för Azure-brandväggen
description: Lär dig mer om FQDN-taggar i Azure-brandväggen
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 6dc7d20d31d9399355b2b3de90ea90f2f3e07af5
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224655"
---
# <a name="fqdn-tags-overview"></a>Översikt över FQDN-taggar

Ett FQDN-taggen representerar en grupp med fullständigt kvalificerade domännamn (FQDN) som är associerade med välkända Microsoft-tjänster. Du kan använda en FQDN-tagg i regler för program så att de nödvändiga utgående nätverkstrafiken via brandväggen.

Till exempel för att tillåta Windows Update-nätverkstrafik via brandväggen manuellt måste du skapa flera regler per Microsoft-dokumentationen. Med FQDN taggar kan du skapa en regel för programmet, innehåller den **Windows-uppdateringar** tagga och nu nätverkstrafik till Microsoft Windows Update-slutpunkter kan flöda via brandväggen.

Du kan inte skapa dina egna FQDN-taggar och inte heller kan du ange vilka FQDN: er ingår i en tagg. Microsoft hanterar FQDN: er som omfattas av taggen FQDN tjänsttaggen och uppdaterar taggen som FQDN ändringen. 

<!--- screenshot of application rule with a FQDN tag.-->

I följande tabell visas de aktuella FQDN-taggar som du kan använda. Microsoft underhåller dessa taggar och du kan förvänta dig ytterligare taggar som ska läggas till med jämna mellanrum.

|FQDN-tagg  |Beskrivning  |
|---------|---------|
|Windows Update     |Tillåter utgående åtkomst till Microsoft Update enligt beskrivningen i [hur du konfigurerar en brandvägg för programuppdateringar](https://technet.microsoft.com/library/bb693717.aspx).|
|Windows-diagnostik|Tillåt utgående åtkomst till alla [Windows diagnostik slutpunkter](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|MAPS (Microsoft Active Protection Service)|Tillåter utgående åtkomst till [MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|App Service-miljö (ASE)|Tillåter utgående åtkomst till ASE plattform-trafik. Den här taggen täcker inte kundspecifika-lagring och SQL-slutpunkter som skapats av ASE. Dessa ska aktiveras [tjänstslutpunkter](../virtual-network/tutorial-restrict-network-access-to-resources.md) eller läggs till manuellt.|
|Azure Backup|Tillåter utgående åtkomst till tjänsterna Azure Backup.

> [!NOTE]
> När du väljer FQDN-tagg i en regel för programmet, fältet protocol: port måste anges till **https**.

## <a name="next-steps"></a>Nästa steg

Läs hur du distribuerar en Azure-brandvägg i [självstudie: distribuera och konfigurera Azure-brandväggen med hjälp av Azure-portalen](tutorial-firewall-deploy-portal.md).