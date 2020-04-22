---
title: Uppgradera från Grundläggande offentlig till offentlig standard – Azure Load Balancer
description: Den här artikeln visar hur du uppgraderar Azure Basic Internal Load Balancer till Standard Public Load Balancer
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: e3eca498e5716ae7c0a03e5e624d618899da8dc8
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770406"
---
# <a name="upgrade-azure-internal-load-balancer---outbound-connection-required"></a>Uppgradera Azure Intern belastningsutjämning – utgående anslutning krävs
[Azure Standard Load Balancer](load-balancer-overview.md) erbjuder en omfattande uppsättning funktioner och hög tillgänglighet via zonredundans. Mer information om belastningsutjämnare SKU finns i [jämförelsetabellen](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus). Eftersom standardintern belastningsutjämnare inte tillhandahåller utgående anslutning tillhandahåller vi en lösning för att skapa en vanlig offentlig belastningsutjämnare i stället.

Det finns fyra steg i en uppgradering:

1. Migrera konfigurationen till Standard Public Load Balancer
2. Lägga till virtuella datorer i backend-pooler med standardvarmtalbelastningsutjämning
3. Ställa in NSG-regler för undernät/virtuella datorer som ska avhållas från/till Internet

Den här artikeln beskriver konfigurationsmigrering. Att lägga till virtuella datorer i backend-pooler kan variera beroende på din specifika miljö. Vissa allmänna rekommendationer på hög nivå [ges](#add-vms-to-backend-pools-of-standard-load-balancer)dock.

## <a name="upgrade-overview"></a>Översikt över uppgradering

Ett Azure PowerShell-skript är tillgängligt som gör följande:

* Skapar en standard SKU Public Load Balancer i resursgruppen och den plats som du anger.
* Kopierar sömlöst konfigurationerna för den grundläggande SKU-interna belastningsutjämnaren till den nyligen skapande standarduppladdningshanteraren för offentlig belastning.
* Skapar en utgående regel som möjliggör egressanslutning.

### <a name="caveatslimitations"></a>Varningar\Begränsningar

* Skriptet stöder intern belastningsutjämningsuppgradering där utgående anslutning krävs. Om utgående anslutning inte krävs för någon av de virtuella datorerna läser du [den här sidan](upgrade-basicInternal-standard.md) för bästa praxis.
* Standardbelastningsutjämnaren har en ny offentlig adress. Det är omöjligt att flytta IP-adresser som är associerade med befintliga grundläggande interna belastningsutjämnare sömlöst till Standard Public Load Balancer eftersom de har olika SKU: er.
* Om standardbelastningsutjämkaren skapas i en annan region kan du inte associera de virtuella datorerna som finns i den gamla regionen till den nyligen skapade standardbelastningsutjämningsapparaten. Du kan komma runt den här begränsningen och se till att skapa en ny virtuell dator i den nya regionen.
* Om din belastningsutjämnare inte har någon ip-konfiguration eller serverdelspool i frontend kommer du troligen att orsaka ett fel som kör skriptet.  Se till att de inte är tomma.

## <a name="download-the-script"></a>Ladda ner skriptet

Hämta migreringsskriptet från [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureLBUpgrade/2.0).
## <a name="use-the-script"></a>Använda skriptet

Det finns två alternativ för dig beroende på din lokala PowerShell-miljö inställning och inställningar:

* Om du inte har Azure Az-modulerna installerade, eller inte har något emot att avinstallera Azure `Install-Script` Az-modulerna, är det bästa alternativet att använda alternativet för att köra skriptet.
* Om du behöver behålla Azure Az-modulerna är det bäst att ladda ned skriptet och köra det direkt.

Ta reda på om du har Azure `Get-InstalledModule -Name az`Az-modulerna installerade kör du . Om du inte ser några installerade Az-moduler kan `Install-Script` du använda metoden.

### <a name="install-using-the-install-script-method"></a>Installera med metoden Install-Script

Om du vill använda det här alternativet får du inte ha Azure Az-modulerna installerade på datorn. Om de är installerade visas ett fel i följande kommando. Du kan antingen avinstallera Azure Az-modulerna eller använda det andra alternativet för att hämta skriptet manuellt och köra det.
  
Kör skriptet med följande kommando:

`Install-Script -Name AzurePublicLBUpgrade`

Det här kommandot installerar också de Az-moduler som krävs.  

### <a name="install-using-the-script-directly"></a>Installera med skriptet direkt

Om du har några Azure Az-moduler installerade och inte kan avinstallera dem (eller inte vill avinstallera dem) kan du hämta skriptet manuellt med hjälp av fliken **Manuell hämtning** i länken för hämtning av skript. Skriptet hämtas som en rå nupkg-fil. Information om hur du installerar skriptet från den här nupkg-filen finns i [Hämta manuellt paket](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Kör skriptet så här:

1. Används `Connect-AzAccount` för att ansluta till Azure.

1. Används `Import-Module Az` för att importera Az-modulerna.

1. Undersök de parametrar som krävs:

   * **oldRgName: [String]: Obligatoriskt** – Det här är resursgruppen för din befintliga grundläggande belastningsutjämnare som du vill uppgradera. Om du vill hitta det här strängvärdet navigerar du till Azure-portalen, väljer din grundläggande belastningsutjämnarekälla och klickar på **Översikt** för belastningsutjämnaren. Resursgruppen finns på den sidan.
   * **oldLBName: [String]: Obligatoriskt** – Det här är namnet på din befintliga Basic Balancer som du vill uppgradera. 
   * **newrgName: [String]: Obligatoriskt** – Det här är den resursgrupp där standardbelastningsutjämningen ska skapas. Det kan vara en ny resursgrupp eller en befintlig. Om du väljer en befintlig resursgrupp bör du tänka på att namnet på belastningsutjämnaren måste vara unikt inom resursgruppen. 
   * **newlocation: [String]: Obligatoriskt** – Det här är den plats där standardbelastningsutjämnaren ska skapas. Vi rekommenderar att du ärver samma plats för den valda grundläggande belastningsutjämningsutjämningen till standardbelastningsutjämningsutjämningen för bättre association med andra befintliga resurser.
   * **newLBName: [String]: Obligatoriskt** – Det här är namnet på standardbelastningsutjämningen som ska skapas.
1. Kör skriptet med lämpliga parametrar. Det kan ta fem till sju minuter att avsluta.

    **Exempel**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newrgName "test_userInput3_rg" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Lägga till virtuella datorer i backend-pooler med standardbelastningsutjämning

Kontrollera först att skriptet har skapat en ny standardbevämningsutjämning med exakt konfiguration som har migrerats från din grundläggande offentliga belastningsutjämnare. Du kan verifiera detta från Azure-portalen.

Var noga med att skicka en liten mängd trafik genom standardbelastningsutjämningsutfärdaren som ett manuellt test.
  
Här är några scenarier med hur du lägger till virtuella datorer i serverdapooler i den nyligen skapade standardbelastningsutjämningsapparaten kan konfigureras och våra rekommendationer för var och en:

* **Flytta befintliga virtuella datorer från backend-pooler med gamla grundläggande offentliga belastningsutjämnar till backend-pooler av nyskapade standardutvecklare**.
    1. Logga in på [Azure-portalen](https://portal.azure.com) för att genomföra alla uppgifter i den här snabbstarten.
 
    1. Välj **Alla resurser** på den vänstra menyn och välj sedan den nyligen skapade **standardbelastningsutjämningsaren** i resurslistan.
   
    1. Under **Inställningar**väljer du **Backend-pooler**.
   
    1. Välj den backend-pool som matchar backend-poolen för basic load balancer, välj följande värde: 
      - **Virtuell dator:** Släpp ned och välj de virtuella datorerna från den matchande servergruppspoolen i den grundläggande belastningsutjämnaren.
    1. Välj **Spara**.
    >[!NOTE]
    >För virtuella datorer som har offentliga IP-adresser måste du först skapa standard-IP-adresser där samma IP-adress inte är garanterad. Ta bort virtuella datorer från grundläggande IP-adresser och associera dem med de nyligen skapade standard-IP-adresserna. Sedan kan du följa instruktionerna för att lägga till virtuella datorer i backend-poolen för standardbelastningsutjämnaren. 

* **Skapa nya virtuella datorer som du vill lägga till i backend-poolerna i den nyligen skapade standardbelastningsutjämningen**.
    * Fler instruktioner om hur du skapar virtuell dator och associerar den med standardbelastningsutjämnaren finns [här](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines).

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Skapa en utgående regel för utgående anslutning

Följ [instruktionerna](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-portal#create-outbound-rule-configuration) för att skapa en utgående regel så att du kan
* Definiera utgående NAT från grunden.
* Skala och justera beteendet för befintlig utgående NAT.

### <a name="create-nsg-rules-for-vms-which-to-refrain-communication-from-or-to-the-internet"></a>Skapa NSG-regler för virtuella datorer som ska avhålla sig från eller till Internet
Om du vill avstå från internettrafik från att nå till dina virtuella datorer kan du skapa en [NSG-regel](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) i nätverksgränssnittet för de virtuella datorerna.

## <a name="common-questions"></a>Vanliga frågor

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Finns det några begränsningar med Azure PowerShell-skriptet för att migrera konfigurationen från v1 till v2?

Ja. Se [varningar/begränsningar](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Växlar Azure PowerShell-skriptet också över trafiken från min grundläggande belastningsutjämnare till den nyligen skapade standardbelastningsutjämningen?

Nej. Azure PowerShell-skriptet migrerar bara konfigurationen. Faktisk trafikmigrering är ditt ansvar och i din kontroll.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Jag stötte på några problem med att använda detta skript. Hur kan jag få hjälp?
  
Du kan skicka slbupgradesupport@microsoft.comett e-postmeddelande till , öppna ett supportärende med Azure Support eller göra båda.

## <a name="next-steps"></a>Nästa steg

[Läs mer om standardbelastningsjämningsapparaten](load-balancer-overview.md)
