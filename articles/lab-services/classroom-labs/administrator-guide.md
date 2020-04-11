---
title: Azure Lab Services – administratörsguide | Microsoft-dokument
description: Den här guiden hjälper administratörer som skapar och hanterar labbkonton med Hjälp av Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2019
ms.author: spelluru
ms.openlocfilehash: 7ce7ef15f0bf13182e4799fb640e83136d0d4695
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115029"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services – administratörsguide
IT-administratörer (Information Technology) som hanterar ett universitets molnresurser är vanligtvis ansvariga för att konfigurera labbkontot för sin skola. När ett labbkonto har konfigurerats skapar administratörer eller lärare klassrumslabb som finns i labbkontot. Den här artikeln innehåller en översikt på hög nivå över de azure-resurser som ingår och vägledningen för att skapa dem.

![Vy på hög nivå av Azure-resurser i ett labbkonto](../media/administrator-guide/high-level-view.png)

- Klassrumslabb finns i en Azure-prenumeration som ägs av Azure Lab Services.
- Labbkonton, delat bildgalleri och bildversioner finns i din prenumeration.
- Du kan ha ditt labbkonto och det delade bildgalleriet i samma resursgrupp. I det här diagrammet finns de i olika resursgrupper. 

## <a name="subscription"></a>Prenumeration
Ditt universitet har en eller flera Azure-prenumerationer. En prenumeration används för att hantera fakturering och säkerhet för alla Azure-resurser\tjänster som används i den, inklusive labbkonton.

Relationen mellan ett labbkonto och dess prenumeration är viktig eftersom:

- Fakturering rapporteras via prenumerationen som innehåller labbkontot.
- Du kan ge användare i prenumerationens Azure Active Directory (AD) klientåtkomst till Azure Lab Services. Du kan lägga till en användare som ett labbkonto ägare\contributor, klassrum lab skapare, eller klassrummet lab ägare.

Klassrumslabb och deras virtuella datorer hanteras och finns för dig inom en prenumeration som ägs av Azure Lab Services.

## <a name="resource-group"></a>Resursgrupp
En prenumeration innehåller en eller flera resursgrupper. Resursgrupper används för att skapa logiska grupperingar av Azure-resurser som används tillsammans i samma lösning.  

När du skapar ett labbkonto måste du konfigurera resursgruppen som innehåller labbkontot. 

En resursgrupp krävs också när du skapar ett [delat bildgalleri](#shared-image-gallery). Du kan välja att placera ditt labbkonto och delat bildgalleri i två separata resursgrupper, vilket är typiskt om du planerar att dela bildgalleriet över olika lösningar. Du kan också välja att placera dem i samma resursgrupp.

När du skapar ett labbkonto kan du automatiskt skapa och bifoga ett delat bildgalleri samtidigt.  Det här alternativet resulterar i att labbkontot och det delade bildgalleriet skapas i separata resursgrupper. Det här problemet visas när du använder stegen som beskrivs i den här självstudien: [Konfigurera det delade bildgalleriet när labbkontot skapas](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). Avbildningen högst upp i den här artikeln använder också den här konfigurationen. 

Vi rekommenderar att du investerar tid på framsidan för att planera strukturen för dina resursgrupper eftersom det *inte* går att ändra ett labbkontos eller delade bildgalleris resursgrupp när det har skapats. Om du behöver ändra resursgruppen för dessa resurser måste du ta bort och återskapa labbkontot och\eller det delade bildgalleriet.

## <a name="lab-account"></a>Labbkonto
Ett labbkonto fungerar som en behållare för ett eller flera klassrumslabb. När du kommer igång med Azure Lab Services är det vanligt att bara ha ett enda labbkonto. När labbanvändningen skalas kan du senare välja att skapa fler labbkonton.

I följande lista framhävers scenarier där mer än ett labbkonto kan vara fördelaktigt:

- **Hantera olika principkrav i klassrumslabb** 
    
    När du konfigurerar ett labbkonto anger du principer som gäller för *alla* klassrumslabb under labbkontot, till exempel:
    - Det virtuella Azure-nätverket med delade resurser som klassrumslabbet kan komma åt. Du kan till exempel ha en uppsättning klassrumslabb som behöver åtkomst till en delad datauppsättning i ett virtuellt nätverk.
    - Avbildningar för den virtuella datorn (VM) som klassrumslabben kan använda för att skapa virtuella datorer. Du kan till exempel ha en uppsättning klassrumslabb som behöver åtkomst till [data science-datorn för Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) Marketplace-avbildningen. 
    
    Om du har klassrumslabb som har unika principkrav från varandra kan det vara fördelaktigt att skapa separata labbkonton för att hantera dessa klassrumslabb separat.

- **Separat budget per labbkonto**
  
    I stället för att rapportera alla labbkostnader i klassrummet via ett enda labbkonto kan du behöva en tydligare separerad budget. Du kan till exempel skapa labbkonton för universitetets matematiska avdelning, datavetenskapsavdelningen och så vidare, för att separera budgeten mellan avdelningar.  Du kan sedan visa kostnaden för varje enskilt labbkonto med [Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview).
    
- **Isolera pilotlaboratoring från aktiva produktionslaboratoring**
  
    Du kan ha fall där du vill testa principändringar för ett labbkonto utan att påverka active\production labs. I den här typen av scenario kan du isolera ändringar genom att skapa ett separat labbkonto för pilotändamål. 

## <a name="classroom-lab"></a>Labb i klassrummet
Ett klassrumslabb innehåller virtuella datorer som var och en tilldelas en enskild elev. I allmänhet kan du förvänta dig att:

- Ha ett klassrumslabb för varje klass.
- Skapa en ny uppsättning klassrumslabb varje termin (eller för varje tidsram som din klass erbjuds). Vanligtvis för klasser som har samma bildbehov bör du använda ett [delat bildgalleri](#shared-image-gallery) för att återanvända bilder över labb och terminer.

Tänk på följande punkter när du bestämmer hur du ska strukturera dina klassrumslabb:

- **Alla virtuella datorer i ett klassrumslabb distribueras med samma bild som publiceras**

    Om du har en klass som kräver olika labbavbildningar samtidigt måste därför separata klassrumslabb skapas för var och en.
  
- **Användningskvoten är inställd på labbnivå och gäller för alla användare i labbet**
    
    Om du vill ange olika kvoter för användare måste du skapa separata klassrumslabb. Det är dock möjligt att lägga till fler timmar till en viss användare när du har angett kvoten.
  
- **Start- eller avstängningsschemat är inställt på labbnivå och gäller alla virtuella datorer i labbet**

    I likhet med föregående punkt, om du behöver ange olika scheman för användare, måste du skapa separata klassrumslabb. 

## <a name="shared-image-gallery"></a>Galleri för delad bild
Ett delat bildgalleri är kopplat till ett labbkonto och fungerar som en central lagringsplats för lagring av bilder. En bild sparas i galleriet när en lärare väljer att exportera från ett klassrumslabbs virtuella dator (VM). Varje gång en lärare gör ändringar i mallen VM och exporterar, nya versioner av avbildningen sparas samtidigt som tidigare versioner.

Lärare kan publicera en bildversion från det delade bildgalleriet när de skapar ett nytt klassrumslabb. Även om galleriet kan lagra flera versioner av en avbildning, kan lärare bara välja den senaste versionen när labbet skapas.

Delat bildgalleri är en valfri resurs som du kanske inte behöver direkt när du börjar med endast ett fåtal klassrumslabb. Att använda det delade bildgalleriet har dock många fördelar som är till hjälp när du skalar till att ha fler klassrumslabb:

- **Gör att du kan spara och hantera versioner av en vm-avbildning i mall**

    Det är användbart att skapa en anpassad avbildning eller göra ändringar (programvara, konfiguration och så vidare) i en avbildning från det offentliga Marketplace-galleriet.  Det är till exempel vanligt att lärare kräver att olika program\verktyg installeras. I stället för att kräva att deltagarna manuellt installerar dessa förutsättningar på egen hand kan olika versioner av mallen VM-avbildning exporteras till ett delat bildgalleri. Dessa bildversioner kan sedan användas när du skapar nya klassrumslabb.
- **Aktiverar delning\återanvändning av vm-bilder i mallen i klassrumslabb**

    Du kan spara och återanvända en avbildning så att du inte behöver konfigurera avbildningen från grunden varje gång du skapar ett nytt klassrumslabb. Om till exempel flera klasser erbjuds som behöver samma bild behöver den här bilden bara skapas en gång och exporteras till det delade bildgalleriet så att den kan delas över klassrumslabb.
- **Säkerställer bildtillgänglighet genom replikering**

    När du sparar i det delade bildgalleriet från ett klassrumslabb replikeras avbildningen automatiskt till andra [regioner inom samma geografi](https://azure.microsoft.com/global-infrastructure/regions/). Om det finns ett avbrott för en region påverkas inte publiceringen av avbildningen i klassrumslabbet eftersom en bildreplik från en annan region kan användas.  Publicera virtuella datorer från flera repliker kan också hjälpa till med prestanda.

Om du logiskt vill gruppera delade bilder har du ett par alternativ:

- Skapa flera delade bildgallerier. Varje labbkonto kan bara ansluta till ett delat bildgalleri, så det här alternativet kräver också att du skapar flera labbkonton.
- Du kan också använda ett enda delat bildgalleri som delas av flera labbkonton. I det här fallet kan varje labbkonto endast aktivera de bilder som är tillämpliga på klassrumslabben som det innehåller.

## <a name="naming"></a>Namngivning
När du kommer igång med Azure Lab Services rekommenderar vi att du upprättar namngivningskonventioner för resursgrupper, labbkonton, klassrumslabb och det delade bildgalleriet. De namngivningskonventioner som du upprättar är unika för organisationens behov, men i följande tabell beskrivs allmänna riktlinjer.

| Resurstyp | Roll | Föreslaget mönster | Exempel |
| ------------- | ---- | ----------------- | -------- | 
| Resursgrupp | Innehåller ett eller flera labbkonton och ett eller flera delade bildgallerier | \<organisation\>-\<kortnamn\>miljö -rg<ul><li>**Organisationens kortnamn** identifierar namnet på den organisation som resursgruppen stöder</li><li>**Miljö** identifierar miljön för resursen, till exempel Pilot eller Produktion</li><li>**Rg** står för resurstypen: resursgrupp.</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| Labbkonto | Innehåller ett eller flera labb | \<organisation\>-\<kortnamn\>miljö -la<ul><li>**Organisationens kortnamn** identifierar namnet på den organisation som resursgruppen stöder</li><li>**Miljö** identifierar miljön för resursen, till exempel Pilot eller Produktion</li><li>**La** står för resurstypen: labbkonto.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| Labb i klassrummet | Innehåller en eller flera virtuella datorer |\<\>-\<klassnamn tidsram\>-utbildaren identifierare\<\><ul><li>**Klassnamnet** identifierar namnet på den klass som labbet stöder.</li><li>**Tidsramen** identifierar den tidsram inom vilken klassen erbjuds.</li>**Utbildningsidentifierare** identifierar den lärare som äger labbet.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe | 
| Galleri för delad bild | Innehåller en eller flera VM-avbildningsversioner | \<galleri för\>organisationskortnamn | contosouniversitylabsgallery |

Mer information om hur du namnger andra Azure-resurser finns i [Namngivningskonventioner för Azure-resurser](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Regioner\platser

När du konfigurerar dina Azure Lab Services resurser måste du tillhandahålla en region (eller plats) för det datacenter som ska vara värd för resursen. Här är mer information om hur regionen påverkar var och en av de resurser som är involverade i att inrätta ett labb.

### <a name="resource-group"></a>Resursgrupp

Regionen anger det datacenter där information om resursgruppen lagras. Azure-resurser som finns i resursgruppen kan finnas i olika regioner än de överordnade.

### <a name="lab-account"></a>Labbkonto

En labbkontos plats anger den region som resursen finns i.  

### <a name="classroom-lab"></a>Labb i klassrummet
    
Platsen som ett klassrumslabb finns i varierar beroende på följande faktorer:

  - **Labbkontot är peered till ett virtuellt nätverk (VNet)**
  
    Ett labbkonto kan [peered med ett virtuella nätverk](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) när de är i samma region.  När ett labbkonto är peered med ett VNet skapas klassrumslabb automatiskt i samma region som både labbkontot och virtuella nätverk.

    > [!NOTE]
    > När ett labbkonto är peered med ett VNet inaktiveras inställningen **tillåt labbskapare att välja labbplats.** Ytterligare information finns om den här inställningen i artikeln: [Tillåt labbskaparen att välja plats för labbet](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location).
    
  - **Inget VNet är peered ***och*** lab skapare får inte välja labbplats**
  
    När det inte finns **något** virtuellt nätverk som är peered med labbkontot *och* [labbskapare **inte** tillåts välja labbplatsen](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)skapas klassrumslabb automatiskt i en region som har tillgänglig vm-kapacitet.  Azure Lab Services söker efter tillgänglighet i [regioner som ligger inom samma geografi som labbkontot](https://azure.microsoft.com/global-infrastructure/regions).

  - **Inget virtuella nätverk är peered ***och*** lab skapare får välja labbplats**
       
    När det inte finns **några** VNet-peered och [lab skapare tillåts att välja labbplats,](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)de platser som kan väljas av labbet skapare är baserade på tillgänglig kapacitet.

> [!NOTE]
> För att säkerställa att det finns tillräcklig vm-kapacitet för en region är det viktigt att du först begär kapacitet via labbkontot eller när du skapar labbet.

En allmän regel är att ange en resurs region till en som är närmast sina användare. För klassrumslabb innebär det att skapa klassrumslabbet närmast dina elever. För onlinekurser där studenter finns över hela världen måste du använda ditt bästa omdöme för att skapa ett klassrumslabb som är centralt beläget. Du kan också dela upp en klass i flera klassrumslabb baserat på elevernas region.

### <a name="shared-image-gallery"></a>Galleri för delad bild

Regionen anger källområdet där den första avbildningsversionen lagras innan den replikeras automatiskt till målområden.

## <a name="vm-sizing"></a>Storlek på virtuella datorer
När administratörer eller labbskapare skapar ett klassrumslabb kan de välja mellan följande vm-storlekar baserat på behoven i klassrummet. Kom ihåg att de beräkningsstorlekar som är tillgängliga beror på vilken region ditt labbkonto finns i:

| Storlek | Specifikationer | Serie | Föreslagen användning |
| ---- | ----- | ------ | ------------- |
| Liten| <ul><li>2 kärnor</li><li>3,5 GB RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Den här storleken passar bäst för kommandoraden, öppna webbläsare, låg trafik webbservrar, små till medelstora databaser. |
| Medel | <ul><li>4 Kärnor</li><li>7 GB RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Den här storleken passar bäst för relationsdatabaser, cachelagring i minnet och analyser. |
| Medium (kapslad virtualisering) | <ul><li>4 Kärnor</li><li>16 GB RAM</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Den här storleken passar bäst för relationsdatabaser, cachelagring i minnet och analyser.  Den här storleken stöder också kapslad virtualisering. |
| Stor | <ul><li>8 kärnor</li><li>32 GB RAM</li></ul>  | [Standard_DC8_v2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Den här storleken passar bäst för program som behöver snabbare processorer, bättre lokal diskprestanda, stora databaser, stora minnescacheminnen.  Den här storleken stöder också kapslad virtualisering. |
| Liten GPU (visualisering) | <ul><li>6 Kärnor</li><li>56 GB RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Den här storleken passar bäst för fjärrvisualisering, streaming, spel, kodning med ramverk som OpenGL och DirectX. |
| Liten GPU (beräkning) | <ul><li>6 Kärnor</li><li>56 GB RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Den här storleken passar bäst för datorintensiva program som artificiell intelligens och djupinlärning. |
| Medium GPU (visualisering) | <ul><li>12 kärnor</li><li>112 GB RAM</li></ul>  | [Standard_NC12](https://docs.microsoft.com/azure/virtual-machines/nc-series) | Den här storleken passar bäst för fjärrvisualisering, streaming, spel, kodning med ramverk som OpenGL och DirectX. |

## <a name="manage-identity"></a>Hantera identitet
Med hjälp av [Azures rollbaserade åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/overview)kan följande roller tilldelas för att ge åtkomst till labbkonton och klassrumslabb:

- **Labbkontoägare**

    Administratören som skapar labbkontot läggs automatiskt till i labbkontots **ägarroll.**  En administratör som har tilldelat **rollen Ägare** kan:
     - Ändra labbkontots inställningar.
     - Ge andra administratörer åtkomst till labbkontot som ägare eller bidragsgivare. 
     - Ge lärare åtkomst till klassrumslabb som innehållsskapare, ägare eller bidragsgivare.
     - Skapa och hantera alla klassrumslabb i labbkontot.

- **Medarbetare på labbkonto**

    En administratör som har tilldelat rollen **Deltagare** kan:
    - Ändra labbkontots inställningar.
    - Skapa och hantera alla klassrumslabb i labbkontot.
    
    De kan dock *inte* ge andra användare åtkomst till vare sig labbkonton eller klassrumslabb.

- **Skapare av labb i Classroom**

    Om du vill skapa klassrumslabb i ett labbkonto måste en lärare vara medlem i rollen **Labbskapare.**  När en lärare skapar ett klassrumslabb läggs de automatiskt till som ägare av labbet.  Se självstudien om hur du lägger till [en användare i rollen Lab **Creator** ](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role). 

- **Ägare av klassrumslabb\deltagare**
  
    En lärare kan visa och ändra ett klassrumslabbs inställningar när de är medlemmar i antingen ett labbs **ägar-** eller **deltagarroll.** De måste också vara medlem i labbkontots **Reader-roll.**

    En viktig skillnad mellan ett labbs **ägar-** och **deltagarroller** är att en medarbetare *inte kan* ge andra användare åtkomst till att hantera labbet – endast ägare kan ge andra användare åtkomst till att hantera labbet.
    
    Dessutom kan en lärare *inte* skapa nya klassrumslabb om de inte också är medlemmar i rollen **Lab Creator.**

- **Galleri för delad bild**
    
    När du bifogar ett delat bildgalleri till ett labbkonto får labbkontoägare\deltagare och labbskapare\ägare\deltagare automatiskt åtkomst till att visa och spara bilder i galleriet. 

Här är några tips som hjälper dig att tilldela roller:
   - Vanligtvis bör endast administratörer vara medlemmar i ett labbkontos **ägar-** eller **deltagarroller.** du kan ha mer än en ägare\deltagare.

   - Att ge en pedagog möjligheten att skapa nya klassrumslabb och hantera de labb som de skapar; Du behöver bara tilldela åtkomst till rollen **Lab Creator.**
   
   - Att ge en lärare möjlighet att hantera specifika klassrumslabb, men *inte* möjligheten att skapa nya labb; Du bör tilldela åtkomst till rollen **Ägare** eller **Deltagare** för vart och ett av klassrumslabrens som de ska hantera.  Du kanske till exempel vill tillåta både en professor och en lärarassistent att samsjungera ett klassrumslabb.  Se guiden om hur du lägger till [en användare som ägare i ett klassrumslabb](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner).

## <a name="pricing"></a>Prissättning

### <a name="azure-lab-services"></a>Azure Lab Services
Priserna för Azure Lab Services beskrivs i följande artikel: [Azure Lab Services prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

Du måste också överväga prissättningen för det delade bildgalleriet om du planerar att använda det för att lagra och hantera bildversioner. 

### <a name="shared-image-gallery"></a>Galleri för delad bild
Det är gratis att skapa ett delat bildgalleri och koppla det till ditt labbkonto. Kostnader uppstår inte förrän du sparar en bildversion i galleriet. Vanligtvis är prissättningen för att använda ett delat bildgalleri ganska försumbar, men det är viktigt att förstå hur det beräknas eftersom det inte ingår i prissättningen för Azure Lab Services.  

#### <a name="storage-charges"></a>Lagringsavgifter
Om du vill lagra bildversioner använder ett delat bildgalleri vanliga hårddiskhanterade diskar. Storleken på den hårddiskhanterade disken som används beror på storleken på den bildversion som lagras. Se följande artikel om du vill visa priser: [Priser för hanterade diskar](https://azure.microsoft.com/pricing/details/managed-disks/).


#### <a name="replication-and-network-egress-charges"></a>Avgifter för replikering och utgående nätverk
När du sparar en avbildningsversion med hjälp av ett klassrumslabbs virtuella malldator (VM) lagrar Azure Lab Services den först i en källregion och replikerar sedan källavbildningsversionen automatiskt till ett eller flera målområden. Det är viktigt att notera att Azure Lab Services automatiskt replikerar källavbildningsversionen till alla [målområden inom geografin](https://azure.microsoft.com/global-infrastructure/regions/) där klassrumslabbet finns. Om ditt klassrumslabb till exempel finns i usa:s geografi replikeras en bildversion till var och en av de åtta regioner som finns i USA.

En nätverksutgående avgift inträffar när en avbildningsversion replikeras från källregionen till ytterligare målområden. Det belopp som debiteras baseras på storleken på bildversionen när bildens data ursprungligen överförs utgående från källområdet.  Information om priser finns i följande artikel: [Bandbreddsprisinformation](https://azure.microsoft.com/pricing/details/bandwidth/).

[Utbildningslösningar](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) kunder kan avstå från att betala utgående avgifter. Tala med din kontohanterare om du vill veta mer.  Mer information finns i avsnittet **Vanliga frågor** och svar i det länkade dokumentet, särskilt frågan "Vilka dataöverföringsprogram finns för akademiska kunder och hur kvalificerar jag mig?".

#### <a name="pricing-example"></a>Exempel på prissättning
Om du vill sammanfatta den prissättning som beskrivs ovan ska vi titta på ett exempel på hur vi sparar den virtuella mallens avbildning i det delade bildgalleriet. Anta följande scenarier:

- Du har en anpassad VM-avbildning.
- Du sparar två versioner av bilden.
- Ditt labb är i USA, som har totalt åtta regioner.
- Varje bildversion är 32 GB i storlek. Som ett resultat är hdd-hanterade diskpriset $ 1,54 per månad.

Den totala kostnaden beräknas som:

Antal bilder × antal versioner × antal repliker × hanterat diskpris

I det här exemplet är kostnaden:

1 anpassad bild (32 GB) x 2 versioner x 8 regioner i USA x $1,54 = $24.64 per månad

#### <a name="cost-management"></a>Kostnadshantering
Det är viktigt för labbkontoadministratören att hantera kostnader genom att rutinmässigt ta bort onödiga bildversioner från galleriet. 

Du bör inte ta bort replikering till specifika regioner som ett sätt att minska kostnaderna (det här alternativet finns i det delade bildgalleriet). Replikeringsändringar kan ha negativa effekter på Azure Lab-tjänstens möjlighet att publicera virtuella datorer från bilder som sparats i ett delat bildgalleri.

## <a name="next-steps"></a>Nästa steg
Se självstudien för steg-för-steg-instruktioner för att skapa ett labbkonto och ett labb: [Konfigurera guide](tutorial-setup-lab-account.md)
