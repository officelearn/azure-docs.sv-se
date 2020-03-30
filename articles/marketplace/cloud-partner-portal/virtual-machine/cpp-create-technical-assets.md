---
title: Skapa tekniska resurser för ett erbjudande om virtuella datorer för Azure Marketplace
description: Förklarar hur du skapar de tekniska tillgångarna för ett erbjudande om virtuella datorer på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: dsindona
ms.openlocfilehash: 57f56a341cfc3db6a5f0664503809e6ab6cf3d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278032"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Skapa tekniska resurser för ett erbjudande om virtuell dator

I det här avsnittet får du hjälp med att skapa och konfigurera de tekniska tillgångarna för ett virtuellt datorerbjudande (VM) för Azure Marketplace.  En virtuell dator innehåller två komponenter: lösningens virtuella hårddisk (VHD) och associerade datadiskar som tillval.  

- *Virtuella hårddiskar (VHDs)*, som innehåller operativsystemet och din lösning, som du kommer att distribuera med ditt Azure Marketplace-erbjudande. Processen att förbereda den virtuella hårddisken skiljer sig åt beroende på om det är en Linux-baserad, Windows-baserad eller en anpassad dator.
- *Datadiskar* representerar dedikerad, beständig lagring för en virtuell dator. Använd *inte* lösningens VIRTUELLAD (till `C:` exempel enheten) för att lagra beständig information.

En VM-avbildning innehåller en operativsystemdisk och noll eller fler datadiskar. En virtuell hårddisk behövs per disk. Även tomma datadiskar kräver att en virtuell hårddisk skapas.
Du måste konfigurera VM-operativsystemet, vm-storleken, portarna för att öppna och upp till 15 anslutna datadiskar.

> [!TIP] 
> Oavsett vilket operativsystem du använder lägger du bara till det minsta antalet datadiskar som behövs av SKU. Kunder kan inte ta bort diskar som ingår i en avbildning vid tidpunkten för distributionen, men de kan alltid lägga till diskar under eller efter distributionen. 

> [!IMPORTANT]
> *Ändra inte diskantalet i en ny avbildningsversion.* Om du måste konfigurera om datadiskar i avbildningen definierar du en ny SKU. Publicering av en ny avbildningsversion med olika diskantal kommer att ha potential att bryta ny distribution baserat på den nya avbildningsversionen vid automatisk skalning, automatisk distribution av lösningar via Azure Resource Manager-mallar och andra scenarier.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Grundläggande teknisk kunskap

Designa, bygga och testa dessa tillgångar tar tid och kräver teknisk kunskap om både Azure-plattformen och de tekniker som används för att skapa erbjudandet. Förutom din lösningsdomän bör ditt ingenjörsteam ha kunskap om följande Microsoft-tekniker: 
-   Grundläggande förståelse för [Azure Services](https://azure.microsoft.com/services/) 
-   Så här [utformar och arkitekt azure-program utformas](https://azure.microsoft.com/solutions/architecture/)
-   Kunskap om [virtuella Azure-datorer,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage) och Azure [Networking](https://azure.microsoft.com/services/?filter=networking)
-   Kunskap om [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Arbetskunskap om [JSON](https://www.json.org/)


## <a name="suggested-tools"></a>Föreslagna verktyg 

Välj en eller båda av följande skriptmiljöer för att hantera virtuella hårddiskar och virtuella datorer:
-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Dessutom rekommenderar vi att du lägger till följande verktyg i utvecklingsmiljön: 

-   [Utforskaren för Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio-kod](https://code.visualstudio.com/)
    *   Tillägg: [Verktyg för Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Förlängning: [Försköna](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Förlängning: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Vi föreslår också att du granskar de tillgängliga verktygen på sidan [Azure Developer Tools](https://azure.microsoft.com/tools/) och, om du använder Visual Studio, Visual Studio [Marketplace](https://marketplace.visualstudio.com/).


## <a name="next-steps"></a>Nästa steg

De efterföljande artiklarna i det här avsnittet går igenom stegen för att skapa och registrera dessa vm-tillgångar:

1. [Skapa en Azure-kompatibel virtuell hårddisk](./cpp-create-vhd.md) förklarar hur du skapar antingen en Linux- eller Windows-baserad virtuell hårddisk som är kompatibel med Azure.  Den innehåller metodtips, till exempel storleksändring, korrigering och förberedelse av den virtuella datorn för uppladdning.

2. [Anslut till den virtuella datorn](./cpp-connect-vm.md) förklarar hur du fjärransluter till den nyskapade virtuella datorn och loggar in på den.  I den här artikeln beskrivs också hur du stoppar den virtuella datorn för att spara på användningskostnader.

3. [Konfigurera den virtuella datorn](./cpp-configure-vm.md) förklarar hur du väljer rätt virtuell hårddiskstorlek, generaliserar avbildningen, tillämpar de senaste uppdateringarna (korrigeringsfiler) och schemalägger anpassade konfigurationer.

4. [Distribuera en virtuell dator från en virtuell hårddisk](./cpp-deploy-vm-vhd.md) förklarar hur du registrerar en virtuell dator från en Azure-distribuerad VIRTUELLD.  Den listar de verktyg som krävs och hur du använder dem för att skapa en virtuell avbildning av användare och distribuera den sedan till Azure med antingen [Microsoft Azure-portalen](https://ms.portal.azure.com/) eller PowerShell-skript. 

5. [Certifiera en avbildning för virtuella datorer](./cpp-certify-vm.md) förklarar hur du testar och skickar en VM-avbildning för Azure Marketplace-certifiering. Det förklarar var du kan hämta *verktyget Certifieringstest för Azure Certified* och hur du använder det här verktyget för att certifiera din VM-avbildning. 

6. [Få SAS URI](./cpp-get-sas-uri.md) förklarar hur du får sas-uri (Shared Access Signature) för vm-avbildningar.
 
Som en stödjande artikel visar [vanliga url-problem med delad åtkomst](./cpp-common-sas-url-issues.md) en lista över några vanliga problem som kan uppstå med SAS-urier och motsvarande möjliga lösningar.

När du har slutfört alla dessa steg är du redo att [publicera ditt VM-erbjudande](./cpp-publish-offer.md) på Azure Marketplace.
