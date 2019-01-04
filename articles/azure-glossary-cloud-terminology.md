---
title: Azure-ordlistan - Azure-ordlistan | Microsoft Docs
description: Använd Azure-ordlistan för att förstå terminologin i molnet på Azure-plattformen. Den här korta Azure ordlista innehåller definitioner av vanliga termer som molnet för Azure.
keywords: Azure-ordlistan, cloud terminologi, Azure-ordlistan, termer som är, villkor för molnet
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: ''
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: monicar
ms.openlocfilehash: 748a9c0c583d4e1af9626527a440b19900898b87
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53600316"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Ordlista för Microsoft Azure: En ordlista med termer i molnet på Azure-plattformen

Ordlista för Microsoft Azure är en kort ordlista med molnet terminologi för Azure-plattformen. Se även:

* [Microsoft Azure och Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) -definitioner av Azure-tjänster och deras AWS-motsvarigheter.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Termer inom molnbaserad databehandling](https://azure.microsoft.com/overview/cloud-computing-dictionary/) – Allmänt branschvillkor för molnet.

## <a name="account"></a>konto
Ett konto som används för att komma åt och hantera en Azure-prenumeration. Det är ofta kallas ett Azure-konto även om ett konto kan vara något av följande: ett befintligt arbets, din skola eller personliga Microsoft-konto, eller en Office 365 användarnamn och lösenord. Du kan också skapa ett konto för att hantera en Azure-prenumeration när du registrerar dig för den [kostnadsfri utvärderingsversion](https://azure.microsoft.com).  
Se [registrera dig för en Azure-prenumeration med ditt Office 365-konto](billing/billing-use-existing-office-365-account-azure-subscription.md) och [konton som du kan använda för att logga in](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>API-app
Ett annat namn för [App Service-app](#app-service-app).

## <a name="app-service-app"></a>App Service-app
Beräkningsresurserna som [Azure App Service](app-service/overview.md) tillhandahåller för hantering av en webbplatsen eller programmet, webb-API, eller [mobilappserverdel](app-service-mobile/app-service-mobile-value-prop.md). App Service-appar kallas även att *Apptjänster*, *webbappar*, *API apps*, och *mobilappar*.

## <a name="availability-set"></a>Tillgänglighetsuppsättning
En samling med virtuella datorer som hanteras tillsammans att tillhandahålla program redundans och tillförlitlighet. Användning av en tillgänglighetsuppsättning säkerställer att minst en virtuell dator under en planerad eller oplanerad underhållshändelse är tillgänglig.  
Se [hantera tillgängligheten för Windows-datorer](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och [hantera tillgängligheten för virtuella Linux-datorer](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="classic-model"></a>Azures klassiska distributionsmodell
Någon av de två [distributionsmodeller](resource-manager-deployment-model.md) används för att distribuera resurser i Azure (den nya modellen är Azure Resource Manager). Vissa Azure-tjänster stöder endast Resource Manager-distributionsmodellen, vissa stöder den klassiska distributionsmodellen och vissa stöder både. I dokumentationen för varje Azure-tjänst anger vilka modell(er) som de stöder.

## <a name="cli"></a>Azure-kommandoradsgränssnittet (CLI)
Ett kommandoradsgränssnitt som kan användas för att hantera Azure-tjänster från Windows, macOS och Linux.  Vissa tjänster eller service-funktioner kan hanteras endast via PowerShell eller CLI. Se [Azure CLI](/cli/azure)

## <a name="powershell"></a>Azure PowerShell
Ett kommandoradsgränssnitt för att hantera Azure-tjänster via en kommandorad från Windows-datorer. Vissa tjänster eller service-funktioner kan hanteras endast via PowerShell eller CLI.
Se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview)

## <a name="arm-model"></a>Azure Resource Manager-distributionsmodellen
Någon av de två [distributionsmodeller](resource-manager-deployment-model.md) används för att distribuera resurser i Microsoft Azure (den andra är den klassiska distributionsmodellen). Vissa Azure-tjänster stöder endast Resource Manager-distributionsmodellen, vissa stöder den klassiska distributionsmodellen och vissa stöder både. I dokumentationen för varje Azure-tjänst anger vilka modell(er) som de stöder.

## <a name="fault-domain"></a>Feldomän
Samling av virtuella datorer i en tillgänglighetsuppsättning som eventuellt kan misslyckas på samma gång. Ett exempel är en grupp av datorer i ett rack som delar samma strömkälla och nätverksswitch. I Azure avgränsas automatiskt de virtuella datorerna i en tillgänglighetsuppsättning via flera feldomäner.  
Se [hantera tillgängligheten för Windows-datorer](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eller [hantera tillgängligheten för virtuella Linux-datorer](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>GEO-replikering
En definierad gräns för dataplacering som vanligtvis innehåller två eller fler regioner. Gränserna kan vara inom eller utanför nationella kanter och påverkas av skatt förordning. Varje geo har minst en region. Exempel på geografiska områden är Asien/Stillahavsområdet och Japan. Kallas även *geografi*.  
Se [Azure-regioner](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>GEO-replikering
Processen för att automatiskt replikera innehåll, till exempel blobbar, tabeller och köer i ett regionalt par.  
Se [aktiv Geo-replikering för Azure SQL Database](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image
En fil som innehåller operativsystemet och programkonfiguration som kan användas för att skapa valfritt antal virtuella datorer. Det finns två typer av avbildningar i Azure: VM-avbildning och OS-avbildning. En VM-avbildning som innehåller ett operativsystem och alla diskar som är anslutna till en virtuell dator när avbildningen har skapats. En operativsystemavbildning innehåller endast en generaliserad operativsystem med diskkonfigurationer inga data.  
Se [navigera och välja Windows-avbildningar i Azure med PowerShell eller CLI](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>Gränser
Hur många resurser som kan skapas eller benchmark för prestanda som kan uppnås. Gränser är vanligtvis kopplade prenumerationer, tjänster och erbjudanden.  
Se [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](azure-subscription-service-limits.md)

## <a name="load-balancer"></a>Belastningsutjämnare
En resurs som distribuerar inkommande trafik mellan datorer i ett nätverk. I Azure distribuerar en belastningsutjämnare trafiken till virtuella datorer som har definierats i en belastningsutjämnare. En [belastningsutjämnare](load-balancer/load-balancer-overview.md) kan vara webbservergrupper på internet eller det kan vara intern.  

## <a name="mobile-app"></a>mobilapp
Ett annat namn för [Apptjänstapp](#app-service-app).

## <a name="offer"></a>erbjudande
Den prissättning, krediter och tillhörande villkor som gäller för en Azure-prenumeration.  
Se den [detaljsida i Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
Säker webbportalen används för att distribuera och hantera Azure-tjänster.

## <a name="region"></a>region
Ett område inom ett geografiskt område som inte mellan nationella kantlinjer och innehåller ett eller flera datacenter. Priser, regionala tjänster och typer av erbjudanden blir tillgängliga på regionsnivån. En region är vanligtvis kopplad till en annan region, vilket kan vara upp till flera hundra mil bort. Regionala par kan användas som en mekanism för katastrofåterställning och scenarier med hög tillgänglighet. Kallas även *plats*.  
Se [Azure-regioner](best-practices-availability-paired-regions.md)

## <a name="resource"></a>resurs
Ett objekt som är en del av din Azure-lösning. Azure-tjänster kan du distribuera olika typer av resurser, till exempel databaser eller virtuella datorer.   
Se [översikt över Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="resource-group"></a>Resursgrupp
En behållare i Resource Manager som innehåller relaterade resurser för ett program. Resursgruppen kan innehålla alla resurser för ett program eller bara de resurser som är logiskt grupperade. Du kan bestämma hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation.  
Se [översikt över Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="arm-template"></a>Resource Manager-mall
En JSON-fil som deklarativt definierar en eller flera Azure-resurser och som definierar beroenden mellan distribuerade resurser. Mallen kan användas för att distribuera resurserna på ett konsekvent sätt och upprepade gånger.  
Se [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>Provider för nätverksresurser
En tjänst som tillhandahåller resurserna som du kan distribuera och hantera via Resource Manager. Varje resursprovider tillhandahåller åtgärder som hjälper dig att arbeta med de resurser du distribuerar. Resursproviders kan nås via Azure portal, Azure PowerShell och flera programmeringsspråk SDK: er.  
Se [översikt över Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="role"></a>roll
Ett sätt för att styra åtkomst som kan tilldelas till användare, grupper och tjänster. Roller kan utföra åtgärder som att skapa, hantera, och Läs om Azure-resurser.  
Se [RBAC: Inbyggda roller](role-based-access-control/built-in-roles.md)

## <a name="sla"></a>serviceavtal (SLA)
Det avtal som beskriver Microsofts åtaganden gällande drifttid och anslutning. Varje Azure-tjänst har en specifik SLA.  
Se [serviceavtal](https://azure.microsoft.com/support/legal/sla/)

## <a name="sas"></a>signatur för delad åtkomst (SAS)
En signatur som gör det möjligt att bevilja begränsad åtkomst till en resurs, utan att exponera din kontonyckel. Till exempel [Azure Storage använder SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) att bevilja klientåtkomst till objekt, till exempel BLOB-objekt. [IoT Hub använder SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) att bevilja behörighet att skicka telemetri för enheter.

## <a name="storage-account"></a>storage account
Ett konto som ger dig åtkomst till Azure Blob, kö, tabell och fil tjänsterna i Azure Storage. Lagringskontonamnet definierar det unika namnområdet för Azure Storage-dataobjekt.  
Se [om Azure storage-konton](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>prenumeration
Kundens avtal med Microsoft som gör det möjligt för dem att få Azure-tjänster. Prenumerationspriser och tillhörande villkor regleras av erbjudandet som valts för prenumerationen.
Se [prenumerationsavtalet för Microsoft Online](https://azure.microsoft.com/support/legal/subscription-agreement/) och [hur Azure-prenumerationer är associerade med Azure Active Directory](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>tagg
En indexering term som gör att du kan kategorisera resurser utifrån dina hanterings- eller faktureringskrav. När du har en komplex samling resurser kan använda du taggar för att visualisera dessa tillgångar på ett sätt som passar bäst. Du kan till exempel tagga resurser som har en liknande roll i organisationen eller som tillhör samma avdelning.  
Se [med taggar för att organisera Azure-resurser](resource-group-using-tags.md)

## <a name="update-domain"></a>Uppdatera domän
Samling av virtuella datorer i en tillgänglighetsuppsättning som har uppdaterats på samma gång. Virtuella datorer i samma uppdateringsdomän startas om tillsammans under planerat underhåll. Azure startar aldrig om mer än en uppdateringsdomän i taget. Kallas även en uppgraderingsdomän.  
Se [hantera tillgängligheten för Windows-datorer](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och [hantera tillgängligheten för virtuella Linux-datorer](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vm"></a>Virtuell dator
Programvara-implementeringen av en fysisk dator som kör ett operativsystem. Flera virtuella datorer kan köras samtidigt på samma maskinvara. I Azure är virtuella datorer tillgängliga i olika storlekar.  
Se [dokumentation om Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="vm-extension"></a>tillägg för virtuell dator
En resurs som implementerar beteenden eller funktioner som antingen underlättar för andra program arbets- eller ge möjlighet att interagera med en dator som körs. Du kan exempelvis använda VM Access-tillägg kan återställa eller ändra värden för fjärråtkomst på en Azure-dator.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Se [om virtuella datorer, tillägg och funktioner (Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eller [om virtuella datorer, tillägg och funktioner (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vnet"></a>Virtuellt nätverk
Ett nätverk som tillhandahåller anslutningen mellan dina Azure-resurser som är isolerad från alla andra Azure-klienter. En [Azure VPN Gateway](vpn-gateway/vpn-gateway-about-vpngateways.md) kan du upprätta anslutningar mellan virtuella nätverk och [mellan ett virtuellt nätverk och ett lokalt nätverk](vpn-gateway/vpn-gateway-plan-design.md). Du kan helt styra IP-Adressblock, DNS-inställningar, säkerhetsprinciper och routningstabeller i ett virtuellt nätverk.  
Se [översikt över virtuella nätverk](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Webbapp
Ett annat namn för [Apptjänstapp](#app-service-app).

## <a name="see-also"></a>Se också

* [Kom igång med Azure](https://azure.microsoft.com/get-started/)
* [Resurscenter i molnet](https://azure.microsoft.com/resources/)  
* [Azure för dina affärsprogram](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure i ditt datacenter](https://azure.microsoft.com/overview/business-apps-on-azure/)

