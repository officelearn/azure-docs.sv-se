---
title: Brandväggsregler på servernivå
description: Brandväggsregler på servernivå
keywords: SQL-anslutning, anslutningssträng
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60202184"
---
1. Logga in på [Azure Portal](https://portal.azure.com/).

2. I listan till vänster, Välj **alla tjänster**.

3. Bläddra och välj **SQL-servrar**.

    ![Hitta din Azure SQL Database-server i portalen][b21-FindServerInPortal]
5. Börja skriva namnet på servern i textrutan filter. Raden visas.

6. Välj raden för din server. Ett blad för servern visas.

7. På din serverbladet och välj **inställningar**.

8. Välj **brandväggen**.

    ![Välj Inställningar > brandvägg][b31-SettingsFirewallNavig]
9. Välj **Lägg till klient IP**. Skriv ett namn för din nya regel i den första textrutan.

10. Ange de låga och höga IP-adressvärdena för intervallet som du vill aktivera.

    * Det kan vara praktiskt att ha lågt värde slutet med **.0** och sluta med hög värdet **.255**.

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
