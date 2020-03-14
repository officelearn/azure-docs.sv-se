---
title: TLS-konfiguration – Azure Portal-Azure Database for MySQL
description: Lär dig hur du ställer in TLS-konfiguration med Azure Portal för din Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 495fea21c4fca12cd1ec6c1ea45105178d72fe1b
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375296"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>Konfigurera TLS-inställningar i Azure Database for MySQL att använda Azure Portal

Den här artikeln beskriver hur du kan konfigurera en Azure Database for MySQL-server för att genomdriva anslutningar för en lägsta TLS-version för att gå igenom och neka alla anslutningar med lägre TLS-version och därmed förbättra nätverks säkerheten.

Kunder kan nu använda TLS-versionen för att ansluta till sina Azure Database for MySQL. Kunder kan nu välja att ange den lägsta TLS-versionen för sin databas server. Om du till exempel ställer in denna minsta TLS-version på 1,0 innebär det att du tillåter klienter att ansluta med TLS 1.0, 1.1 och 1,2. Du kan också ställa in detta på 1,2 innebär att du bara tillåter klienter som ansluter med TLS 1,2 och alla inkommande anslutningar med TLS 1,0 och TLS 1,1 att avvisas.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra den här guiden:

* En [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>Ange TLS-konfigurationer för Azure Database for MySQL

Följ de här stegen om du vill ange lägsta TLS-server version:

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for MySQL-server.

1. På sidan MySQL server, under **Inställningar**, klickar du på **anslutnings säkerhet** för att öppna sidan anslutnings säkerhets konfiguration.

1. I **lägsta TLS-version**väljer du **1,2** för att neka anslutningar med en TLS-version som är mindre än TLS 1,2 för MySQL-servern.

    ![Azure Database for MySQL TLS-konfiguration](./media/howto-tls-configurations/setting-tls-value.png)

1. Klicka på **Spara** för att spara ändringarna.

1. Ett meddelande bekräftar att anslutnings säkerhets inställningen har Aktiver ATS.

    ![Azure Database for MySQL TLS-konfiguration lyckades](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [hur du skapar aviseringar för mått](howto-alert-on-metric.md).
