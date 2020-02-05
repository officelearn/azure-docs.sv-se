---
title: Hantera Azure VMware-lösningar (AVS) privat moln
description: Beskriver de funktioner som är tillgängliga för hantering av moln resurser och aktiviteter i molnet
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 47bf2251f71204b99245c1a9d55ef87157c41dd8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014835"
---
# <a name="manage-avs-private-cloud-resources-and-activities"></a>Hantera resurser och aktiviteter för molnets privata moln

AVS-privata moln hanteras från AVS-portalen. Kontrol lera status, tillgängliga resurser, aktivitet i molnets privata moln och andra inställningar från AVS-portalen.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Åtkomst till AVS-portalen

Få åtkomst till [AVS-portalen](access-cloudsimple-portal.md).

## <a name="view-the-list-of-avs-private-clouds"></a>Visa listan över AVS-privata moln

På fliken **AVS-privata moln** på sidan **resurser** visas alla moln privata moln i din prenumeration. Information inkluderar namn, antal vSphere-kluster, plats, nuvarande tillstånd för det privata moln molnet och resursinformation.

![Sidan AVS-privat moln](media/manage-private-cloud.png)

Välj ett privat AVS-moln för ytterligare information och åtgärder.

## <a name="avs-private-cloud-summary"></a>Sammanfattning av molnets privata moln

Visa en omfattande sammanfattning av det valda molnet i AVS-molnet. Sammanfattnings sidan innehåller de DNS-servrar som distribueras i det privata moln molnet. Du kan konfigurera DNS-vidarebefordran från lokala DNS-servrar till dina DNS-servrar för molnets privata moln. Mer information om vidarebefordring av DNS finns i [Konfigurera DNS för namn matchning för molnets privata moln vCenter från lokalt](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![Sammanfattning av molnets privata moln](media/private-cloud-summary.png)

### <a name="available-actions"></a>Tillgängliga åtgärder

* [Starta vSphere-klienten](https://docs.azure.cloudsimple.com/vsphere-access/). Få åtkomst till vCenter för det här AVS-privata molnet.
* [Köpa noder](create-nodes.md). Lägg till noder i det här AVS-privata molnet.
* [Expandera](expand-private-cloud.md). Lägg till noder i det här AVS-privata molnet.
* **Uppdatera**. Uppdatera informationen på den här sidan.
* **Ta bort**. Du kan när som helst ta bort det privata AVS-molnet. **Innan du tar bort kontrollerar du att du har säkerhetskopierat alla system och data.** Om du tar bort ett privat AVS-moln tas alla virtuella datorer, vCenter-konfiguration och data bort. Klicka på **ta bort** i sammanfattnings avsnittet för det valda AVS-molnet. Efter borttagning raderas alla data i molnets privata moln i en säker och mycket kompatibel radering-process.
* [Ändra vSphere-privilegier](escalate-private-cloud-privileges.md). Eskalera dina privilegier på det här AVS-molnet.

## <a name="avs-private-cloud-vlanssubnets"></a>VLAN/undernät för moln-privata moln

Visa listan över definierade VLAN/undernät för det valda molnets privata moln. Listan innehåller de hanterings-VLAN/-undernät som skapats när det privata moln molnet skapades.

![AVS-privat moln – VLAN/undernät](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Tillgängliga åtgärder

* [Lägg till VLAN/undernät](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Lägg till ett VLAN/en delmängd till det här AVS-privata molnet.

Välj ett VLAN/undernät för följande åtgärder
* [Koppla brand Väggs tabell](https://docs.azure.cloudsimple.com/firewall/). Koppla en brand Väggs tabell till det här AVS-molnet.
* **Ras**
* **Ta bort** (endast användardefinierade VLAN/undernät)

## <a name="avs-private-cloud-activity"></a>Aktivitet i molnets privata moln

Visa följande information för det valda AVS-molnet. Aktivitets informationen är en filtrerad lista över alla aktiviteter för det valda AVS-molnet. Den här sidan visar upp till 25 senaste aktiviteter.

* Senaste aviseringar
* Senaste händelser
* Senaste aktiviteter
* Senaste granskning

![Molnets privata moln – aktivitet](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Moln räcken

Moln räcken är Bygg stenarna i ditt AVS-privata moln. Varje rack tillhandahåller en kapacitets enhet. AVS konfigurerar automatiskt moln räcken baserat på dina val när du skapar eller utökar ett moln privat moln. Visa en fullständig lista över moln räcken, inklusive det AVS-privata moln som varje har tilldelats.

![Molnets privata moln – moln räcken](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere hanterings nätverk

Lista över VMware-hanterings resurser och virtuella datorer som för närvarande är konfigurerade i det privata moln molnet. Informationen omfattar program varu versionen, det fullständigt kvalificerade domän namnet (FQDN) och IP-adressen för resurserna.

![AVS-privat moln – vSphere hanterings nätverk](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer i Azure](quickstart-create-vmware-virtual-machine.md)
* Lär dig mer om [moln privata moln](cloudsimple-private-cloud.md)