---
title: Visa Azure Automation uppdaterings bedömningar
description: Den här artikeln beskriver hur du visar uppdaterings utvärderingar för Uppdateringshantering distributioner.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 92861304a946e357b2b265cd825eceb8e22f7d2d
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450401"
---
# <a name="view-update-assessments"></a>Visa uppdateringsutvärderingar

I Uppdateringshantering kan du Visa information om dina datorer, saknade uppdateringar, uppdaterings distributioner och schemalagda uppdaterings distributioner.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com)

## <a name="view-update-assessment"></a>Visa kontroll av uppdateringar

I Uppdateringshantering kan du Visa information om dina datorer, saknade uppdateringar, uppdaterings distributioner och schemalagda uppdaterings distributioner.

[![Uppdateringshantering standardvy](./media/update-mgmt-overview/update-management-view.png)](./media/update-mgmt-overview/update-management-view-expanded.png#lightbox)

Gör följande om du vill visa en uppdaterings utvärdering.

1. I Azure Portal går du till **Automation-konton** och väljer ditt Automation-konto med uppdateringshantering aktiverat i listan.

2. I ditt Automation-konto väljer du **uppdaterings hantering** i det vänstra fönstret.

3. Uppdateringarna för din miljö visas på sidan hantering av **uppdateringar** . Om några uppdateringar identifieras som saknade visas en lista över dem på fliken **uppdateringar som saknas** .

   Under kolumnen **efterlevnad** kan du se den senaste gången datorn utvärderades. I kolumnen **Uppdatera agent beredskap** kan du se hälso tillståndet för uppdaterings agenten. Om ett problem uppstår väljer du länken för att gå till fel söknings dokumentation som kan hjälpa dig att åtgärda problemet.

4. Under **informations länk**väljer du länken för en uppdatering för att öppna Support artikeln som innehåller viktig information om uppdateringen.

     [![Visa uppdaterings status](./media/update-mgmt-view-update-assessments/missing-updates.png)](./media/update-mgmt-view-update-assessments/missing-updates-expanded.png#lightbox)

5. Klicka på någon annan stans på uppdateringen för att öppna loggs öknings fönstret. Frågan för loggsökningen är fördefinierad för den specifika uppdateringen. Du kan ändra den här frågan eller skapa en egen fråga för att visa detaljerad information.

    [![Visa resultat från logg fråga](./media/update-mgmt-view-update-assessments/logsearch-results.png)](./media/update-mgmt-view-update-assessments/logsearch-results-expanded.png#lightbox)

## <a name="view-missing-updates"></a>Visa saknade uppdateringar

Välj **saknade uppdateringar** om du vill visa en lista med uppdateringar som saknas på dina datorer. Varje uppdatering visas och kan väljas. Information om antalet datorer som kräver uppdatering, operativ Systems information och en länk för mer information visas. I fönstret loggs ökning visas även mer information om uppdateringarna.

![Uppdateringar som saknas](./media/update-mgmt-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>Arbeta med uppdaterings klassificeringar

I följande tabeller visas de uppdaterings klassificeringar som stöds i Uppdateringshantering, med en definition för varje klassificering.

### <a name="windows"></a>Windows

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar     | Uppdateringar för vissa problem som åtgärdar kritiska, icke-säkerhetsrelaterade buggar.        |
|Säkerhetsuppdateringar     | Uppdateringar för produktspecifika, säkerhetsrelaterade problem.        |
|Samlade uppdateringar     | Uppsättningar med snabb korrigeringar som är paketerade tillsammans för enkel distribution.        |
|Funktionspaket     | Nya produkt funktioner som distribueras utanför en produkt lansering.        |
|Service Pack     | Uppsättningar med snabb korrigeringar som tillämpas på ett program.        |
|Definitionsuppdateringar     | Uppdateringar av virus-eller andra definitionsfiler.        |
|Verktyg     | Verktyg eller funktioner som hjälper dig att slutföra en eller flera uppgifter.        |
|Uppdateringar     | Uppdateringar av program eller filer som installeras för närvarande.        |

### <a name="linux"></a>Linux

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar och säkerhetsuppdateringar     | Uppdateringar för ett enskilt problem eller ett produktspecifik, säkerhetsrelaterat problem.         |
|Övriga uppdateringar     | Alla andra uppdateringar som inte är kritiska eller som inte är av säkerhets uppdateringar.        |

För Linux kan Uppdateringshantering skilja mellan kritiska uppdateringar och säkerhets uppdateringar i molnet och Visa utvärderings data. (Denna granularitet är möjlig på grund av data berikning i molnet.) Vid uppdatering Uppdateringshantering förlitar sig på klassificerings data som är tillgängliga på datorn. Till skillnad från andra distributioner har CentOS inte den här informationen tillgänglig i RTM-versionerna av produkten. Om du har CentOS-datorer som har kon figurer ATS för att returnera säkerhets data för följande kommando kan Uppdateringshantering korrigeras baserat på klassificeringar:

```bash
sudo yum -q --security check-update
```

Det finns för närvarande ingen metod som stöds för att aktivera intern klassificerings data tillgänglighet på CentOS. För närvarande erbjuds kunder som har aktiverat den här funktionen på egen hand support.

Om du vill klassificera uppdateringar i Red Hat Enterprise version 6 måste du installera plugin-programmet yum-Security. På Red Hat Enterprise Linux 7 är plugin-programmet redan en del av yum, men du behöver inte installera något. Mer information finns i följande artiklar om Red Hat- [kunskap](https://access.redhat.com/solutions/10021).

## <a name="next-steps"></a>Nästa steg

Nästa fas i processen är att [distribuera uppdateringar](update-mgmt-deploy-updates.md) till icke-kompatibla datorer och granska distributions resultat.
