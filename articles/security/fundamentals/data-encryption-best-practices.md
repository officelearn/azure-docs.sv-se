---
title: Metodtips för datasäkerhet och kryptering – Microsoft Azure
description: Den här artikeln innehåller en uppsättning metodtips för datasäkerhet och kryptering med hjälp av inbyggda Azure-funktioner.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: c5bf62f434b2095f7200b5562c38c252a0195c5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243502"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Metodtips för datasäkerhet och kryptering i Azure
I den här artikeln beskrivs metodtips för datasäkerhet och kryptering.

De bästa metoderna baseras på konsensus och de fungerar med aktuella Azure-plattformsfunktioner och funktionsuppsättningar. Åsikter och teknik förändras med tiden och denna artikel uppdateras regelbundet för att återspegla dessa förändringar.

## <a name="protect-data"></a>Skydda data
För att skydda data i molnet måste du ta hänsyn till möjliga tillstånd där dina data kan inträffa och vilka kontroller som är tillgängliga för det tillståndet. Metodtips för Azure-datasäkerhet och kryptering gäller följande datatillstånd:

- I vila: Detta inkluderar alla informationslagringsobjekt, behållare och typer som finns statiskt på fysiska medier, oavsett om det är magnetiskt eller optiskt disk.
- Under överföring: När data överförs mellan komponenter, platser eller program är de under överföring. Exempel är överföring över nätverket, över en servicebuss (från lokalt till moln och vice versa, inklusive hybridanslutningar som ExpressRoute) eller under en indata-/utdataprocess.

## <a name="choose-a-key-management-solution"></a>Välj en nyckelhanteringslösning

Att skydda dina nycklar är viktigt för att skydda dina data i molnet.

[Azure Key Vault](/azure/key-vault/key-vault-overview) hjälper till att skydda kryptografiska nycklar och hemligheter som används av molnprogram och molntjänster. Key Vault förenklar nyckelhanteringen och låter dig behålla kontrollen över nycklar som kommer åt och krypterar data. Utvecklare kan skapa nycklar för utveckling och testning på några minuter och sedan migrera dem till produktionsnycklar. Säkerhetsadministratörer kan bevilja (och återkalla) behörighet till nycklar efter behov.

Du kan använda Key Vault för att skapa flera säkra behållare, så kallade valv. Dessa valv backas upp av HSMs. Med valv så minskar risken för att säkerhetsinformation förloras av misstag eftersom lagringen av hemligheter centraliseras. Nyckelvalv kontrollerar och loggar dessutom åtkomsten till allt som lagras i dem. Azure Key Vault kan hantera begäranden och förnya TLS-certifikat (Transport Layer Security). Den innehåller funktioner för en robust lösning för hantering av certifikatlivscykeln.

Azure Key Vault är utformat för att stödja programnycklar och hemligheter. Key Vault är inte avsett att vara en butik för användarlösenord.

Följande är bästa säkerhetspraxis för att använda Key Vault.

**Bästa praxis:** Bevilja åtkomst till användare, grupper och program med ett visst scope.   
**Detalj**: Använd RBAC:s fördefinierade roller. Om du till exempel vill bevilja åtkomst till en användare för att hantera nyckelvalv, skulle du tilldela den fördefinierade rollen [Key Vault Contributor](/azure/role-based-access-control/built-in-roles) till den här användaren i ett visst omfång. Omfånget i det här fallet skulle vara en prenumeration, en resursgrupp eller bara ett specifikt nyckelvalv. Om de fördefinierade rollerna inte passar dina behov kan du [definiera dina egna roller](/azure/role-based-access-control/custom-roles).

**Bästa praxis:** Kontrollera vad användarna har åtkomst till.   
**Detalj:** Åtkomst till ett nyckelvalv styrs via två separata gränssnitt: hanteringsplan och dataplan. Hanteringsplanets och dataplanets åtkomstkontroller fungerar oberoende av varandra.

Använd RBAC för att kontrollera vad användare har åtkomst till. Om du till exempel vill bevilja en programåtkomst för att använda nycklar i ett nyckelvalv behöver du bara bevilja åtkomstbehörigheter för dataplan med hjälp av åtkomstprinciper för nyckelvalv och ingen åtkomst till hanteringsplan behövs för det här programmet. Motsvarande gäller att om du vill att en användare ska kunna läsa valvegenskaper och taggar, men inte ha åtkomst till nycklar, hemligheter eller certifikat, så kan du bevilja den här användaren läsbehörighet med hjälp av RBAC. Det behövs ingen åtkomst till dataplanet.

**Bästa praxis:** Lagra certifikat i nyckelvalvet. Dina certifikat är av högt värde. I fel händer kan programmets säkerhet eller säkerheten för dina data äventyras.   
**Detalj:** Azure Resource Manager kan distribuera certifikat som lagras i Azure Key Vault till virtuella Azure-datorer när de virtuella datorerna distribueras. Genom att ange lämpliga åtkomstprinciper för nyckelvalvet kan du även kontrollera vem som får åtkomst till ditt certifikat. En annan fördel är att du hanterar alla dina certifikat på en och samma plats i Azure Key Vault. Mer information [finns i Distribuera certifikat till virtuella datorer från kundhanterade Key Vault.](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)

**Bästa praxis:** Se till att du kan återställa en borttagning av nyckelvalv eller nyckelvalvobjekt.   
**Detalj:** Borttagning av nyckelvalv eller nyckelvalvobjekt kan vara oavsiktligt eller skadligt. Aktivera skyddsfunktionerna för mjuk borttagning och rensning i Key Vault, särskilt för nycklar som används för att kryptera vilande data. Borttagning av dessa nycklar motsvarar dataförlust, så du kan återställa borttagna valv och valvobjekt om det behövs. Öva nyckelvalvåterställningsåtgärder regelbundet.

> [!NOTE]
> Om en användare har deltagarbehörigheter (RBAC) till hanteringsplanet för ett nyckelvalv kan denne ge sig själv åtkomst till dataplanet genom att ange en åtkomstprincip för nyckelvalv. Vi rekommenderar att du noggrant styr vem som har deltagare tillgång till dina viktigaste valv, för att säkerställa att endast behöriga personer kan komma åt och hantera dina nyckelvalv, nycklar, hemligheter och certifikat.
>
>

## <a name="manage-with-secure-workstations"></a>Hantera med säkra arbetsstationer

> [!NOTE]
> Prenumerationsadministratören eller ägaren bör använda en arbetsstation för säker åtkomst eller en arbetsstation för privilegierad åtkomst.
>
>

Eftersom de allra flesta attacker riktar sig mot slutanvändaren blir slutpunkten en av de primära angreppspunkterna. En angripare som äventyrar slutpunkten kan använda användarens autentiseringsuppgifter för att få åtkomst till organisationens data. De flesta slutpunktsattacker utnyttjar det faktum att användare är administratörer på sina lokala arbetsstationer.

**Bästa praxis**: Använd en säker hanteringsarbetsstation för att skydda känsliga konton, uppgifter och data.   
**Detalj**: Använd en [fungerande arbetsstation](https://technet.microsoft.com/library/mt634654.aspx) för privilegierad åtkomst för att minska angreppsytan på arbetsstationer. Dessa säkra hanteringsarbetsstationer kan hjälpa dig att minska vissa av dessa attacker och se till att dina data är säkrare.

**Bästa praxis**: Se till att slutpunktsskydd skyddas.   
**Detalj:** Framtvinga säkerhetsprinciper på alla enheter som används för att använda data, oavsett dataplats (moln eller lokalt).

## <a name="protect-data-at-rest"></a>Skydda data i vila

[Datakryptering i vila](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) är ett obligatoriskt steg mot datasekretess, efterlevnad och datasuveränitet.

**Bästa praxis:** Använd diskkryptering för att skydda dina data.   
**Detalj:** Använd [Azure Disk Kryptering](/azure/security/azure-security-disk-encryption-overview). Det gör det möjligt för IT-administratörer att kryptera Windows och Linux IaaS VM-diskar. Diskkryptering kombinerar den branschstandarda Windows BitLocker-funktionen och Linux dm-crypt-funktionen för att tillhandahålla volymkryptering för operativsystemet och datadiskarna.

Azure Storage och Azure SQL Database krypterar data i vila som standard, och många tjänster erbjuder kryptering som ett alternativ. Du kan använda Azure Key Vault till att behålla kontrollen över de nycklar som används för åtkomst till och kryptering av dina data. Se [Azure-resursleverantörer krypteringsmodell stöd för att lära dig mer](encryption-atrest.md#azure-resource-providers-encryption-model-support).

**Metodtips**: Använd kryptering för att minska riskerna med obehörig dataåtkomst.   
**Detalj:** Kryptera dina enheter innan du skriver känsliga data till dem.

Organisationer som inte tillämpar datakryptering är mer utsatta för problem med datasekretess. Obehöriga eller oseriösa användare kan till exempel stjäla data i komprometterade konton eller få obehörig åtkomst till data som kodats i Rensa format. Företagen måste också bevisa att de är flitiga och använder korrekta säkerhetskontroller för att förbättra sin datasäkerhet för att följa branschbestämmelserna.

## <a name="protect-data-in-transit"></a>Skydda data under överföring

Att skydda data under överföring är en viktig del av din dataskyddsstrategi. Eftersom data flyttas fram och tillbaka från många olika platser rekommenderar vi normalt att du alltid använder SSL/TLS-protokoll när du utbyter data mellan olika platser. I en del fall kan det vara bra att isolera hela kommunikationskanalen mellan den lokala och den molnbaserade infrastrukturen med en VPN-anslutning.

Du bör använda lämpliga skydd som HTTPS eller VPN för data som flyttas mellan den lokala infrastrukturen och Azure. När du skickar krypterad trafik mellan ett virtuellt Azure-nätverk och en lokal plats via det offentliga internet använder du [Azure VPN Gateway](../../vpn-gateway/index.yml).

Följande är metodtips som är specifika för att använda Azure VPN Gateway, SSL/TLS och HTTPS.

**Bästa praxis:** Säker åtkomst från flera arbetsstationer som finns lokalt till ett virtuellt Azure-nätverk.   
**Detalj**: Använd [plats-till-plats VPN](/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Bästa praxis:** Säker åtkomst från en enskild arbetsstation som finns lokalt till ett virtuellt Azure-nätverk.   
**Detalj**: Använd [punkt-till-plats VPN](/azure/vpn-gateway/vpn-gateway-point-to-site-create).

**Bästa praxis:** Flytta större datauppsättningar över en dedikerad WAN-länk med hög hastighet.   
**Detalj**: Använd [ExpressRoute](/azure/expressroute/expressroute-introduction). Om du väljer att använda ExpressRoute kan du även bättra på skyddet genom att kryptera data på programnivå med hjälp av SSL/TLS eller andra protokoll.

**Bästa praxis:** Interagera med Azure Storage via Azure-portalen.   
**Detalj**: Alla transaktioner sker via HTTPS. Du kan också använda [STORAGE REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) via HTTPS för att interagera med Azure [Storage](https://azure.microsoft.com/services/storage/).

Organisationer som misslyckas med att skydda data i transit är mer mottagliga för [man-in-the-middle-attacker,](https://technet.microsoft.com/library/gg195821.aspx) [avlyssning](https://technet.microsoft.com/library/gg195641.aspx)och session kapning. Sådana attacker kan vara ett första steg i att få åtkomst till konfidentiella data.

## <a name="secure-email-documents-and-sensitive-data"></a>Säker e-post, dokument och känsliga data

Du vill styra och skydda e-post, dokument och känsliga data som du delar utanför företaget. [Azure Information Protection](/azure/information-protection/) är en molnbaserad lösning som hjälper företag att klassificera, märka och skydda sina dokument och e-postmeddelanden. Detta kan göras automatiskt av administratörer som definierar regler och villkor, manuellt av användare eller en kombination där användarna får rekommendationer.

Klassificeringen kan alltid identifieras, oavsett var data lagras eller med vem de delas med. Dessa etiketter kan vara visuella markeringar som sidhuvuden, sidfoter eller vattenstämplar. Metadata har lagts till för filer och e-postmeddelandens rubriker i klartext. Den tydliga texten säkerställer att andra tjänster, till exempel lösningar för att förhindra dataförlust, kan identifiera klassificeringen och vidta lämpliga åtgärder.

Skyddstekniken använder Azure Rights Management (Azure RMS). Den här tekniken är integrerad med andra Microsoft-molntjänster och Microsoft-molnprogram, till exempel Office 365 och Azure Active Directory. Den här skyddstekniken använder kryptering, identitet och auktoriseringsprinciper. Skydd som tillämpas via Azure RMS finns kvar i dokument och e-postmeddelanden, oberoende av platsen – inom eller utanför organisationen, nätverk, filservrar och program.

Den här informationsskyddslösningen håller dig i kontroll över dina data, även när den delas med andra. Du kan också använda Azure RMS med dina egna affärsprogram och informationsskyddslösningar från programvaruleverantörer, oavsett om dessa program och lösningar är lokala eller i molnet.

Vi rekommenderar att du gör följande:

- [Distribuera Azure-informationsskydd](/azure/information-protection/deployment-roadmap) för din organisation.
- Använd etiketter som återspeglar dina affärsbehov. Till exempel: Använd en etikett med namnet "mycket konfidentiellt" på alla dokument och e-postmeddelanden som innehåller topphemliga data för att klassificera och skydda dessa data. Sedan kan endast behöriga användare komma åt dessa data, med eventuella begränsningar som du anger.
- Konfigurera [användningsloggning för Azure RMS](/azure/information-protection/log-analyze-usage) så att du kan övervaka hur din organisation använder skyddstjänsten.

Organisationer som är svaga på [dataklassificering](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) och filskydd kan vara mer mottagliga för dataläckage eller datamissbruk. Med rätt filskydd kan du analysera dataflöden för att få insikt i ditt företag, upptäcka riskfyllda beteenden och vidta korrigerande åtgärder, spåra åtkomst till dokument och så vidare.

## <a name="next-steps"></a>Nästa steg

Se metodtips och mönster för [Azure-säkerhet](best-practices-and-patterns.md) för fler metodtips för säkerhet som ska användas när du utformar, distribuerar och hanterar dina molnlösningar med hjälp av Azure.

Följande resurser är tillgängliga för att ge mer allmän information om Azure-säkerhet och relaterade Microsoft-tjänster:
* [Blogg för Azure Security Team](https://blogs.msdn.microsoft.com/azuresecurity/) – för aktuell information om det senaste inom Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – där Microsofts säkerhetsproblem, inklusive problem med Azure, kan rapporteras eller via e-post tillsecure@microsoft.com
