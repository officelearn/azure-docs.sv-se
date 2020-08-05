---
title: Konfigurera Azure SQL Edge (för hands version)
description: Läs mer om hur du konfigurerar Azure SQL Edge (för hands version).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: 0cb2eed0895c10f649facaa184a5f9f9ea158aa5
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/04/2020
ms.locfileid: "87551990"
---
# <a name="configure-azure-sql-edge-preview"></a>Konfigurera Azure SQL Edge (för hands version)

Azure SQL Edge stöder konfiguration via något av följande två alternativ:

- Miljövariabler
- En MSSQL. conf-fil placerad i mappen/var/opt/MSSQL

> [!NOTE]
> Att ställa in miljövariabler åsidosätter inställningarna som anges i filen MSSQL. conf.

## <a name="configure-by-using-environment-variables"></a>Konfigurera med hjälp av miljövariabler

Azure SQL Edge visar flera olika miljövariabler som kan användas för att konfigurera SQL Edge-behållaren. Dessa miljövariabler är en delmängd av de som är tillgängliga för SQL Server på Linux. Mer information om SQL Server på Linux miljövariabler finns i [miljövariabler](/sql/linux/sql-server-linux-configure-environment-variables/).

Följande SQL Server på Linux Environment-variabel stöds inte för Azure SQL Edge. Om det är definierat ignoreras den här miljövariabeln vid initiering av behållare.

| Miljövariabel | Beskrivning |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Aktivera tillgänglighets grupp. Till exempel **1** är aktive rad och **0** är inaktiverat. |

> [!IMPORTANT]
> **MSSQL_PID** -miljövariabeln för SQL Edge accepterar bara **Premium** och **Developer** som giltiga värden. Azure SQL Edge stöder inte initiering med en produkt nyckel.

> [!NOTE]
> Hämta [licens villkoren för program vara från Microsoft](https://go.microsoft.com/fwlink/?linkid=2128283) för Azure SQL Edge.

### <a name="specify-the-environment-variables"></a>Ange miljövariabler

Ange miljövariabler för SQL Edge när du distribuerar tjänsten via [Azure Portal](deploy-portal.md). Du kan lägga till dem antingen i avsnittet **miljövariabler** i modulen distribution eller som en del av alternativen för att **skapa behållare**.

Lägg till värden i **miljövariabler**.

![Ange med hjälp av listan miljövariabler](media/configure/set-environment-variables.png)

Lägg till värden i **behållar skapande alternativ**.

![Ange med hjälp av alternativet för att skapa behållare](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-by-using-an-mssqlconf-file"></a>Konfigurera med hjälp av en MSSQL. conf-fil

Azure SQL Edge innehåller inte det [konfigurations verktyg för MSSQL-conf](/sql/linux/sql-server-linux-configure-mssql-conf/) som SQL Server på Linux gör. Du måste konfigurera filen MSSQL. conf manuellt och placera den i den beständiga lagrings enhet som är mappad till mappen/var/opt/MSSQL/i SQL Edge-modulen. När du distribuerar SQL Edge från Azure Marketplace anges den här mappningen som alternativet **monteringar** i **behållaren skapa alternativ**.

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

|Alternativ|Beskrivning|
|:---|:---|
|**Kundfeedback** | Välj om SQL Server skicka feedback till Microsoft. |
|**Database mail-profil** | Ange e-postprofilen för standard databasen för SQL Server på Linux. |
|**Hög tillgänglighet** | Aktivera tillgänglighets grupper. |
|**Microsoft koordinator för distribuerad transaktion** | Konfigurera och Felsök MSDTC i Linux. Ytterligare distribuerade transaktions-relaterade konfigurations alternativ stöds inte för SQL Edge. Mer information om dessa ytterligare konfigurations alternativ finns i [Configure MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc). |
|**Service avtal för ML** | Godkänn R-och python-licensavtalet för Azure Machine Learning-paket. Gäller endast SQL Server 2019.|
|**outboundnetworkaccess** |Aktivera utgående nätverks åtkomst för [Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) R-, python-och Java-tillägg.|

Följande exempel på MSSQL. conf-filen fungerar för SQL Edge. Mer information om formatet för en MSSQL. conf-fil finns i [MSSQL. conf-format](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

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

## <a name="run-azure-sql-edge-as-non-root-user"></a>Kör Azure SQL Edge som icke-rot användare

Från och med Azure SQL Edge CTP 2.2 kan SQL Edge-behållare köras med en icke-rot användare/grupp. När den distribueras via Azure Marketplace, om inte en annan användare/grupp anges, startar SQL Edge-behållare som MSSQL-användare (inte rot). Om du vill ange en annan icke-rot användare under distributionen lägger du till `*"User": "<name|uid>[:<group|gid>]"*` nyckel/värde-paret under behållare skapa alternativ. I exemplet nedan är SQL Edge konfigurerat att starta som användare `*IoTAdmin*` .

```json
{
    ..
    ..
    ..
    "User": "IoTAdmin",
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "MSSQL_PID=Premium"
    ]
}
```

Om du vill tillåta att icke-rot användaren har åtkomst till DB-filer som finns på monterade volymer, kontrollerar du att den användare/grupp som du kör behållaren under har Läs & Skriv behörigheter för lagring med beständig fil. I exemplet nedan ställer vi in den icke-rot användaren med user_id 10001 som ägare till filerna. 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>Uppgradera från tidigare CTP-versioner

Tidigare CTP-versioner av Azure SQL Edge har kon figurer ATS för att köras som rot användare. Följande alternativ är tillgängliga när du uppgraderar från tidigare CTP: s

- Fortsätt att använda rot användaren – om du vill fortsätta använda rot användaren lägger du till `*"User": "0:0"*` nyckel/värde-paret under behållare skapa alternativ.
- Använd standard-MSSQL-användaren för att använda standard-MSSQL-användaren, Följ stegen nedan
  - Lägg till en användare med namnet MSSQL på Docker-värden. I exemplet nedan lägger vi till en användar-MSSQL med ID 10001. Den här användaren läggs också till i rot gruppen.
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - Ändra behörigheten för den katalog/monterings volym där databas filen finns 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- Använd ett annat icke-rot användar konto – om du vill använda ett annat konto än rot användare
  - Uppdatera behållaren skapa alternativ för att ange Lägg till `*"User": "user_name | user_id*` nyckel/värde-par under behållare skapa alternativ. Ersätt user_name eller user_id med en faktisk user_name eller user_id från Docker-värden. 
  - Ändra behörigheter för katalogen/monterings volymen.



## <a name="next-steps"></a>Nästa steg

- [Ansluta till Azure SQL Edge](connect.md)
- [Skapa en IoT-lösning från slutpunkt till slutpunkt med SQL Edge](tutorial-deploy-azure-resources.md)
