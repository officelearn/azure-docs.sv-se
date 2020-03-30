---
title: Samla in Linux-programprestanda i Azure Monitor | Microsoft-dokument
description: Den här artikeln innehåller information om hur du konfigurerar Log Analytics-agenten för Linux för att samla in prestandaräknare för MySQL och Apache HTTP Server.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2017
ms.openlocfilehash: 2fd148dbb85a4fd60fe63d4fb73128bf92dea1d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670567"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Samla in prestandaräknare för Linux-program i Azure Monitor 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
Den här artikeln innehåller information om hur du konfigurerar [Log Analytics-agenten för Linux för](https://github.com/Microsoft/OMS-Agent-for-Linux) att samla in prestandaräknare för specifika program i Azure Monitor.  De program som ingår i den här artikeln är:  

- [MySQL](#mysql)
- [Apache HTTP-server](#apache-http-server)

## <a name="mysql"></a>MySQL
Om MySQL Server eller MariaDB Server identifieras på datorn när Log Analytics-agenten är installerad installeras en prestandaövervakningsleverantör för MySQL Server automatiskt. Den här leverantören ansluter till den lokala MySQL/MariaDB-servern för att exponera prestandastatistik. MySQL-användarautentiseringsuppgifter måste konfigureras så att leverantören kan komma åt MySQL-servern.

### <a name="configure-mysql-credentials"></a>Konfigurera MySQL-autentiseringsuppgifter
MySQL OMI-providern kräver en förkonfigurerad MySQL-användare och installerade MySQL-klientbibliotek för att kunna fråga prestanda- och hälsoinformationen från MySQL-instansen.  Dessa autentiseringsuppgifter lagras i en autentiseringsfil som lagras på Linux-agenten.  Autentiseringsfilen anger vilken bindadress och port MySQL-instansen lyssnar på och vilka autentiseringsuppgifter som ska användas för att samla in mått.  

Under installationen av Log Analytics-agenten för Linux kommer MySQL OMI-providern att skanna MySQL my.cnf-konfigurationsfiler (standardplatser) efter bindadress och port och delvis ställa in MySQL OMI-autentiseringsfilen.

MySQL-autentiseringsfilen `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`lagras på .


### <a name="authentication-file-format"></a>Filformat för autentisering
Följande är formatet för MySQL OMI-autentiseringsfilen

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

Posterna i autentiseringsfilen beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--|:--|
| Port | Representerar den aktuella porten som MySQL-instansen lyssnar på. Port 0 anger att egenskaperna som följer används för standardinstansen. |
| Bind-Adress| Aktuell MySQL-bindningsadress. |
| användarnamn| MySQL-användare brukade använda för att övervaka MySQL-serverinstansen. |
| Base64 kodat lösenord| Lösenord för MySQL-övervakningsanvändaren kodad i Base64. |
| Autoupdate| Anger om du vill söka om för ändringar i filen my.cnf och skriva över mySQL OMI-autentiseringsfilen när MySQL OMI-providern uppgraderas. |

### <a name="default-instance"></a>Standardinstans
MySQL OMI-autentiseringsfilen kan definiera en standardinstans och portnummer för att göra det enklare att hantera flera MySQL-instanser på en Linux-värd.  Standardinstansen betecknas med en instans med port 0. Alla ytterligare instanser ärver egenskaper som angetts från standardinstansen om de inte anger olika värden. Om till exempel MySQL-instans som lyssnar på porten "3308" läggs till, används standardinstansens bindadress, användarnamn och Base64-kodade lösenord för att försöka övervaka instanslyssning på 3308. Om instansen på 3308 är bunden till en annan adress och använder samma MySQL användarnamn och lösenord par endast bind-adressen behövs, och de andra egenskaperna kommer att ärvas.

Följande tabell har exempelinstansinställningar 

| Beskrivning | Fil |
|:--|:--|
| Standardinstans och instans med port 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Standardinstans och instans med port 3308 och olika användarnamn och lösenord. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>MySQL OMI-autentiseringsfilprogram
Ingår i installationen av MySQL OMI-leverantören är ett MySQL OMI-autentiseringsfilprogram som kan användas för att redigera MySQL OMI Authentication-filen. Autentiseringsfilprogrammet finns på följande plats.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> Autentiseringsfilen måste kunna läsas av omsagent-kontot. Köra mycimprovauth kommandot som omsgent rekommenderas.

Följande tabell innehåller information om syntaxen för att använda mycimprovauth.

| Åtgärd | Exempel | Beskrivning
|:--|:--|:--|
| autoupdate *falskt eller sant* | mycimprovauth autoupdate falskt | Anger om autentiseringsfilen ska uppdateras automatiskt vid omstart eller uppdatering. |
| lösenord *för standardadressens användarnamn* | mycimprovauth standard 127.0.0.1 root pwd | Anger standardinstansen i MySQL OMI-autentiseringsfilen.<br>Lösenordsfältet ska anges i oformaterad text - lösenordet i MySQL OMI-autentiseringsfilen kommer att vara Base 64-kodad. |
| ta bort *standard eller port_num* | mycimprovauth 3308 | Tar bort den angivna instansen antingen som standard eller efter portnummer. |
| Hjälp | mycimprov hjälp | Skriver ut en lista med kommandon som ska användas. |
| skriva ut | mycimprov-utskrift | Skriver ut en lättläst MySQL OMI-autentiseringsfil. |
| uppdatera port_num *lösenord för bindadressens användarnamn* | mycimprov uppdatering 3307 127.0.0.1 rot pwd | Uppdaterar den angivna instansen eller lägger till instansen om den inte finns. |

Följande exempelkommandon definierar ett standardanvändarkonto för MySQL-servern på localhost.  Lösenordsfältet ska anges i oformaterad text - lösenordet i MySQL OMI-autentiseringsfilen kommer att vara Base 64 kodad

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Databasbehörigheter som krävs för MySQL-prestandaräknare
MySQL-användaren kräver åtkomst till följande frågor för att samla in MySQL Server-prestandadata. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


MySQL-användaren kräver också SELECT-åtkomst till följande standardtabeller.

- information_schema
- Mysql. 

Dessa privilegier kan beviljas genom att köra följande bidragskommandon.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> För att bevilja behörigheter till en MySQL-övervakningsanvändare måste den beviljande användaren ha privilegiet "GRANT-alternativ" samt det privilegium som beviljas.

### <a name="define-performance-counters"></a>Definiera prestandaräknare

När du har konfigurerat Log Analytics-agenten för Linux för att skicka data till Azure Monitor måste du konfigurera prestandaräknarna för insamling.  Använd proceduren i [Prestandadatakällor för Windows och Linux i Azure Monitor](data-sources-performance-counters.md) med räknarna i följande tabell.

| Objektets namn | Motnamn |
|:--|:--|
| MySQL-databas | Diskutrymme i byte |
| MySQL-databas | Tabeller |
| MySQL-server | Avbruten anslutningsdator |
| MySQL-server | Anslutning Använd PCT |
| MySQL-server | Diskutrymme Användning i byte |
| MySQL-server | Full Tabell Scan Pct |
| MySQL-server | InnoDB buffert pool Hit Pct |
| MySQL-server | InnoDB buffertpool Använd Pct |
| MySQL-server | InnoDB buffertpool Använd Pct |
| MySQL-server | Nyckel Cache Hit Pct |
| MySQL-server | Nyckel cache använda pct |
| MySQL-server | Skriva pct för nyckelcache |
| MySQL-server | Fråga cache hit pct |
| MySQL-server | Pct för frågecachecriner |
| MySQL-server | Frågecache Använd Pct |
| MySQL-server | Tabell Cache Hit Pct |
| MySQL-server | Tabellcache Använd pct |
| MySQL-server | Tabell lås konkurrens pct |

## <a name="apache-http-server"></a>Apache HTTP-server 
Om Apache HTTP Server identifieras på datorn när omsagent-paketet installeras installeras en prestandaövervakningsprovider för Apache HTTP Server automatiskt. Den här providern är beroende av en Apache-modul som måste läsas in i Apache HTTP-servern för att komma åt prestandadata. Modulen kan laddas med följande kommando:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Så här tar du bort apacheövervakningsmodulen:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definiera prestandaräknare

När du har konfigurerat Log Analytics-agenten för Linux för att skicka data till Azure Monitor måste du konfigurera prestandaräknarna för insamling.  Använd proceduren i [Prestandadatakällor för Windows och Linux i Azure Monitor](data-sources-performance-counters.md) med räknarna i följande tabell.

| Objektets namn | Motnamn |
|:--|:--|
| Apache HTTP-server | Upptagna arbetare |
| Apache HTTP-server | Inaktiva arbetare |
| Apache HTTP-server | Pct upptagna arbetare |
| Apache HTTP-server | Totalt Pct CPU |
| Apache virtuell värd | Fel per minut - Klient |
| Apache virtuell värd | Fel per minut - Server |
| Apache virtuell värd | KB per begäran |
| Apache virtuell värd | Begär KB per sekund |
| Apache virtuell värd | Begäranden per sekund |



## <a name="next-steps"></a>Nästa steg
* [Samla in prestandaräknare](data-sources-performance-counters.md) från Linux-agenter.
* Lär dig mer om [loggfrågor](../log-query/log-query-overview.md) för att analysera data som samlas in från datakällor och lösningar. 
