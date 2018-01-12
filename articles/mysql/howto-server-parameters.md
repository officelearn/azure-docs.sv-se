---
title: "Konfigurera parametrar för Server i Azure-databas för MySQL | Microsoft Docs"
description: "Den här artikeln beskriver hur du konfigurerar MySQL serverparametrar i Azure-databas för MySQL med Azure-portalen."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/10/2017
ms.openlocfilehash: f3b32c1f6b33bc60b50f1496414a300db468dc92
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Konfigurera parametrar för server i Azure-databas för MySQL med hjälp av Azure portal

Azure-databas för MySQL stöder konfiguration av vissa serverparametrar. Det här avsnittet beskriver hur du konfigurerar dessa parametrar med hjälp av Azure portal. Parametrar för server kan justeras. 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigera till serverparametrar på Azure-portalen
1. Logga in på Azure-portalen och sedan leta upp din Azure-databas för MySQL-servern.
2. Under den **inställningar** klickar du på **serverparametrar** att öppna sidan parametrar för Azure-databas för MySQL.
3. Hitta alla inställningar som du måste justera. Granska de **beskrivning** kolumnen att förstå syftet med och tillåtna värden. 
4. Klicka på **spara** att spara ändringarna.

![Azure portal parametrar serverblad](./media/howto-server-parameters/auzre-portal-server-parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista över konfigurerbara serverparametrar

Listan över parametrar stöds server växer ständigt. Använd fliken server parametrar i Azure-portalen för att hämta definitionen och konfigurera parametrar för server baserat på kraven för application. 

## <a name="nonconfigurable-server-parameters"></a>Nonconfigurable serverparametrar
InnoDB buffertpool och högsta antal anslutningar är inte kan konfigureras och bundet till din [prisnivån](concepts-service-tiers.md). 

| **prisnivå** | **InnoDB buffertpool (MB)** | **Högsta antal anslutningar** |
| :------------------------ | :-------- | :----------- |
| Grundläggande 50 | 1024 | 50 | 
| Grundläggande 100  | 2560 | 100 | 
| Standard 100 | 2560 | 200 | 
| Standard 200 | 5120 | 400 | 
| Standard 400 | 10240 | 800 | 
| Standard 800 | 20480 | 1600 |

Dessa ytterligare parametrar finns nonconfigurable i systemet <br>
 Innodb_file_per_table i grundläggande nivån: av<br>
 innodb_flush_log_at_trx_commit = 1<br>
 sync_binlog = 1<br>
 innodb_log_file_size = 512MB<br>
 
Andra server som inte listas här har angetts till MySQL out-of-box standardvärdena för versioner [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) och [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="next-steps"></a>Nästa steg
- [Anslutningsbibliotek för Azure-databas för MySQL](concepts-connection-libraries.md).
