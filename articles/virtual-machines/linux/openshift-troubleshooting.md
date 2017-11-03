---
title: "Felsökning av OpenShift distribution i Azure | Microsoft Docs"
description: "Felsökning av OpenShift distribution i Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: ea3664870480f6ed170972a5f52940dc4a852219
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="troubleshooting-openshift-deployment-in-azure"></a>Felsökning av OpenShift distribution i Azure

Om klustret OpenShift inte distribuerar har finns några felsökning uppgifter som kan göras om du vill begränsa problemet. Visa distributionens status och jämföra med följande lista över koder.

- Slutkoden 3: Red Hat prenumeration användarnamn / lösenord eller organisations-ID aktiveringsnyckeln är felaktig
- Slutkoden 4: Red Hat programpools-ID är felaktigt eller det finns inga rättigheter
- Slutkoden 5: Det gick inte att etablera Docker tunn poolen volym
- Slutkoden 6: OpenShift klustret installationen misslyckades
- Slutkoden 7: OpenShift klustret installationen lyckades men Azure Cloud providerkonfigurationen misslyckades - master config på huvudnod problemet
- Slutkoden 8: OpenShift klustret installationen lyckades men Azure-molnet providerkonfigurationen misslyckades - nod config på huvudnod problemet
- Slutkoden 9: OpenShift klustret installationen lyckades men Azure-molnet providerkonfigurationen misslyckades - nod config på Infra eller Appnod problemet
- Slutkoden 10: OpenShift klustret installationen har slutförts men Azure Cloud providerkonfigurationen misslyckades - korrigera Master noder eller går inte att ange Master som unschedulable
- Slutkoden 11: Det gick inte att distribuera med mått
- Slutkoden 12: Det gick inte att distribuera loggning

För slutkoder 7 – 10 OpenShift klustret installerades men Molntjänstleverantör för Azure-konfigurationen misslyckades. Du kan SSH till huvudnod (ursprungliga) eller Skyddsmiljö nod (behållaren plattform) och från SSH till varje nod i klustret och åtgärda problemen.

En vanlig orsak till fel med slutkoden koder 7 - 9 är huvudnamn för tjänsten inte har rätt behörighet för att prenumerationen eller resursgruppen. Om det verkligen är problemet tilldela rätt behörighet och manuellt köra skriptet som misslyckats en alla efterföljande skript.
Se till att starta om tjänsten misslyckades (till exempel systemctl omstart atomic-openshift-node.service) innan du kör skripten igen.

För ytterligare felsökning, SSH till din huvudnod på port 2200 (ursprungliga) eller Skyddsmiljö nod på port 22 (behållaren Platform). Du måste vara rot (sudo su-) och gå sedan till följande katalog: /var/lib/waagent/custom-script/download

Du bör se en mapp med namnet '0' och '1'. I var och en av dessa mappar ser du två filer, stderr och stdout. Du kan titta igenom filerna för att fastställa där felet inträffade.
