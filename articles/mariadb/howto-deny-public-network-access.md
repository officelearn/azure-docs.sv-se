---
title: Neka offentlig nätverksåtkomst - Azure-portal - Azure Database för MariaDB
description: Lär dig hur du konfigurerar Neka offentlig nätverksåtkomst med Azure-portalen för din Azure-databas för MariaDB
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 87b6033e486e9009573436628d0183c8a022aced
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375244"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Neka offentlig nätverksåtkomst i Azure Database för MariaDB med Azure-portal

I den här artikeln beskrivs hur du kan konfigurera en Azure-databas för MariaDB-server för att neka alla offentliga konfigurationer och endast tillåta anslutningar via privata slutpunkter för att ytterligare förbättra nätverkssäkerheten.

## <a name="prerequisites"></a>Krav

För att slutföra den här guiden behöver du:

* En [Azure-databas för MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Ange neka offentlig nätverksåtkomst

Så här anger du att MariaDB-servern nekar offentlig nätverksåtkomst:

1. Välj [Azure portal](https://portal.azure.com/)din befintliga Azure-databas för MariaDB-server i Azure-portalen.

1. Klicka på **Anslutningssäkerhet** under **Inställningar**på sidan MariaDB-server för att öppna konfigurationssidan för anslutningssäkerhet.

1. I Neka offentlig nätverksåtkomst väljer du **Ja** för att aktivera allmän åtkomst för din MariaDB-server.

    ![Azure-databas för MariaDB neka nätverksåtkomst](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Klicka på **Spara** för att spara ändringarna.

1. Ett meddelande bekräftar att anslutningssäkerhetsinställningen har aktiverats.

    ![Azure Database för MariaDB Neka nätverksåtkomst lyckades](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du [skapar aviseringar om mått](howto-alert-metric.md).