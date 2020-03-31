---
title: Neka offentlig nätverksåtkomst - Azure-portal - Azure Database för MySQL
description: Lär dig hur du konfigurerar Neka offentlig nätverksåtkomst med Azure-portalen för din Azure-databas för MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b5f93f3a3583900810ca75f925c6a88df9102652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79374958"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Neka offentlig nätverksåtkomst i Azure Database för MySQL med Azure-portal

I den här artikeln beskrivs hur du kan konfigurera en Azure-databas för MySQL-server för att neka alla offentliga konfigurationer och endast tillåta anslutningar via privata slutpunkter för att ytterligare förbättra nätverkssäkerheten.

## <a name="prerequisites"></a>Krav

För att slutföra den här guiden behöver du:

* En [Azure-databas för MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Ange neka offentlig nätverksåtkomst

Så här anger du MySQL-server Neka offentlig nätverksåtkomst:

1. Välj [Azure portal](https://portal.azure.com/)din befintliga Azure-databas för MySQL-server i Azure-portalen.

1. Klicka på **Anslutningssäkerhet** under **Inställningar**på sidan MySQL-server för att öppna konfigurationssidan för anslutningssäkerhet.

1. I **Neka offentlig nätverksåtkomst**väljer du **Ja** för att aktivera neka offentlig åtkomst för MySQL-servern.

    ![Azure-databas för MySQL neka nätverksåtkomst](./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG)

1. Klicka på **Spara** för att spara ändringarna.

1. Ett meddelande bekräftar att anslutningssäkerhetsinställningen har aktiverats.

    ![Azure Database for MySQL Neka nätverksåtkomst lyckas](./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du [skapar aviseringar om mått](howto-alert-on-metric.md).