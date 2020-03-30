---
title: Brandväggsregler på servernivå
description: Brandväggsregler på servernivå
keywords: sql-anslutning,anslutningssträng
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187422"
---
1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **Alla tjänster**i listan till vänster .

3. Bläddra och välj **SQL-servrar**.

    ![Hitta din Azure SQL Database-server i portalen][b21-FindServerInPortal]
5. Börja skriva namnet på servern i textrutan för filtret. Din rad visas.

6. Markera raden för servern. Ett blad för servern visas.

7. Välj **Inställningar**på serverbladet .

8. Välj **Brandvägg**.

    ![Välj inställningar > brandvägg][b31-SettingsFirewallNavig]
9. Välj **Lägg till klient-IP**. Skriv ett namn på den nya regeln i den första textrutan.

10. Skriv in de låga och höga IP-adressvärdena för det intervall som du vill aktivera.

    * Det kan vara praktiskt att ha det låga värdet med **0,0** och det höga värdet med **0,255**.

11. Välj **Spara**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
