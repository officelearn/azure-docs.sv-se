---
title: 'Snabbstart: Nytt principtilldelning med portal'
description: I den här snabbstarten använder du Azure-portalen för att skapa en Azure Policy-tilldelning för att identifiera icke-kompatibla resurser.
ms.date: 03/24/2020
ms.topic: quickstart
ms.openlocfilehash: 3a514478f3cefa49c37c3431a80b578dff480584
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80240013"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Snabbstart: Skapa en principtilldelning för att identifiera icke-kompatibla resurser

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status.
Denna snabbstart vägleder dig genom processen för att skapa en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar.

Efter den här genomgången kommer du att kunna identifiera virtuella datorer som inte använder hanterade diskar. De är _inkompatibla_ med principtilldelningen.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabbstarten skapar du en principtilldelning och tilldelar policydefinitionen _Granska virtuella datorer som inte använder hanterade diskar_.

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

   :::image type="content" source="./media/assign-policy-portal/search-policy.png" alt-text="Sök efter policy i alla tjänster" border="false":::

1. Välj **Tilldelningar** till vänster på sidan Azure Policy. En tilldelning är en princip som tilldelats ett specifikt område.

   :::image type="content" source="./media/assign-policy-portal/select-assignments.png" alt-text="Välj sidan Tilldelningar från sidan Principöversikt" border="false":::

1. Välj **Tilldela princip** längst upp på sidan **Princip – Tilldelningar**.

   :::image type="content" source="./media/assign-policy-portal/select-assign-policy.png" alt-text="Tilldela en principdefinition från sidan Tilldelningar" border="false":::

1. På sidan **Tilldela princip** väljer du **Omfång** genom att klicka på ellipsen och antingen välja en hanteringsgrupp eller en prenumeration. Du kan även välja en resursgrupp. En omfattning avgör vilka resurser eller grupper med resurser som principtilldelningen används på. Klicka sedan på **Välj** längst ned på sidan **Omfång**.

   I det här exemplet används **Contoso-prenumerationen**. Din prenumeration skiljer sig.

1. Resurser som kan uteslutas baserat på **omfång**. **Undantag** startar på en nivå som är lägre än nivån för **Omfång**. **Undantag** är valfria, så lämna det tomt just nu.

1. Välj ellipsen **Principdefinition** för att öppna listan med tillgängliga definitioner. Azure Policy har inbyggda principdefinitioner som du kan använda. Många är tillgängliga, till exempel:

   - Framtvinga tagg och dess värde
   - Använd tagg och dess värde
   - Ärva en tagg från resursgruppen om den saknas

   En ofullständig lista över tillgängliga inbyggda principer finns i [Azure Policy-exempel](./samples/index.md).

1. Sök igenom listan med principdefinitioner för att hitta definitionen _Granska virtuella datorer som inte använder hanterade diskar_. Klicka på principen och sedan på **Välj**.

   :::image type="content" source="./media/assign-policy-portal/select-available-definition.png" alt-text="Hitta rätt principdefinition" border="false":::

1. **Tilldelningsnamn** fylls i automatiskt med namnet på principen som du valde, men du kan ändra det om du vill. I det här exemplet lämnar du _Granska virtuella datorer som inte använder hanterade diskar_. Du kan också lägga till en valfri **Beskrivning**. Beskrivningen innehåller information om den här principtilldelningen.
   **Assigned by** (Tilldelats av) anges automatiskt baserat på vem som är inloggad. Det här fältet är valfritt, så du kan ange anpassade värden.

1. Lämna **Skapa en hanterad identitet** avmarkerat. Den här rutan _måste_ markeras när principen eller initiativet omfattar en princip med effekten [deployIfNotExists](./concepts/effects.md#deployifnotexists). Eftersom principen som används för den här snabbstarten inte gör det kan du lämna det tomt. Mer information finns i avsnitten om [hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md) och [hur reparationssäkerhet fungerar](./how-to/remediate-resources.md#how-remediation-security-works).

1. Klicka på **Tilldela**.

Du är nu redo att identifiera icke-kompatibla resurser för att förstå efterlevnadstillståndet för din miljö.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Välj **Efterlevnad** till vänster på sidan. Leta sedan upp principtilldelningen **Granska virtuella datorer som inte använder hanterade diskar** som du skapade.

:::image type="content" source="./media/assign-policy-portal/policy-compliance.png" alt-text="Information om efterlevnad på sidan Policy Compliance" border="false":::

Om det finns befintliga resurser som inte är kompatibla med denna nya tilldelning visas de under **Icke-kompatibla resurser**.

När ett villkor utvärderas mot de befintliga resurserna och visas vara korrekt markeras dessa resurser som inkompatibla med principen. Följande tabell visar hur olika principåtgärder fungerar med villkorsutvärderingen för den efterlevnadsstatus som blir resultatet. Även om du inte ser utvärderingslogiken i Azure-portalen visas efterlevnadstillståndsresultaten. Resultatet för kompatibilitetstillståndet är antingen kompatibla eller icke-kompatibla resurser.

| **Resurstillstånd** | **Effekt** | **Principutvärdering** | **Kompatibilitetstillstånd** |
| --- | --- | --- | --- |
| Finns | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Icke-kompatibel |
| Finns | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Kompatibel |
| Ny | Audit, AuditIfNotExist\* | True | Icke-kompatibel |
| Ny | Audit, AuditIfNotExist\* | False | Kompatibel |

\* För åtgärderna Append, DeployIfNotExist och AuditIfNotExist måste IF-instruktionen är TRUE.
Åtgärderna kräver också att villkoret Finns är FALSE för att vara icke-kompatibla. När det är TRUE utlöser IF-villkoret utvärdering av villkoret Finns för de relaterade resurserna.

## <a name="clean-up-resources"></a>Rensa resurser

Följ dessa steg för att ta bort tilldelningen som skapades:

1. Välj **Efterlevnad** (eller **Tilldelningar**) till vänster på sidan Azure Policy och leta upp principtilldelningen **Granska virtuella datorer som inte använder hanterade diskar** som du skapade.

1. Högerklicka på de **virtuella gransknings-datorerna som inte använder tilldelning av hanterade diskar** och välj **Ta bort tilldelning**.

   :::image type="content" source="./media/assign-policy-portal/delete-assignment.png" alt-text="Ta bort en tilldelning från sidan Efterlevnad" border="false":::

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition till ett omfång och utvärderat dess efterlevnadsrapport.
Principdefinitionen kontrollerar att alla resurser i omfånget är kompatibla och identifierar vilka som inte är det.

Om du vill ha mer information om tilldelning av principer för att validera att de nya resurserna är kompatibla fortsätter du till självstudien för att:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)