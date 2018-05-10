---
title: Skapa en principtilldelning för att identifiera ej kompatibla resurser i Azure-miljön | Microsoft Docs
description: Den här artikeln beskriver stegen för att skapa en principdefinition för att identifiera icke-kompatibla resurser.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/18/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 6bb9eddb6a663e1f230c9c46835661ad20c02cfd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>Skapa en principtilldelning för att identifiera icke-kompatibla resurser i Azure-miljön
Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status. Denna snabbstart vägleder dig genom processen för att skapa en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar.

Efter den här genomgången kommer du att kunna identifiera virtuella datorer som inte använder hanterade diskar. De är *inkompatibla* med principtilldelningen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabbstarten skapar du en principtilldelning och tilldelar principdefinitionen *Granska virtuella datorer utan Managed Disks*.

1. Välj **Tilldelningar** i det vänstra fönstret på sidan för Azure Policy.
2. Välj **Tilldela princip** längst upp på sidan **Tilldelningar**.

   ![Tilldela en principdefinition](media/assign-policy-definition/select-assign-policy.png)

3. På sidan **Tilldela princip** klickar du på knappen ![Principdefinition](media/assign-policy-definition/definitions-button.png) bredvid fältet **Princip** för att öppna listan med tillgängliga definitioner.

   ![Öppna tillgängliga principdefinitioner](media/assign-policy-definition/open-policy-definitions.png)

   Azure Policy har inbyggda principdefinitioner som du kan använda. Du kan se inbyggda principdefinitioner som:

   - Framtvinga tagg och dess värde
   - Använd tagg och dess värde
   - Kräv SQL Server Version 12.0

    En fullständig lista över alla tillgängliga inbyggda principer finns i [Principmallar](json-samples.md).

4. Sök igenom principdefinitionerna för att hitta definitionen *Granska virtuella datorer som inte använder hanterade diskar*. Klicka på principen och sedan på **Välj**.

   ![Hitta rätt principdefinition](media/assign-policy-definition/select-available-definition.png)

5. Ange ett **Namn** för visning för principtilldelningen. I det här fallet använder vi *Granska virtuella datorer som inte använder hanterade diskar*. Du kan också lägga till en valfri **Beskrivning**. Beskrivningen innehåller information om hur principtilldelningen identifierar alla virtuella datorer som inte använder hanterade diskar.
6. Ändra prisnivån till **Standard** för att se till att principen används på befintliga resurser.

   Det finns två prisnivåer i Azure Policy – *Kostnadsfri* och *Standard*. Med den kostnadsfria nivån kan du bara tillämpa principer på framtida resurser, medan Standard gör det möjligt att även tillämpa dem på befintliga resurser för att förstå kompatibilitetsstatusen bättre. Mer information om prissättning finns i [priserna för Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy/).

7. Välj vilken **Omfattning** som principen ska användas på.  En omfattning avgör vilka resurser eller grupper med resurser som principtilldelningen används på. Det kan vara allt från en prenumeration till resursgrupper.
8. Välj den prenumeration (eller resursgrupp) du registrerade tidigare. I det här exemplet används prenumerationen **Azure Analytics Capacity Dev**, men dina alternativ kan variera. Klicka på **Välj**.

   ![Hitta rätt principdefinition](media/assign-policy-definition/assign-policy.png)

9. Lämna **Undantag** tomt just nu och klicka sedan på **Tilldela**.

Du är nu redo att identifiera icke-kompatibla resurser för att förstå miljöns kompatibilitetsstatus.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Välj **Efterlevnad** i det vänstra fönstret och sök efter den principtilldelning du skapade.

![Principefterlevnad](media/assign-policy-definition/policy-compliance.png)

Om det finns befintliga resurser som inte är kompatibla med denna nya tilldelning visas de under **Icke-kompatibla resurser**.

När ett villkor utvärderas mot de befintliga resurserna och visas vara korrekt markeras dessa resurser som inkompatibla med principen. Den föregående exempelbilden visar icke-kompatibla resurser. Följande tabell visar hur olika principåtgärder fungerar med villkorsutvärderingen för resultatet av kompatibilitetstillståndet. Även om du inte ser utvärderingslogiken i Azure Portal visas resultatet för kompatibilitetstillståndet. Resultatet för kompatibilitetstillståndet är antingen kompatibla eller icke-kompatibla resurser.

| **Resurstillstånd** | **Åtgärd** | **Principutvärdering** | **Kompatibilitetstillstånd** |
| --- | --- | --- | --- |
| Finns | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Icke-kompatibel |
| Finns | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Kompatibel |
| Ny | Audit, AuditIfNotExist\* | True | Icke-kompatibel |
| Ny | Audit, AuditIfNotExist\* | False | Kompatibel |

\* Åtgärderna Append, DeployIfNotExist och AuditIfNotExist kräver att IF-instruktionen är TRUE. Åtgärderna kräver också att villkoret Finns är FALSE för att vara icke-kompatibla. När det är TRUE utlöser IF-villkoret utvärdering av villkoret Finns för de relaterade resurserna.
## <a name="clean-up-resources"></a>Rensa resurser

De andra guiderna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta arbeta med efterföljande självstudier ska du inte rensa upp resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten i Azure-portalen.
1. Välj **Tilldelningar** i det vänstra fönstret.
2. Sök efter tilldelningen som du skapade och högerklicka på den.

   ![Ta bort en tilldelning](media/assign-policy-definition/delete-assignment.png)

3.  Välj **Ta bort tilldelning**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition till ett omfång. Principdefinitionen säkerställer att alla resurser i omfånget är kompatibla och identifierar vilka som inte är det.

Om du vill lära dig mer om att tilldela principer för att se till att **framtida** resurser som skapas är kompatibla kan du gå vidare till självstudien för:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./create-manage-policy.md)
