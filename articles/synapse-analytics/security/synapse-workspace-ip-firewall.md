---
title: Konfigurera IP-brandväggsregler i Azure Synapse Analytics
description: En artikel som lär dig att konfigurera IP-brandväggsregler i Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: f36a76187538d93b57d2d3f5973408f141271f67
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424113"
---
# <a name="azure-synapse-analytics-ip-firewall-rules-preview"></a>Azure Synapse Analytics IP-brandväggsregler (förhandsversion)

I den här artikeln beskrivs IP-brandväggsregler och du får lära dig hur du konfigurerar dem i Azure Synapse Analytics.

## <a name="ip-firewall-rules"></a>REGLER för IP-brandväggen

IP-brandväggsregler beviljar eller nekar åtkomst till din Synapse-arbetsyta baserat på den ursprungliga IP-adressen för varje begäran. Du kan konfigurera IP-brandväggsregler för arbetsytan. IP-brandväggsregler som konfigurerats på arbetsytasnivå gäller för alla offentliga slutpunkter i arbetsytan (SQL-pooler, SQL on-demand och utveckling).

## <a name="create-and-manage-ip-firewall-rules"></a>Skapa och hantera IP-brandväggsregler

Ip-brandväggsregler läggs till på en Synapse-arbetsyta. Om du vill lägga till en IP-brandvägg på arbetsytan väljer du **Säkerhet + nätverk** och kontrollerar Tillåt anslutningar från alla **IP-adresser** när arbetsytan skapas.

![Azure portal Synapse arbetsyta IP-konfiguration.](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![Azure portal Synapse arbetsyta IP-konfiguration.](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

Du kan också lägga till IP-brandväggsregler på en Synapse-arbetsyta när arbetsytan har skapats. Välj **Brandväggar** under **Säkerhet** från Azure-portalen. Om du vill lägga till en ny IP-brandväggsregel ger du den ett namn, Start-IP och End IP. Välj **Spara** när du är klar.

![Azure Synapse workspace IP-konfiguration i Azure-portalen.](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connecting-to-synapse-from-your-own-network"></a>Ansluta till Synapse från ditt eget nätverk

Du kan ansluta till din Synapse-arbetsyta med Synapse Studio. Du kan också använda SQL Server Management Studio (SSMS) för att ansluta till SQL-resurserna (SQL-pooler och SQL on-demand) på arbetsytan.

Kontrollera att brandväggen i nätverket och den lokala datorn tillåter utgående kommunikation på TCP-portarna 80, 443 och 1443 för Synapse Studio.

Du måste också tillåta utgående kommunikation på UDP-port 53 för Synapse Studio. Om du vill ansluta med verktyg som SSMS och Power BI måste du tillåta utgående kommunikation på TCP-port 1433.

Om du använder standardinställningen för omdirigeradanslutning kan du behöva tillåta utgående kommunikation på ytterligare portar. Du kan läsa mer om anslutningsprinciper här.

## <a name="next-steps"></a>Nästa steg

Skapa en [Azure Synapse-arbetsyta](../quickstart-create-workspace.md)

Skapa en Azure Synapse-arbetsyta med ett [hanterat virtuella nätverk för arbetsyta](./synapse-workspace-managed-vnet.md)