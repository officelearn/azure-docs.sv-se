---
title: TLS-konfiguration – Azure Portal-Azure Database for PostgreSQL-enskild server
description: Lär dig hur du ställer in TLS-konfiguration med Azure Portal för din Azure Database for PostgreSQL enskild server
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 049b30689c86881ce89d4fbcd54175ee1ee5497e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375114"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql---single-server-using-azure-portal"></a>Konfigurera TLS-inställningar i Azure Database for PostgreSQL-enskild server med Azure Portal

I den här artikeln beskrivs hur du kan konfigurera en Azure Database for PostgreSQL-enskild server för att genomdriva en lägsta TLS-version för alla inkommande anslutningar, vilket bidrar till att förbättra nätverks säkerheten.

Kunder kan nu använda TLS-versionen för att ansluta till sin Azure Database for PostgreSQL-enskild server. Kunder kan nu välja att ange den lägsta TLS-versionen för sin databas server. Om du till exempel anger den lägsta TLS-inställningen version till TLS 1,0 betyder det att servern tillåter anslutningar från klienter som använder TLS 1,0, 1,1 och 1.2 +. Du kan också ställa in detta på 1,2 innebär att du bara tillåter anslutningar från klienter som använder TLS 1,2 och att alla anslutningar med TLS 1,0 och TLS 1,1 avvisas.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra den här guiden:

* En [Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>Ange TLS-konfigurationer för Azure Database for PostgreSQL-enskild server

Följ de här stegen för att ställa in PostgreSQL enskild server lägsta TLS-version:

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for PostgreSQL-enskild server.

1.  På sidan Azure Database for PostgreSQL-enskild server, under **Inställningar**, klickar du på **anslutnings säkerhet** för att öppna sidan anslutnings säkerhets konfiguration.

1. I **lägsta TLS-version**väljer du **1,2** för att neka anslutningar med en TLS-version som är mindre än TLS 1,2 för din postgresql-server.

    ![Azure Database for PostgreSQL TLS-konfiguration för enskild server](./media/howto-tls-configurations/setting-tls-value.png)

1. Klicka på **Spara** för att spara ändringarna.

1. Ett meddelande bekräftar att anslutnings säkerhets inställningen har Aktiver ATS.

    ![Azure Database for PostgreSQL-konfiguration av en enskild server-TLS lyckades](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [hur du skapar aviseringar för mått](howto-alert-on-metric.md).
