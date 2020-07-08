---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2689e81b089147dfc913fb119e0a499d60574b60
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050439"
---
I det här steget testar du tillgänglighets gruppens lyssnare genom att använda ett klient program som körs i samma nätverk.

Klient anslutningen har följande krav:

* Klient anslutningar till lyssnaren måste komma från datorer som finns i en annan moln tjänst än den som är värd för alltid på tillgänglighets repliker.
* Om Always on-repliker finns i olika undernät måste klienterna ange *MultisubnetFailover = True* i anslutnings strängen. Det här tillståndet resulterar i parallella anslutnings försök till repliker i olika undernät. I det här scenariot ingår en tillgänglighets grupps distribution över flera regioner.

Ett exempel är att ansluta till lyssnaren från en av de virtuella datorerna i samma virtuella Azure-nätverk (men inte en som är värd för en replik). Ett enkelt sätt att slutföra det här testet är att försöka ansluta SQL Server Management Studio till tillgänglighets gruppens lyssnare. En annan enkel metod är att köra [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx), enligt följande:

```console
sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15
```

> [!NOTE]
> Om värdet EndpointPort är *1433*behöver du inte ange det i anropet. Föregående anrop förutsätter också att klient datorn är ansluten till samma domän och att anroparen har beviljats behörigheter för databasen med hjälp av Windows-autentisering.
> 
> 

När du testar lyssnaren måste du växla över tillgänglighets gruppen för att se till att klienterna kan ansluta till lyssnaren över redundans.

