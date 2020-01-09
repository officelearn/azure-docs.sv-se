---
title: Aktivera automatisk säkerhetskopiering av skapande av virtuell dator med Azure Policy
description: En artikel som beskriver hur du använder Azure Policy för att automatiskt aktivera säkerhets kopiering för alla virtuella datorer som skapats inom ett angivet omfång
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 7a3b526d654936d4e7ec89127a9074146c1b0179
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450124"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Aktivera automatisk säkerhetskopiering av skapande av virtuell dator med Azure Policy

En av de viktigaste ansvars områdena för en säkerhets kopierings-eller efterlevnadsprincip i en organisation är att se till att alla affärs kritiska datorer säkerhets kopie ras med lämplig kvarhållning.

Idag tillhandahåller Azure Backup en inbyggd princip (med Azure Policy) som kan tilldelas till **alla virtuella Azure-datorer på en angiven plats i en prenumeration eller resurs grupp**. När den här principen tilldelas ett angivet omfång konfigureras alla nya virtuella datorer i det omfånget automatiskt för säkerhets kopiering till ett **befintligt valv på samma plats och i en prenumeration**. Användaren kan ange valvet och den bevarande princip som de säkerhetskopierade virtuella datorerna ska associeras med.

## <a name="supported-scenarios"></a>Scenarier som stöds 

* Den inbyggda principen stöds för närvarande endast för virtuella Azure-datorer. Användarna måste noga se till att den bevarande princip som anges under tilldelningen är en bevarande princip för virtuella datorer. Se [det här](https://aka.ms/PolicySupportedSKUs) dokumentet för att se alla VM-SKU: er som stöds av den här principen.

* Principen kan tilldelas till en enda plats och en prenumeration i taget. Om du vill aktivera säkerhets kopiering för virtuella datorer över platser och prenumerationer måste flera instanser av princip tilldelningen skapas, en för varje kombination av plats och prenumeration.

* Det angivna valvet och de virtuella datorer som har kon figurer ATS för säkerhets kopiering kan ligga under olika resurs grupper.

* Hanterings gruppens omfång stöds inte för tillfället.

* Den inbyggda principen är för närvarande inte tillgänglig i nationella moln.

## <a name="using-the-built-in-policy"></a>Använda den inbyggda principen

Följ stegen nedan om du vill tilldela principen till det begärda omfånget:

1. Logga in på Azure Portal och gå till **princip** instrument panelen.
2. Välj **definitioner** på den vänstra menyn för att få en lista över alla inbyggda principer i Azure-resurser.
3. Filtrera listan för **kategori = säkerhets kopiering**. Du ser listan filtrerad nedåt till en enda princip med namnet "Konfigurera säkerhets kopiering på virtuella datorer för en plats till ett befintligt centralt valv på samma plats".
Instrument panel för ![princip](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Klicka på namnet på principen. Du kommer att omdirigeras till den detaljerade definitionen för den här principen.
Blad](./media/backup-azure-auto-enable-backup/policy-definition-blade.png) för ![princip definition
5. Klicka på knappen **tilldela** överst på bladet. Detta omdirigerar dig till bladet **tilldela princip** .
6. Under **grunderna**klickar du på de tre punkterna bredvid fältet **omfång** . Detta öppnar ett blad med rätt kontext där du kan välja prenumerationen för den princip som ska tillämpas på. Du kan också välja en resurs grupp, så att principen endast tillämpas för virtuella datorer i en viss resurs grupp.
Grundläggande](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png) för ![princip tilldelning
7. På fliken **parametrar** väljer du en plats i list rutan och väljer det valv och den säkerhets kopierings princip som de virtuella datorerna i omfånget måste vara associerade till.
Parametrar för ![princip tilldelning](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Se till att **inställningen är inställd** på deployIfNotExists.
9. Gå till **Granska + skapa** och klicka på **skapa**.

> [!NOTE]
>
> Azure Policy kan också användas på befintliga virtuella datorer, med hjälp av [reparation](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources).

> [!NOTE]
>
> Vi rekommenderar att den här principen inte är tilldelad till fler än 200 virtuella datorer i taget. Om principen är tilldelad till fler än 200 virtuella datorer kan det leda till att säkerhets kopieringen utlöses några timmar senare än vad som anges i schemat.

## <a name="next-steps"></a>Efterföljande moment

[Läs mer om Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview)