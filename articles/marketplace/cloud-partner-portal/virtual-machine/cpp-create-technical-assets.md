---
title: Skapa tekniska resurser till en virtuell dator för Azure Marketplace
description: Beskriver hur du skapar de tekniska resurserna till en virtuell dator på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 08/20/2018
ms.author: pabutler
ms.openlocfilehash: 6113c10cd152a22bd31e7212d86925b0c2107e58
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938417"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Skapa tekniska resurser till en virtuell dator

Det här avsnittet visar hur du skapar och konfigurerar de tekniska resurserna till en virtuell dator (VM) för Azure Marketplace.  En virtuell dator innehåller två komponenter: lösning virtuell hårddisk (VHD) och valfria associerade datadiskar.  

- *Virtuella hårddiskar (VHD)*, som innehåller operativsystemet och din lösning som du distribuerar i ditt Azure Marketplace-erbjudande. Processen med att förbereda den virtuella Hårddisken som skiljer sig beroende på om det är en Linux-baserade, Windows-baserad eller en anpassad-baserad virtuell dator.
- *Datadiskar* representerar dedikerad, beständig lagring för en virtuell dator. Gör *inte* använder lösningen VHD (till exempel den `C:` enhet) för lagring av beständig information.

En datoravbildning av virtuell innehåller en operativsystemdisk och noll eller flera datadiskar. En VHD krävs per disk. Även tomma datadiskar kräver en virtuell Hårddisk som ska skapas.
Du måste konfigurera VM-OS, VM-storlek, portar som ska öppnas, och upp till 15 anslutna datadiskar.

> [!TIP] 
> Oavsett vilket operativsystem du använder lägger du endast till det minsta antalet datadiskar som SKU n kräver. Kunder kan inte ta bort diskar som är en del av en avbildning vid distributionen, men de kan alltid lägga till diskar under eller efter distributionen. 

> [!IMPORTANT]
> *Ändra inte Diskantalet i en ny Avbildningsversion.* Om du måste konfigurera om datadiskar i bilden, definierar du en ny SKU. Publicera en ny Avbildningsversion med annan disk antal har risken med att bryta ny distribution som baseras på den nya avbildningsversionen i fall av automatisk skalning, automatiska distributioner av lösningar via Azure Resource Manager-mallar och andra scenarier.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Grundläggande teknisk kunskap

Utforma, bygga och testa dessa tillgångar ta tid och kräver teknisk kunskap av både Azure-plattformen och de tekniker som används för att skapa erbjudandet. Förutom att din lösning domän, bör ingenjörsteamet ha kunskaper om följande Microsoft-tekniker: 
-   Grundläggande förståelse för [Azure-tjänster](https://azure.microsoft.com/services/) 
-   Så här [utforma och skapa Azure-program](https://azure.microsoft.com/solutions/architecture/)
-   Kunskaper om [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) och [Azure Networking](https://azure.microsoft.com/services/?filter=networking)
-   Kunskaper om [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Kunskaper om [JSON](https://www.json.org/)


## <a name="suggested-tools"></a>Föreslaget verktyg 

Välj en eller båda av följande skript miljöer för att hantera virtuella hårddiskar och virtuella datorer:
-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Vi rekommenderar dessutom att lägga till följande verktyg i utvecklingsmiljön: 

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Anknytning: [Verktyget Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Anknytning: [Tjärgropar](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Anknytning: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Vi rekommenderar också granska de tillgängliga verktyg i den [Azure-utvecklarverktyg](https://azure.microsoft.com/tools/) sidan och, om du använder Visual Studio i [Visual Studio Marketplace](https://marketplace.visualstudio.com/).


## <a name="next-steps"></a>Nästa steg

I efterföljande artiklar i det här avsnittet vägleder dig igenom steg för att skapa och registrera dessa VM-resurser:

1. [Skapa en virtuell hårddisk i Azure-kompatibel](./cpp-create-vhd.md) förklarar hur du skapar antingen en Linux - eller Windows-baserade virtuella Hårddisk som är kompatibla med Azure.  Den innehåller bästa praxis, till exempel ändra storlek, korrigering och förbereda den virtuella datorn för att ladda upp.

2. [Ansluta till den virtuella datorn](./cpp-connect-vm.md) förklarar hur du ansluter till den nyligen skapade virtuella datorn och loggar in via en fjärranslutning.  Den här artikeln beskriver också hur du stoppa den virtuella datorn att spara på kostnader.

3. [Konfigurera den virtuella datorn](./cpp-configure-vm.md) förklarar hur du väljer rätt VHD-storlek, generalisera bilden, gäller de senaste uppdateringarna (korrigeringar) och schemalägger anpassade konfigurationer.

4. [Distribuera en virtuell dator från en virtuell hårddisk](./cpp-deploy-vm-vhd.md) beskrivs hur du registrerar en virtuell dator från en Azure-distribuerade virtuella Hårddisken.  Visas en lista med de verktyg som krävs och hur du använder dem för att skapa en VM-avbildning för användaren och sedan distribuera den till Azure med hjälp av antingen den [Microsoft Azure-portalen](https://ms.portal.azure.com/) eller PowerShell-skript. 

5. [Certifiera en avbildning av virtuell dator](./cpp-certify-vm.md) förklarar hur du testar och skicka in en VM-avbildning för certifiering för Azure Marketplace. Den förklarar var du kan hämta den *Test Certification Tool för Azure Certified* verktyget och hur du använder det här verktyget för att certifiera din avbildning. 

6. [Hämta SAS-URI](./cpp-get-sas-uri.md) förklarar hur du hittar signatur för delad åtkomst (SAS)-URI för VM-avbildningar.
 
Som en stödjande artikel [gemensamma delade URL: en signatur-åtkomstproblem](./cpp-common-sas-url-issues.md) visas några vanliga problem som kan uppstå med hjälp av SAS URI: er och motsvarande möjliga lösningar.

När du har slutfört de här stegen kan du vara redo att [publicera ditt erbjudande för virtuell dator](./cpp-publish-offer.md) på Azure Marketplace.
