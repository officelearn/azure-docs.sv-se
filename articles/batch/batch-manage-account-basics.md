---
title: Hantera ditt konto
description: Lär dig vad som omfattar ett Azure Batch konto
ms.topic: conceptual
ms.date: 03/30/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ce0c612e3434249c0c7d078949b10e0b9f6b1f10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116017"
---
# <a name="manage-your-batch-account"></a>Hantera ditt batch-konto

Ett Batch-konto är en unikt identifierad entitet i Batch-tjänsten. All bearbetning är associerad med ett Batch-konto.

Du kan skapa ett Azure Batch-konto med [Azure Portal](batch-account-create-portal.md) eller programmässigt som med [Batch Management .NET-biblioteket](batch-management-dotnet.md). När du skapar kontot kan du koppla ett Azure-lagringskonto för att lagra jobbrelaterade in- och utdata eller program.

Du kan köra flera Batch-arbetsbelastningar i samma Batch-konto eller distribuera dina arbetsbelastningar mellan Batch-konton som är i samma prenumeration, men i olika Azure-regioner.

## <a name="components-of-the-batch-account"></a>Komponenter för batch-kontot

Batch-kontot gör att du kan köra storskaliga batchjobb parallellt och HPC-jobb (data behandling med höga prestanda) i Azure. I det konto som du hanterar:

- De program du kör

- Allokering av pooler och noder i pooler

- Antalet och typerna av aktiviteter 

- Indata och utdata för data. Du behöver inte installera ytterligare program vara för att hantera uppgifter.

- När du skapar batch-kontot uppmanas du att tilldela det ett namn. Det här namnet är dess ID och när det har tilldelats kan inte ändras.

- Om du vill ändra namnet på ett konto måste du ta bort det och skapa ett nytt batch-konto.

- Kontot skapas i den prenumeration som du vill använda.

- Använd kontot för att identifiera och hämta primära och sekundära konto nycklar från alla batch-konton i din prenumeration.

- Kontot innehåller information om tilldelning av pooler och kärn kvoter.  

- Kontot innehåller plats information.

- Kontot identifierar ditt lagrings konto.

## <a name="next-steps"></a>Nästa steg

- Skapa ett batch-konto med hjälp av [Azure Portal](batch-account-create-portal.md).
- Skapa ett batch-konto program mässigt, till exempel med [batch Management .net-biblioteket](batch-management-dotnet.md).
- [Konfigurera eller inaktivera fjärråtkomst för att beräkna noder i en Azure Batch pool](pool-endpoint-configuration.md).
- [Kör jobb förberedelse-och jobb publicerings aktiviteter i batch Compute-noder](batch-job-prep-release.md)

