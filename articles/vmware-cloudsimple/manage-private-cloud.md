---
title: Hantera Azure VMware-lösning av CloudSimple privat moln
description: Beskriver funktionerna som är tillgängliga för att hantera dina CloudSimple privata molnresurser och aktivitet
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 05a2fb451b3acce1011c1d5f4cf17f0a865d57d0
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332579"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Hantera resurser i privata moln och aktivitet

Privata moln som hanteras från CloudSimple-portalen.  Kontrollera status, tillgängliga resurser, aktivitet på det privata molnet och andra inställningar från CloudSimple-portalen.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Få åtkomst till CloudSimple-portalen

Åtkomst till den [CloudSimple portal](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Visa en lista över privata moln

Den **privata moln** fliken på den **resurser** sidan listar alla privata moln i din prenumeration. Information omfattar namnet, antalet vSphere-kluster, plats, aktuell status för privata moln och resurs informationen.

![Private Cloud page](media/manage-private-cloud.png)

Välj ett privat moln för ytterligare information och åtgärder.

## <a name="private-cloud-summary"></a>Sammanfattning av privat moln

Visa en omfattande översikt över den valda privat moln.  Sammanfattningssida innehåller DNS-servrar som är distribuerade på det privata molnet.  Du kan ställa in DNS-vidarebefordran från den lokala DNS-servrar till dina privata moln DNS-servrar.  Mer information om vidarebefordran av DNS finns i [konfigurera DNS för namnmatchning för privat moln vCenter från den lokala](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![Private Cloud Summary](media/private-cloud-summary.png)

### <a name="available-actions"></a>Tillgängliga åtgärder

* [Starta vSphere-klienten](https://docs.azure.cloudsimple.com/vsphere-access/). Få åtkomst till vCenter för det här privata molnet.
* [Köpa noder](create-nodes.md). Lägga till noder i det här privata molnet.
* [Expandera](expand-private-cloud.md). Lägga till noder i det här privata molnet.
* **Uppdatera**. Uppdatera informationen på den här sidan.
* **Ta bort**. Du kan ta bort det privata molnet när som helst. **Innan du tar bort, se till att du har säkerhetskopierat alla system och data.** Ett privat moln tar du bort alla virtuella datorer, vCenter konfiguration och data. Klicka på **ta bort** i avsnittet Sammanfattning för valt privata moln. Efter borttagningen raderas alla data för privat moln i en säker och kompatibel med hög raderingen process.
* [Ändra behörigheter för vSphere](escalate-private-cloud-privileges.md).  Eskalera dina behörigheter i det här privata molnet.

## <a name="private-cloud-vlanssubnets"></a>Privata moln VLAN/undernät

Visa listan över definierade VLAN/undernät för det valda privata molnet.  Listan innehåller hanteringen VLAN/undernät skapas när det privata molnet har skapats.

![Privat moln - VLAN/undernät](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Tillgängliga åtgärder

* [Lägg till VLAN/undernät](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Lägg till ett VLAN/delmängd i detta privata moln.

Välj ett VLAN/undernät för följande åtgärder
* [Bifoga brandväggen tabell](https://docs.azure.cloudsimple.com/firewall/). Koppla en brandvägg tabell till det här privata molnet.
* **Redigera**
* **Ta bort** (endast användardefinierade VLAN/undernät)

## <a name="private-cloud-activity"></a>Privat moln-aktivitet

Visa följande information för det valda privata molnet.  Aktivitetsinformationen om är en filtrerad lista över alla aktiviteter för det valda privata molnet.  Den här sidan visar upp till 25 senaste aktiviteter.

* Nya varningar
* De senaste händelserna
* Senaste aktiviteter
* Senaste granskning

![Private Cloud - Activity](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Molnet rack

Molnet rack är byggblocken i ditt privata moln. Varje rack innehåller en enhet med kapacitet. CloudSimple konfigurerar automatiskt molnet rack baserat på dina val när du skapar eller expandera ett privat moln.  Visa en fullständig lista över molnet rack, inklusive privata moln som har tilldelats.

![Private Cloud - Cloud Racks](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere Management Network

Lista över VMware-hanteringsresurser och virtuella datorer som är konfigurerade på privat moln. Informationen inkluderar programvaruversion, fullständigt kvalificerade domännamnet (FQDN) och IP-adressen för resurserna.

![Private Cloud - vSphere Management Network](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* Läs mer om [privata moln](cloudsimple-private-cloud.md)