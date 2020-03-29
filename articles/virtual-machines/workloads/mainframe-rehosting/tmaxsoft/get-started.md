---
title: Komma igång med TmaxSoft OpenFrame på virtuella Azure-datorer
description: Rehost din IBM z / OS stordator arbetsbelastningar med TmaxSoft OpenFrame miljö på Azure Virtual Machines (VIRTUELLA datorer).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61485537"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Komma igång med TmaxSoft OpenFrame på Azure

Ta dina befintliga stordatortillgångar och flytta dem till Microsoft Azure med TmaxSoft OpenFrame. Den här populära rehosting-lösningen skapar en emuleringsmiljö på Azure, så att du snabbt kan migrera program. Ingen omformulering krävs.

## <a name="openframe-rehosting-environment"></a>OpenFrame rehosting miljö

Konfigurera en OpenFrame-miljö på Azure för utveckling, demonstrationer, testning eller produktionsarbetsbelastningar. Som följande bild visar innehåller OpenFrame flera komponenter som skapar stordatoremuleringsmiljön på Azure. Till exempel ersätter OpenFrame-onlinetjänster stordatorns mellanprogram som IBM Customer Information Control System (CICS). OpenFrame Batch, med tjes-komponenten, ersätter IBM-stordatorns undersystem Job Entry (JES). 

![OpenFrame rehosting process](media/openframe-01.png)

> [!NOTE]
> Om du vill köra OpenFrame-miljön på Azure måste du ha en giltig produktlicens eller utvärderingslicens från TmaxSoft.

## <a name="openframe-components"></a>OpenFrame-komponenter

Följande komponenter är en del av OpenFrame-miljön på Azure:

- **Migreringsverktyg** inklusive OFMiner, en lösning som analyserar stordatortillgångarna och sedan migrerar dem till Azure.
- **Kompilatorer**, inklusive OFCOBOL, en kompilator som tolkar stordatorns COBOL-program; OFPLI, som tolkar stordatorns PL/I-program; och OFASM, en kompilator som tolkar stordatorns monteringsprogram.
- **Frontend-komponenter,** inklusive Java Enterprise User Solution (JEUS), en webbprogramserver som är certifierad för Java Enterprise Edition 6.OFGW och OpenFrame gateway-komponenten som tillhandahåller en 3270-lyssnare.
- **Programmiljö.** OpenFrame Base är mellanprogram som hanterar hela systemet. OpenFrame Server Type C (OSC) ersätter stordatorns mellanprogram och IBM CICS.
- **Relationsdatabas**, till exempel Tibero (visas), Oracle Database, Microsoft SQL Server, IBM Db2 eller MySQL. OpenFrame-programmen använder ODBC-protokollet (Open Database Connectivity) för att kommunicera med databasen.
- **Säkerhet** via TACF, en tjänstmodul som styr användarnas åtkomst till system och resurser. 
- **OFManager** är en lösning som tillhandahåller OpenFrames drift- och hanteringsfunktioner i webbmiljön.

![OpenFrame-arkitektur](media/openframe-02.png)

## <a name="next-steps"></a>Nästa steg

- [Installera TmaxSoft OpenFrame på Azure](./install-openframe-azure.md)
