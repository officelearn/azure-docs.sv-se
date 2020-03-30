---
title: Hantera Azure VMware-lösning via CloudSimple Private Cloud
description: Beskriver de funktioner som är tillgängliga för att hantera dina CloudSimple Private Cloud-resurser och aktivitet
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 620c0226d3aca907352658ebbe1b94c7673d91cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014835"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Hantera privata molnresurser och aktiviteter

Privata moln hanteras från CloudSimple-portalen.  Kontrollera status, tillgängliga resurser, aktivitet i det privata molnet och andra inställningar från CloudSimple-portalen.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Få åtkomst till CloudSimple-portalen

Öppna [CloudSimple-portalen](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Visa listan över privata moln

På fliken **Privata moln** på sidan **Resurser** visas alla privata moln i din prenumeration. Informationen omfattar namn, antal vSphere-kluster, plats, aktuellt tillstånd för det privata molnet och resursinformation.

![Privat molnsida](media/manage-private-cloud.png)

Välj ett privat moln för ytterligare information och åtgärder.

## <a name="private-cloud-summary"></a>Sammanfattning av privat moln

Visa en omfattande sammanfattning av det valda privata molnet.  Sammanfattningssidan innehåller DNS-servrar som distribueras i det privata molnet.  Du kan ställa in DNS-vidarebefordran från lokala DNS-servrar till dina privata DNS-servrar i molnet.  Mer information om DNS-vidarekoppling finns i [Konfigurera DNS för namnmatchning för Private Cloud vCenter från lokalt](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![Sammanfattning av privat moln](media/private-cloud-summary.png)

### <a name="available-actions"></a>Tillgängliga åtgärder

* [Starta vSphere-klient](https://docs.azure.cloudsimple.com/vsphere-access/). Öppna vCenter för det här privata molnet.
* [Inköpsnoder](create-nodes.md). Lägg till noder i det här privata molnet.
* [Expandera](expand-private-cloud.md). Lägg till noder i det här privata molnet.
* **Uppdatera**. Uppdatera informationen på den här sidan.
* **Ta bort**. Du kan ta bort det privata molnet när som helst. **Innan du tar bort det kontrollerar du att du har säkerhetskopierat alla system och data.** Om du tar bort ett privat moln tas alla virtuella datorer, vCenter-konfiguration och data bort. Klicka på **Ta bort** i sammanfattningsavsnittet för det markerade privata molnet. Efter borttagning raderas alla privata molndata i en säker och mycket kompatibel raderingsprocess.
* [Ändra vSphere-privilegier](escalate-private-cloud-privileges.md).  Eskalera dina privilegier i det här privata molnet.

## <a name="private-cloud-vlanssubnets"></a>Privata moln-VLANS/undernät

Visa listan över definierade VLAN/undernät för det valda privata molnet.  Listan innehåller hanterings-VLAN/undernät som skapades när det privata molnet skapades.

![Privat moln – VLAN/undernät](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Tillgängliga åtgärder

* [Lägg till VLANS/undernät](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Lägg till en VLAN/delmängd i det här privata molnet.

Välj ett VLAN/-undernät för följande åtgärder
* [Bifoga brandväggstabell](https://docs.azure.cloudsimple.com/firewall/). Bifoga en brandväggstabell till det här privata molnet.
* **Redigera**
* **Ta bort** (endast användardefinierade VLAN/-undernät)

## <a name="private-cloud-activity"></a>Privat molnaktivitet

Visa följande information för det valda privata molnet.  Aktivitetsinformationen är en filtrerad lista över alla aktiviteter för det valda privata molnet.  Den här sidan visar upp till 25 nya aktiviteter.

* Senaste aviseringar
* Senaste händelserna
* Senaste uppgifter
* Senaste revisionen

![Privat moln - Aktivitet](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Molnställ

Molnställ är byggstenarna i ditt privata moln. Varje rack ger en kapacitet. CloudSimple konfigurerar automatiskt molnrack baserat på dina val när du skapar eller expanderar ett privat moln.  Visa hela listan över molnställ, inklusive det privata molnet som var och en har tilldelats.

![Privat moln - molnrack](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere Management Network

Lista över VMware-hanteringsresurser och virtuella datorer som för närvarande är konfigurerade i det privata molnet. Informationen omfattar programvaruversionen, fullständigt kvalificerat domännamn (FQDN) och IP-adressen för resurserna.

![Privat moln – vSphere Management Network](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* Läs mer om [privata moln](cloudsimple-private-cloud.md)