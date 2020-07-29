---
title: Neka offentlig nätverks åtkomst – Azure Portal-Azure Database for PostgreSQL-enskild server
description: Lär dig hur du konfigurerar neka offentlig nätverks åtkomst med hjälp av Azure Portal för din Azure Database for PostgreSQL enskild server
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 01a256e17b1101782eaee9bebd85f5e7093773d3
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287782"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Neka offentlig nätverks åtkomst i Azure Database for PostgreSQL enskild server med Azure Portal

I den här artikeln beskrivs hur du kan konfigurera en Azure Database for PostgreSQL enskild server för att neka alla offentliga konfigurationer och bara tillåta anslutningar via privata slut punkter för att ytterligare förbättra nätverks säkerheten.

## <a name="prerequisites"></a>Krav

För att slutföra den här instruktions guiden behöver du:

* En [Azure Database for PostgreSQL enskild server](quickstart-create-server-database-portal.md)

## <a name="set-deny-public-network-access"></a>Ange neka offentlig nätverks åtkomst

Följ dessa steg för att ställa in PostgreSQL-Server neka offentlig nätverks åtkomst:

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for PostgreSQL enskild server.

1. På sidan PostgreSQL enskild server under **Inställningar**klickar du på **anslutnings säkerhet** för att öppna sidan anslutnings säkerhets konfiguration.

1. I **neka offentlig nätverks åtkomst**väljer du **Ja** för att aktivera neka offentlig åtkomst för postgresql-servern.

    ![Azure Database for PostgreSQL enskild server neka nätverks åtkomst](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Klicka på **Spara** för att spara ändringarna.

1. Ett meddelande bekräftar att anslutnings säkerhets inställningen har Aktiver ATS.

    ![Azure Database for PostgreSQL enskild server neka nätverks åtkomst](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [hur du skapar aviseringar för mått](howto-alert-on-metric.md).
