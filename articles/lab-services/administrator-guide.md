---
title: Azure Lab Services-administratörs guide | Microsoft Docs
description: Den här guiden hjälper administratörer som skapar och hanterar labb konton med Azure Lab Services.
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: 8670a9d56575dbfb6d3e565ec97191581dc612a8
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491043"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services-administratörs guide
IT-administratörer som hanterar ett universitets moln resurser är vanligt vis ansvariga för att ställa in labb kontot för sin skola. När ett labb konto har kon figurer ATS kan administratörer eller lärare skapa labb som finns i labb kontot. Den här artikeln innehåller en översikt över de Azure-resurser som ingår och vägledningen för att skapa dem.

![Övergripande visning av Azure-resurser i ett labb konto](./media/administrator-guide/high-level-view.png)

- Labb finns i en Azure-prenumeration som ägs av Azure Lab Services.
- Labb konton, delade avbildnings gallerier och avbildnings versioner finns i din prenumeration.
- Du kan ha ditt labb konto och det delade avbildnings galleriet i samma resurs grupp. I det här diagrammet finns de i olika resurs grupper.

Mer information om arkitekturen finns i artikeln: [labb arkitektur grunderna](https://docs.microsoft.com/azure/lab-services/classroom-labs-fundamentals)

## <a name="subscription"></a>Prenumeration
Ditt universitet har en eller flera Azure-prenumerationer. En prenumeration används för att hantera fakturering och säkerhet för alla Azure-resources\services som används i den, inklusive Lab-konton.

Relationen mellan ett labb konto och dess prenumeration är viktig eftersom:

- Faktureringen rapporteras via prenumerationen som innehåller labb kontot.
- Du kan ge användare i prenumerationens Azure Active Directory (AD) klient åtkomst till Azure Lab Services. Du kan lägga till en användare som labb konto owner\contributor, labb skapare eller labb ägare.

Labb och virtuella datorer (VM) hanteras och hanteras i en prenumeration som ägs av Azure Lab Services.

## <a name="resource-group"></a>Resursgrupp
En prenumeration innehåller en eller flera resurs grupper. Resurs grupper används för att skapa logiska grupperingar av Azure-resurser som används tillsammans inom samma lösning.  

När du skapar ett labb konto måste du konfigurera resurs gruppen som innehåller labb kontot. 

Du måste också ha en resurs grupp när du skapar ett [delat avbildnings Galleri](#shared-image-gallery). Du kan välja att ange ditt labb konto och ditt delade avbildnings galleri i två separata resurs grupper, vilket är vanligt om du planerar att dela avbildnings galleriet över olika lösningar. Eller så kan du välja att använda dem i samma resurs grupp.

När du skapar ett labb konto kan du automatiskt skapa och koppla ett delat avbildnings galleri på samma gång.  Det här alternativet resulterar i labb kontot och det delade avbildnings galleriet skapas i separata resurs grupper. Du ser det här beteendet när du använder stegen som beskrivs i den här självstudien: [Konfigurera delade avbildnings galleriet när labb kontot skapas](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). Bilden överst i den här artikeln använder också den här konfigurationen. 

Vi rekommenderar att du planerar fram till att planera strukturen för dina resurs grupper eftersom det *inte* går att ändra en labb kontos resurs grupp eller delade avbildnings galleriets resurs grupp när den har skapats. Om du behöver ändra resurs gruppen för de här resurserna måste du ta bort och återskapa ditt labb konto och/eller-Galleri för delade avbildningar.

## <a name="lab-account"></a>Labb konto

Ett labb konto fungerar som en behållare för en eller flera labb. När du kommer igång med Azure Lab Services är det vanligt att bara ha ett enda labb konto. När din labb användning skalas kan du senare välja att skapa fler labb konton.

I följande lista beskrivs scenarier där mer än ett labb konto kan vara fördelaktigt:

- **Hantera olika princip krav i labbet**

    När du konfigurerar ett labb konto ställer du in principer som gäller för *alla* labb under labb kontot, till exempel:
    - Det virtuella Azure-nätverket med delade resurser som labbet kan komma åt. Du kan till exempel ha en uppsättning labb som behöver åtkomst till en delad data uppsättning i ett virtuellt nätverk.
    - De virtuella dator avbildningar som labbet kan använda för att skapa virtuella datorer. Du kan till exempel ha en uppsättning labb som behöver åtkomst till [data science VM för Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) Marketplace-avbildningen.

    Om du har labb som har unika princip krav från varandra kan det vara bra att skapa separata labb konton för att hantera dessa labb separat.

- **Separat budget efter labb konto**
  
    I stället för att rapportera alla labb kostnader via ett enda labb konto kan du behöva en tydligare budget. Du kan till exempel skapa labb konton för ditt universitets matematik avdelning, dator vetenskaps avdelning och så vidare, för att avgränsa budgeten mellan olika avdelningar.  Du kan sedan Visa kostnaden för varje enskilt labb konto med [Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview).

- **Isolera pilot labb från active\production Labs**
  
    Det kan finnas fall där du vill pilot policy ändringar för ett labb konto utan att potentiellt påverka active\production Labs. I den här typen av scenario kan du skapa ett separat labb konto för pilotering i syfte att isolera ändringar. 

## <a name="lab"></a>Labb

Ett labb innehåller virtuella datorer (VM) som är tilldelade till en enda student.  I allmänhet kan du förväntar dig att:

- Ha ett labb för varje klass.
- Skapa en ny uppsättning labb varje termin (eller för varje tidsram som din klass erbjuds). För klasser som har samma bild måste du använda ett [delat avbildnings Galleri](#shared-image-gallery) för att återanvända bilder i labb och termin.

Tänk på följande när du bestämmer hur du ska strukturera dina labb:

- **Alla virtuella datorer i ett labb distribueras med samma avbildning som publiceras**

    Det innebär att om du har en klass som kräver att olika labb bilder publiceras samtidigt, måste separata labb skapas för var och en.
  
- **Användnings kvoten anges på labb nivån och gäller för alla användare i labbet**

    Om du vill ange olika kvoter för användare måste du skapa separata labb. Det går dock att lägga till fler timmar till en speciell användare efter att du har angett kvoten.
  
- **Schemat för start eller avstängning anges på labb nivån och gäller för alla virtuella datorer i labbet**

    I likhet med den föregående punkten måste du skapa separata labb om du behöver ange olika scheman för användarna.

Som standard har varje labb sitt eget virtuella nätverk.  Om du har aktiverat VNet-peering har varje labb sitt eget undernät som peer-kopplas till det angivna virtuella nätverket.

## <a name="shared-image-gallery"></a>Galleri för delad avbildning

Ett delat avbildnings galleri är kopplat till ett labb konto och fungerar som en central lagrings plats för att lagra avbildningar. En bild sparas i galleriet när en lärare väljer att exportera från en labbs mall för virtuella datorer (VM). Varje gången en lärare gör ändringar i mallen VM och export, sparas nya versioner av avbildningen samtidigt som tidigare versioner bibehålls.

Lärare kan publicera en avbildnings version från galleriet för delad avbildning när de skapar ett nytt labb. Även om galleriet lagrar flera versioner av en avbildning, kan lärare bara välja den senaste versionen under labb skapandet.

Delade avbildnings galleri är en valfri resurs som du kanske inte behöver omedelbart när du startar med bara några få labb. Att använda delade avbildnings Galleri har dock många fördelar som är användbara när du skalar för att få fler labb:

- **Gör att du kan spara och hantera versioner av en mall för virtuella dator bilder**

    Det är praktiskt att skapa en anpassad avbildning eller göra ändringar (program vara, konfiguration och så vidare) till en avbildning från det offentliga Marketplace-galleriet.  Det är till exempel vanligt för lärare att kräva att olika software\tooling ska installeras. I stället för att kräva att studenter installerar dessa krav manuellt på egen hand, kan olika versioner av mallens VM-avbildning exporteras till ett delat avbildnings Galleri. Dessa avbildnings versioner kan sedan användas när du skapar nya labb.
- **Aktiverar sharing\reuse av mallens VM-avbildningar i labbet**

    Du kan spara och återanvända en bild så att du inte behöver konfigurera avbildningen från grunden varje gången du skapar ett nytt labb. Om till exempel flera klasser erbjuds som behöver samma avbildning, behöver avbildningen bara skapas en gång och exporteras till det delade bild galleriet så att den kan delas mellan labb.
- **Garanterar avbildnings tillgänglighet via replikering**

    När du sparar till galleriet för delad avbildning från ett labb, replikeras din avbildning automatiskt till andra [regioner i samma geografi](https://azure.microsoft.com/global-infrastructure/regions/). Om det finns ett avbrott för en region påverkas inte publiceringen av avbildningen till ditt labb eftersom en avbildnings replik från en annan region kan användas.  Det kan också vara bättre att publicera virtuella datorer från flera repliker.

För att gruppera delade avbildningar logiskt, har du ett par alternativ:

- Skapa flera Galleri för delade avbildningar. Varje labb konto kan bara ansluta till ett delat avbildnings Galleri, så det här alternativet kräver också att du skapar flera labb konton.
- Du kan också använda ett enda delat avbildnings galleri som delas av flera labb konton. I det här fallet kan varje labb konto bara aktivera de avbildningar som är tillämpliga för de labb som det innehåller.

## <a name="naming"></a>Namngivning

När du kommer igång med Azure Lab Services rekommenderar vi att du skapar namn konventioner för resurs grupper, labb konton, labb och delade avbildnings Galleri. De namngivnings konventioner som du fastställer är unika för organisationens behov, och följande tabell beskriver allmänna rikt linjer.

| Resurstyp | Roll | Föreslaget mönster | Exempel |
| ------------- | ---- | ----------------- | -------- | 
| Resursgrupp | Innehåller ett eller flera labb konton och ett eller flera gallerier för delade avbildningar | \<organization short name\>-\<environment\>– RG<ul><li>**Organisationens kort namn** identifierar namnet på den organisation som resurs gruppen stöder</li><li>**Miljö** identifierar resursens miljö, till exempel pilot eller produktion</li><li>**RG** står för resurs typen: resurs grupp.</li></ul> | contosouniversitylabs – rg<br/>contosouniversitylabs – pilot – rg<br/>contosouniversitylabs-Prod-rg |
| Labb konto | Innehåller en eller flera labb | \<organization short name\>-\<environment\>-La<ul><li>**Organisationens kort namn** identifierar namnet på den organisation som resurs gruppen stöder</li><li>**Miljö** identifierar resursens miljö, till exempel pilot eller produktion</li><li>**La** står för resurs typen: labb konto.</li></ul> | contosouniversitylabs-La<br/>mathdeptlabs-La<br/>sciencedeptlabs-pilot-La<br/>sciencedeptlabs-Prod-La |
| Labb | Innehåller en eller flera virtuella datorer |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**Klass namn** identifierar namnet på klassen som labbet stöder.</li><li>**Tidsram** identifierar den tidsram i vilken klassen erbjuds.</li>**Utbildnings identifierare** identifierar den lärare som äger labbet.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| Galleri för delad avbildning | Innehåller en eller flera versioner av virtuella dator avbildningar | \<organization short name\>formatmall | contosouniversitylabsgallery |

Mer information om hur du namnger andra Azure-resurser finns i [namngivnings konventioner för Azure-resurser](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Regions\locations

När du konfigurerar Azure Lab Services resurser måste du ange en region (eller en plats) för det data Center som ska vara värd för resursen. Här finns mer information om hur regioner påverkar var och en av de resurser som ingår i konfigurationen av ett labb.

### <a name="resource-group"></a>Resursgrupp

Regionen anger data centret där information om resurs gruppen lagras. Azure-resurser som ingår i resurs gruppen kan finnas i olika regioner från deras överordnade platser.

### <a name="lab-account"></a>Labb konto

Ett labb kontos plats anger den region som resursen finns i.  

### <a name="lab"></a>Labb

Den plats som ett labb finns i varierar beroende på följande faktorer:

  - **Labb kontot är peer-kopplat till ett virtuellt nätverk (VNet)**
  
    Ett labb konto kan peer-kopplas [med ett VNet](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) när de befinner sig i samma region.  När ett labb konto är peer-kopplat med ett VNet skapas labb automatiskt i samma region som både labb kontot och VNet.

    > [!NOTE]
    > När ett labb konto är peer-kopplat med ett VNet inaktive ras inställningen för att **tillåta labb skapare att välja labb plats** . Ytterligare information finns i artikeln: [Tillåt labb skapare att välja plats för labbet](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location).
    
  - * * Inget VNet är peer-kopplat * *_och_* _ labb skapare tillåts inte att välja labb location_ *
  
    Om **ingen** VNet är peer-kopplad med labb kontot *och* [labb skapare **inte** tillåts att välja labb platsen](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), skapas labb automatiskt i en region som har tillgänglig VM-kapacitet.  Mer specifikt Azure Lab Services letar efter tillgänglighet i [regioner som ligger inom samma geografi som labb kontot](https://azure.microsoft.com/global-infrastructure/regions).

  - * * Inget VNet är peer-kopplat * *_och_* _ labb skapare kan välja labb location_ *
       
    När det **inte finns något** virtuellt nätverk och [labb skapare tillåts att välja labb platsen](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), baseras de platser som kan väljas av Labbets skapare på tillgänglig kapacitet.

> [!NOTE]
> För att säkerställa att det finns tillräckligt med VM-kapacitet för en region är det viktigt att du först begär kapacitet genom att använda labb kontot eller när du skapar labbet.

En allmän regel är att ange en resurs region till en som är närmast dess användare. För labb, innebär det att skapa labb labbet närmast dina studenter. För onlinekurser där studenterna är placerade över hela världen, måste du använda ditt bästa omdöme för att skapa ett labb som är centralt befinner dig. Du kan också dela upp en klass i flera labb baserat på din studenters region.

### <a name="shared-image-gallery"></a>Galleri för delad avbildning

Regionen anger käll regionen där den första avbildnings versionen lagras innan den replikeras automatiskt till mål regionerna.

## <a name="vm-sizing"></a>Storlek på virtuell dator

När administratörer eller labb skapare skapar ett labb kan de välja mellan följande VM-storlekar baserat på behoven i klass rummet. Kom ihåg att de tillgängliga beräknings storlekarna är beroende av den region där ditt labb konto finns:

| Storlek | Specifikationer | Serie | Föreslagen användning |
| ---- | ----- | ------ | ------------- |
| Liten| <ul><li>2 kärnor</li><li>3,5 GB RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Den här storleken passar bäst för kommando rad, öppna webbläsare, webb servrar med låg trafik, små till medel stora databaser. |
| Medium | <ul><li>4 kärnor</li><li>7 GB RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Den här storleken passar bäst för Relations databaser, minnes intern cachelagring och analys. |
| Medium (kapslad virtualisering) | <ul><li>4 kärnor</li><li>16 GB RAM</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Den här storleken passar bäst för Relations databaser, minnes intern cachelagring och analys.
| Stor | <ul><li>8 kärnor</li><li>16 GB RAM</li></ul>  | [Standard_A8_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | Den här storleken lämpar sig bäst för program som behöver snabbare processorer, bättre prestanda för lokala diskar, stora databaser, stora cacheminnen.  Den här storleken stöder även kapslad virtualisering. |
| Stor (kapslad virtualisering) | <ul><li>8 kärnor</li><li>32 GB RAM</li></ul>  | [Standard_D8s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Den här storleken lämpar sig bäst för program som behöver snabbare processorer, bättre prestanda för lokala diskar, stora databaser, stora cacheminnen. |
| Liten GPU (visualisering) | <ul><li>6 kärnor</li><li>56 GB RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Den här storleken passar bäst för fjärrvisualisering, strömning, spel, kodning med hjälp av ramverk som OpenGL och DirectX. |
| Liten GPU (Compute) | <ul><li>6 kärnor</li><li>56 GB RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Den här storleken passar bäst för dator intensiva program som artificiell intelligens och djup inlärning. |
| Medelhög GPU (visualisering) | <ul><li>12 kärnor</li><li>112 GB RAM</li></ul>  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Den här storleken passar bäst för fjärrvisualisering, strömning, spel, kodning med hjälp av ramverk som OpenGL och DirectX. |

## <a name="manage-identity"></a>Hantera identitet

Med hjälp av [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)kan följande roller tilldelas för att ge åtkomst till labb konton och labb:

- **Labbkontoägare**

    Administratören som skapar labb kontot läggs automatiskt till i labb kontots **ägar** roll.  En administratör som har tilldelats **ägar** rollen kan:
     - Ändra Lab-kontots inställningar.
     - Ge andra administratörer åtkomst till labb kontot som ägare eller deltagare.
     - Ge lärare till gång till labb som skapare, ägare eller deltagare.
     - Skapa och hantera alla labb i labb kontot.

- **Labb konto deltagare**

    En administratör som har tilldelats **deltagar** rollen kan:
    - Ändra Lab-kontots inställningar.
    - Skapa och hantera alla labb i labb kontot.

    De kan dock *inte* ge andra användare åtkomst till antingen labb konton eller labb.

- **Labbskapare**

    För att skapa labb i ett labb konto måste en lärare vara medlem i rollen **labb skapare** .  När en lärare skapar ett labb läggs de automatiskt till som en ägare till labbet.  Läs självstudien om hur du [lägger till en användare i rollen **labb skapare**](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role). 

- **Labb owner\contributor**
  
    En lärare kan visa och ändra inställningarna för labbet när de är medlemmar i endera Labbets **ägare** eller **deltagar** roll. de måste också vara medlemmar i labb kontots **läsar** roll.

    En viktig skillnad mellan Labbets **ägare** och **deltagar** roller är att en deltagare *inte kan* ge andra användare åtkomst till att hantera labb ägare kan ge andra användare åtkomst till att hantera labbet.

    Dessutom kan en lärare *inte* skapa nya labb, om de inte också är medlem i rollen **labb skapare** .

- **Galleri för delad avbildning**

    När du ansluter ett delat avbildnings galleri till ett labb konto får Lab-kontot owners\contributors och Lab creators\owners\contributors automatiskt åtkomst till att visa och spara bilder i galleriet.

Här följer några tips på hur du tilldelar roller:
   - Normalt bör endast administratörer vara medlemmar i ett labb kontos **ägare** eller **deltagar** roller. Du kan ha mer än en owner\contributor.
   - För att ge en lärare möjlighet att skapa nya labb och hantera de labb som de skapar. du behöver bara tilldela åtkomst till rollen **labb skapare** .
   - För att ge en lärare möjlighet att hantera vissa labb, men *inte* möjligheten att skapa nya labb. Du bör tilldela åtkomst till antingen rollen **ägare** eller **deltagare** för varje labb som de ska hantera.  Du kanske till exempel vill tillåta både en lärare och en undervisnings assistent att samar beta med ett labb.  Läs guiden om hur du [lägger till en användare som ägare till ett labb](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner).

## <a name="pricing"></a>Prissättning

### <a name="azure-lab-services"></a>Azure Lab Services

Prissättningen för Azure Lab Services beskrivs i följande artikel: [Azure Lab Services prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

Du måste också beakta priserna för galleriet för delad avbildning om du planerar att använda det för att lagra och hantera avbildnings versioner. 

### <a name="shared-image-gallery"></a>Galleri för delad avbildning

Det kostar inget att skapa ett delat avbildnings galleri och koppla det till ditt labb konto. Kostnaderna uppstår tills du sparar en avbildnings version i galleriet. Normalt är prissättningen för att använda ett delat avbildnings Galleri ganska försumbar, men det är viktigt att förstå hur det beräknas eftersom det inte ingår i prissättningen för Azure Lab Services.  

#### <a name="storage-charges"></a>Lagrings kostnader

Om du vill lagra avbildnings versioner använder ett delat avbildnings Galleri standard diskar som hanteras av hård diskar. Storleken på den HDD-hanterade disk som används beror på storleken på avbildnings versionen som lagras. Se följande artikel för att se prissättningen priser: [Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Utgående kostnader för replikering och nätverk

När du sparar en avbildnings version med hjälp av en labbs mall för virtuella datorer, lagrar Azure Lab Services först den i en käll region och replikerar sedan automatiskt käll avbildnings versionen till en eller flera mål regioner. Det är viktigt att Observera att Azure Lab Services automatiskt replikerar käll avbildnings versionen till alla mål [regioner inom det geografiska område](https://azure.microsoft.com/global-infrastructure/regions/) där labbet finns. Om ditt labb t. ex. finns i geografien i USA replikeras en avbildnings version till var och en av de åtta regionerna i USA

Nätverks utgående belastning sker när en avbildnings version replikeras från käll regionen till ytterligare mål regioner. Det debiterade beloppet baseras på avbildnings versionens storlek när avbildningens data först överförs utgående från käll regionen.  Pris information finns i följande artikel: [pris information för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/).

Kunder med [utbildnings lösningar](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) kan avstå från att betala utgående kostnader. Prata med din konto ansvarige om du vill veta mer.  Mer information finns i avsnittet **vanliga frågor och svar** i det länkade dokumentet, särskilt frågan "vilka data överförings program finns för akademiska kunder och hur kan jag kvalificera mig?".

#### <a name="pricing-example"></a>Prissättnings exempel

För att Sammanfattning de priser som beskrivs ovan ska vi titta på ett exempel på hur du sparar vår VM-avbildning för mallen i det delade avbildnings galleriet. Antag följande scenarier:

- Du har en anpassad VM-avbildning.
- Du håller på att spara två versioner av avbildningen.
- Ditt labb är i USA, som har totalt åtta regioner.
- Varje avbildnings version är 32 GB i storlek. Därför är hård disk-Managed disk pris $1,54 per månad.

Den totala kostnaden beräknas som:

Antal avbildningar × antal versioner × antalet repliker × Managed disk pris

I det här exemplet är kostnaden:

1 anpassad bild (32 GB) x 2 versioner x 8 amerikanska regioner x $1,54 = $24,64 per månad

#### <a name="cost-management"></a>Kostnadshantering

Det är viktigt för labb konto administratören att hantera kostnader genom att rutinmässigt ta bort onödiga bild versioner från galleriet. 

Du bör inte ta bort replikeringen till vissa regioner som ett sätt att minska kostnaderna (det här alternativet finns i delade avbildnings galleriet). Ändringar i replikeringen kan ha skadliga effekter på Azure Lab Services möjlighet att publicera virtuella datorer från avbildningar som sparats i ett delat avbildnings Galleri.

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanligt för att ställa in en labb miljö.

- [Installations guide för labb konto](account-setup-guide.md)
- [Installations guide för labb](setup-guide.md)
- [Kostnadshantering för labb](cost-management-guide.md)
- [Använda Azure Lab Services i Teams](lab-services-within-teams-overview.md)

