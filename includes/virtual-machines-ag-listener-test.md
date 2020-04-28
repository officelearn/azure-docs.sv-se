---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d579e7a4fd83c1a0ce335e0b2357dcbafb217398
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "67187288"
---
I det här steget testar du tillgänglighets gruppens lyssnare genom att använda ett klient program som körs i samma nätverk.

Klient anslutningen har följande krav:

* Klient anslutningar till lyssnaren måste komma från datorer som finns i en annan moln tjänst än den som är värd för alltid på tillgänglighets repliker.
* Om Always on-repliker finns i olika undernät måste klienterna ange *MultisubnetFailover = True* i anslutnings strängen. Det här tillståndet resulterar i parallella anslutnings försök till repliker i olika undernät. I det här scenariot ingår en tillgänglighets grupps distribution över flera regioner.

Ett exempel är att ansluta till lyssnaren från en av de virtuella datorerna i samma virtuella Azure-nätverk (men inte en som är värd för en replik). Ett enkelt sätt att slutföra det här testet är att försöka ansluta SQL Server Management Studio till tillgänglighets gruppens lyssnare. En annan enkel metod är att köra [SQLCMD. exe](https://technet.microsoft.com/library/ms162773.aspx)på följande sätt:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Om värdet EndpointPort är *1433*behöver du inte ange det i anropet. Föregående anrop förutsätter också att klient datorn är ansluten till samma domän och att anroparen har beviljats behörigheter för databasen med hjälp av Windows-autentisering.
> 
> 

När du testar lyssnaren måste du växla över tillgänglighets gruppen för att se till att klienterna kan ansluta till lyssnaren över redundans.

