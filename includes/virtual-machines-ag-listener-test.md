---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d579e7a4fd83c1a0ce335e0b2357dcbafb217398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187288"
---
I det här steget testar du tillgänglighetsgruppavlyssnaren med hjälp av ett klientprogram som körs i samma nätverk.

Klientanslutningen har följande krav:

* Klientanslutningar till lyssnaren måste komma från datorer som finns i en annan molntjänst än den som är värd för alltid på tillgänglighetsrepliker.
* Om alltid på replikerna finns i olika undernät måste klienter ange *MultisubnetFailover=True* i anslutningssträngen. Det här villkoret resulterar i parallella anslutningsförsök till repliker i de olika undernäten. Det här scenariot innehåller en distribution av alltid regionröver på tillgänglighetsgrupp.

Ett exempel är att ansluta till lyssnaren från en av de virtuella datorerna i samma virtuella Azure-nätverk (men inte en som är värd för en replik). Ett enkelt sätt att slutföra det här testet är att försöka ansluta SQL Server Management Studio till tillgänglighetsgruppens lyssnare. En annan enkel metod är att köra [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx)enligt följande:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Om slutpunktportvärdet är *1433*behöver du inte ange det i anropet. Det föregående anropet förutsätter också att klientdatorn är ansluten till samma domän och att anroparen har beviljats behörighet för databasen med hjälp av Windows-autentisering.
> 
> 

När du testar lyssnaren måste du växla över tillgänglighetsgruppen för att se till att klienter kan ansluta till lyssnaren över redundans.

