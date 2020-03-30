---
title: Azure-ordlista – Azure-ordlista | Microsoft-dokument
description: Använd Azure-ordlistan för att förstå molnterminologi på Azure-plattformen. Den här korta Azure-ordlistan innehåller definitioner för vanliga molntermer för Azure.
keywords: Azure-ordlista, molnterminologi, Azure-ordlista, terminologidefinitioner, molntermer
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
ms.openlocfilehash: 7c80ba6b15cec08da9bcefea243493a824ef66bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276483"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Microsoft Azure-ordlista: En ordlista med molnterminologi på Azure-plattformen

Microsoft Azure-ordlistan är en kort ordlista med molnterminologi för Azure-plattformen. Se även:

* [Microsoft Azure och Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) – Definitioner av Azure-tjänster och deras AWS-motsvarigheter.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Cloud computing termer](https://azure.microsoft.com/overview/cloud-computing-dictionary/) - Allmänna branschmoln termer.

## <a name="account"></a>konto
Ett konto som används för att komma åt och hantera en Azure-prenumeration. Det kallas ofta för ett Azure-konto även om ett konto kan vara något av dessa: ett befintligt arbete, skola eller personligt Microsoft-konto eller ett Användarnamn och lösenord för Office 365. Du kan också skapa ett konto för att hantera en Azure-prenumeration när du registrerar dig för den [kostnadsfria utvärderingsversionen](https://azure.microsoft.com).  
Se [Registrera dig för en Azure-prenumeration med ditt Office 365-konto](cost-management-billing/manage/office-365-account-for-azure-subscription.md) och konton som du kan använda för att logga [in](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>API-app
Ett annat namn för [App Service-appen](#app-service-app).

## <a name="app-service-app"></a>App Service-app
De beräkningsresurser som [Azure App Service](app-service/overview.md) tillhandahåller för värdskap för en webbplats eller ett webbprogram, webb-API eller en [serverdel för mobilappen](app-service-mobile/app-service-mobile-value-prop.md). Apptjänstappar kallas även *App Services,* *webbappar,* *API-appar*och *mobilappar*.

## <a name="availability-set"></a>tillgänglighetsuppsättning
En samling virtuella datorer som hanteras tillsammans för att ge programredundans och tillförlitlighet. Användningen av en tillgänglighetsuppsättning säkerställer att det under en planerad eller oplanerad underhållshändelse är minst en virtuell dator tillgänglig.  
Se [Hantera tillgängligheten för virtuella Windows-datorer](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och [Hantera tillgängligheten för virtuella Linux-datorer](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="azure-classic-deployment-model"></a><a name="classic-model"></a>Klassisk Azure-distributionsmodell
En av två [distributionsmodeller](resource-manager-deployment-model.md) som används för att distribuera resurser i Azure (den nya modellen är Azure Resource Manager). Vissa Azure-tjänster stöder endast Resource Manager-distributionsmodellen, vissa stöder endast den klassiska distributionsmodellen och vissa stöder båda. Dokumentationen för varje Azure-tjänst anger vilka modeller de stöder.

## <a name="azure-command-line-interface-cli"></a><a name="cli"></a>Azure kommandoradsgränssnitt (CLI)
Ett kommandoradsgränssnitt som kan användas för att hantera Azure-tjänster från Windows, macOS och Linux.  Vissa tjänster eller tjänstfunktioner kan endast hanteras via PowerShell eller CLI. Se [Azure CLI](/cli/azure)

## <a name="azure-powershell"></a><a name="powershell"></a>Azure PowerShell
Ett kommandoradsgränssnitt för att hantera Azure-tjänster via en kommandorad från Windows-datorer. Vissa tjänster eller tjänstfunktioner kan endast hanteras via PowerShell eller CLI.
Se [Hur du installerar och konfigurerar Azure PowerShell](/powershell/azure/overview)

## <a name="azure-resource-manager-deployment-model"></a><a name="arm-model"></a>Distributionsmodell för Azure Resource Manager
En av två [distributionsmodeller](resource-manager-deployment-model.md) som används för att distribuera resurser i Microsoft Azure (den andra är den klassiska distributionsmodellen). Vissa Azure-tjänster stöder endast Resource Manager-distributionsmodellen, vissa stöder endast den klassiska distributionsmodellen och vissa stöder båda. Dokumentationen för varje Azure-tjänst anger vilka modeller de stöder.

## <a name="fault-domain"></a>feldomän
Samlingen av virtuella datorer i en tillgänglighetsuppsättning som eventuellt kan misslyckas samtidigt. Ett exempel är en grupp datorer i ett rack som delar en gemensam strömkälla och nätverksväxel. I Azure separeras de virtuella datorerna i en tillgänglighetsuppsättning automatiskt mellan flera feldomäner.  
Se [Hantera tillgängligheten för virtuella Windows-datorer](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eller [Hantera tillgängligheten för virtuella Linux-datorer](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>Geo
En definierad gräns för databest finns som vanligtvis innehåller två eller flera regioner. Gränserna kan ligga inom eller utanför de nationella gränserna och påverkas av skattereglering. Varje geo har minst en region. Exempel på geos är Asien och Stillahavsområdet och Japan. Kallas även *geografi*.  
Se [Azure-regioner](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>geo-replikering
Processen att automatiskt replikera innehåll som blobbar, tabeller och köer inom ett regionalt par.  
Se [Active Geo-Replication för Azure SQL Database](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image
En fil som innehåller operativsystemet och programkonfigurationen som kan användas för att skapa valfritt antal virtuella datorer. I Azure finns det två typer av avbildningar: VM-avbildning och OS-avbildning. En VM-avbildning innehåller ett operativsystem och alla diskar som är anslutna till en virtuell dator när avbildningen skapas. En OS-avbildning innehåller endast ett generaliserat operativsystem utan datadiskkonfigurationer.  
Se [Navigera och välja Windows-avbildningar för virtuella datorer i Azure med PowerShell eller CLI](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>Gränser
Antalet resurser som kan skapas eller det prestandariktmärke som kan uppnås. Begränsningar associeras vanligtvis med prenumerationer, tjänster och erbjudanden.  
Se [Azure-prenumerations- och tjänstbegränsningar, kvoter och begränsningar](azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="load-balancer"></a>belastningsutjämnare
En resurs som distribuerar inkommande trafik mellan datorer i ett nätverk. I Azure distribuerar en belastningsutjämnare trafik till virtuella datorer som definierats i en belastningsutjämnad uppsättning. En [belastningsutjämnare](load-balancer/load-balancer-overview.md) kan vara internetvänd, eller så kan den vara intern.  

## <a name="mobile-app"></a>mobilapp
Ett annat namn för [App Service App](#app-service-app).

## <a name="offer"></a>offer
De priser, krediter och relaterade villkor som gäller för en Azure-prenumeration.  
Se [sidan för information om Azure-erbjudanden](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
Den säkra webbportalen som används för att distribuera och hantera Azure-tjänster.

## <a name="region"></a>regionen
Ett område inom en geo som inte korsar nationella gränser och innehåller ett eller flera datacenter. Prissättning, regionala tjänster och erbjudandetyper exponeras på regionnivå. En region paras vanligtvis ihop med en annan region, som kan vara upp till flera hundra miles away. Regionala par kan användas som en mekanism för haveriberedskap och scenarier med hög tillgänglighet. Kallas även *plats*.  
Se [Azure-regioner](best-practices-availability-paired-regions.md)

## <a name="resource"></a>resource
Ett objekt som ingår i din Azure-lösning. Varje Azure-tjänst kan du distribuera olika typer av resurser, till exempel databaser eller virtuella datorer.   
Se [översikt över Azure Resource Manager](azure-resource-manager/management/overview.md)

## <a name="resource-group"></a>Resursgrupp
En behållare i Resource Manager som innehåller relaterade resurser för ett program. Resursgruppen kan innehålla alla resurser för ett program, eller bara de resurser som logiskt grupperas tillsammans. Du kan bestämma hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation.  
Se [översikt över Azure Resource Manager](azure-resource-manager/management/overview.md)

## <a name="resource-manager-template"></a><a name="arm-template"></a>Mall för Resurshanteraren
En JSON-fil som deklarativt definierar en eller flera Azure-resurser och som definierar beroenden mellan de distribuerade resurserna. Mallen kan användas för att distribuera resurserna på ett konsekvent sätt och upprepade gånger.  
Se [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>resursleverantör
En tjänst som tillhandahåller de resurser som du kan distribuera och hantera via Resource Manager. Varje resursprovider tillhandahåller åtgärder som hjälper dig att arbeta med de resurser du distribuerar. Resursleverantörer kan nås via Azure-portalen, Azure PowerShell och flera programmerings-SDK:er.  
Se [översikt över Azure Resource Manager](azure-resource-manager/management/overview.md)

## <a name="role"></a>roll
Ett sätt att kontrollera åtkomst som kan tilldelas användare, grupper och tjänster. Roller kan utföra åtgärder som att skapa, hantera och läsa på Azure-resurser.  
Se [RBAC: Inbyggda roller](role-based-access-control/built-in-roles.md)

## <a name="service-level-agreement-sla"></a><a name="sla"></a>servicenivåavtal (SLA)
Avtalet som beskriver Microsofts åtaganden för drifttid och anslutning. Varje Azure-tjänst har ett specifikt serviceavtal.  
Se [Servicenivåavtal](https://azure.microsoft.com/support/legal/sla/)

## <a name="shared-access-signature-sas"></a><a name="sas"></a>signatur för delad åtkomst (SAS)
En signatur som gör att du kan bevilja begränsad åtkomst till en resurs, utan att exponera din kontonyckel. Azure Storage använder till exempel [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) för att bevilja klientåtkomst till objekt som blobbar. [IoT Hub använder SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) för att ge enheter behörighet att skicka telemetri.

## <a name="storage-account"></a>storage account
Ett konto som ger dig åtkomst till Azure Blob-,Queue, Table och File services i Azure Storage. Lagringskontonamnet definierar det unika namnområdet för Azure Storage-dataobjekt.  
Se [Om Azure-lagringskonton](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>prenumeration
En kunds avtal med Microsoft som gör det möjligt för dem att skaffa Azure-tjänster. Prenumerationspriser och relaterade villkor regleras av det erbjudande som valts för prenumerationen.
Se [Microsoft Online-prenumerationsavtal](https://azure.microsoft.com/support/legal/subscription-agreement/) och [hur Azure-prenumerationer associeras med Azure Active Directory](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>tagg
En indexeringsterm som gör att du kan kategorisera resurser efter dina krav för hantering eller fakturering. När du har en komplex samling resurser kan du använda taggar för att visualisera dessa tillgångar på det sätt som är mest meningsfullt. Du kan till exempel tagga resurser som har en liknande roll i organisationen eller som tillhör samma avdelning.  
Se [Använda taggar för att ordna dina Azure-resurser](resource-group-using-tags.md)

## <a name="update-domain"></a>uppdatera domän
Samlingen av virtuella datorer i en tillgänglighetsuppsättning som uppdateras samtidigt. Virtuella datorer i samma uppdateringsdomän startas om tillsammans under planerat underhåll. Azure startar aldrig om mer än en uppdateringsdomän åt gången. Kallas även en uppgraderingsdomän.  
Se [Hantera tillgängligheten för virtuella Windows-datorer](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och [Hantera tillgängligheten för virtuella Linux-datorer](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-machine"></a><a name="vm"></a>virtuell maskin
Programvaruimplementeringen av en fysisk dator som kör ett operativsystem. Flera virtuella datorer kan köras samtidigt på samma maskinvara. I Azure finns virtuella datorer i en mängd olika storlekar.  
Se [dokumentation för virtuella datorer](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="virtual-machine-extension"></a><a name="vm-extension"></a>tillägg för virtuell dator
En resurs som implementerar beteenden eller funktioner som antingen hjälper andra program att fungera eller ger dig möjlighet att interagera med en dator som körs. Du kan till exempel använda VM Access-tillägget för att återställa eller ändra fjärråtkomstvärden på en virtuell Azure-dator.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Se Om tillägg och funktioner för virtuella datorer [(Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eller [Om tillägg och funktioner för virtuella datorer (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-network"></a><a name="vnet"></a>virtuellt nätverk
Ett nätverk som tillhandahåller anslutning mellan dina Azure-resurser som är isolerade från alla andra Azure-klienter. Med [en Azure VPN-gateway](vpn-gateway/vpn-gateway-about-vpngateways.md) kan du upprätta anslutningar mellan virtuella nätverk och mellan ett virtuellt nätverk och ett lokalt nätverk. Du kan styra IP-adressblocken, DNS-inställningarna, säkerhetsprinciperna och vägtabellerna i ett virtuellt nätverk.  
Se [översikt över virtuellt nätverk](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Webbapp
Ett annat namn för [App Service App](#app-service-app).

## <a name="see-also"></a>Se även

* [Kom igång med Azure](https://azure.microsoft.com/get-started/)
* [Molnresurscenter](https://azure.microsoft.com/resources/)  
* [Azure för ditt företagsprogram](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure i ditt datacenter](https://azure.microsoft.com/overview/business-apps-on-azure/)

