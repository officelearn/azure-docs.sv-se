---
title: "Felsöka OpenShift distribution i Azure | Microsoft Docs"
description: "Felsöka OpenShift distribution i Azure."
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
ms.openlocfilehash: 35e554d3a9c7e7d56546ae9723c33eb59e906472
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2017
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Felsöka OpenShift distribution i Azure

Om klustret OpenShift inte distribuerar har försök med dessa felsöka aktiviteter för att begränsa problemet. Visa distributionens status och jämföra med följande lista över koder:

- Slutkod 3: din Red Hat prenumeration användarnamn / lösenord eller organisations-ID aktiveringsnyckeln är felaktig
- Slutkod 4: din Red Hat programpoolens ID är felaktigt eller det finns inga rättigheter
- Slutkod 5: Det gick inte att etablera Docker tunn poolen volym
- Slutkod 6: OpenShift kluster-installationen misslyckades
- Slutkod 7: OpenShift klustret installationen lyckades men Azure Cloud Solution Provider konfigurationen misslyckades - master config på huvudnod problemet
- Slutkod 8: OpenShift klustret installationen lyckades men Azure Cloud Solution Provider konfigurationen misslyckades - nod config på huvudnod problemet
- Slutkod 9: OpenShift klustret installationen lyckades men Azure Cloud Solution Provider konfigurationen misslyckades - nod config på Infra eller Appnod problemet
- Slutkod 10: OpenShift klustret installationen har slutförts men konfiguration för Azure Cloud Solution Provider misslyckades - korrigera Master noder eller går inte att ange Master som unschedulable
- Slutkod 11: Det gick inte att distribuera mått
- Slutkod 12: Det gick inte att distribuera loggning

OpenShift klustret har installerats för slutkoder 7 – 10, men konfigurationen av Azure Cloud Solution Provider misslyckades. Du kan SSH till huvudnoden (OpenShift ursprung) eller skyddsmiljö-noden (OpenShift behållare plattform) och från SSH till varje nod i klustret att åtgärda problemen.

En vanlig orsak till fel med slutkoder 7-9 är att tjänstens huvudnamn inte har rätt behörighet för att prenumerationen eller resursgruppen. Om det är problemet tilldela rätt behörighet och manuellt köra skriptet som misslyckats och alla efterföljande skript.

Se till att starta om tjänsten misslyckades (till exempel systemctl omstart atomic-openshift-node.service) innan du kör skripten igen.

För ytterligare felsökning, SSH till din master noden på port 2200 (ursprungliga) eller skyddsmiljö på port 22 (behållaren Platform). Du måste finnas i roten (sudo su-) och bläddra sedan till följande katalog: /var/lib/waagent/custom-script/download.

Här ser du mappar med namnet ”0” och ”1”. Var och en av mapparna visas i två filer, ”stderr” och ”stdout”. Titta igenom filerna för att fastställa där felet inträffade.
