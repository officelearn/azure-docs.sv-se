---
title: Versioner som stöds i Azure Database för PostgreSQL – enskild Server
description: Beskriver versionerna som stöds i Azure Database för PostgreSQL – enskild Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 0fc4eb90d9bed3877b4618fd839d9e8bf1608718
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063726"
---
# <a name="supported-postgresql-database-versions"></a>PostgreSQL-databas-versioner som stöds
Microsoft syftar till att stödja n-2 versioner av PostgreSQL-motorn i Azure Database för PostgreSQL – enskild Server. Versionerna som är den aktuella huvudversionen på Azure (n) och de två föregående största versionerna (-2).

Azure Database för PostgreSQL stöder för närvarande följande versioner:

## <a name="postgresql-version-112"></a>PostgreSQL-Version 11.2
Referera till den [PostgreSQL-dokumentation](https://www.postgresql.org/docs/11/static/release-11-2.html) mer information om förbättringar och korrigeringar i den här minor-version.

>[!NOTE]
> PostgreSQL version 11 är tillgänglig som förhandsversion. Stöd för att skapa med Azure portal distribueras och ännu inte tillgängliga i din region. Du kan använda den [Azure CLI](quickstart-create-server-database-azure-cli.md) att skapa en Postgres-11-server i valfri region. Till exempel `az postgres server create -g group -n server -u username -p password -l westeurope --sku-name GP_Gen5_2 --version 11`.

## <a name="postgresql-version-107"></a>PostgreSQL-Version 10.7
Referera till den [PostgreSQL-dokumentation](https://www.postgresql.org/docs/10/static/release-10-7.html) mer information om förbättringar och korrigeringar i den här minor-version.

## <a name="postgresql-version-9612"></a>PostgreSQL-Version 9.6.12
Referera till den [PostgreSQL-dokumentation](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html) mer information om förbättringar och korrigeringar i den här minor-version.

## <a name="postgresql-version-9516"></a>PostgreSQL-Version 9.5.16
Referera till den [PostgreSQL-dokumentation](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html) vill veta mer om förbättringar och korrigeringar i den här minor-version.

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Azure Database för PostgreSQL hanterar automatiskt delversion korrigeringar. För närvarande stöds huvudversion uppgradering inte. Till exempel stöds uppgradera från PostgreSQL 9.5 till PostgreSQL 9.6 inte. Om du vill uppgradera till nästa större version, skapa en databas [dumpa och Återställ](./howto-migrate-using-dump-and-restore.md) den till en server som har skapats med ny version.

## <a name="next-steps"></a>Nästa steg
Mer information om stöd för olika PostgreSQL-tillägg finns i [PostgreSQL-tillägg](concepts-extensions.md).
