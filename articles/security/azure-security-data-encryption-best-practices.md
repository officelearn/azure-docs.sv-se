---
title: Metodtips för datasäkerhet och kryptering | Microsoft Docs
description: Den här artikeln innehåller en uppsättning Metodtips för datasäkerhet och kryptering med inbyggda i Azure-funktioner.
services: security
documentationcenter: na
author: barclayn
manager: mbalwin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: barclayn
ms.openlocfilehash: 263c04fd15240f365f2325c69d5cb25aa1a539f0
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465885"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Metodtips för datasäkerhet i Azure och kryptering
För att skydda data i molnet, som du behöver för möjliga tillstånd som kan uppstå i dina data och vilka kontroller som finns för det aktuella tillståndet. Metodtips för datasäkerhet i Azure och kryptering som är relaterade till dessa data tillstånd:

- Vilande: Detta omfattar alla information lagringsobjekt, behållare, och typer som statiskt finns på fysiska mediet, om magnetiska och optisk disk.
- Under överföring: när data överförs mellan komponenter, platser eller program, är det under överföringen. Exempel är överföring över nätverket, i ett service bus (från lokalt till molnet och tvärtom, inklusive hybridanslutningar, till exempel ExpressRoute), eller under en in-/ utdata-process.

I den här artikeln diskuterar vi en samling Azure säkerhet och kryptering Metodtips för data. Dessa metodtips härleds från vår erfarenhet med Azure datasäkerhet och kryptering och erfarenheter från kunder som dig själv.

För varje skull förklarar vi:

* Vad den bästa metoden är
* Varför du vill aktivera den bästa praxis
* Vad kan vara resultatet om du inte aktivera den bästa metoden
* Möjliga alternativ till den bästa praxis
* Hur du kan lära dig att aktivera ett metodtips

Den här Azure-datasäkerhet och kryptering metodtips artikeln är baserad på en konsensus-åsikter och funktioner för Azure-plattformen och funktioner, eftersom de finns när den här artikeln skrevs. Andras åsikter och tekniker som ändras med tiden och den här artikeln kommer att uppdateras regelbundet att återspegla dessa ändringar.

## <a name="choose-a-key-management-solution"></a>Välj en lösning för hantering av nycklar
Skydda dina nycklar är nödvändig för att skydda dina data i molnet.

[Azure Key Vault](../key-vault/key-vault-overview.md) hjälper dig att skydda kryptografiska nycklar och hemligheter som program och tjänster i molnet använder. Key Vault förenklar nyckelhanteringen och låter dig behålla kontrollen över nycklar som kommer åt och krypterar data. Utvecklare kan skapa nycklar för utveckling och testning på några minuter och migrera dem till produktionsnycklar. Säkerhetsadministratörer kan bevilja (och återkalla) behörighet till nycklar efter behov.

Du kan använda Key Vault för att skapa flera säkra behållare, som kallas valv. De här valven stöds av HSM: er. Med valv så minskar risken för att säkerhetsinformation förloras av misstag eftersom lagringen av hemligheter centraliseras. Nyckelvalv kan du också styra och logga åtkomst till något som lagras i dem. Azure Key Vault kan hantera förfrågningar om och förnyande Transport Layer Security (TLS)-certifikat. Den innehåller funktioner för en robust lösning för livscykelhantering för certifikat.

Azure Key Vault har utformats som stöd för programnycklar och hemligheter. Key Vault är inte avsedd att vara en lagring för lösenord.

Följande är rekommenderade säkerhetsmetoder för att använda Key Vault.

**Bästa praxis**: bevilja åtkomst till användare, grupper och program i ett visst omfång.   
**Information om**: Använd RBAC fördefinierade roller. Om du vill bevilja åtkomst till en användare att hantera nyckelvalven, skulle du till exempel tilldela rollen fördefinierade [Key Vault deltagare](../role-based-access-control/built-in-roles.md) till den här användaren i ett visst omfång. Omfånget är i det här fallet en prenumeration, en resursgrupp eller bara ett visst nyckelvalv. Om de fördefinierade rollerna inte passar dina behov, kan du [definiera egna roller](../role-based-access-control/custom-roles.md).

**Bästa praxis**: Kontrollera vilka användare som har åtkomst till.   
**Information om**: åtkomst till ett nyckelvalv styrs via två separata gränssnitt: Hanteringsplanet och dataplanet. Hanteringsplanets och dataplanets åtkomstkontroller fungerar oberoende av varandra.

Använd RBAC för att styra vilka användare har åtkomst till. Till exempel om du vill ge ett programåtkomst för att använda nycklar i key vault behöver du bara ge dataplansåtkomstbehörigheter med nyckelvalvets åtkomstprinciper och ingen hanteringsplansåtkomst behövs för det här programmet. Däremot, om du vill att användaren ska kunna läsa valvegenskaper och taggar, men inte har åtkomst till nycklar, hemligheter eller certifikat, kan du bevilja den här användaren läsbehörighet med RBAC och ingen åtkomst till dataplanet krävs.

**Bästa praxis**: Store certifikat i ditt nyckelvalv. Certifikaten är för högt värde. Säkerhet för dina program eller säkerheten för dina data kan komprometteras i fel händer.   
**Information om**: Azure Resource Manager kan på ett säkert sätt att distribuera certifikat som lagras i Azure Key Vault till virtuella Azure-datorer när de virtuella datorerna distribueras. Genom att ange rätt åtkomstprinciper för nyckelvalvet kan styra du också vilka som får åtkomst till ditt certifikat. En annan fördel är att du hanterar alla certifikat på en plats i Azure Key Vault. Se [distribuera certifikat till virtuella datorer från kundhanterad Key Vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) för mer information.

**Bästa praxis**: se till att du kan återställa en borttagning av nyckelvalv eller nyckelvalvobjekt.   
**Information om**: borttagning av nyckeln valv eller nyckelvalvobjekt kan vara oavsiktlig eller skadlig. Aktivera mjuk borttagning och rensa Dataskyddsfunktioner för Key Vault, särskilt för nycklar som används för att kryptera vilande data. Borttagning av de här nycklarna motsvarar förlust av data, så att du kan återställa borttagna valv och valv objekt vid behov. Öva återställningsåtgärder för Key Vault med jämna mellanrum.

> [!NOTE]
> Om en användare har bidragsgivarbehörighet (RBAC) till ett nyckelvalv Hanteringsplanet, kan de ge sig själva åtkomst till dataplanet genom att ange en åtkomstprincip för nyckelvalvet. Vi rekommenderar att du noggrant kontrollera vem som har deltagaråtkomst till dina nyckelvalv så att endast auktoriserade personer kan komma åt och hantera dina nyckelvalv, nycklar, hemligheter och certifikat.
>
>

## <a name="manage-with-secure-workstations"></a>Hantera med säkra arbetsstationer
> [!NOTE]
> Administratör för prenumerationen eller ägare bör använda en säker åtkomst arbetsstation eller en arbetsstation för privilegierad åtkomst.
>
>

Eftersom det stora flertalet attacker mål slutanvändaren, blir en av de primära aspekterna av angrepp av slutpunkten. En angripare som komprometterar slutpunkten använda användarens autentiseringsuppgifter för att få åtkomst till organisationens data. De flesta endpoint attacker dra nytta av det faktum att användare är administratörer i sina lokala arbetsstationer.

**Bästa praxis**: Använd en säker hantering arbetsstation för att skydda känsliga konton, aktiviteter och data.   
**Information om**: Använd en [arbetsstation för privilegierad åtkomst](https://technet.microsoft.com/library/mt634654.aspx) att minska risken för angrepp i arbetsstationer. Dessa arbetsstationer för säker hantering kan hjälpa dig att lösa några av dessa attacker och se till att dina data är säkrare.

**Bästa praxis**: se till att endpoint protection.   
**Information om**: tillämpa säkerhetsprinciper på alla enheter som används för att nyttja data oavsett var data (i molnet eller lokalt).

## <a name="protect-data-at-rest"></a>Skydda data i vila
[Datakryptering i viloläge](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) är ett obligatoriskt steg mot att sekretess, efterlevnad och datasuveränitet.

**Bästa praxis**: tillämpa diskkryptering för att skydda dina data.   
**Information om**: Använd [Azure Disk Encryption](azure-security-disk-encryption.md). Det gör det möjligt för IT-administratörer att kryptera Windows och Linux IaaS VM-diskar. Diskkryptering kombinerar funktionen branschstandard BitLocker för Windows och Linux dm-crypt funktionen till att kryptera volymer för Operativsystemet och datadiskarna.

Azure Storage och Azure SQL Database kryptera vilande data som standard och många tjänster erbjudandet kryptering som ett alternativ. Du kan använda Azure Key Vault för att behålla kontrollen över nycklar som kommer åt och krypterar dina data. Se [Azure resource providers modellen stöd för kryptering mer](azure-security-encryption-atrest.md#azure-resource-providers-encryption-model-support).

**Bästa praxis**: användning av kryptering för att minska riskerna rör obehörig åtkomst.   
**Information om**: kryptera dina enheter innan du skriver känsliga data till dem.

Organisationer som inte framtvingar datakryptering exponeras mer för dataintegritet problem. Till exempel obehöriga eller icke-registrerade användare stjäla data i kapade konton eller få obehörig åtkomst till data som är kodade i Rensa Format. Företag måste också bevisa att de är flitig och använda rätt säkerhetsåtgärder för att förbättra deras datasäkerhet för att följa industrins föreskrifter.

## <a name="protect-data-in-transit"></a>Skydda data under överföring
Bör vara en väsentlig del av din strategi för att skydda data genom att skydda data under överföring. Eftersom data flyttas fram och tillbaka från flera platser, allmänhet rekommenderar vi att du alltid använder SSL/TLS-protokollen för att utbyta data mellan olika platser. I vissa fall kanske du vill isolera hela kommunikationskanalen mellan din lokala och molnbaserade infrastrukturer med hjälp av en VPN-anslutning.

Överväg att lämpliga säkerhetsåtgärder, till exempel HTTPS- eller VPN för data som flyttas mellan den lokala infrastrukturen och Azure. Använd när du skickar krypterad trafik mellan Azure-nätverk och en lokal plats via det offentliga internet, [Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/).

Följande är rekommenderade metoder som är specifika för med Azure VPN Gateway-, SSL/TLS- och HTTPS.

**Bästa praxis**: säker åtkomst från flera arbetsstationer som finns lokalt till Azure-nätverk.   
**Information om**: Använd [plats-till-plats VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

**Bästa praxis**: säker åtkomst från en enskild arbetsstation dem på plats till en Azure-nätverk.   
**Information om**: Använd [punkt-till-plats VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

**Bästa praxis**: flytta större datauppsättningar via en dedikerad höghastighetsnätverk WAN-länk.   
**Information om**: Använd [ExpressRoute](../expressroute/expressroute-introduction.md). Om du väljer att använda ExpressRoute, du kan också kryptera data på programnivå med hjälp av [SSL/TLS](https://support.microsoft.com/kb/257591) eller andra protokoll för extra skydd.

**Bästa praxis**: interagera med Azure Storage via Azure portal.   
**Information om**: alla transaktioner sker via HTTPS. Du kan också använda [Storage REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) via HTTPS kan interagera med [Azure Storage](https://azure.microsoft.com/services/storage/) och [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Organisationer som inte vill skydda data under överföring är svårare att [man-in-the-middle-attacker](https://technet.microsoft.com/library/gg195821.aspx), [avlyssning](https://technet.microsoft.com/library/gg195641.aspx), och sessionskapning. Dessa attacker kan vara det första steget i att komma åt känsliga data.

## <a name="secure-email-documents-and-sensitive-data"></a>Skydda e-post, dokument och känsliga data
Du vill kontrollera och skydda e-post, dokument och känsliga data som du delar utanför företaget. [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/) är en molnbaserad lösning som hjälper företag att klassificera, etikettera och skydda sina dokument och e-postmeddelanden. Detta kan göras automatiskt av administratörer som definierar regler och villkor, manuellt av användare eller en kombination där användare får rekommendationer.

Klassificering kan identifieras på hela tiden, oavsett var data lagras eller vem de delas. Dessa etiketter kan vara visuella markeringar som ett sidhuvud, sidfot eller vattenstämpel. Metadata har lagts till filer och e-posthuvuden i klartext. Klartext ser till att andra tjänster, till exempel lösningar för att förhindra förlust av data, kan identifiera klassificeringen och vidta lämpliga åtgärder.

Skyddstekniken använder Azure Rights Management (Azure RMS). Den här tekniken är integrerad med andra Microsoft-molntjänster och program, till exempel Office 365 och Azure Active Directory. Den här skyddstekniken använder kryptering, identitet och auktoriseringsprinciper. Skyddet som tillämpas via Azure RMS kvar i dokumenten och e-postmeddelanden, oavsett var finns – i eller utanför din organisation, nätverk, filservrar och program.

Den här informationsskyddslösningen behåller du kontrollen över dina data, även när de delas med andra. Du kan också använda Azure RMS med dina egna line-of-business-program och informationsskyddslösningar från programvaruleverantörer, oavsett om dessa program och lösningar finns lokalt eller i molnet.

Vi rekommenderar att du:

- [Distribuera Azure Information Protection](https://docs.microsoft.com/azure/information-protection/deployment-roadmap) för din organisation.
- Använda etiketter som motsvarar dina affärsbehov. Till exempel: tillämpa en etikett med namnet ”konfidentiell” för alla dokument och e-postmeddelanden som innehåller superhemlig data, för att klassificera och skydda dessa data. Endast behöriga användare kan sedan komma åt dessa data med några begränsningar som du anger.
- Konfigurera [användningsloggning för Azure RMS](https://docs.microsoft.com/azure/information-protection/log-analyze-usage) så att du kan övervaka hur din organisation använder skyddstjänsten.

Organisationer som har svag på [dataklassificering](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) och Filskydd kan vara svårare att data läcker ut eller missbruk av data. Du kan analysera dataflöden för att få insikt i verksamheten, identifiera riskfyllda beteenden och vidta lämpliga åtgärder, spåra åtkomst till dokument och så vidare med rätt Filskydd.

## <a name="next-steps"></a>Nästa steg
Se [säkerhet i Azure-metodtips och mönster](security-best-practices-and-patterns.md) för flera beprövade metoder för att använda när du utforma, distribuera och hantera dina molnlösningar med hjälp av Azure.

Följande resurser är tillgängliga för att tillhandahålla mer allmän information om Azure-säkerhet och relaterade Microsoft-tjänster:
* [Azure-Säkerhetsteamets blogg](https://blogs.msdn.microsoft.com/azuresecurity/) – uppdaterad information på senast inom Azure-säkerhet
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – där kan du rapportera säkerhetsproblem i Microsoft, inklusive problem med Azure, eller mejla till secure@microsoft.com
