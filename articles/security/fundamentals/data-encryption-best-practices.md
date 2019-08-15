---
title: Metod tips för data säkerhet och kryptering – Microsoft Azure
description: Den här artikeln innehåller en uppsättning metod tips för data säkerhet och kryptering med hjälp av inbyggda Azure-funktioner.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 83b4f2fce3dbae2168627194a45e62a2d4479936
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934753"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Metodtips för datasäkerhet och kryptering i Azure
Den här artikeln beskriver metod tips för data säkerhet och kryptering.

Bästa praxis bygger på en uppfattning om yttrandet och de fungerar med aktuella funktioner i Azure-plattformen och-funktions uppsättningar. Åsikter och tekniker förändras med tiden och den här artikeln uppdateras regelbundet för att avspegla ändringarna.

## <a name="protect-data"></a>Skydda data
För att skydda data i molnet måste du ta hänsyn till de tillstånd som dina data kan utföra och vilka kontroller som är tillgängliga för det tillståndet. Metod tips för Azure Data Security och kryptering är relaterade till följande data tillstånd:

- I vila: Detta omfattar alla informations lagrings objekt, behållare och typer som finns statiskt på fysiska media, oavsett om det gäller magnet eller optisk disk.
- Under överföring: När data överförs mellan komponenter, platser eller program är den under överföring. Exempel överförs över nätverket, via en Service Bus (från lokalt till molnet och vice versa, inklusive hybrid anslutningar som ExpressRoute) eller under en indata/utdata-process.

## <a name="choose-a-key-management-solution"></a>Välj en nyckel hanterings lösning

Det är viktigt att skydda dina nycklar för att skydda dina data i molnet.

[Azure Key Vault](/azure/key-vault/key-vault-overview) hjälper till att skydda kryptografiska nycklar och hemligheter som används av moln program och tjänster. Key Vault förenklar nyckelhanteringen och låter dig behålla kontrollen över nycklar som kommer åt och krypterar data. Utvecklare kan skapa nycklar för utveckling och testning på några minuter och sedan migrera dem till produktions nycklar. Säkerhetsadministratörer kan bevilja (och återkalla) behörighet till nycklar efter behov.

Du kan använda Key Vault för att skapa flera säkra behållare, som kallas valv. Dessa valv backas upp av HSM: er. Med valv så minskar risken för att säkerhetsinformation förloras av misstag eftersom lagringen av hemligheter centraliseras. Nyckel valv styr och loggar även åtkomst till allt som lagras i dem. Azure Key Vault kan hantera certifikat för att begära och förnya Transport Layer Security (TLS). Den innehåller funktioner för en robust lösning för hantering av certifikat livs cykeln.

Azure Key Vault har utformats för att stödja program nycklar och hemligheter. Key Vault är inte avsedd att vara en lagrings plats för användar lösen ord.

Nedan följer rekommenderade säkerhets metoder för att använda Key Vault.

**Bästa praxis**: Bevilja åtkomst till användare, grupper och program i ett särskilt omfång.   
**Information**: Använd RBACs fördefinierade roller. Om du t. ex. vill bevilja åtkomst till en användare för att hantera nyckel valv, tilldelar du den fördefinierade rollen [Key Vault deltagare](/azure/role-based-access-control/built-in-roles) till den här användaren vid en speciell omfattning. Omfånget i det här fallet är en prenumeration, en resurs grupp eller bara ett särskilt nyckel valv. Om de fördefinierade rollerna inte passar dina behov kan du [definiera dina egna roller](/azure/role-based-access-control/custom-roles).

**Bästa praxis**: Styr vilka användare som har åtkomst till.   
**Information**: Åtkomst till ett nyckelvalv styrs via två separata gränssnitt: hanteringplanet och dataplanet. Hanteringsplanets och dataplanets åtkomstkontroller fungerar oberoende av varandra.

Använd RBAC för att kontrol lera vilka användare som har åtkomst till. Om du till exempel vill ge en program åtkomst till att använda nycklar i ett nyckel valv, behöver du bara bevilja åtkomst behörighet för data planet med hjälp av åtkomst principer för nyckel valv och ingen åtkomst till hanterings planet krävs för det här programmet. Om du vill att en användare ska kunna läsa valv egenskaper och taggar, men inte ha åtkomst till nycklar, hemligheter eller certifikat, kan du bevilja användaren Läs behörighet genom att använda RBAC och ingen åtkomst till data planet krävs.

**Bästa praxis**: Lagra certifikat i ditt nyckel valv. Dina certifikat är av högt värde. I fel händer kan ditt programs säkerhet eller säkerheten för dina data komprometteras.   
**Information**: Azure Resource Manager kan distribuera certifikat på ett säkert sätt som lagras i Azure Key Vault till virtuella Azure-datorer när de virtuella datorerna distribueras. Genom att ange lämpliga åtkomst principer för nyckel valvet kan du också kontrol lera vem som får åtkomst till ditt certifikat. En annan fördel är att du hanterar alla dina certifikat på en plats i Azure Key Vault. Mer information finns i [distribuera certifikat till virtuella datorer från kund hanterade Key Vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) .

**Bästa praxis**: Se till att du kan återställa ett borttagnings nyckel valv eller Key Vault-objekt.   
**Information**: Borttagning av nyckel valv eller Key Vault-objekt kan vara oavsiktligt eller skadligt. Aktivera funktionerna för mjuk borttagning och rensning av Key Vault, särskilt för nycklar som används för att kryptera data i vila. Att ta bort dessa nycklar motsvarar data förlust, så du kan återställa borttagna valv och valv objekt om det behövs. Öva Key Vault återställnings åtgärder regelbundet.

> [!NOTE]
> Om en användare har deltagar behörighet (RBAC) till ett nyckel valv hanterings plan kan de ge sig själva åtkomst till data planet genom att ange en åtkomst princip för nyckel valvet. Vi rekommenderar att du noggrant kontrollerar vem som har åtkomst till dina nyckel valv för att säkerställa att endast behöriga personer kan komma åt och hantera nyckel valv, nycklar, hemligheter och certifikat.
>
>

## <a name="manage-with-secure-workstations"></a>Hantera med säkra arbets stationer

> [!NOTE]
> Prenumerations administratören eller ägaren bör använda en säker arbets Station eller en privilegie rad åtkomst arbets Station.
>
>

Eftersom de stora majoriteten av attacker som riktar sig till slutanvändaren blir slut punkten en av de primära angrepps punkterna. En angripare som komprometterar slut punkten kan använda användarens autentiseringsuppgifter för att få åtkomst till organisationens data. De flesta slut punkts attacker drar nytta av det faktum att användarna är administratörer på sina lokala arbets stationer.

**Bästa praxis**: Använd en säker hanterings arbets station för att skydda känsliga konton, uppgifter och data.   
**Information**: Använd en [privilegie rad åtkomst arbets Station](https://technet.microsoft.com/library/mt634654.aspx) för att minska risken för attacker i arbets stationer. Dessa säkra hanterings arbets stationer kan hjälpa dig att undvika vissa av dessa attacker och se till att dina data är säkrare.

**Bästa praxis**: Se till att Endpoint Protection.   
**Information**: Tillämpa säkerhets principer på alla enheter som används för att använda data, oavsett data plats (moln eller lokalt).

## <a name="protect-data-at-rest"></a>Skydda data i vila

[Data kryptering i vila](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) är ett obligatoriskt steg mot data sekretess, efterlevnad och data suveränitet.

**Bästa praxis**: Använd disk kryptering för att skydda dina data.   
**Information**: Använd [Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview). Det gör det möjligt för IT-administratörer att kryptera Windows-och Linux-IaaS VM-diskar. Disk kryptering kombinerar den Windows BitLocker-funktion som är bransch standard och Linux dm-crypt-funktionen för att tillhandahålla volym kryptering för operativ systemet och data diskarna.

Azure Storage och Azure SQL Database kryptera data i vila som standard och många tjänster erbjuder kryptering som ett alternativ. Du kan använda Azure Key Vault för att behålla kontrollen över nycklar som kommer åt och krypterar dina data. [Läs mer i Azure Resource providers stöd för krypterings modell](encryption-atrest.md#azure-resource-providers-encryption-model-support).

**Bästa praxis**: Använd kryptering för att minimera risker som rör obehörig data åtkomst.   
**Information**: Kryptera dina enheter innan du skriver känsliga data till dem.

Organisationer som inte tillämpar data kryptering är mer utsatta för problem med data sekretess. Obehöriga eller falska användare kan till exempel stjäla data i komprometterade konton eller få obehörig åtkomst till data som kodats i klartext. Företag måste också bevisa att de är noggranna och använda rätt säkerhets kontroller för att förbättra sin data säkerhet för att följa bransch bestämmelser.

## <a name="protect-data-in-transit"></a>Skydda data under överföring

Att skydda data i överföring bör vara en viktig del av din strategi för data skydd. Eftersom data flyttas fram och tillbaka från många platser rekommenderar vi vanligt vis att du alltid använder SSL/TLS-protokoll för att utbyta data mellan olika platser. I vissa fall kanske du vill isolera hela kommunikations kanalen mellan dina lokala och molnbaserade infrastrukturer med hjälp av en VPN-anslutning.

För data som flyttas mellan den lokala infrastrukturen och Azure bör du överväga lämpliga skydd som HTTPS eller VPN. Använd [Azure VPN gateway](../../vpn-gateway/index.yml)när du skickar krypterad trafik mellan ett virtuellt Azure-nätverk och en lokal plats via det offentliga Internet.

Följande är metod tips som är speciella för att använda Azure VPN Gateway, SSL/TLS och HTTPS.

**Bästa praxis**: Säker åtkomst från flera arbets stationer som finns lokalt till ett virtuellt Azure-nätverk.   
**Information**: Använd [plats-till-plats-VPN](/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Bästa praxis**: Säker åtkomst från en enskild arbets station som finns lokalt i ett virtuellt Azure-nätverk.   
**Information**: Använd [punkt-till-plats-VPN](/azure/vpn-gateway/vpn-gateway-point-to-site-create).

**Bästa praxis**: Flytta större data uppsättningar över en dedikerad WAN-länk med hög hastighet.   
**Information**: Använd [ExpressRoute](/azure/expressroute/expressroute-introduction). Om du väljer att använda ExpressRoute kan du också kryptera data på program nivå med hjälp av [SSL/TLS](https://support.microsoft.com/kb/257591) eller andra protokoll för ytterligare skydd.

**Bästa praxis**: Interagera med Azure Storage via Azure Portal.   
**Information**: Alla transaktioner sker via HTTPS. Du kan också använda [lagrings REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) över HTTPS för att interagera med [Azure Storage](https://azure.microsoft.com/services/storage/).

Organisationer som inte kan skydda data under överföringen är mer mottagliga för att man ska kunna skydda data i mellanliggande attacker, [avlyssning](https://technet.microsoft.com/library/gg195641.aspx)och kapning [av](https://technet.microsoft.com/library/gg195821.aspx)sessioner. Dessa attacker kan vara det första steget för att få åtkomst till konfidentiella data.

## <a name="secure-email-documents-and-sensitive-data"></a>Skydda e-post, dokument och känsliga data

Du vill kontrol lera och skydda e-post, dokument och känsliga data som du delar utanför företaget. [Azure information Protection](/azure/information-protection/) är en molnbaserad lösning som hjälper en organisation att klassificera, märka och skydda sina dokument och e-postmeddelanden. Detta kan göras automatiskt av administratörer som definierar regler och villkor, manuellt av användare eller en kombination där användarna får rekommendationer.

Klassificeringen kan identifieras hela tiden, oavsett var data lagras eller vem den delas. Etiketterna innehåller visuella markeringar som ett sidhuvud, en sidfot eller en vattenstämpel. Metadata har lagts till i filer och e-postrubriker i klartext. Klartext garanterar att andra tjänster, till exempel lösningar för att förhindra data förlust, kan identifiera klassificeringen och vidta lämpliga åtgärder.

Skydds tekniken använder Azure Rights Management (Azure RMS). Den här tekniken är integrerad med andra moln tjänster och program från Microsoft, till exempel Office 365 och Azure Active Directory. Den här skydds tekniken använder krypterings-, identitets-och Auktoriseringsprinciper. Skydd som tillämpas via Azure RMS hålls kvar med dokumenten och e-postmeddelandena, oberoende av platsen, i eller utanför din organisation, nätverk, fil servrar och program.

Den här informations skydds lösningen ser till att du styr dina data, även när de delas med andra personer. Du kan också använda Azure RMS med dina egna affärs program och informations skydds lösningar från program varu leverantörer, oavsett om programmen och lösningarna är lokala eller i molnet.

Vi rekommenderar att du:

- [Distribuera Azure information Protection](/azure/information-protection/deployment-roadmap) för din organisation.
- Använd etiketter som återspeglar dina affärs behov. Exempel: Använd en etikett med namnet "hög konfidentiell" för alla dokument och e-postmeddelanden som innehåller topp hemliga data för att klassificera och skydda dessa data. Sedan kan endast behöriga användare komma åt dessa data, med de begränsningar som du anger.
- Konfigurera [användnings loggning för Azure RMS](/azure/information-protection/log-analyze-usage) så att du kan övervaka hur din organisation använder skydds tjänsten.

Organisationer som är svaga på [data klassificering](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) och Filskydd kan vara mer känsliga för data läckage eller missbruk av data. Med rätt Filskydd kan du analysera data flöden för att få insikt i verksamheten, identifiera riskfyllda beteenden och vidta lämpliga åtgärder, spåra åtkomst till dokument och så vidare.

## <a name="next-steps"></a>Nästa steg

Se [metod tips och mönster för Azure-säkerhet](best-practices-and-patterns.md) för att få bättre säkerhets metoder att använda när du utformar, distribuerar och hanterar dina moln lösningar med hjälp av Azure.

Följande resurser är tillgängliga för att ge mer allmän information om Azure-säkerhet och relaterade Microsoft-tjänster:
* [Azure Security Team-bloggen](https://blogs.msdn.microsoft.com/azuresecurity/) – för uppdaterad information om det senaste i Azure-säkerhet
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – där säkerhets problem i Microsoft, inklusive problem med Azure, kan rapporteras eller via e-post tillsecure@microsoft.com
