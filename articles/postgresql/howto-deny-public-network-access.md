---
title: Neka offentlig nätverksåtkomst - Azure-portal - Azure Database för PostgreSQL - Enkel server
description: Lär dig hur du konfigurerar Neka offentlig nätverksåtkomst med Azure-portalen för din Azure-databas för en enda PostgreSQL-server
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4dff2321414721dbd415b468e59aea0ab4b3acee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375127"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Neka offentlig nätverksåtkomst i Azure Database för PostgreSQL Single-server med Azure-portal

I den här artikeln beskrivs hur du kan konfigurera en Azure-databas för PostgreSQL Single-server för att neka alla offentliga konfigurationer och endast tillåta anslutningar via privata slutpunkter för att ytterligare förbättra nätverkssäkerheten.

## <a name="prerequisites"></a>Krav

För att slutföra den här guiden behöver du:

* En [Azure-databas för en enda PostgreSQL-server](quickstart-create-PostgreSQL Single server-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Ange neka offentlig nätverksåtkomst

Så här anger du postgresQL-åtkomst för en server neka offentligt nätverk:

1. Välj din befintliga Azure-databas för PostgreSQL-server i [Azure-portalen.](https://portal.azure.com/)

1. Klicka på **Anslutningssäkerhet** under **Inställningar**på sidan PostgreSQL Single server för att öppna konfigurationssidan för anslutningssäkerhet.

1. I **Neka offentlig nätverksåtkomst**väljer du **Ja** för att aktivera neka offentlig åtkomst för din PostgreSQL-server.

    ![Azure-databas för PostgreSQL Enkel server Neka nätverksåtkomst](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Klicka på **Spara** för att spara ändringarna.

1. Ett meddelande bekräftar att anslutningssäkerhetsinställningen har aktiverats.

    ![Azure Database för PostgreSQL Enkel server Neka nätverksåtkomst lyckades](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du [skapar aviseringar om mått](howto-alert-on-metric.md).