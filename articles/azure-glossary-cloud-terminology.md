---
title: Ordlista för Azure - Azure ordlistan | Microsoft Docs
description: Använd Azure ordlista för att förstå molnet terminologi för Azure-plattformen. Den här korta Azure ordlistan innehåller definitioner för vanliga molnet villkoren för Azure.
keywords: Azure ordlista, molnet terminologi, Azure ordlista, termer, molnet villkor
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
ms.openlocfilehash: 953320734c1601c411144bd7a6496716a65e452e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Ordlista för Microsoft Azure: en ordlista med molnet terminologi för Azure-plattformen

Ordlista för Microsoft Azure är en kort dictionary med molnet terminologi för Azure-plattformen. Se även:

* [Microsoft Azure och Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) -definitioner av Azure-tjänster och motsvarigheterna AWS.<!-- I propose to link to https://azure.microsoft.com/en-us/services/ instead of this -->
* [Molnet databehandling villkoren](https://azure.microsoft.com/overview/cloud-computing-dictionary/) -Allmänt branschen molnet villkoren.

## <a name="account"></a>konto
Ett konto som används för att komma åt och hantera en Azure-prenumeration. Det är kallas ofta för ett Azure-konto även om ett konto kan vara något av följande: ett befintligt arbets, skolan, eller personliga Microsoft-konto, eller en Office 365 användarnamn och lösenord. Du kan också skapa ett konto för att hantera en Azure-prenumeration när du registrerar dig för den [kostnadsfri utvärderingsversion](https://azure.microsoft.com).  
Se [registrera dig för en Azure-prenumeration med ditt Office 365-konto](billing/billing-use-existing-office-365-account-azure-subscription.md) och [konton som du kan använda för att logga in](active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>API-app
Ett annat namn för [Apptjänst-app](#app-service-app).

## <a name="app-service-app"></a>App Service-app
Beräkningsresurserna som [Azure App Service](app-service/app-service-web-overview.md) tillhandahåller för hantering av en webbplatsen eller programmet web API eller [mobilappsserverdel](app-service-mobile/app-service-mobile-value-prop.md). Apptjänst-appar kallas även att *Apptjänster*, *webbappar*, *API apps*, och *mobilappar*.

## <a name="availability-set"></a>Tillgänglighetsuppsättning
En samling med virtuella datorer som hanteras tillsammans för att tillhandahålla redundans för programmet och tillförlitlighet. Användning av en tillgänglighetsuppsättning garanterar att minst en virtuell dator under en planerad eller oplanerad underhållshändelse är tillgänglig.  
Se [hantera tillgängligheten för virtuella Windows-datorer](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och [hantera tillgängligheten för virtuella Linux-datorer](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="classic-model"></a>Azure klassiska distributionsmodellen
En av två [distributionsmodeller](resource-manager-deployment-model.md) används för att distribuera resurser i Azure (den nya modellen är Azure Resource Manager). Vissa Azure tjänster stöder endast Resource Manager-distributionsmodellen, vissa stöder den klassiska distributionsmodellen och vissa stöder både. I dokumentationen för varje Azure-tjänst anger vilka modeller som de stöder.

## <a name="cli"></a>Azure-kommandoradsgränssnittet (CLI)
Ett kommandoradsgränssnitt som kan användas för att hantera Azure-tjänster från Windows-, macOS- och Linux.  Vissa tjänster eller tjänstens funktioner kan hanteras endast via PowerShell eller CLI. Se [Azure CLI 2.0](/cli/azure)

## <a name="powershell"></a>Azure PowerShell
Ett kommandoradsgränssnitt för att hantera Azure-tjänster via en kommandorad från Windows-datorer. Vissa tjänster eller tjänstens funktioner kan hanteras endast via PowerShell eller CLI.
Se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview)

## <a name="arm-model"></a>Azure Resource Manager-distributionsmodellen
En av två [distributionsmodeller](resource-manager-deployment-model.md) används för att distribuera resurser i Microsoft Azure (den andra är den klassiska distributionsmodellen). Vissa Azure tjänster stöder endast Resource Manager-distributionsmodellen, vissa stöder den klassiska distributionsmodellen och vissa stöder både. I dokumentationen för varje Azure-tjänst anger vilka modeller som de stöder.

## <a name="fault-domain"></a>feldomän
Samlingen av virtuella datorer i en tillgänglighetsuppsättning som eventuellt kan misslyckas på samma gång. Ett exempel är en grupp datorer i ett rack som delar en gemensam käll- och strömbrytare. I Azure separeras de virtuella datorerna i en tillgänglighetsuppsättning automatiskt över flera feldomäner.  
Se [hantera tillgängligheten för virtuella Windows-datorer](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eller [hantera tillgängligheten för virtuella Linux-datorer](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>GEO-replikering
En definierad gräns för data land som innehåller vanligtvis två eller flera regioner. Gränser kan göras inom eller utanför gränser och påverkas av skatt förordning. Varje geo har minst en region. Asien/Stillahavsområdet och Japan är exempel på regioner. Kallas även *geografi*.  
Se [Azure-regioner](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>GEO-replikering
Processen med att replikera innehållet, till exempel blobbar, tabeller och köer i ett regionalt par automatiskt.  
Se [aktiv Geo-replikering för Azure SQL-databas](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>Bild
En fil som innehåller operativsystemet och konfiguration som kan användas för att skapa valfritt antal virtuella datorer. Det finns två typer av avbildningar i Azure: VM-avbildning och OS-avbildningen. En VM-avbildning innehåller ett operativsystem och alla diskar som är anslutna till en virtuell dator när avbildningen har skapats. En operativsystemavbildning innehåller endast en generaliserad operativsystem med diskkonfigurationer inga data.  
Se [navigera och välj virtuella Windows-avbildningar i Azure med PowerShell eller CLI](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>Gränser
Antal resurser som kan skapas eller benchmark-prestanda som kan uppnås. Gränserna är vanligtvis kopplad till prenumerationer, tjänster och erbjudanden.  
Se [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](azure-subscription-service-limits.md)

## <a name="load-balancer"></a>Belastningsutjämnare
En resurs som distribuerar inkommande trafik mellan datorer i ett nätverk. I Azure distribuerar en belastningsutjämnare trafik till virtuella datorer som har definierats i en belastningsutjämnare. En [belastningsutjämnare](load-balancer/load-balancer-overview.md) kan vara mot internet, eller det kan vara interna.  

## <a name="mobile-app"></a>mobilapp
Ett annat namn för [Apptjänst-App](#app-service-app).

## <a name="offer"></a>erbjudande
De priser, krediter och tillhörande villkor som gäller för en Azure-prenumeration.  
Finns det [informationssidan för Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
Säker webbportalen används för att distribuera och hantera Azure-tjänster.

## <a name="region"></a>region
Ett område i en geo som inte mellan gränser och innehåller en eller flera datacenter. Priser regionala tjänster och erbjudandetyper exponeras på regionsnivån. En region vanligtvis paras ihop med en annan region, vilket kan vara upp till flera hundra mil bort. Regional par kan användas som en mekanism för katastrofåterställning och scenarier med hög tillgänglighet. Kallas även *plats*.  
Se [Azure-regioner](best-practices-availability-paired-regions.md)

## <a name="resource"></a>resurs
Ett objekt som är en del av din Azure-lösning. Varje Azure-tjänst kan du distribuera olika typer av resurser, t.ex databaser eller virtuella datorer.   
Se [översikt över Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="resource-group"></a>Resursgrupp
En behållare i Resource Manager som innehåller relaterade resurser för ett program. Resursgruppen kan innehålla alla resurser för ett program eller bara de resurser som är logiskt grupperade. Du kan bestämma hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation.  
Se [översikt över Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="arm-template"></a>Resource Manager-mall
En JSON-fil som deklarativt definierar en eller flera Azure-resurser och som definierar beroenden mellan distribuerade resurser. Mallen kan användas för att distribuera resurserna på ett konsekvent sätt och upprepade gånger.  
Se [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>resursprovider
En tjänst som tillhandahåller de resurser som du kan distribuera och hantera via Resource Manager. Varje resursprovider tillhandahåller åtgärder som hjälper dig att arbeta med de resurser du distribuerar. Resursproviders kan nås via Azure-portalen, Azure PowerShell och flera programmeringsspråk SDK: er.  
Se [översikt över Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="role"></a>roll
Ett sätt för att styra åtkomsten som kan tilldelas användare, grupper och tjänster. Roller kan utföra åtgärder som att skapa, hantera, och läsas på Azure-resurser.  
Se [RBAC: inbyggda roller](role-based-access-control/built-in-roles.md)

## <a name="sla"></a>servicenivåavtal (SLA)
Avtalet som beskriver Microsofts åtaganden för drifttid och anslutningar. Varje Azure-tjänsten har en specifik SLA.  
Se [serviceavtal](https://azure.microsoft.com/support/legal/sla/)

## <a name="sas"></a>Signatur för delad åtkomst (SAS)
En signatur som gör att du kan bevilja begränsad åtkomst till en resurs, utan att utsätta din kontonyckel. Till exempel [Azure Storage använder SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) att ge klientåtkomst till objekt, till exempel BLOB. [IoT-hubb använder SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) att bevilja behörighet att skicka telemetri för enheter.

## <a name="storage-account"></a>storage account
Ett konto som ger dig åtkomst till Azure Blob, kön, tabell och filen tjänster i Azure Storage. Lagringskontonamnet definierar den unika namnrymden för Azure Storage-dataobjekt.  
Se [om Azure storage-konton](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>prenumeration
Kundens avtal med Microsoft som gör det möjligt för dem att hämta Azure-tjänster. Priser för prenumeration och tillhörande villkor regleras av erbjudandet valt för prenumerationen.
Se [prenumerationsavtalet för Microsoft Online](https://azure.microsoft.com/support/legal/subscription-agreement/) och [hur Azure-prenumerationer är associerade med Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>tagg
En indexering term som gör det möjligt att kategorisera resurser utifrån dina hanterings- eller faktureringskrav. När du har en komplex samling resurser, använder du taggar visualisera dessa tillgångar på ett sätt som är bäst. Du kan till exempel tagga resurser som har en liknande roll i organisationen eller som tillhör samma avdelning.  
Se [med taggar för att organisera dina Azure-resurser](resource-group-using-tags.md)

## <a name="update-domain"></a>Uppdatera domänen
Samlingen av virtuella datorer i en tillgänglighetsuppsättning uppdateras samtidigt. Virtuella datorer i samma uppdateringsdomän startas om tillsammans under planerat underhåll. Azure startar aldrig om mer än en uppdateringsdomän i taget. Kallas även en uppgraderingsdomän.  
Se [hantera tillgängligheten för virtuella Windows-datorer](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och [hantera tillgängligheten för virtuella Linux-datorer](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vm"></a>Virtuell dator
Programvara implementeringen av en fysisk dator som kör ett operativsystem. Flera virtuella datorer kan köras samtidigt på samma maskinvara. I Azure är virtuella datorer tillgängliga i olika storlekar.  
Se [dokumentation för virtuella datorer](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="vm-extension"></a>tillägg för virtuell dator
En resurs som implementerar beteenden eller funktioner som antingen hjälper andra program arbets- eller ge möjlighet för dig att arbeta med en dator som körs. Exempelvis kan du använda tillägget för virtuell dator åtkomst att återställa eller ändra värden för fjärråtkomst på en virtuell Azure-dator.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Se [om virtuella datortillägg och funktioner (Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eller [om virtuella datortillägg och funktioner (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vnet"></a>Virtuellt nätverk
Ett nätverk som tillhandahåller anslutningar mellan dina Azure-resurser som är isolerad från andra Azure klienter. En [Azure VPN-Gateway](vpn-gateway/vpn-gateway-about-vpngateways.md) kan du upprätta anslutningar mellan virtuella nätverk och [mellan ett virtuellt nätverk och ett lokalt nätverk](vpn-gateway/vpn-gateway-plan-design.md). Du kan helt styra IP-Adressblock, DNS-inställningar, säkerhetsprinciper och Routingtabellerna inom ett virtuellt nätverk.  
Se [översikt över virtuella nätverk](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Webbapp
Ett annat namn för [Apptjänst-App](#app-service-app).

## <a name="see-also"></a>Se också

* [Kom igång med Azure](https://azure.microsoft.com/get-started/)
* [Molnet resource center](https://azure.microsoft.com/resources/)  
* [Azure för dina affärsprogram](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure i ditt datacenter](https://azure.microsoft.com/overview/business-apps-on-azure/)

