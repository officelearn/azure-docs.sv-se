---
title: Skapa tekniska till gångar för ett erbjudande för virtuella datorer för Azure Marketplace
description: Förklarar hur du skapar tekniska till gångar för ett erbjudande för virtuella datorer på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 08/20/2018
ms.author: pabutler
ms.openlocfilehash: c1ef00f846dfad76629b0603ab79fba17249417c
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "71224527"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Skapa tekniska till gångar för ett erbjudande för virtuell dator

Det här avsnittet beskriver hur du skapar och konfigurerar tekniska till gångar för ett erbjudande för virtuella datorer (VM) för Azure Marketplace.  En virtuell dator innehåller två komponenter: den virtuella hård disken för lösningen (VHD) och valfria associerade data diskar.  

- *Virtuella hård diskar (VHD: er)* , som innehåller operativ systemet och din lösning, som du ska distribuera med ditt Azure Marketplace-erbjudande. Processen för att förbereda den virtuella hård disken varierar beroende på om det är en Linux-baserad, Windows-baserad eller en anpassad virtuell dator.
- *Data diskar* representerar dedikerad, beständig lagring för en virtuell dator. Använd *inte* den virtuella hård disk lösningen (till exempel `C:` enheten) för att lagra beständig information.

En avbildning av en virtuell dator innehåller en operativ system disk och noll eller flera data diskar. En virtuell hård disk krävs per disk. Även tomma data diskar kräver att en virtuell hård disk skapas.
Du måste konfigurera VM-OS, storleken på den virtuella datorn, portar som ska öppnas och upp till 15 anslutna data diskar.

> [!TIP] 
> Oavsett vilket operativsystem du använder lägger du endast till det minsta antalet datadiskar som SKU:n kräver. Kunder kan inte ta bort diskar som ingår i en avbildning vid tidpunkten för distributionen, men de kan alltid lägga till diskar under eller efter distributionen. 

> [!IMPORTANT]
> *Ändra inte antalet diskar i en ny avbildnings version.* Om du måste konfigurera om data diskarna i avbildningen definierar du en ny SKU. Om du publicerar en ny avbildnings version med olika disk antal får du möjlighet att dela upp nya distributioner baserat på den nya avbildnings versionen i händelse av automatisk skalning, automatisk distribution av lösningar via Azure Resource Manager mallar och andra scenarier.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Grundläggande teknisk kunskap

Att utforma, skapa och testa dessa till gångar tar tid och kräver teknisk kunskap om både Azure-plattformen och teknikerna som används för att skapa erbjudandet. Förutom din lösnings domän bör ditt tekniska team ha kunskap om följande Microsoft-tekniker: 
-   Grundläggande förståelse för [Azure-tjänster](https://azure.microsoft.com/services/) 
-   Så här [utformar och skapar du Azure-program](https://azure.microsoft.com/solutions/architecture/)
-   Arbeta med kunskaper om [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) och [Azure-nätverk](https://azure.microsoft.com/services/?filter=networking)
-   Arbeta med [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Arbeta med kunskaper om [JSON](https://www.json.org/)


## <a name="suggested-tools"></a>Rekommenderade verktyg 

Välj en eller båda av följande skript miljöer för att hantera virtuella hård diskar och virtuella datorer:
-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Dessutom rekommenderar vi att du lägger till följande verktyg i utvecklings miljön: 

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Utöka [Azure Resource Manager verktyg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Utöka [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Utöka [Prettify-JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Vi rekommenderar också att du går igenom de tillgängliga verktygen på [Azure utvecklarverktyg](https://azure.microsoft.com/tools/) -sidan och, om du använder Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).


## <a name="next-steps"></a>Nästa steg

Följande artiklar i det här avsnittet beskriver steg för steg hur du skapar och registrerar dessa VM-tillgångar:

1. [Skapa en Azure-kompatibel virtuell hård disk](./cpp-create-vhd.md) förklarar hur du skapar en Linux-eller Windows-baserad virtuell hård disk som är kompatibel med Azure.  Den innehåller metod tips, till exempel storlek, uppdatering och förberedelse av den virtuella dator som ska laddas upp.

2. [Anslut till den virtuella datorn](./cpp-connect-vm.md) och lär dig hur du fjärransluter till den virtuella datorn som du skapade och logga in på den.  Den här artikeln beskriver också hur du stoppar den virtuella datorn för att spara användnings kostnader.

3. [Konfigurera den virtuella datorn](./cpp-configure-vm.md) förklarar hur du väljer rätt VHD-storlek, generaliserar avbildningen, tillämpar de senaste uppdateringarna (patch) och schemalägger anpassade konfigurationer.

4. Om du [distribuerar en virtuell dator från en virtuell hård disk](./cpp-deploy-vm-vhd.md) förklaras hur du registrerar en virtuell dator från en Azure-distribuerad virtuell hård disk.  Den visar en lista över de verktyg som krävs och hur du använder dem för att skapa en användar avbildning av en virtuell dator och sedan distribuera den till Azure med hjälp av antingen [Microsoft Azure-portalen](https://ms.portal.azure.com/) -eller PowerShell-skript. 

5. [Certifiera en avbildning av en virtuell dator](./cpp-certify-vm.md) förklarar hur du testar och skickar en VM-avbildning för Azure Marketplace-certifiering. Den förklarar var du får verktyget *för certifierings test för Azure Certified* Tool och hur du använder det här verktyget för att certifiera din avbildning av virtuella datorer. 

6. [Hämta SAS URI](./cpp-get-sas-uri.md) förklarar hur du hämtar URL: en för signaturen för delad åtkomst (SAS) för dina VM-avbildningar.
 
Som en Support artikel innehåller [vanliga URL: er för signatur för delad åtkomst](./cpp-common-sas-url-issues.md) en lista med några vanliga problem som kan uppstå med SAS-URI: er och motsvarande möjliga lösningar.

När du har slutfört alla dessa steg är du redo att [publicera ditt virtuella dator erbjudande](./cpp-publish-offer.md) på Azure Marketplace.
