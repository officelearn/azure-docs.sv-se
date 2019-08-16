---
title: Azure VMware-lösning av CloudSimple – konfigurera brand Väggs tabeller och regler
description: Beskriver hur du konfigurerar privata moln brand Väggs tabeller och regler för att begränsa trafik på undernät och VLAN.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c56d469360814cb663b4c5c11689961225eebb2d
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69544674"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>Konfigurera brand Väggs tabeller och regler för privata moln

Med brand Väggs tabeller och associerade regler kan du ange begränsningar för trafik som ska gälla för särskilda undernät och virtuella lokala nätverk.

* Ett undernät kan associeras med en brand Väggs tabell.
* En brand Väggs tabell kan associeras med flera undernät.

## <a name="add-a-new-firewall-table"></a>Lägg till en ny brand Väggs tabell

1. [Öppna CloudSimple-portalen](monitor-activity.md) och välj **nätverk** på sido menyn.
2. Välj **brand Väggs tabeller**.
3. Välj **skapa brand Väggs tabell**.

    ![Sidan VLAN/undernät](media/firewall-tables-page.png)

4. Ange ett namn för tabellen.
5. En standard regel för tabellen visas. Klicka på **Skapa ny regel** för att skapa en ytterligare regel. Se följande procedur för mer information.
6. Klicka på **färdig** för att spara brand Väggs tabellen.

## <a name="firewall-rules"></a>Brandväggsregler

Brand Väggs regler avgör hur brand väggen behandlar vissa typer av trafik. Fliken **regler** för en vald brand Väggs tabell visar alla associerade regler.

![Tabell över brand Väggs regler](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Skapa en brandväggsregel

1. Visa inställningarna för att skapa en brand Väggs regel på något av följande sätt:
    * Klicka på **Lägg till regel** när du skapar en brand Väggs tabell.
    * Välj en viss brand Väggs tabell på sidan **nätverks > brand Väggs tabeller** och klicka på **Skapa ny brand Väggs regel**.
2. Konfigurera regeln enligt följande:
    * **Namn**. Ge regeln ett namn.
    * **Prioritet**. Tilldela regeln en prioritet. Regler med lägre tal körs först.
    * **Trafik typ**. Välj om regeln gäller för privata moln, Internet eller VPN-trafik (tillstånds lösa) eller för en offentlig IP-adress (tillstånds lös).
    * **Protokoll**. Välj det protokoll som omfattas av regeln (TCP, UDP eller något protokoll).
    * **Riktning**. Välj om regeln gäller inkommande eller utgående trafik. Du måste definiera separata regler för inkommande och utgående trafik.
    * **Åtgärd**. Välj den åtgärd som ska vidtas om regeln matchar (Tillåt eller neka).
    * **Källa**. Ange de källor som omfattas av regeln (CIDR-block, intern eller valfri källa).
    * **Käll port intervall**. Ange intervallet för portarna som omfattas av regeln.
    * **Riktning**. Välj inkommande eller utgående.
    * **Mål**. Ange de mål som omfattas av regeln (CIDR-block, intern eller valfri källa).
    * **Käll port intervall**. Ange intervallet för portarna som omfattas av regeln.

    ![Tilläggs regel för brand Väggs tabell](media/firewall-rule-create.png)

3. Klicka på **Slutför** för att spara regeln och lägga till den i listan över regler för brand Väggs tabellen.

## <a name="attach-vlanssubnets"></a>Bifoga VLAN/undernät

När du har definierat en brand Väggs tabell kan du ange de undernät som omfattas av reglerna i tabellen.

1. Välj en brand Väggs tabell på sidan **nätverks** > **brand Väggs tabeller** .
2. Öppna fliken **anslutna VLAN/undernät** .
3. Klicka på **Anslut till ett VLAN/undernät**.
4. Välj det privata molnet och VLAN. Det associerade under näts namnet och CIDR-blocket visas.
5. Klicka på **Skicka**.
