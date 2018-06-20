---
title: Skapa en principtilldelning för att identifiera icke-kompatibla resurser i Azure-miljön
description: Den här artikeln beskriver stegen för att skapa en principdefinition för att identifiera icke-kompatibla resurser.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 43f069fbd8f4fcc13bbc4d9e75763fa98aec1065
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601470"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>Skapa en principtilldelning för att identifiera icke-kompatibla resurser i Azure-miljön

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status. Denna snabbstart vägleder dig genom processen för att skapa en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar.

Efter den här genomgången kommer du att kunna identifiera virtuella datorer som inte använder hanterade diskar. De är *inkompatibla* med principtilldelningen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabbstarten skapar du en principtilldelning och tilldelar principdefinitionen *Granska virtuella datorer utan Managed Disks*.

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

   ![Sök efter princip](media/assign-policy-definition/search-policy.png)

2. Välj **Tilldelningar** till vänster på sidan Azure Policy. En tilldelning är en princip som tilldelats ett specifikt område.
3. Välj **Tilldela princip** längst upp på sidan **Princip – Tilldelningar**.

   ![Tilldela en principdefinition](media/assign-policy-definition/select-assign-policy.png)

4. På sidan **Tilldela princip** väljer du **Omfång** genom att klicka på ellipsen och välja en prenumeration (krävs) och en resursgrupp (valfritt). En omfattning avgör vilka resurser eller grupper med resurser som principtilldelningen används på.  Klicka sedan på **Välj** längst ned på sidan **Omfång**.

   I det här exemplet används **Contoso-prenumerationen**. Din prenumeration skiljer sig.

5. Om du vill undanta en eller flera resursgrupper (om du bara använder en prenumeration i omfånget) eller specifika resurser inom en resursgrupp (något av omfångsalternativen), kan du konfigurera **Undantag** från principtilldelningen. Lämna det tomt just nu.

6. Välj ellipsen **Principdefinition** för att öppna listan med tillgängliga definitioner. Azure Policy har inbyggda principdefinitioner som du kan använda. Många inbyggda principdefinitioner är tillgängliga, såsom:

   - Framtvinga tagg och dess värde
   - Använd tagg och dess värde
   - Kräv SQL Server version 12.0

    En fullständig lista över alla tillgängliga inbyggda principer finns i [Principexempel](json-samples.md).

7. Sök igenom listan med principdefinitioner för att hitta definitionen *Granska virtuella datorer som inte använder hanterade diskar*. Klicka på principen och sedan på **Välj**.

   ![Hitta rätt principdefinition](media/assign-policy-definition/select-available-definition.png)

8. **Tilldelningsnamn** fylls i automatiskt med namnet på principen som du valde, men du kan ändra det om du vill. I det här exemplet lämnar du *Granska virtuella datorer som inte använder hanterade diskar*. Du kan också lägga till en valfri **Beskrivning**. Beskrivningen innehåller information om den här principtilldelningen.

9. Klicka på **Tilldela**.

Du är nu redo att identifiera icke-kompatibla resurser för att förstå miljöns kompatibilitetsstatus.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Välj **Efterlevnad** till vänster på sidan och leta upp principtilldelningen **Granska virtuella datorer som inte använder hanterade diskar** som du skapade.

![Principefterlevnad](media/assign-policy-definition/policy-compliance.png)

Om det finns befintliga resurser som inte är kompatibla med denna nya tilldelning visas de under **Icke-kompatibla resurser**.

När ett villkor utvärderas mot de befintliga resurserna och visas vara korrekt markeras dessa resurser som inkompatibla med principen. Följande tabell visar hur olika principåtgärder fungerar med villkorsutvärderingen för den efterlevnadsstatus som blir resultatet. Även om du inte ser utvärderingslogiken i Azure Portal visas resultatet för kompatibilitetstillståndet. Resultatet för kompatibilitetstillståndet är antingen kompatibla eller icke-kompatibla resurser.

| **Resurstillstånd** | **Effekt** | **Principutvärdering** | **Kompatibilitetstillstånd** |
| --- | --- | --- | --- |
| Finns | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Icke-kompatibel |
| Finns | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Kompatibel |
| Ny | Audit, AuditIfNotExist\* | True | Icke-kompatibel |
| Ny | Audit, AuditIfNotExist\* | False | Kompatibel |

\* För åtgärderna Append, DeployIfNotExist och AuditIfNotExist måste IF-instruktionen är TRUE. Åtgärderna kräver också att villkoret Finns är FALSE för att vara icke-kompatibla. När det är TRUE utlöser IF-villkoret utvärdering av villkoret Finns för de relaterade resurserna.

## <a name="clean-up-resources"></a>Rensa resurser

De andra guiderna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta arbeta med efterföljande självstudier ska du inte rensa upp resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten i Azure-portalen.

1. Välj **Efterlevnad** (eller **Tilldelningar**) till vänster på sidan Azure Policy och leta upp principtilldelningen **Granska virtuella datorer som inte använder hanterade diskar** som du skapade.

2. Högerklicka på principtilldelningen **Granska virtuella datorer som inte använder hanterade diskar** och välj **Ta bort tilldelning**

   ![Ta bort en tilldelning](media/assign-policy-definition/delete-assignment.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition till ett omfång och utvärderat dess efterlevnadsrapport. Principdefinitionen säkerställer att alla resurser i omfånget är kompatibla och identifierar vilka som inte är det.

Om du vill lära dig mer om att tilldela principer för att se till att **framtida** resurser som skapas är kompatibla kan du gå vidare till självstudien för:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](create-manage-policy.md)