---
title: Styrning i Azure DevTest Labs-infrastruktur
description: Den här artikeln innehåller riktlinjer för styrning i Azure DevTest Labs-infrastruktur.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: spelluru
ms.openlocfilehash: 8a661c94ecc660e0ebd0e9818acef81b8a7b819b
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978623"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Styrning av infrastruktur för Azure DevTest Labs - programmigreringen och integration
När din miljö för utveckling/test lab har upprättats, måste du tänka på följande frågor:

- Hur du för att använda miljön i gruppen?
- Hur undviker du att du följa alla nödvändiga organisationens principer och underhålla flexibilitet för att lägga till värdet för ditt program?

## <a name="azure-marketplace-images-vs-custom-images"></a>Azure Marketplace-avbildningar och anpassade avbildningar

### <a name="question"></a>Fråga
När ska jag använda en Azure Marketplace-avbildning jämfört med min egen anpassade organisationens avbildning?

### <a name="answer"></a>Svar
Azure Marketplace ska användas som standard om du inte har specifika frågor eller organisationens krav. Några vanliga exempel är;

- Konfiguration av komplexa programvara som kräver ett program för att ingå som en del av basavbildningen.
- Installationen av ett program kan ta flera timmar, som inte är en effektiv användning av beräkningstid som ska läggas till på en Azure Marketplace-avbildning.
- Utvecklare och testare behöver snabbt åtkomst till en virtuell dator och vill minimera tiden för installationen av en ny virtuell dator.
- Efterlevnad eller föreskrifter (till exempel säkerhetsprinciper) som måste vara uppfyllda för alla datorer.

Med hjälp av anpassade avbildningar ska inte betraktas lätt. Nu när du har att hantera VHD-filer för de underliggande Källavbildningen medför de extra komplexiteten. Du måste också regelbundet uppdatera dessa grundläggande avbildningar med programuppdateringar. Dessa uppdateringar innehåller nya uppdateringar av operativsystemet (OS), och eventuella uppdateringar eller konfigurationsändringar som krävs för programpaketet själva.

## <a name="formula-vs-custom-image"></a>Formeln jämfört med anpassad avbildning

### <a name="question"></a>Fråga
När ska jag använda en formel jämfört med anpassad avbildning?

### <a name="answer"></a>Svar
Normalt den avgörande faktorn som i det här scenariot är kostnaden och återanvända.

Om du har ett scenario där många användare/labs kräver en avbildning med en mängd program ovanpå basavbildningen kan minska du kostnaderna genom att skapa en anpassad avbildning. Det innebär att avbildningen skapas en gång. Det minskar tiden för installationen av den virtuella datorn och kostnader som uppstår på grund av den virtuella datorn körs när installationsprogrammet sker.

Ytterligare en faktor att notera är dock frekventa ändringarna till programpaketet. Om du kör varje dag bygger och kräver att programvaran på dina användares virtuella datorer, Överväg att använda en formel i stället för en anpassad avbildning.

## <a name="use-custom-organizational-images"></a>Använd anpassade organisationens avbildningar

### <a name="question"></a>Fråga
Hur ställer jag in ett enkelt repeterbar process att ta med min organisations anpassade avbildningar i en labb-miljö?

### <a name="answer"></a>Svar
Se [den här videon på Immage Factory mönster](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/). Det här scenariot är ett avancerat scenario och skript som angetts är bara exempel på skript. Om några förändringar krävs, måste du hantera och underhålla de skript som används i din miljö.

Med labb för att skapa en anpassad avbildning pipeline i Azure Pipelines:

- [Introduktion: Förbereda virtuella datorer på några minuter genom att ställa in en avbildning datafabrik i Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/09/14/introduction-get-vms-ready-in-minutes-by-setting-up-image-factory-in-azure-devtest-labs/)
- [Bild fabriken – del 2! Konfigurera Azure Pipelines och Factory labb för att skapa virtuella datorer](https://blogs.msdn.microsoft.com/devtestlab/2017/10/25/image-factory-part-2-setup-vsts-to-create-vms-based-on-devtest-labs/)
- [Bild fabriken – del 3: Spara anpassade avbildningar och distribuera till flera Labs](https://blogs.msdn.microsoft.com/devtestlab/2018/01/10/image-factory-part-3-save-custom-images-and-distribute-to-multiple-labs/)
- [Video: Anpassad avbildning Factory med Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/)

## <a name="patterns-to-set-up-network-configuration"></a>Mönster för att ställa in nätverkskonfiguration

### <a name="question"></a>Fråga
Hur gör jag se till att utveckla och testa virtuella datorer kan inte nå det offentliga internet? Finns det några rekommenderade mönster du ställer in nätverkskonfigurationen?

### <a name="answer"></a>Svar
Ja. Det finns två aspekter att tänka på – inkommande och utgående trafik.

**Inkommande trafik** – om den virtuella datorn inte har en offentlig IP-adress, och sedan den inte går att nå via internet. En vanlig metod är att säkerställa att en princip på prenumerationsnivå har angetts, så att ingen användare kan skapa en offentlig IP-adress.

**Utgående trafik** – om du vill förhindra att virtuella datorer som kommer direkt att offentliga internet och tvinga trafik via en företagsbrandvägg, och du kan dirigera trafik lokalt via expressroute eller VPN, med hjälp av framtvingad routning.

> [!NOTE]
> Glöm inte att lägga till undantag för den testmiljön artefaktlagringskonto, om du har en proxyserver som blockerar trafik utan proxy-inställningar.

Du kan också använda nätverkssäkerhetsgrupper för virtuella datorer eller undernät. Det här steget lägger till ytterligare ett lager för att tillåta / blockera trafik.

## <a name="new-vs-existing-virtual-network"></a>Nytt eller befintligt virtuellt nätverk

### <a name="question"></a>Fråga
När bör jag skapa ett nytt virtuellt nätverk för min labb-miljö och att använda ett befintligt virtuellt nätverk?

### <a name="answer"></a>Svar
Om dina virtuella datorer behöver interagera med befintlig infrastruktur, bör du försöka med ett befintligt virtuellt nätverk i labb-miljö. Dessutom om du använder ExpressRoute, kan du minska mängden virtuella nätverk / undernät så att du inte Fragmentera ditt IP-adressutrymme som hämtar tilldelade för användning i prenumerationerna. Du bör överväga att använda VNet peering mönstret här (typen Hub-Spoke modell). Den här metoden kan virtuella nätverk/undernät kommunikation mellan prenumerationer inom en viss region, även om peering mellan regioner är en upp-kommer funktion i Azure-nätverk.

I annat fall kan varje labb-miljö ha sitt eget virtuella nätverk. Observera dock att det inte finns [gränser](../azure-subscription-service-limits.md) på antalet virtuella nätverk per prenumeration. Standardvärdet är 50, även om den här gränsen kan höjas upp till 100.

## <a name="shared-public-or-private-ip"></a>Delade, offentliga eller privata IP

### <a name="question"></a>Fråga
När ska jag använda en delad IP och offentlig IP-adress jämfört med privata IP?

### <a name="answer"></a>Svar
Om du använder en plats-till-plats VPN eller Express Route kan du använda privata IP-adresser så att dina datorer är tillgängliga via det interna nätverket, och kan inte användas via offentliga internet.

> [!NOTE]
> Labbägare kan ändra den här principen för undernätet för att säkerställa att ingen råkar skapar offentliga IP-adresser för sina virtuella datorer. Prenumerationsägaren ska skapa en prenumerationsprincip som förhindrar att offentliga IP-adresser som skapas.

När du använder delade offentliga IP-adresser kan dela en offentlig IP-adress i de virtuella datorerna i ett labb. Den här metoden kan vara användbart när du behöver att undvika att tränga in begränsningar i den offentliga IP-adresser för en viss prenumeration.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Gränsen för antalet virtuella datorer per användare eller labb

### <a name="question"></a>Fråga
Finns det en regel när det gäller hur många virtuella datorer som jag bör ange per användare eller per labb?

### <a name="answer"></a>Svar
När du överväger hur många virtuella datorer per användare eller per labb, finns det tre huvudsakliga frågeställningar:

- Den **total kostnad** som teamet kan användas för resurser i laboratoriet. Det är enkelt att skapa många datorer. Om du vill kontrollera kostnader, är en mekanism att begränsa antalet virtuella datorer per användare och/eller per labb
- Det totala antalet virtuella datorer i ett labb påverkas av den [på prenumerationskvoter](../azure-subscription-service-limits.md) tillgängliga. En av den övre gränsen är 800 resursgrupper per prenumeration. DevTest Labs skapar för närvarande en ny resursgrupp för varje virtuell dator (såvida inte delade offentliga IP-adresser som används). Om det finns 10 labs i en prenumeration, labs kan anpassa cirka 79 virtuella datorer i varje labb (800 övre gräns – 10 resursgrupper för de 10 labbarna själva) = 79 virtuella datorer per labb.
- Om labbet är ansluten till den lokala via Express Route (till exempel), finns **definierats IP-adressutrymmen tillgängliga** för VNet/undernätet. Så att virtuella datorer i labbet inte inte skapas (fel: Det går inte att hämta IP-adressen), labbägare kan ange max virtuella datorer per labb i linje med tillgängliga IP-adressutrymme.

## <a name="use-resource-manager-templates"></a>Använda Resource Manager-mallar

### <a name="question"></a>Fråga
Hur kan jag använda Resource Manager-mallar i min DevTest Labs-miljö?

### <a name="answer"></a>Svar
Du distribuera dina Resource Manager-mallar i en labb med hjälp av stegen i den [miljöer funktionen i DevTest labs](devtest-lab-test-env.md) artikeln. I princip du checka in Resource Manager-mallar i en Git-lagringsplats (Azure-databaser eller GitHub) och lägga till en [privat lagringsplats för dina mallar](devtest-lab-test-env.md) till labbet.

Det här scenariot kanske inte användbart om du använder labb till värddatorerna för utveckling, men kan vara användbart om du skapar en mellanlagringsmiljö, som är representativ för produktion.

Det är också värt att antalet virtuella datorer per labb eller per användare endast begränsar antalet datorer som skapats internt i själva labben och inte av några miljöer (Resource Manager-mallar).

## <a name="next-steps"></a>Nästa steg
Se [använda miljöer i DevTest Labs](devtest-lab-test-env.md).