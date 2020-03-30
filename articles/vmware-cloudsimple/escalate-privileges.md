---
title: Azure VMware-lösning från CloudSimple – Eskalera CloudSimple-privilegier
description: Beskriver hur du eskalerar CloudSimple-behörigheter för att utföra administrativa funktioner i Private Cloud vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 36c6969ed89d0bb9222f52aa81de0d4128b9e533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025341"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>Eskalera CloudSimple-behörigheter för att utföra administrativa funktioner i Private Cloud vCenter

CloudSimple-privilegier-metoden är utformad för att ge vCenter-användare de privilegier de behöver för att utföra normala åtgärder. I vissa fall kan en användare kräva ytterligare behörigheter för att utföra en viss uppgift.  Du kan eskalera privilegier för en vCenter SSO-användare under en begränsad period.

Orsaker till eskalerande privilegier kan vara följande:

* Konfiguration av identitetskällor
* Användarhantering
* Borttagning av distribuerad portgrupp
* Installera vCenter-lösningar (t.ex. säkerhetskopieringsappar)
* Skapa tjänstkonton

> [!WARNING]
> Åtgärder som vidtas i det eskalerade privilegierade tillståndet kan påverka systemet negativt och kan leda till att systemet blir otillgängligt. Utför endast nödvändiga åtgärder under eskaleringsperioden.

Från CloudSimple-portalen [eskalerar du behörigheter](escalate-private-cloud-privileges.md) för den lokala cloudowner-användaren på vCenter SSO.  Du kan bara eskalera fjärranvändarens behörighet om ytterligare identitetsprovider har konfigurerats på vCenter.  Eskalering av privilegier innebär att lägga till den valda användaren i gruppen vSphere inbyggd administratörer.  Endast en användare kan ha eskalerade privilegier.  Om du behöver eskalera en annan användares privilegier de-eskalerar först de-eskalera privilegierna för de aktuella användarna.

Användare från ytterligare identitetskällor måste läggas till som medlemmar i CloudOwner-gruppen.

> [!CAUTION]
> Nya användare får endast läggas till *i Cloud-Owner-Group,* *Cloud-Global-Cluster-Admin-Group,* *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* eller *Cloud-Global-VM-Admin-Group*.  Användare som läggs till i gruppen *Administratörer* tas bort automatiskt.  Endast tjänstkonton får läggas till *i grupp-* och tjänstkonton för administratörer får inte användas för att logga in på vSphere-webbgränssnittet.

Under eskaleringsperioden använder CloudSimple automatisk övervakning med associerade varningsmeddelanden för att identifiera eventuella oavsiktliga ändringar i miljön.
