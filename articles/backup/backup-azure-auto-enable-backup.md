---
title: Aktivera automatisk säkerhetskopiering av skapande av virtuell dator med Azure Policy
description: En artikel som beskriver hur du använder Azure Policy för att automatiskt aktivera säkerhetskopiering för alla virtuella datorer som skapats i ett visst scope
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1d423371788f87155328e8f5c9334e47349a68e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584276"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Aktivera automatisk säkerhetskopiering av skapande av virtuell dator med Azure Policy

Ett av de viktigaste ansvarsområdena för en administratör för säkerhetskopiering eller efterlevnad i en organisation är att se till att alla affärskritiska datorer säkerhetskopieras med lämplig kvarhållning.

Idag tillhandahåller Azure Backup en inbyggd princip (med Azure-princip) som kan tilldelas **alla virtuella Azure-datorer på en angiven plats i en prenumeration eller resursgrupp**. När den här principen tilldelas ett visst scope konfigureras alla nya virtuella datorer som skapas i det omfånget automatiskt för säkerhetskopiering till ett **befintligt valv på samma plats och prenumeration**. Användaren kan ange valvet och bevarandeprincipen som de säkerhetskopierade virtuella datorerna ska associeras till.

## <a name="supported-scenarios"></a>Scenarier som stöds

* Den inbyggda principen stöds för närvarande endast för virtuella Azure-datorer. Användare måste se till att bevarandeprincipen som anges under tilldelningen är en princip för bevarande av virtuella datorer. Se [det här](https://docs.microsoft.com/azure/backup/backup-azure-policy-supported-skus) dokumentet om du vill se alla SKU:er för virtuella datorer som stöds av den här principen.

* Principen kan tilldelas en enda plats och prenumeration åt gången. För att aktivera säkerhetskopiering för virtuella datorer på olika platser och prenumerationer måste flera instanser av principtilldelningen skapas, en för varje kombination av plats och prenumeration.

* Det angivna valvet och de virtuella datorerna som konfigurerats för säkerhetskopiering kan vara under olika resursgrupper.

* Ledningens omfattning stöds för närvarande inte.

* Den inbyggda policyn är för närvarande inte tillgänglig i nationella moln.

## <a name="using-the-built-in-policy"></a>Använda den inbyggda principen

För att tilldela principen till det nödvändiga omfånget, följ nedanstående steg:

1. Logga in på Azure Portal och navigera till **principinstrumentpanelen.**
2. Välj **Definitioner** i den vänstra menyn för att få en lista över alla inbyggda principer i Azure Resources.
3. Filtrera listan för **Category=Backup**. Listan filtreras ned till en enda princip med namnet Konfigurera säkerhetskopiering på virtuella datorer på en plats till ett befintligt centralt Arkiv på samma plats.
![Instrumentpanel för princip](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Klicka på namnet på principen. Du kommer att omdirigeras till den detaljerade definitionen för den här principen.
![Blad för principdefinition](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Klicka på knappen **Tilldela** högst upp på bladet. Detta omdirigerar dig till bladet **Tilldela princip.**
6. Klicka på de tre punkterna bredvid **fältet Scope** under **Grunderna.** Detta öppnar ett rätt kontextblad där du kan välja prenumeration för den princip som ska tillämpas på. Du kan också välja en resursgrupp, så att principen endast tillämpas för virtuella datorer i en viss resursgrupp.
![Grundläggande om principtilldelning](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. På fliken **Parametrar** väljer du en plats i listrutan och väljer den princip för valv och säkerhetskopiering som de virtuella datorerna i omfånget måste associeras till.
![Parametrar för principtilldelning](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Kontrollera att **Effekten** är inställd på deployIfNotExists.
9. Navigera till **Granska+skapa** och klicka på **Skapa**.

> [!NOTE]
>
> Azure Policy kan också användas på befintliga virtuella datorer med hjälp av [reparation](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources).

> [!NOTE]
>
> Vi rekommenderar att den här principen inte tilldelas mer än 200 virtuella datorer åt gången. Om principen har tilldelats fler än 200 virtuella datorer kan det leda till att säkerhetskopieringen utlöses några timmar senare än vad som anges i schemat.

## <a name="next-steps"></a>Efterföljande moment

[Läs mer om Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview)
