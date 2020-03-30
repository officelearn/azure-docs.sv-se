---
title: Programmigrering och integrering i Azure DevTest Labs
description: Den här artikeln innehåller vägledning för styrning av Azure DevTest Labs-infrastruktur i samband med programmigrering och integrering.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 14641e9096fa9366334e9f7460ae55cda0e6c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644894"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Styrning av Azure DevTest Labs-infrastruktur – Programmigrering och -integrering
När din utvecklings-/testlabbetmiljö har etablerats måste du tänka på följande frågor:

- Hur utnyttjar du miljön i ditt projektteam?
- Hur ser du till att du följer alla nödvändiga organisationsprinciper och behåller flexibiliteten för att skapa mervärde i ditt program?

## <a name="azure-marketplace-images-vs-custom-images"></a>Azure Marketplace-avbildningar jämfört med anpassade avbildningar

### <a name="question"></a>Fråga
När ska jag använda en Azure Marketplace-avbildning jämfört med min egen anpassade organisationsavbildning?

### <a name="answer"></a>Svar
Azure Marketplace bör användas som standard om du inte har specifika problem eller organisatoriska krav. Några vanliga exempel är;

- Komplex programvarukonfiguration som kräver att ett program inkluderas som en del av basavbildningen.
- Installation och installation av ett program kan ta många timmar, vilket inte är en effektiv användning av beräkningstid som ska läggas till på en Azure Marketplace-avbildning.
- Utvecklare och testare kräver åtkomst till en virtuell dator snabbt och vill minimera inställningstiden för en ny virtuell dator.
- Efterlevnad eller regelvillkor (till exempel säkerhetsprinciper) som måste finnas på plats för alla datorer.

Användning av anpassade bilder bör inte betraktas lättvindigt. De introducerar extra komplexitet, eftersom du nu måste hantera VHD-filer för de underliggande basavbildningar. Du måste också rutinmässigt korrigera dessa basavbildningar med programuppdateringar. Dessa uppdateringar inkluderar nya operativsystemuppdateringar och eventuella uppdateringar eller konfigurationsändringar som behövs för själva programvarupaketet.

## <a name="formula-vs-custom-image"></a>Formel kontra anpassad bild

### <a name="question"></a>Fråga
När ska jag använda en formel kontra anpassad bild?

### <a name="answer"></a>Svar
Vanligtvis är den avgörande faktorn i det här scenariot kostnad och återanvändning.

Om du har ett scenario där många användare / labs kräver en avbildning med mycket programvara ovanpå basavbildningen, då kan du minska kostnaderna genom att skapa en anpassad avbildning. Det innebär att bilden skapas en gång. Det minskar inställningstiden för den virtuella datorn och kostnaden som uppstår på grund av att den virtuella datorn körs när installationen inträffar.

En ytterligare faktor att notera är dock hur ofta du ändrar programvarupaketet. Om du kör dagliga versioner och kräver att programvaran ska finnas på användarnas virtuella datorer kan du överväga att använda en formel i stället för en anpassad avbildning.

## <a name="use-custom-organizational-images"></a>Använda anpassade organisationsavbildningar

### <a name="question"></a>Fråga
Hur kan jag ställa in en lätt repeterbar process för att föra in mina anpassade organisationsavbildningar i en DevTest Labs-miljö?

### <a name="answer"></a>Svar
Se [den här videon på Image Factory mönster](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/). Det här scenariot är ett avancerat scenario och skripten som tillhandahålls är endast exempelskript. Om några ändringar krävs måste du hantera och underhålla skripten som används i din miljö.

Använda DevTest Labs för att skapa en anpassad avbildningspipeline i Azure Pipelines:

- [Introduktion: Förbered virtuella datorer på några minuter genom att konfigurera en avbildningsfabrik i Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/09/14/introduction-get-vms-ready-in-minutes-by-setting-up-image-factory-in-azure-devtest-labs/)
- [Bild Factory - Del 2! Konfigurera Azure Pipelines och Factory Lab för att skapa virtuella datorer](https://blogs.msdn.microsoft.com/devtestlab/2017/10/25/image-factory-part-2-setup-vsts-to-create-vms-based-on-devtest-labs/)
- [Image Factory – del 3: Spara anpassade bilder och distribuera till flera laboratorier](https://blogs.msdn.microsoft.com/devtestlab/2018/01/10/image-factory-part-3-save-custom-images-and-distribute-to-multiple-labs/)
- [Video: Anpassad avbildningsfabrik med Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/)

## <a name="patterns-to-set-up-network-configuration"></a>Mönster för att konfigurera nätverkskonfiguration

### <a name="question"></a>Fråga
Hur ser jag till att utvecklings- och test virtuella datorer inte kan nå det offentliga internet? Finns det några rekommenderade mönster för att ställa in nätverkskonfiguration?

### <a name="answer"></a>Svar
Ja. Det finns två aspekter att tänka på – inkommande och utgående trafik.

**Inkommande trafik** – Om den virtuella datorn inte har en offentlig IP-adress kan den inte nås av Internet. En vanlig metod är att se till att en princip på prenumerationsnivå har angetts, så att ingen användare kan skapa en offentlig IP-adress.

**Utgående trafik** – Om du vill förhindra virtuella datorer som går direkt till offentligt internet och tvinga trafik genom en företagsbrandvägg kan du dirigera trafik lokalt via expressväg eller VPN, med hjälp av tvångsroutning.

> [!NOTE]
> Om du har en proxyserver som blockerar trafik utan proxyinställningar, glöm inte att lägga till undantag till labbets artefaktlagringskonto.

Du kan också använda nätverkssäkerhetsgrupper för virtuella datorer eller undernät. Detta steg lägger till ett extra lager av skydd för att tillåta / blockera trafik.

## <a name="new-vs-existing-virtual-network"></a>Nytt kontra befintligt virtuellt nätverk

### <a name="question"></a>Fråga
När ska jag skapa ett nytt virtuellt nätverk för min DevTest Labs-miljö jämfört med ett befintligt virtuellt nätverk?

### <a name="answer"></a>Svar
Om dina virtuella datorer behöver interagera med befintlig infrastruktur bör du överväga att använda ett befintligt virtuellt nätverk i din DevTest Labs-miljö. Dessutom, om du använder ExpressRoute, kanske du vill minimera mängden virtuella nätverk / undernät så att du inte fragmentera din IP-adress utrymme som tilldelas för användning i prenumerationer. Du bör också överväga att använda VNet peering-mönstret här (Hub-Spoke-modellen). Den här metoden möjliggör kommunikation mellan virtuella nätverk och undernät över prenumerationer inom en viss region, även om peering mellan regioner är en kommande funktion i Azure-nätverk.

Annars kan varje DevTest Labs-miljö ha ett eget virtuellt nätverk. Observera dock att det finns [gränser för](../azure-resource-manager/management/azure-subscription-service-limits.md) antalet virtuella nätverk per prenumeration. Standardbeloppet är 50, men den här gränsen kan höjas till 100.

## <a name="shared-public-or-private-ip"></a>Delad, offentlig eller privat IP

### <a name="question"></a>Fråga
När ska jag använda en delad IP kontra offentlig IP kontra privat IP?

### <a name="answer"></a>Svar
Om du använder en VPN-tjänst eller expressväg från plats kan du överväga att använda privata IPs så att dina datorer är tillgängliga via ditt interna nätverk och otillgängliga via offentligt internet.

> [!NOTE]
> Labbägare kan ändra den här undernätsprincipen för att säkerställa att ingen av misstag skapar offentliga IP-adresser för sina virtuella datorer. Prenumerationsägaren bör skapa en prenumerationsprincip som förhindrar att offentliga IPs skapas.

När du använder delade offentliga IP-adresser delar de virtuella datorerna i ett labb en offentlig IP-adress. Den här metoden kan vara till hjälp när du behöver undvika att överskrida gränserna för offentliga IP-adresser för en viss prenumeration.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Gränser för antalet virtuella datorer per användare eller labb

### <a name="question"></a>Fråga
Finns det en regel när det gäller hur många virtuella datorer jag ska ställa in per användare, eller per labb?

### <a name="answer"></a>Svar
När man överväger antalet virtuella datorer per användare eller per labb, det finns tre huvudsakliga frågor:

- Den **totala kostnaden** som teamet kan spendera på resurser i labbet. Det är lätt att snurra upp många maskiner. För att kontrollera kostnaderna är en mekanism för att begränsa antalet virtuella datorer per användare och/eller per
- Det totala antalet virtuella datorer i ett labb påverkas av de [tillgängliga prenumerationsnivåkvoterna.](../azure-resource-manager/management/azure-subscription-service-limits.md) En av de övre gränserna är 800 resursgrupper per prenumeration. DevTest Labs skapar för närvarande en ny resursgrupp för varje virtuell dator (såvida inte delade offentliga IPs används). Om det finns 10 labb i en prenumeration kan labb passa cirka 79 virtuella datorer i varje labb (800 övre gräns – 10 resursgrupper för själva 10 labb) = 79 virtuella datorer per labb.
- Om labbet är anslutet till lokalt via Express Route (till exempel) finns det **definierade IP-adressutrymmen tillgängliga** för det virtuella nätverket/undernätet. För att säkerställa att virtuella datorer i labbet inte misslyckas med att skapas (fel: kan inte få IP-adress) kan labbägare ange max virtuella datorer per labb som är anpassat till det tillgängliga IP-adressutrymmet.

## <a name="use-resource-manager-templates"></a>Använda Resource Manager-mallar

### <a name="question"></a>Fråga
Hur kan jag använda Resource Manager-mallar i min DevTest Labs-miljö?

### <a name="answer"></a>Svar
Du distribuerar dina Resource Manager-mallar i en DevTest Labs-miljö med hjälp av steg som nämns i [miljöfunktionen i devtest-labben.](devtest-lab-test-env.md) I grund och botten kontrollerar du dina Resource Manager-mallar i en Git-databas (antingen Azure Repos eller GitHub) och lägger till en [privat databas för dina mallar](devtest-lab-test-env.md) i labbet.

Det här scenariot kanske inte är användbart om du använder DevTest Labs som värd för utvecklingsdatorer, men kan vara användbart om du skapar en mellanlagringsmiljö som är representativ för produktionen.

Det är också värt att notera att antalet virtuella datorer per labb eller per användare alternativet endast begränsar antalet datorer som skapats internt i labbet själv, och inte av några miljöer (Resource Manager mallar).

## <a name="next-steps"></a>Nästa steg
Se [Använda miljöer i DevTest Labs](devtest-lab-test-env.md).