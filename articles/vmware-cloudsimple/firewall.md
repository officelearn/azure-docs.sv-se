---
title: Azure VMware Solution by CloudSimple – Konfigurera brandväggstabeller och regler
description: Beskriver hur du ställer in private cloud-brandväggstabeller och regler för att begränsa trafiken på undernät och VLAN.Describes how to set up Private Cloud firewall tables and rules to restrict traffic on subnets and VLAN.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 986f4b0da7254ebac3725a704f32af785c72fbcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244672"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>Konfigurera brandväggstabeller och regler för privata moln

Med brandväggstabeller och tillhörande regler kan du ange trafikbegränsningar som ska gälla för vissa undernät och VLAN.Vedsocieringstabeller och tillhörande regler kan du ange begränsningar för trafik som ska gälla för vissa undernät och VLAN.Vedsociã¤nde

* Ett undernät kan associeras med en brandväggstabell.
* En brandväggstabell kan associeras med flera undernät.

## <a name="add-a-new-firewall-table"></a>Lägga till en ny brandväggstabell

1. [Öppna CloudSimple-portalen](access-cloudsimple-portal.md) och välj **Nätverk** på sidomenyn.
2. Välj **brandväggstabeller**.
3. Välj **Skapa brandväggstabell**.

    ![VLAN/undernätssida](media/firewall-tables-page.png)

4. Ange ett namn på tabellen.
5. En standardregel för tabellen visas. Klicka på **Skapa ny regel** om du vill skapa ytterligare en regel. Mer information finns i följande procedur.
6. Klicka på **Klar** om du vill spara brandväggstabellen.

> [!IMPORTANT]
> Du kan skapa upp till två brandväggstabeller per privat moln.

## <a name="firewall-rules"></a>Brandväggsregler

Brandväggsregler avgör hur brandväggen behandlar specifika typer av trafik. På fliken **Regler** för en markerad brandväggstabell visas alla associerade regler.

![Tabell över brandväggsregler](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Skapa en brandväggsregel

1. Visa inställningarna för att skapa en brandväggsregel på något av följande sätt:
    * Klicka på **Lägg till regel** när du skapar en brandväggstabell.
    * Markera en viss brandväggstabell på sidan **Nätverks- > brandväggstabeller** och klicka på **Skapa ny brandväggsregel**.
2. Ställ in regeln på följande sätt:
    * **Namn**. Ge regeln ett namn.
    * **Prioritet**. Tilldela en prioritet till regeln. Regler med lägre tal körs först.
    * **Trafiktyp**. Välj om regeln är för Privat moln-, Internet- eller VPN-trafik (tillståndslös) eller för en offentlig IP-adress (tillståndskänslig).
    * **Protokoll**. Välj det protokoll som omfattas av regeln (TCP, UDP eller något protokoll).
    * **Riktning**. Välj om regeln är för inkommande eller utgående trafik. Du måste definiera separata regler för inkommande och utgående trafik.
    * **Åtgärd**. Välj den åtgärd som ska vidtas om regeln matchar (tillåt eller neka).
    * **Källa**. Ange de källor som omfattas av regeln (CIDR-block, internt eller någon källa).
    * **Källportintervall**. Ange det portintervall som omfattas av regeln.
    * **Riktning**. Välj inkommande eller utgående.
    * **Destination**. Ange de destinationer som omfattas av regeln (CIDR-block, internt eller någon källa).
    * **Källportintervall**. Ange det portintervall som omfattas av regeln.

    ![Tilläggsregel för brandväggstabell](media/firewall-rule-create.png)

3. Klicka på **Klar** om du vill spara regeln och lägga till den i listan med regler för brandväggstabellen.

> [!IMPORTANT]
> Varje brandväggstabell kan ha upp till 10 inkommande regler och 20 utgående regler. Dessa gränser kan höjas genom [att kontakta supporten.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="attach-vlanssubnets"></a><a name="attach-vlans-subnet"></a>Bifoga VLAN/undernät

När du har definierat en brandväggstabell kan du ange de undernät som omfattas av reglerna i tabellen.

1. Välj en brandväggstabell på sidan > **Nätverksbrandväggstabeller.** **Network**
2. Öppna fliken **Anslutna VLAN/Undernät.**
3. Klicka **på Bifoga till ett VLAN/-undernät**.
4. Välj det privata molnet och VLAN. Det associerade undernätsnamnet och CIDR-blocket visas.
5. Klicka på **Skicka**.
