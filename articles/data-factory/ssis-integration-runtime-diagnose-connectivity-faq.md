---
title: Felsök diagnostisering av anslutningar i SSIS integration runtime
description: Den här artikeln innehåller fel söknings vägledning för att diagnostisera anslutningar i SSIS integration runtime
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: 8e520048a6067f134e847953f4f4aa0598d9926e
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86172713"
---
# <a name="troubleshoot-diagnose-connectivity-in-the-ssis-integration-runtime"></a>Felsök diagnostisering av anslutningar i SSIS integration runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Om det uppstår problem med anslutningen när du kör SSIS-paket i SSIS integration runtime, särskilt om din SSIS integration Runtime har anslutit till det virtuella Azure-nätverket. Du kan försöka att själv diagnostisera problem genom att använda den här funktionen för att diagnostisera anslutningar på sidan för övervakning av SSIS-integrering i Azure Data Factory-portalen. 

 ![Sidan övervaka sidan – diagnostisera anslutnings ](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png) ![ övervakaren – testa anslutning](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)
 
Den här artikeln innehåller de vanligaste fel som du kan hitta när du testar anslutningen i SSIS integration Runtime. Det beskriver möjliga orsaker och åtgärder för att lösa felen. 

## <a name="common-errors-potential-causes-and-recommendation-solutions"></a>Vanliga fel, potentiella orsaker och rekommendations lösningar

### <a name="error-code-invalidinput"></a>Felkod: InvalidInput.
* **Fel meddelande**: kontrol lera att du har angett rätt information.
* **Möjliga orsaker:** Inaktuella inaktuella ininformation.
* **Rekommendation:** Kontrol lera dina inaktuella ininformation.

### <a name="error-code-firewallornetworkissue"></a>Felkod: FirewallOrNetworkIssue.
* **Fel meddelande**: kontrol lera att den här porten är öppen i brand väggen/servern/NSG och att nätverket är stabilt.
* **Möjliga orsaker:** 
  * Servern öppnar inte den här porten.
  * Nätverks säkerhets gruppen nekade utgående trafik på den här porten
  * Din NVA/Azure Firewall/lokal-brandvägg öppnar inte den här porten.
* **Rekommenderade** 
  * Öppna den här porten på servern.
  * Uppdatera nätverks säkerhets gruppen för att tillåta utgående trafik på den här porten.
  * Öppna den här porten på NVA/Azure Firewall/on-lokal-brandväggen.

### <a name="error-code-misconfigureddnssettings"></a>Felkod: MisconfiguredDnsSettings.
* **Fel meddelande**: om du använder en egen DNS-server i det virtuella nätverket som du Azure-SSIS IR, kontrollerar du att den kan matcha värd namnet.
* **Möjliga orsaker:** 
  *  Problemet med anpassad DNS
  *  Du använder inte ett fullständigt kvalificerat domän namn (FQDN) för ditt privata värdnamn
* **Rekommenderade** 
  *  Åtgärda ditt anpassade DNS-problem för att kontrol lera att det kan matcha värd namnet.
  *  Använd det fullständigt kvalificerade domän namnet (FQDN), t. ex. använder <your_private_server>. contoso.com i stället för <your_private_server>, eftersom Azure-SSIS IR inte automatiskt lägger till ditt eget DNS-suffix.

### <a name="error-code-servernotallowremoteconenction"></a>Felkod: ServerNotAllowRemoteConenction.
* **Fel meddelande**: kontrol lera att servern tillåter fjärr-TCP-anslutningar via den här porten.
* **Möjliga orsaker:** 
  *  Server brand väggen tillåter inte fjärr-TCP-anslutningar.
  *  Servern är inte online.
* **Rekommenderade** 
  *  Tillåt fjärr-TCP-anslutningar i Server brand väggen.
  *  Starta servern.
   
### <a name="error-code-misconfigurednsgsettings"></a>Felkod: MisconfiguredNsgSettings.
* **Fel meddelande**: kontrol lera att NSG för ditt VNet tillåter utgående trafik via den här porten. Om du använder Azure ExpressRoute och eller en UDR måste du kontrol lera att den här porten är öppen på brand väggen/servern.
* **Möjliga orsaker:** 
  *  Nätverks säkerhets gruppen nekade utgående trafik på den här porten.
  *  Din NVA/Azure Firewall/lokal-brandvägg öppnar inte den här porten.
* **Rekommenderade** 
  *  Uppdatera nätverks säkerhets gruppen för att tillåta utgående trafik på den här porten.
  *  Öppna den här porten på NVA/Azure Firewall/on-lokal-brandväggen.

### <a name="error-code-genericissues"></a>Felkod: GenericIssues.
* **Fel meddelande**: det gick inte att testa anslutningen på grund av allmänna problem.
* **Möjliga orsaker:** Test anslutningen påträffade ett allmänt tillfälligt problem.
* **Rekommendation:** Försök att testa anslutningen igen senare. Kontakta Azure Data Factory support-teamet om du försöker igen.


### <a name="error-code-pspingexecutiontimeout"></a>Felkod: PSPingExecutionTimeout.
* **Fel meddelande**: tids gräns för test anslutning, försök igen senare.
* **Möjliga orsaker:** Tids gränsen för test anslutningen uppnåddes.
* **Rekommendation:** Försök att testa anslutningen igen senare. Kontakta Azure Data Factory support-teamet om du försöker igen.

### <a name="error-code-networkinstable"></a>Felkod: NetworkInstable.
* **Fel meddelande**: test anslutningen är oregelbundet klar på grund av instabilitet i nätverket.
* **Möjliga orsaker:** Tillfälligt nätverks problem.
* **Rekommendation:** Kontrol lera om Server-eller brand Väggs nätverket är stabilt.

## <a name="next-steps"></a>Nästa steg

- Distribuera dina paket. Mer information finns i [distribuera ett SSIS-projekt till Azure med SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms).
- Kör dina paket. Mer information finns i [köra SSIS-paket i Azure med SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms).
- Schemalägg dina paket. Mer information finns i [Schemalägga SSIS-paket i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).

