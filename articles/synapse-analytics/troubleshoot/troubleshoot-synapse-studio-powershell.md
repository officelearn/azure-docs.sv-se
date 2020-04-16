---
title: Felsöka Azure Synapse Studio (förhandsversion) anslutning PowerShell
description: Felsöka Azure Synapse Studio-anslutning med PowerShell
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: bbc985407a6cb56f4f1b539f514ab092b5f7d0de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431480"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>Diagnostisera problem med Azure Synapse Studio-anslutning (förhandsversion) med PowerShell-skript

Azure Synapse Studio (förhandsversion) beror på en uppsättning webb-API-slutpunkter för att fungera korrekt. Den här guiden hjälper dig att identifiera orsaker till anslutningsproblem när du är:
- konfigurera ditt lokala nätverk (till exempel nätverk bakom en företagsbrandvägg) för åtkomst till Azure Synapse Studio.
- anslutningsproblem med Azure Synapse Studio.

## <a name="prerequisite"></a>Krav

* PowerShell 5.0 eller senare version i Windows, eller
* PowerShell Core 6.0 eller senare version på Windows eller Linux.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Högerklicka på följande länk och klicka på "Spara mål som":

- [Testa AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Du kan också öppna länken direkt och spara den öppnade skriptfilen. Spara inte adressen till länken ovan, eftersom det kan ändras i framtiden.

Högerklicka på den nedladdade skriptfilen i utforskaren och klicka på "Kör med PowerShell".

![Kör nedladdad skriptfil med PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

När du uppmanas att ange azure Synapse-arbetsytenamnet som för närvarande har problem eller som du vill testa för anslutning och tryck enter.

![Ange arbetsytenamn](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

Diagnostiksessionen startas. Vänta tills den är klar.

![Vänta tills diagnosen är klar](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

I slutändan kommer en diagnos sammanfattning visas. Om datorn inte kan ansluta till en eller flera av slutpunkterna visas några förslag i avsnittet Sammanfattning.

![Granska diagnostiksammanfattning](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Dessutom genereras en diagnostikloggfil för den här sessionen i samma mapp som felsökningsskriptet. Dess placering visas i avsnittet "Allmänna tips" (`D:\TestAzureSynapse_2020....log`). Du kan skicka filen till teknisk support om det behövs.

Om du är nätverksadministratör och justerar brandväggskonfigurationen för Azure Synapse Studio kan den tekniska informationen som visas ovanför avsnittet Sammanfattning vara till hjälp.

* Alla testobjekt (begäranden) som markerats med "Godkänd" innebär att de har klarat anslutningstester, oavsett HTTP-statuskoden.
 För misslyckade begäranden visas orsaken i gult, till exempel `NamedResolutionFailure` eller `ConnectFailure`. Dessa orsaker kan hjälpa dig att ta reda på om det finns felkonfigurationer med nätverksmiljön.


## <a name="next-steps"></a>Nästa steg
Om de föregående stegen inte hjälper till att lösa problemet [problemet Skapa en supportbiljett](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).
