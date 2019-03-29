---
title: Samla in programprestanda på Linux i Azure Monitor | Microsoft Docs
description: Den här artikeln innehåller information för att konfigurera Log Analytics-agenten för Linux för att samla in prestandaräknare för MySQL och Apache HTTP Server.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: ea74440a5c8a9a2584e742ec72ccf888b6bb5ad9
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621540"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Samla in prestandaräknare för Linux-program i Azure Monitor 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
Den här artikeln innehåller information för att konfigurera den [Log Analytics-agenten för Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) att samla in prestandaräknare för specifika program i Azure Monitor.  De program som ingår i den här artikeln är:  

- [MySQL](#mysql)
- [Apache HTTP Server](#apache-http-server)

## <a name="mysql"></a>MySQL
Om MySQL-Server eller MariaDB-Server har upptäckts på datorn när Log Analytics-agenten är installerad, installeras en provider för MySQL-Server för prestandaövervakning automatiskt. Den här providern ska ansluta till den lokala MySQL-/ MariaDB-servern att exponera prestandastatistik. Autentiseringsuppgifterna för MySQL-användare måste konfigureras så att providern har åtkomst till MySQL-servern.

### <a name="configure-mysql-credentials"></a>Konfigurera autentiseringsuppgifterna för MySQL
MySQL OMI-providern kräver en förkonfigurerad MySQL-användare och installerat MySQL-klientbibliotek för att fråga efter prestanda- och hälsoinformation från MySQL-instans.  Dessa autentiseringsuppgifter lagras i en authentication-fil som lagras på Linux-agenten.  Autentiseringsfilen anger vilka bind-adressen och porten MySQL-instans lyssnar på och vilka autentiseringsuppgifter som ska använda för att samla in mått.  

Under installationen av Log Analytics-agenten för Linux MySQL OMI ska provider skanna MySQL my.cnf-konfigurationsfilerna (standardplatserna) för bind-adressen och porten och delvis ange MySQL OMI autentiseringsfilen.

MySQL-autentiseringsfilen lagras på `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`.


### <a name="authentication-file-format"></a>Filformat för autentisering
Följande är formatet för filen MySQL OMI-autentisering

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

Poster i autentiseringsfilen beskrivs i följande tabell.

| Egenskap  | Beskrivning |
|:--|:--|
| Port | Representerar den aktuella porten MySQL-instans lyssnar på. Port 0 anger att egenskaperna efter används för standardinstansen. |
| Bind-Address| Aktuella MySQL bind-adress. |
| användarnamn| MySQL-användaren används för att använda för att övervaka MySQL-serverinstansen. |
| Base64-kodade lösenord| Lösenordet för MySQL övervakning användaren i Base64-kodad. |
| AutoUpdate| Anger om att skanna efter ändringar i filen my.cnf och skriva över filen MySQL OMI autentisering när MySQL OMI providern uppgraderas. |

### <a name="default-instance"></a>Standardinstans
MySQL OMI autentiseringsfilen kan definiera en standard-instans och port kod för att göra hantering av flera MySQL-instanser på en Linux-värd enklare.  Standardinstansen markeras med en instans med port 0. Alla ytterligare instanser ärver egenskaperna från standardinstansen såvida inte de anger olika värden. Till exempel MySQL-instans som lyssnar på port '3308' läggs används standardinstansen bind-adress, användarnamn och lösenord för Base64-kodad att testa och övervaka den instans som lyssnar på 3308. Om instansen på 3308 är bunden till en annan adress och använder samma MySQL användarnamn och lösenord par endast bind-adress krävs och de andra egenskaperna ärvs.

Följande tabell innehåller exempel på instans-inställningar 

| Beskrivning | Fil |
|:--|:--|
| Standardinstansen och instansen med port 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Standardinstansen och instansen med port 3308 och olika användarnamn och lösenord. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>Programmet för MySQL OMI autentisering fil
Ingår i installationen av MySQL OMI-providern är ett program av MySQL OMI autentisering-fil som kan användas för att redigera filen MySQL OMI-autentisering. Programmet för autentisering-filen finns på följande plats.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> Filen med autentiseringsuppgifter måste läsas av omsagent-kontot. Du bör köra kommandot mycimprovauth som omsgent.

I följande tabell finns information om syntaxen för att använda mycimprovauth.

| Åtgärd | Exempel | Beskrivning
|:--|:--|:--|
| automatisk uppdatering *false eller true* | mycimprovauth autoupdate FALSKT | Anger huruvida autentiseringsfilen uppdateras automatiskt på Starta om eller uppdatera. |
| standard *bind-address användarnamn lösenord* | mycimprovauth standard 127.0.0.1 rot pwd | Anger den standardinstansen i MySQL OMI autentiseringsfilen.<br>Lösenordsfältet ska anges i oformaterad text - lösenordet i filen MySQL OMI autentisering blir Base64-kodad. |
| ta bort *standard eller portnummer* | mycimprovauth 3308 | Tar bort den angivna instansen som antingen standard eller genom att portnumret. |
| Hjälp | mycimprov hjälp | Visar en lista med kommandon för att använda. |
| Skriv ut | mycimprov Skriv ut | Visar ett lättläst MySQL OMI autentiseringsfilen. |
| Uppdatera portnummer *bind-address användarnamn lösenord* | mycimprov update 3307 127.0.0.1 rot pwd | Uppdaterar den angivna instansen eller lägger till instansen om det inte finns. |

Kommandona i följande exempel definierar en standardanvändarkontot för MySQL-server på localhost.  Lösenordsfältet ska anges i oformaterad text – lösenordet i filen MySQL OMI autentisering blir Base 64-kodat

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Databasbehörigheter som krävs för MySQL-prestandaräknare
MySQL-användaren kräver åtkomst till följande frågor för att samla in prestandadata för MySQL-Server. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


MySQL-användaren kräver också SELECT-åtkomst till standard i tabellerna nedan.

- information_schema
- mysql. 

Dessa behörigheter kan beviljas genom att köra följande kommandon för beviljande.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> För att tilldela behörigheter till en MySQL måste övervakning användare beviljande användaren ha behörigheten ”BEVILJA alternativet” samt den behörighet som beviljas.

### <a name="define-performance-counters"></a>Definiera prestandaräknare

När du har konfigurerat Log Analytics-agenten för Linux för att skicka data till Azure Monitor, måste du konfigurera prestandaräknarna som samlar in.  Stegen nedan i [Windows och Linux prestanda datakällor i Azure Monitor](data-sources-performance-counters.md) med räknarna i följande tabell.

| Objektnamn | Räknarnamn |
|:--|:--|
| MySQL-databas | Ledigt diskutrymme i byte |
| MySQL-databas | Tabeller |
| MySQL-server | Avbrutna anslutning Pct |
| MySQL-server | Anslutningen används Pct |
| MySQL-server | Användning av diskutrymme i byte |
| MySQL-server | Fullständiga genomsökning Pct |
| MySQL-server | InnoDB Buffertpoolen når Pct |
| MySQL-server | InnoDB Pool Använd Buffertprocent |
| MySQL-server | InnoDB Pool Använd Buffertprocent |
| MySQL-server | Viktiga Cacheträff Pct |
| MySQL-server | Viktiga Cache används Pct |
| MySQL-server | Viktiga Cache skrivning Pct |
| MySQL-server | Fråga Cache träffar Pct |
| MySQL-server | Fråga Cache Prunes Pct |
| MySQL-server | Fråga Cache används Pct |
| MySQL-server | Tabellen Cacheträff Pct |
| MySQL-server | Tabellen Cache används Pct |
| MySQL-server | Tabellen Lås konkurrens Pct |

## <a name="apache-http-server"></a>Apache HTTP Server 
Om Apache HTTP Server har upptäckts på datorn när omsagent paketet installeras, installeras en prestandaövervakning provider för Apache HTTP Server automatiskt. Den här providern är beroende av en Apache-modul som måste läsas in i Apache HTTP-Server för att komma åt prestandadata. Modulen kan läsas in med följande kommando:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Om du vill ta bort övervakningsmodulen Apache, kör du följande kommando:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definiera prestandaräknare

När du har konfigurerat Log Analytics-agenten för Linux för att skicka data till Azure Monitor, måste du konfigurera prestandaräknarna som samlar in.  Stegen nedan i [Windows och Linux prestanda datakällor i Azure Monitor](data-sources-performance-counters.md) med räknarna i följande tabell.

| Objektnamn | Räknarnamn |
|:--|:--|
| Apache HTTP Server | Upptagen arbetare |
| Apache HTTP Server | Inaktiva arbetare |
| Apache HTTP Server | PCT upptagen arbetare |
| Apache HTTP Server | Totalt antal Pct CPU |
| Apache virtuell värd | Fel per minut - klienten |
| Apache virtuell värd | Fel per minut - Server |
| Apache virtuell värd | KB per begäran |
| Apache virtuell värd | Begäranden KB per sekund |
| Apache virtuell värd | Begäranden per sekund |



## <a name="next-steps"></a>Nästa steg
* [Samla in prestandaräknare](data-sources-performance-counters.md) från Linux-agenter.
* Lär dig mer om [logga frågor](../log-query/log-query-overview.md) att analysera data som samlas in från datakällor och lösningar. 