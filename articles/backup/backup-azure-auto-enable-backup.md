---
title: Aktivera automatisk säkerhetskopiering av skapande av virtuell dator med Azure Policy
description: En artikel som beskriver hur du använder Azure Policy för att automatiskt aktivera säkerhets kopiering för alla virtuella datorer som skapats inom ett angivet omfång
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 19985ebc51fe713ee0392800e2791ea1891ff3cd
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612681"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Aktivera automatisk säkerhetskopiering av skapande av virtuell dator med Azure Policy

En av de viktigaste ansvars områdena för en säkerhets kopierings-eller efterlevnadsprincip i en organisation är att se till att alla affärs kritiska datorer säkerhets kopie ras med lämplig kvarhållning.

Idag tillhandahåller Azure Backup en inbyggd princip (med Azure Policy) som kan tilldelas till **alla virtuella Azure-datorer på en angiven plats i en prenumeration eller resurs grupp**. När den här principen tilldelas ett angivet omfång konfigureras alla nya virtuella datorer i det omfånget automatiskt för säkerhets kopiering till ett **befintligt valv på samma plats och i en prenumeration**. Användaren kan ange valvet och den bevarande princip som de säkerhetskopierade virtuella datorerna ska associeras med.

## <a name="supported-scenarios"></a>Scenarier som stöds

* Den inbyggda principen stöds för närvarande endast för virtuella Azure-datorer. Användarna måste noga se till att den bevarande princip som anges under tilldelningen är en bevarande princip för virtuella datorer. Se [det här](./backup-azure-policy-supported-skus.md) dokumentet för att se alla VM-SKU: er som stöds av den här principen.

* Principen kan tilldelas till en enda plats och en prenumeration i taget. Om du vill aktivera säkerhets kopiering för virtuella datorer över platser och prenumerationer måste flera instanser av princip tilldelningen skapas, en för varje kombination av plats och prenumeration.

* Det angivna valvet och de virtuella datorer som har kon figurer ATS för säkerhets kopiering kan ligga under olika resurs grupper.

* Hanterings gruppens omfång stöds inte för tillfället.

* Den inbyggda principen är för närvarande inte tillgänglig i nationella moln.

## <a name="using-the-built-in-policy"></a>Använda den inbyggda principen

Följ stegen nedan om du vill tilldela principen till det begärda omfånget:

1. Logga in på Azure Portal och gå till instrument panelen för **principer** .
1. Välj **definitioner** på den vänstra menyn för att få en lista över alla inbyggda principer i Azure-resurser.
1. Filtrera listan för **kategori = säkerhets kopiering**. Du ser listan filtrerad nedåt till en enda princip med namnet "Konfigurera säkerhets kopiering på virtuella datorer för en plats till ett befintligt centralt valv på samma plats".
![Princip instrument panel](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
1. Välj namnet på principen. Du omdirigeras till den detaljerade definitionen för den här principen.
![Fönstret princip definition](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
1. Välj knappen **tilldela** högst upp i fönstret. Detta omdirigerar dig till fönstret **tilldela princip** .
1. Under **grunderna**väljer du de tre punkterna bredvid fältet **omfång** . Det här öppnar ett höger kontext fönster där du kan välja prenumerationen för den princip som ska tillämpas på. Du kan också välja en resurs grupp, så att principen endast tillämpas för virtuella datorer i en viss resurs grupp.
![Grundläggande princip tilldelning](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
1. På fliken **parametrar** väljer du en plats i list rutan och väljer det valv och den säkerhets kopierings princip som de virtuella datorerna i omfånget måste vara associerade till.
![Parametrar för princip tilldelning](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
1. Se till att **inställningen är inställd** på deployIfNotExists.
1. Gå till **Granska + skapa** och välj **skapa**.

> [!NOTE]
>
> Azure Policy kan också användas på befintliga virtuella datorer, med hjälp av [reparation](../governance/policy/how-to/remediate-resources.md).

> [!NOTE]
>
> Vi rekommenderar att den här principen inte är tilldelad till fler än 200 virtuella datorer i taget. Om principen är tilldelad till fler än 200 virtuella datorer kan det leda till att säkerhets kopieringen utlöses några timmar senare än vad som anges i schemat.

## <a name="next-steps"></a>Efterföljande moment

[Läs mer om Azure Policy](../governance/policy/overview.md)
