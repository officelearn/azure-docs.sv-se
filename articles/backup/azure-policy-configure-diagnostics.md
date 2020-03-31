---
title: Konfigurera inställningar för Arkivdiagnostik i stor skala
description: Konfigurera inställningar för diagnostik i logganalys för alla valv i ett visst scope med Azure-principen
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: c92957cab3e1ed745e7031e3c6f32e7ecda550a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584531"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>Konfigurera inställningar för Arkivdiagnostik i stor skala

Rapporteringslösningen som tillhandahålls av Azure Backup utnyttjar Log Analytics (LA). För att data för ett visst valv ska skickas till LA måste en [diagnostikinställning skapas](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events) för det valvet.

Ofta kan det vara besvärligt att lägga till en diagnostikinställning manuellt per valv. Dessutom måste alla nya valv som skapas också ha diagnostikinställningar aktiverade för att kunna visa rapporter för det här valvet. 

Azure Backup tillhandahåller en inbyggd [Azure-princip](https://docs.microsoft.com/azure/governance/policy/)för att förenkla skapandet av diagnostikinställningar i stor skala (med LA som mål). Den här principen lägger till en LA-diagnostikinställning i alla valv i en viss prenumeration eller resursgrupp. I följande avsnitt finns instruktioner om hur du använder den här principen.

## <a name="supported-scenarios"></a>Scenarier som stöds

* Principen kan tillämpas samtidigt på alla Recovery Services-valv i en viss prenumeration (eller på en resursgrupp inom prenumerationen). Användaren som tilldelar principen måste ha "Ägare" åtkomst till den prenumeration som principen är tilldelad.

* LA-arbetsytan som anges av användaren (som diagnostikdata ska skickas till) kan finnas i en annan prenumeration än de valv som principen är tilldelad till. Användaren måste ha åtkomst till den prenumeration där den angivna LA-arbetsytan finns.

* Ledningens omfattning stöds för närvarande inte.

* Den inbyggda policyn är för närvarande inte tillgänglig i nationella moln.

## <a name="assigning-the-built-in-policy-to-a-scope"></a>Tilldela den inbyggda principen till ett scope

Så här tilldelar du principen för valv i det nödvändiga omfånget:

1. Logga in på Azure-portalen och navigera till **principinstrumentpanelen.**
2. Välj **Definitioner** i den vänstra menyn för att få en lista över alla inbyggda principer i Azure Resources.
3. Filtrera listan för **Category=Monitoring**. Leta reda på principen **[Förhandsversion]: Distribuera diagnostikinställningar för Recovery Services Vault till Log Analytics-arbetsytan för resursspecifika kategorier**.

![Blad för principdefinition](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Klicka på namnet på principen. Du kommer att omdirigeras till den detaljerade definitionen för den här principen.

![Detaljerad principdefinition](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. Klicka på knappen **Tilldela** högst upp på bladet. Detta omdirigerar dig till bladet **Tilldela princip.**

6. Klicka på de tre punkterna bredvid **fältet Scope** under **Grunderna.** Detta öppnar ett rätt kontextblad där du kan välja prenumeration för den princip som ska tillämpas på. Du kan också välja en resursgrupp, så att principen endast tillämpas för valv i en viss resursgrupp.

![Grundläggande om principtilldelning](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. Under **Parametrar**anger du följande information:

* **Profilnamn** - Namnet som ska tilldelas diagnostikinställningarna som skapas av principen.
* **Log Analytics Workspace** - Arbetsytan Log Analytics som diagnostikinställningen ska associeras till. Diagnostikdata för alla valv i principtilldelningens omfattning kommer att skickas till den angivna LA-arbetsytan.

* **Namn på undantagstagg (valfritt) och uteslutningstaggvärde (valfritt)** – Du kan välja att utesluta valv som innehåller ett visst taggnamn och värde från principtilldelningen. Om du till exempel **inte** vill att en diagnostikinställning ska läggas till i de valv som har taggen 'isTest' inställt på värdet "ja", måste du ange 'isTest' i fältet **Uteslutningstaggnamn** och "ja" i fältet **Uteslutningstaggvärde.** Om några (eller båda) av dessa två fält lämnas tomma, tillämpas principen på alla relevanta valv oavsett vilka taggar de innehåller.

![Parametrar för principtilldelning](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **Skapa en reparationsuppgift** - När principen har tilldelats ett scope får alla nya valv som skapas i det omfånget automatiskt LA-diagnostikinställningar konfigurerade (inom 30 minuter från det att valvet skapades). Om du vill lägga till en diagnostikinställning i befintliga valv i omfånget kan du utlösa en reparationsaktivitet vid tilldelningstiden för principen. Om du vill utlösa en reparationsaktivitet markerar du kryssrutan **Skapa en reparationsaktivitet**. 

![Reparation av principtilldelning](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Navigera till fliken **Granska+Skapa** och klicka på **Skapa**.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>Under vilka villkor gäller reparationsuppgiften för ett valv?

Reparationsuppgiften tillämpas på valv som inte är kompatibla enligt definitionen av principen. Ett valv är inte kompatibelt om det uppfyller något av följande villkor:

* Det finns ingen diagnostikinställning för valvet.
* Diagnostikinställningar finns för valvet, men ingen av inställningarna har **alla** resursspecifika händelser aktiverade med LA som mål och **Resursspecifikt** markerat i växlingsknappen. 

Så även om en användare har ett valv med AzureBackupReport-händelsen aktiverad i AzureDiagnostics-läge (som stöds av säkerhetskopieringsrapporter), gäller reparationsuppgiften fortfarande för det här valvet, eftersom resursspecifikt läge är det rekommenderade sättet att skapa diagnostikinställningar, [framöver](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event).

Om en användare har ett valv med endast en delmängd av de sex resursspecifika händelser som är aktiverade, kommer reparationsaktiviteten att gälla för det här valvet, eftersom säkerhetskopieringsrapporter fungerar som förväntat endast om alla sex resursspecifika händelser är aktiverade.

> [!NOTE]
>
> Om ett valv har en befintlig diagnostikinställning med en **delmängd av resursspecifika** kategorier aktiverade, konfigurerade för att skicka data till en viss LA-arbetsyta, säger "Workspace X", misslyckas reparationsaktiviteten (bara för det valvet) om målet LA Workspace som anges i principtilldelningen är **samma** "Workspace X". 
>
>Detta beror på att om de händelser som aktiveras av två olika diagnostikinställningar på samma resurs **överlappar** varandra i någon form, kan inställningarna inte ha samma LA-arbetsyta som målet. Du måste lösa det här felet manuellt genom att navigera till det relevanta valvet och konfigurera en diagnostikinställning med en annan LA-arbetsyta som mål.
>
> Observera att reparationsuppgiften **inte** misslyckas om den befintliga diagnostikinställningen som endast AzureBackupReport aktiverad med Workspace X som mål, eftersom det i det här fallet inte finns någon överlappning mellan de händelser som aktiveras av den befintliga inställningen och de händelser som aktiveras av inställningen som skapats av reparationsuppgiften.

## <a name="next-steps"></a>Efterföljande moment

* [Läs om hur du använder säkerhetsrapporter](https://docs.microsoft.com/azure/backup/configure-reports)
* [Läs mer om Azure Policy](https://docs.microsoft.com/azure/governance/policy/)
* [Använd Azure Policy för att automatiskt aktivera säkerhetskopiering för alla virtuella datorer i ett give-scope](https://docs.microsoft.com/azure/backup/backup-azure-auto-enable-backup)
