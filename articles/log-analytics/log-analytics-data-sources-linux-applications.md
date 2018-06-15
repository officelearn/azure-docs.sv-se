---
title: Samla in Linux programprestanda i OMS Log Analytics | Microsoft Docs
description: Den här artikeln innehåller information för att konfigurera OMS-Agent för Linux för att samla in prestandaräknare för MySQL och Apache HTTP-servern.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: bfb9b62a8965fa1f7daf62d814665ca23491cc04
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32168290"
---
# <a name="collect-performance-counters-for-linux-applications-in-log-analytics"></a>Samla in prestandaräknare för Linux-program i logganalys 
Den här artikeln innehåller information om hur du konfigurerar den [OMS-Agent för Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) att samla in prestandaräknare för specifika program.  Program som ingår i den här artikeln är:  

- [MySQL](#MySQL)
- [Apache HTTP-Server](#apache-http-server)

## <a name="mysql"></a>MySQL
Om MySQL-Server eller MariaDB Server har identifierats på datorn när OMS-agenten är installerad, installeras en provider för MySQL-Server för prestandaövervakning automatiskt. Den här providern ansluter till den lokala MySQL/MariaDB servern att exponera prestandastatistik. MySQL-autentiseringsuppgifter måste konfigureras så att providern har åtkomst till MySQL-servern.

### <a name="configure-mysql-credentials"></a>Konfigurera MySQL-autentiseringsuppgifter
MySQL OMI-providern kräver att en förkonfigurerad MySQL-användare och installerat MySQL-klientbibliotek för att fråga efter prestanda och hälsoinformation från MySQL-instans.  Dessa autentiseringsuppgifter lagras i en fil för autentisering som är lagrad på Linux-agenten.  Autentiseringsfilen anger vilka bind-adress och port MySQL-instans lyssnar på och vilka autentiseringsuppgifter du använder för att samla in mått.  

Under installationen av OMS-Agent för Linux MySQL OMI providern genomsöker MySQL my.cnf configuration-filer (standardplatserna) för bind-adress och port och delvis anger MySQL OMI autentisering.

MySQL-autentisering lagras på `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`.


### <a name="authentication-file-format"></a>Filformatet för autentisering
Följande är formatet för filen MySQL OMI-autentisering

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

Poster i autentiseringsfilen beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--|:--|
| Port | Representerar den aktuella porten MySQL-instans lyssnar på. Port 0 anger att egenskaperna för följande används för standardinstansen. |
| Bind-adress| Aktuell MySQL bind-adress. |
| användarnamn| MySQL-användare som används för att använda för att övervaka MySQL-serverinstansen. |
| Base64-kodat lösenord| Lösenordet för MySQL övervakning användaren kodad i Base64. |
| Automatisk uppdatering| Anger om genomsökning efter ändringar i filen my.cnf och skriva över filen MySQL OMI autentisering när MySQL OMI providern uppgraderas. |

### <a name="default-instance"></a>Standardinstansen
Filen MySQL OMI-autentisering kan definiera en standard-instans och port kod för att hantera flera MySQL-instanser på en Linux-värd enklare.  Standardinstansen markeras med en instans med port 0. Alla ytterligare instanser ärver egenskaper som anges från standardinstansen om de ange olika värden. Till exempel om MySQL-instans som lyssnar på port '3308' läggs används standardinstansen bind-adress, användarnamn och lösenord för Base64-kodade att testa och övervaka den instans som lyssnar på 3308. Om instansen på 3308 är bunden till en annan adress och använder samma MySQL användarnamn och lösenord par behövs bara bind-adress och andra egenskaper ärvs.

I följande tabell har exempel instans inställningar 

| Beskrivning | Fil |
|:--|:--|
| Standardinstansen och instansen med port 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Standardinstansen och instansen med port 3308 och annat användarnamn och lösenord. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>Programmet för MySQL OMI autentisering fil
Ingår i installationen av MySQL OMI-providern är ett MySQL OMI autentisering filen program som kan användas för att redigera filen MySQL OMI-autentisering. Programmet för autentisering-filen finns på följande plats.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> Filen autentiseringsuppgifter måste kunna läsas av kontot omsagent. Du bör köra kommandot mycimprovauth som omsgent.

Följande tabell innehåller information om syntaxen för att använda mycimprovauth.

| Åtgärd | Exempel | Beskrivning
|:--|:--|:--|
| automatisk uppdatering *FALSKT eller SANT* | mycimprovauth automatisk uppdatering false | Anger huruvida autentiseringsfilen uppdateras automatiskt på Starta om eller uppdatera. |
| standard *användarlösenordet för bind-adress* | mycimprovauth standard 127.0.0.1 rot pwd | Anger standardinstansen i MySQL OMI autentiseringsfilen.<br>Lösenordsfältet ska anges i klartext - lösenordet i filen MySQL OMI autentisering blir Base64-kodade. |
| ta bort *standard eller portnummer* | mycimprovauth 3308 | Tar bort den angivna instansen som antingen standard eller av portnummer. |
| hjälp | mycimprov hjälp | Visar en lista med kommandon för att använda. |
| Skriv ut | mycimprov utskrift | Visar en lättläst MySQL OMI autentiseringsfilen. |
| Uppdatera portnummer *användarlösenordet för bind-adress* | mycimprov uppdatering 3307 127.0.0.1 rot pwd | Uppdaterar den angivna instansen eller lägger till instansen om det inte finns. |

Följande exempelkommandon definiera ett standard-användarkonto för MySQL-server på localhost.  Lösenordsfältet ska anges i klartext - lösenord i filen MySQL OMI autentisering kommer att Base64-kodade

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Databasbehörigheter som krävs för MySQL-prestandaräknare
MySQL-användare behöver åtkomst till följande frågor för att samla in prestandadata för MySQL-servern. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


MySQL-användaren kräver också väljer åtkomst till standard i tabellerna nedan.

- information_schema
- MySQL. 

Dessa behörigheter kan tilldelas genom att köra följande kommandon för bevilja.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> Om du vill ge behörighet till en MySQL måste övervakning användaren att bevilja användaren ha behörigheten ' GRANT Option, samt behörigheten beviljas.

### <a name="define-performance-counters"></a>Definiera prestandaräknare

När du konfigurerar OMS-Agent för Linux för att skicka data till logganalys, måste du konfigurera prestandaräknarna som samlar in.  Använd proceduren i [Windows och Linux prestanda datakällor i logganalys](log-analytics-data-sources-windows-events.md) med räknarna i följande tabell.

| Objektnamn | Räknarens namn |
|:--|:--|
| MySQL-databas | Ledigt diskutrymme i byte |
| MySQL-databas | Tabeller |
| MySQL-server | Avbrutna anslutning Pct |
| MySQL-server | Anslutningen används Pct |
| MySQL-server | Användning av diskutrymme i byte |
| MySQL-server | Fullständiga genomsökning Pct |
| MySQL-server | InnoDB buffertpool träffar Pct |
| MySQL-server | InnoDB Pool Använd Buffertprocent |
| MySQL-server | InnoDB Pool Använd Buffertprocent |
| MySQL-server | Viktiga träffar Pct |
| MySQL-server | Viktiga Cache används Pct |
| MySQL-server | Viktiga Cache skrivåtgärder Pct |
| MySQL-server | Frågan Cache träffar Pct |
| MySQL-server | Frågan Cache Prunes Pct |
| MySQL-server | Frågan Cache används Pct |
| MySQL-server | Tabell träffar Pct |
| MySQL-server | Tabell Cache används Pct |
| MySQL-server | Tabell Lås konkurrens Pct |

## <a name="apache-http-server"></a>Apache HTTP-Server 
Om Apache HTTP-Server har identifierats på datorn när omsagent-paket installeras, installeras en provider för Apache HTTP-Server för prestandaövervakning automatiskt. Den här providern förlitar sig på en Apache-modulen måste läsas in i Apache HTTP-Server för att komma åt prestandadata. Går att läsa in modulen med följande kommando:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Om du vill ta bort modulen Apache övervakning, kör du följande kommando:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definiera prestandaräknare

När du konfigurerar OMS-Agent för Linux för att skicka data till logganalys, måste du konfigurera prestandaräknarna som samlar in.  Använd proceduren i [Windows och Linux prestanda datakällor i logganalys](log-analytics-data-sources-windows-events.md) med räknarna i följande tabell.

| Objektnamn | Räknarens namn |
|:--|:--|
| Apache HTTP-Server | Upptagen arbetare |
| Apache HTTP-Server | Inaktiv arbetare |
| Apache HTTP-Server | PCT upptagen arbetare |
| Apache HTTP-Server | Totalt antal Pct CPU |
| Apache virtuell värddator | Fel per minut - klient |
| Apache virtuell värddator | Fel per minut - Server |
| Apache virtuell värddator | KB per begäran |
| Apache virtuell värddator | Begäranden KB per sekund |
| Apache virtuell värddator | Begäranden per sekund |



## <a name="next-steps"></a>Nästa steg
* [Samla in prestandaräknare](log-analytics-data-sources-performance-counters.md) från Linux-agenter.
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att analysera data som samlas in från datakällor och lösningar. 
