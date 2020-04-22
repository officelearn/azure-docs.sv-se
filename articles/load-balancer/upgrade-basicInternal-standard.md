---
title: Uppgradera från grundläggande intern till standard intern - Azure Load Balancer
description: Den här artikeln visar hur du uppgraderar Azure Internal Load Balancer från Basic SKU till Standard SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: 239dc0f3133a5adf59a23d333131c91d3a655597
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770377"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Uppgradera Azure Intern belastningsutjämning- ingen utgående anslutning krävs
[Azure Standard Load Balancer](load-balancer-overview.md) erbjuder en omfattande uppsättning funktioner och hög tillgänglighet via zonredundans. Mer information om belastningsutjämnare SKU finns i [jämförelsetabellen](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus).

Den här artikeln introducerar ett PowerShell-skript som skapar en standardbelastningsjämningsfaktor med samma konfiguration som den grundläggande belastningsutjämnaren tillsammans med migrerande trafik från grundläggande belastningsutjämnare till standardbelastningsutjämning.

## <a name="upgrade-overview"></a>Översikt över uppgradering

Ett Azure PowerShell-skript är tillgängligt som gör följande:

* Skapar en intern SKU-belastningsutjämning för standard på den plats som du anger. Observera att ingen [utgående anslutning](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) inte kommer att tillhandahållas av standardintern belastningsutjämnare.
* Kopierar sömlöst konfigurationerna för den grundläggande SKU-belastningsutjämnaren till den nyligen skapade standardbelastningsutjämningsapparaten.
* Flytta de privata IPs-adresserna sömlöst från grundläggande belastningsutjämning till den nyligen skapade standardbelastningsutjämningsapparaten.
* Flytta de virtuella datorerna sömlöst från backend-poolen i den grundläggande belastningsutjämnaren till backend-poolen på standardbelastningsutjämningsapparaten

### <a name="caveatslimitations"></a>Varningar\Begränsningar

* Skriptet stöder endast intern belastningsutjämningsuppgradering där ingen utgående anslutning krävs. Om du behöver [utgående anslutning](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) för några av dina virtuella datorer läser du den här [sidan](upgrade-InternalBasic-To-PublicStandard.md) för instruktioner. 
* Om standardbelastningsutjämkaren skapas i en annan region kan du inte associera de virtuella datorerna som finns i den gamla regionen till den nyligen skapade standardbelastningsutjämningsapparaten. Du kan komma runt den här begränsningen och se till att skapa en ny virtuell dator i den nya regionen.
* Om din belastningsutjämnare inte har någon ip-konfiguration eller serverdelspool i frontend kommer du troligen att orsaka ett fel som kör skriptet. Se till att de inte är tomma.

## <a name="download-the-script"></a>Ladda ner skriptet

Hämta migreringsskriptet från [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureILBUpgrade/2.0).
## <a name="use-the-script"></a>Använda skriptet

Det finns två alternativ för dig beroende på din lokala PowerShell-miljö inställning och inställningar:

* Om du inte har Azure Az-modulerna installerade, eller inte har något emot att avinstallera Azure `Install-Script` Az-modulerna, är det bästa alternativet att använda alternativet för att köra skriptet.
* Om du behöver behålla Azure Az-modulerna är det bäst att ladda ned skriptet och köra det direkt.

Ta reda på om du har Azure `Get-InstalledModule -Name az`Az-modulerna installerade kör du . Om du inte ser några installerade Az-moduler kan `Install-Script` du använda metoden.

### <a name="install-using-the-install-script-method"></a>Installera med metoden Install-Script

Om du vill använda det här alternativet får du inte ha Azure Az-modulerna installerade på datorn. Om de är installerade visas ett fel i följande kommando. Du kan antingen avinstallera Azure Az-modulerna eller använda det andra alternativet för att hämta skriptet manuellt och köra det.
  
Kör skriptet med följande kommando:

`Install-Script -Name AzureILBUpgrade`

Det här kommandot installerar också de Az-moduler som krävs.  

### <a name="install-using-the-script-directly"></a>Installera med skriptet direkt

Om du har några Azure Az-moduler installerade och inte kan avinstallera dem (eller inte vill avinstallera dem) kan du hämta skriptet manuellt med hjälp av fliken **Manuell hämtning** i länken för hämtning av skript. Skriptet hämtas som en rå nupkg-fil. Information om hur du installerar skriptet från den här nupkg-filen finns i [Hämta manuellt paket](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Kör skriptet så här:

1. Används `Connect-AzAccount` för att ansluta till Azure.

1. Används `Import-Module Az` för att importera Az-modulerna.

1. Undersök de parametrar som krävs:

   * **rgName: [String]: Obligatoriskt** – Det här är resursgruppen för din befintliga grundläggande belastningsutjämnare och ny standardbelastningsutjämning. Om du vill hitta det här strängvärdet navigerar du till Azure-portalen, väljer din grundläggande belastningsutjämnarekälla och klickar på **Översikt** för belastningsutjämnaren. Resursgruppen finns på den sidan.
   * **oldLBName: [String]: Obligatoriskt** – Det här är namnet på din befintliga Basic Balancer som du vill uppgradera. 
   * **newlocation: [String]: Obligatoriskt** – Det här är den plats där standardbelastningsutjämnaren ska skapas. Vi rekommenderar att du ärver samma plats för den valda grundläggande belastningsutjämningsutjämningen till standardbelastningsutjämningstagaren för bättre association med andra befintliga resurser.
   * **newLBName: [String]: Obligatoriskt** – Det här är namnet på standardbelastningsutjämningen som ska skapas.
1. Kör skriptet med lämpliga parametrar. Det kan ta fem till sju minuter att avsluta.

    **Exempel**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>Vanliga frågor

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Finns det några begränsningar med Azure PowerShell-skriptet för att migrera konfigurationen från v1 till v2?

Ja. Se [varningar/begränsningar](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Växlar Azure PowerShell-skriptet också över trafiken från min grundläggande belastningsutjämnare till den nyligen skapade standardbelastningsutjämningen?

Ja det migrerar trafik. Om du vill migrera trafik personligen använder du [det här skriptet](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) som inte flyttar virtuella datorer åt dig.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Jag stötte på några problem med att använda detta skript. Hur kan jag få hjälp?
  
Du kan skicka slbupgradesupport@microsoft.comett e-postmeddelande till , öppna ett supportärende med Azure Support eller göra båda.

## <a name="next-steps"></a>Nästa steg

[Läs mer om standardbelastningsjämningsapparaten](load-balancer-overview.md)
