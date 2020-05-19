---
title: Konfigurera Azure SQL Edge (för hands version)
description: Läs om hur du konfigurerar Azure SQL Edge (för hands version)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5dcdd1604674ca56fb7a646d4c571d63bd2c0e3e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596962"
---
# <a name="configure-azure-sql-edge-preview"></a>Konfigurera Azure SQL Edge (för hands version)

Azure SQL Edge stöder konfiguration via något av följande två alternativ:

- Använda miljövariabler.
- Använda MSSQL. conf-filen som placerats i mappen/var/opt/MSSQL

> [!NOTE]
> Att ställa in miljövariabler åsidosätter inställningarna som anges i filen MSSQL. conf.

## <a name="configure-using-environment-variables"></a>Konfigurera med miljövariabler

Azure SQL Edge visar flera olika miljövariabler som kan användas för att konfigurera SQL Edge-behållaren. Dessa miljövariabler är en delmängd av de miljövariabler som är tillgängliga för SQL Server på Linux. Mer information om SQL Server på Linux miljövariabler finns i [miljövariabler](/sql/linux/sql-server-linux-configure-environment-variables/).

Följande SQL Server på Linux miljövariabler stöds inte för Azure SQL Edge. Om det här är definierat ignoreras dessa miljövariabler vid initiering av behållare.

| Miljövariabel | Description |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Aktivera tillgänglighets grupp. Till exempel är 1 "aktiverat" och "0" är inaktiverat |

> [!IMPORTANT]
> *MSSQL_PID* -miljövariabeln för SQL Edge accepterar bara **Premium** och **Developer** som giltiga värden. Azure SQL Edge stöder inte initiering med en produkt nyckel.

> [!NOTE]
> Om du vill ladda ned användar licens avtalet för Azure SQL Edge kan du läsa [licens avtalet för slutanvändare](https://go.microsoft.com/fwlink/?linkid=2128283).

### <a name="specifying-the-environment-variables"></a>Ange miljövariabler

Miljövariabler för SQL Edge kan anges när du distribuerar Azure SQL Edge via [Azure Portal](deploy-portal.md). Detta kan läggas till antingen i avsnittet "miljövariabler" i modulen distribution eller som en del av alternativet för att skapa behållare enligt beskrivningen nedan.

*Ange alternativ för att använda miljövariabler*

![Ange med lista med miljövariabler](media/configure/set-environment-variables.png)

*Ange användnings alternativ för behållare*

![Ange användnings alternativ för behållare](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-using-mssqlconf-file"></a>Konfigurera med hjälp av MSSQL. conf-filen

Azure SQL Edge inkluderar inte [konfigurations verktyget MSSQL-conf](/sql/linux/sql-server-linux-configure-mssql-conf/) som SQL Server på Linux gör, eftersom filen MSSQL. conf måste konfigureras manuellt och placeras i den permanenta lagrings enheten som är mappad till mappen/var/opt/MSSQL/i SQL Edge-modulen. När du distribuerar SQL Edge från Azure Marketplace anges den här mappningen som alternativet * * monteringar i alternativet container Create

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

Följande MSSQL. conf-alternativ gäller inte för SQL Edge:
</br></br>
|Alternativ|Description|
|:---|:---|
|**Kundfeedback** | Välj om SQL Server skicka feedback till Microsoft. |
|**Database Mail profil** | Ange e-postprofilen för standard databasen för SQL Server på Linux. |
|**Hög tillgänglighet** | Aktivera tillgänglighets grupper. |
|**Microsoft koordinator för distribuerad transaktion** | Konfigurera och Felsök MSDTC i Linux. Ytterligare konfigurations alternativ för distribuerade transaktioner stöds inte heller för SQL Edge. Mer information om dessa ytterligare konfigurations alternativ finns i [Konfigurera MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc) |
|**MLServices-licensavtalet** | Godkänn R-och python-licensavtalet för Machine Learning Services-paket. Gäller endast SQL Server 2019.|
|**outboundnetworkaccess** |Aktivera utgående nätverks åtkomst för [Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) R-, python-och Java-tillägg.|

Ett exempel på en MSSQL. conf-fil som fungerar för SQL Edge finns nedan. Mer information om formatet för filen MSSQL. conf finns i [MSSQL. conf-format](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="next-step"></a>Nästa steg

- [Ansluta till Azure SQL Edge](connect.md)
- [Skapa en IoT-lösning från slut punkt till slut punkt med SQL Edge](tutorial-deploy-azure-resources.md)
