---
title: Felsöka Azure Synapse Studio (för hands version), PowerShell för anslutning
description: Felsöka Azure Synapse Studio-anslutning med PowerShell
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: bbc985407a6cb56f4f1b539f514ab092b5f7d0de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431480"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>Diagnostisera anslutnings problem med Azure Synapse Studio (för hands version) med PowerShell-skript

Azure Synapse Studio (för hands version) är beroende av en uppsättning webb-API-slutpunkter för att fungera korrekt. Den här guiden hjälper dig att identifiera orsaker till anslutnings problem när du är:
- Konfigurera ditt lokala nätverk (till exempel ett nätverk bakom en företags brand vägg) för åtkomst till Azure Synapse Studio.
- problem med anslutningen med Azure Synapse Studio.

## <a name="prerequisite"></a>Krav

* PowerShell 5,0 eller senare versioner av Windows, eller
* PowerShell Core 6,0 eller senare version på Windows eller Linux.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Högerklicka på följande länk och klicka på "Spara mål som":

- [Test-AzureSynapse. ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Alternativt kan du öppna länken direkt och spara den öppna skript filen. Spara inte adressen till länken ovan, eftersom den kan ändras i framtiden.

I Utforskaren högerklickar du på den nedladdade skript filen och klickar på Kör med PowerShell.

![Kör Hämtad skript fil med PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

När du uppmanas till det anger du namnet på den Azure Synapse-arbetsyta som för närvarande har problem, eller om du vill testa anslutningen och trycka på RETUR.

![Ange namn på arbets yta](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

Den diagnostiska sessionen kommer att startas. Vänta tills den är klar.

![Vänta tills diagnostiken har slutförts](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

I slutet visas en diagnos Sammanfattning. Om datorn inte kan ansluta till en eller flera slut punkter visas några förslag i avsnittet "Sammanfattning".

![Granska diagnostisk Sammanfattning](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Dessutom genereras en diagnostisk loggfil för sessionen i samma mapp som fel söknings skriptet. Dess plats visas i avsnittet "allmänna tips" (`D:\TestAzureSynapse_2020....log`). Du kan skicka den här filen till teknisk support om det behövs.

Om du är nätverks administratör och justerar brand Väggs konfigurationen för Azure Synapse Studio kan den tekniska informationen som visas ovanför avsnittet "Sammanfattning" hjälpa dig.

* Alla test objekt (begär Anden) som marker ATS med "lyckades" innebär att de har godkänt anslutnings test, oavsett HTTP-statuskod.
 För de misslyckade förfrågningarna visas orsaken i gult, till exempel `NamedResolutionFailure` eller. `ConnectFailure` Dessa orsaker kan hjälpa dig att ta reda på om det finns fel i konfigurationen med din nätverks miljö.


## <a name="next-steps"></a>Nästa steg
Om föregående steg inte hjälper till att lösa problemet [skapar du ett support ärende](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).
