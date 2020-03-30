---
title: Skapa VLAN/undernät - Azure VMware-lösning av CloudSimple
description: Azure VMware Solutions by CloudSimple - Beskriver hur du skapar och hanterar VLAN/undernät för dina privata moln och tillämpar sedan brandväggsregler.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0160513eb9abca54adbc3819b982348dc202c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77566003"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>Skapa och hantera VLAN/undernät för dina privata moln

Öppna fliken VLAN/Undernät på sidan Nätverk för att skapa och hantera VLAN/undernät för dina privata moln. När du har skapat ett VLAN/-undernät kan du använda brandväggsregler.

## <a name="create-a-vlansubnet"></a>Skapa ett VLAN/-undernät

1. [Öppna CloudSimple-portalen](access-cloudsimple-portal.md) och välj **Nätverk** på sidomenyn.
2. Välj **VLAN/undernät**.
3. Klicka på **Skapa VLAN/Undernät**.

    ![VLAN/undernätssida](media/vlan-subnet-page.png)

4. Välj det privata molnet för det nya VLAN/-undernätet.
5. Ange ett VLAN-ID.
6. Ange undernätsnamnet.
7. Om du vill aktivera routning på VLAN (undernät) anger du CIDR-området för undernät. Kontrollera att CIDR-intervallet inte överlappar något av dina lokala undernät, Azure-undernät eller gateway-undernät.
8. Klicka på **Skicka**.

    ![Skapa VLAN/undernät](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> Det finns en kvot på 30 VLAN per privat moln. Dessa gränser kan höjas genom [att kontakta supporten.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>Använda VLAN-information för att ställa in en distribuerad portgrupp i vSphere

Om du vill skapa en distribuerad portgrupp i vSphere följer du instruktionerna i VMware-avsnittet "Lägg till en distribuerad portgrupp" i <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">vSphere Networking Guide</a>. När du konfigurerar den distribuerade portgruppen anger du VLAN-informationen från CloudSimple-konfigurationen.

![Distribuerad portgrupp](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Markera en brandväggstabell

Brandväggstabeller och tillhörande regler definieras på sidan **Nätverks>-brandväggstabeller.** Om du vill välja den brandväggstabell som ska tillämpas på VLAN/undernätet för ett privat moln markerar du VLAN/undernätet och klickar på **Tabellbilaga** för brandvägg på sidan **VLAN/undernät.** Se [Brandväggstabeller](firewall.md) för instruktioner om hur du konfigurerar brandväggstabeller och definierar regler.

![Länk till brandväggstabell](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Ett undernät kan associeras med en brandväggstabell. En brandväggstabell kan associeras med flera undernät.

## <a name="edit-a-vlansubnet"></a>Redigera ett VLAN/-undernät

Om du vill redigera inställningarna för ett VLAN/-undernät markerar du det på sidan **VLAN/undernät** och klickar på ikonen **Redigera.** Gör ändringar och klicka på **Undermet**.

## <a name="delete-a-vlansubnet"></a>Ta bort ett VLAN/-undernät

Om du vill ta bort ett VLAN/-undernät markerar du det på sidan **VLAN/undernät** och klickar på ikonen **Ta bort.** Klicka på **Ta bort** för att bekräfta.
