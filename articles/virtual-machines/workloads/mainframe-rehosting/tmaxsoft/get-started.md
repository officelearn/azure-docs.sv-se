---
title: Kom igång med TmaxSoft OpenFrame på Azure Virtual Machines
description: Ange ny värd för dina IBM z/OS stordatorprogram arbetsbelastningar med hjälp av TmaxSoft OpenFrame miljö på Azure Virtual Machines (VM).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896475"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Kom igång med TmaxSoft OpenFrame på Azure

Ta dina befintliga tillgångar stordatorprogram och flytta dem till Microsoft Azure med TmaxSoft OpenFrame. Den här populära rehosting lösningen skapar en emulering miljö på Azure, så att du kan snabbt migrera program. Formatera om ingen krävs.

## <a name="openframe-rehosting-environment"></a>OpenFrame rehosting miljö

Konfigurera en OpenFrame miljö på Azure för utveckling, demonstrationer, testning eller produktionsarbetsbelastningar. Enligt följande bild visar innehåller OpenFrame flera komponenter som skapar stordatorprogram emulering miljö på Azure. OpenFrame onlinetjänster Ersätt till exempel stordatorprogram mellanprogram, till exempel IBM kundens Information kontroll System (CICS). OpenFrame Batch ersätter med dess TJES-komponenten IBM-stordatorer jobbet post undersystem (JES). 

![OpenFrame rehosting process](media/openframe-01.png)

> [!NOTE]
> Om du vill köra OpenFrame-miljö på Azure måste du ha en giltig produktlicens eller utvärderingslicens från TmaxSoft.

## <a name="openframe-components"></a>OpenFrame komponenter

Följande komponenter ingår i OpenFrame miljön i Azure:

- **Migreringsverktyg** inklusive OFMiner, en lösning som analyserar stordatorer tillgångar och migrerar dem till Azure.
- **Kompilatorer**, inklusive OFCOBOL, en kompilator som tolkar det stordatorprogram COBOL program. OFPLI, som tolkar det stordatorprogram PL / jag program; och OFASM, en kompilator som tolkar det stordatorprogram assembler program.
- **Klientdelen** komponenter, inklusive Java Enterprise användaren lösning (JEUS) och en webbserver för program som är certifierad för Java Enterprise Edition 6.OFGW OpenFrame gateway-komponenten som tillhandahåller en 3270 lyssnare.
- **Programmet** miljö. OpenFrame Base är mellanprogram som hanterar hela systemet. OpenFrame Server typ C (OSC) ersätter stordatorprogram mellanprogram och IBM CICS.
- **Relationsdatabas**, till exempel Tibero (visas), Oracle Database, Microsoft SQL Server, IBM Db2 eller MySQL. OpenFrame-programmen använder Open Database Connectivity (ODBC)-protokollet för att kommunicera med databasen.
- **Security** via TACF, en tjänst-modul som styr användarnas åtkomst till system och resurser. 
- **OFManager** är en lösning som ger Openframes åtgärden och hanteringsfunktioner i webbmiljö.

![OpenFrame arkitektur](media/openframe-02.png)

## <a name="next-steps"></a>Nästa steg

- [Installera TmaxSoft OpenFrame på Azure](./install-openframe-azure.md)
