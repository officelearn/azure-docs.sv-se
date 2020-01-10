---
title: Azure Lab Services-administratörs guide | Microsoft Docs
description: Den här guiden hjälper administratörer som skapar och hanterar labb konton med Azure Lab Services.
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
ms.openlocfilehash: 638a90615d248b3c2829770432dd6a08eb4bb2fb
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771742"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services-administratörs guide
IT-administratörer som hanterar en organisations moln resurser är också vanligt vis ansvariga för att ställa in labb kontot för organisationen. Administratörer eller lärare skapa klass rum labb labb i labb kontot. Den här artikeln innehåller en översikt över de Azure-resurser som ingår och vägledningen för att skapa dem.

![Övergripande visning av Azure-resurser i ett labb konto](../media/administrator-guide/high-level-view.png)

- Klass rums labb finns i en Azure-prenumeration som ägs av Azure Lab Services
- Labb konton, delade avbildnings galleri och avbildnings versioner finns i din prenumeration
- Du kan ha Lab-konto och Shard-avbildnings galleriet i samma resurs grupp. I det här diagrammet finns de i olika resurs grupper. 

## <a name="subscription"></a>Prenumeration
Din organisation har en eller flera Azure-prenumerationer. En prenumeration används för att hantera fakturering och säkerhet för alla Azure-resources\services som används i den, inklusive Lab-konton.

Relationen mellan ett labb konto och dess prenumeration är viktig eftersom:

- Faktureringen rapporteras via prenumerationen som innehåller labb kontot.
- Du kan ge användare i den Azure Active Directory-klient (AD) som är associerad med prenumerationen åtkomst till Azure Lab Services. Du kan lägga till användaren antingen som ett labb konto owner\contributor eller som ett klass rum av labbet.

Klass rum labb och deras virtuella datorer hanteras helt åt dig. För att vara specifikt, finns de i en dedikerad prenumeration som ägs av Azure Lab Services.

## <a name="resource-group"></a>Resursgrupp
En prenumeration innehåller en eller flera resurs grupper. Resurs grupper används för att skapa logiska grupperingar av Azure-resurser som används tillsammans inom samma lösning.  

När du skapar ett labb konto måste du konfigurera resurs gruppen som innehåller labb kontot. 

Du måste också ha en resurs grupp när du skapar ett [delat avbildnings Galleri](#shared-image-gallery). Du kan välja att ange ditt labb konto och ditt delade avbildnings galleri i två separata resurs grupper, vilket är vanligt om du planerar att dela avbildnings galleriet över olika lösningar. Eller så kan du välja att använda dem i samma resurs grupp.

När du skapar ett labb konto och automatiskt skapar och kopplar ett delat avbildnings Galleri samtidigt, skapas labb kontot och det delade avbildnings galleriet i separata resurs grupper som standard. Du ser det här beteendet när du använder stegen som beskrivs i den här självstudien: [Konfigurera delade avbildnings galleriet när labb kontot skapas](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). Bilden överst i den här artikeln använder också den här konfigurationen. 

Vi rekommenderar att du planerar fram till att planera strukturen för dina resurs grupper eftersom det inte går att ändra ett labb kontos resurs grupp eller delade avbildnings Galleri när den har skapats. Om du behöver ändra resurs gruppen för de här resurserna måste du ta bort och återskapa ditt labb konto och/eller-Galleri för delade avbildningar.

## <a name="lab-account"></a>Labb konto
Ett labb konto fungerar som en behållare för en eller flera klass rums labb. När du kommer igång med Azure Lab Services är det vanligt att bara ha ett enda labb konto. När din labb användning skalas kan du senare välja att skapa fler labb konton.

I följande lista beskrivs scenarier där mer än ett labb konto kan vara fördelaktigt:

- **Hantera olika princip krav i klass rums labb** 

    När du konfigurerar ett labb konto ställer du in principer som gäller för alla klass rums labb under labb kontot, till exempel:
    - Det virtuella Azure-nätverket med delade resurser som klass rummets labb har åtkomst till. Du kan till exempel ha en uppsättning klass rums labb som behöver åtkomst till en delad data uppsättning i ett virtuellt nätverk.
    - De virtuella dator avbildningarna som klass labb labbet kan använda för att skapa virtuella datorer. Du kan till exempel ha en uppsättning klass rums labb som behöver åtkomst till [data science VM för Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) Marketplace-avbildningen.  

    Om du har klass rums labb som har unika princip krav från varandra kan det vara bra att skapa separata labb konton för att hantera dessa klass rums labb separat.
- **Begränsa labb skapare till gång till vissa klass rums labb**  

    När en användare läggs till som en labb skapare får de åtkomst till alla klass rums labb i labb kontot, inklusive labb som skapats av andra labb skapare. Om du vill begränsa labb skapare till att hantera särskilda labb kan du skapa separata labb konton för att begränsa åtkomstens omfattning. Du kan till exempel skapa ett separat labb konto för varje avdelning i ett universitet. Till exempel: ett labb konto för vetenskaps avdelningen och ett annat för matematik avdelningen och så vidare.   
- **Separat budget efter labb konto**

    I stället för att alla klass rums labb kostnader rapporteras för ett enda labb konto, kan du behöva en tydligare budget. Om du fortsätter med exemplet i ovanstående punkt kan du skapa ett labb konto för varje universitets avdelning för att åtskilja budgeten i enlighet med detta. Med hjälp av Azure Cost Management kan du Visa kostnaden för varje enskilt labb konto.
- **Isolera pilot labb från aktiva labb**

    Du kan ha fall där du vill pilot policy ändringar i ett labb konto utan att påverka aktiva labb. I den här typen av scenario kan du skapa ett separat labb konto för pilotering i syfte att isolera ändringar. 

## <a name="classroom-lab"></a>Klass rums labb
Ett klass rums labb innehåller en eller flera virtuella datorer som är tilldelade till en speciell student. I allmänhet kan du förväntar dig att:

- Ha ett klass rums labb för varje klass.
- Skapa en ny uppsättning klass rums labb varje termin (eller för varje tidsram som din klass erbjuds). För klasser som har samma bild måste du använda ett [delat avbildnings Galleri](#shared-image-gallery) för att dela bilder mellan labb och termin.

Tänk på följande när du bestämmer hur du ska strukturera klass rum labb:

- **Alla virtuella datorer i ett klass rum labb distribueras med samma avbildning som publiceras**. Det innebär att om du har en klass som kräver att olika labb avbildningar publiceras samtidigt, måste separata klass rums labb skapas för var och en.
- **Användnings kvoten anges på labb nivån och gäller alla användare i labbet**. Du kan till exempel ha en uppsättning lärare som behöver åtkomst till en Klasss virtuella datorer för att förbereda för undervisning, men lärare kräver bara en kvot på 10 timmar medan studenter som är registrerade i klassen kräver en kvot på 40 timmar. Om du vill ange olika kvoter för användare måste du skapa separata klass rums labb. Det går dock att lägga till fler timmar till en speciell användare efter att du har angett kvoten.
- **Schemat för start eller avstängning anges på labb nivån och gäller för alla virtuella datorer i labbet**. I likhet med föregående punkt, om du behöver ange olika scheman för användare måste du skapa separata klass rums labb. 

## <a name="shared-image-gallery"></a>Galleri för delad avbildning
Ett delat avbildnings galleri är kopplat till ett labb konto och fungerar som en central lagrings plats för att lagra avbildningar. En bild sparas i galleriet när en lärare väljer att spara från en klass rums labbs mall virtuell dator (VM). Varje gången en lärare gör ändringar i mallen VM och sparar, sparas nya versioner av avbildningen samtidigt som tidigare versioner bibehålls.

Lärare kan publicera en avbildnings version från galleriet för delad avbildning när de skapar ett nytt klass rums labb. Även om galleriet kan lagra flera versioner av en avbildning, kan lärare bara välja den senaste versionen under labb skapandet.

Delade avbildnings galleri är en valfri resurs som du kanske inte behöver omedelbart när du startar med bara några klass rums labb. Att använda Shared Image Gallery har dock många fördelar som är användbara när du skalar för att ha fler klass rums labb:

- **Gör att du kan spara och hantera versioner av en mall för virtuella dator avbildningar**.

    Det är användbart när du skapar en anpassad avbildning eller gör ändringar (program vara, konfiguration och så vidare) till en bild från det offentliga Marketplace-galleriet.  Det är till exempel vanligt för lärare att kräva att olika software\tooling ska installeras. I stället för att kräva att studenter installerar dessa krav manuellt på egen hand, kan olika versioner av mallen VM-avbildning sparas i ett delat avbildnings Galleri. Dessa avbildnings versioner kan sedan användas när du skapar nya klass rums labb.
- **Aktiverar sharing\reuse av mallens VM-avbildningar i klass rum labb labb**.

    Det innebär att du inte behöver konfigurera en avbildning från grunden varje gången du skapar ett nytt klass rums labb. Om till exempel flera klasser erbjuds som behöver samma avbildning, behöver avbildningen bara skapas en gång och sparas i det delade bild galleriet så att den kan delas mellan klass rums labb.
- **Garanterar avbildnings tillgänglighet via replikering**.

    När du sparar i det delade bild galleriet från ett klass rums labb, replikeras din avbildning automatiskt till andra regioner i samma geografi. Om det finns ett avbrott för en region påverkas inte publiceringen av mallen VM i klass rummet med hjälp av en avbildnings replik i andra regioner. Dessutom hjälper det till med prestanda i publicerings scenarier med flera virtuella datorer genom att sprida ut för att använda olika repliker.

För att gruppera delade avbildningar logiskt, har du ett par alternativ:

- Skapa flera Galleri för delade avbildningar. Varje labb konto kan bara ansluta till ett delat avbildnings Galleri, så det här alternativet kräver också att du skapar flera labb konton.
- Du kan också använda ett enda delat avbildnings galleri som delas av flera labb konton. I det här fallet kan varje labb konto bara aktivera de avbildningar som är tillämpliga för de klass rums labb som det innehåller.

## <a name="naming"></a>Namngivning
När du kommer igång med Azure Lab Services rekommenderar vi att du skapar namn konventioner för resurs grupper, labb konton, klass rums labb och delade avbildnings Galleri. De namngivnings konventioner som du fastställer är unika för organisationens behov, och följande tabell beskriver allmänna rikt linjer.

| Resurstyp | Roll | Föreslaget mönster | Exempel |
| ------------- | ---- | ----------------- | -------- | 
| Resursgrupp | Innehåller ett eller flera labb konton och ett eller flera gallerier för delade avbildningar | \<organisationens korta namn\>-\<miljö\>-rg<ul><li>**Organisationens kort namn** identifierar namnet på den organisation som resurs gruppen stöder</li><li>**Miljö** identifierar resursens miljö, till exempel test eller produktion</li><li>**RG** står för resurs typen: resurs grupp.</li></ul> | contosouniversitylabs – rg<br/>contosouniversitylabs-test-rg<br/>contosouniversitylabs-Prod-rg |
| Labb konto | Innehåller en eller flera labb | \<organisationens korta namn\>-\<miljö\>-La<ul><li>**Organisationens kort namn** identifierar namnet på den organisation som resurs gruppen stöder</li><li>**Miljö** identifierar resursens miljö, till exempel test eller produktion</li><li>**La** står för resurs typen: labb konto.</li></ul> | contosouniversitylabs-La<br/>mathdeptlabs-La<br/>sciencedeptlabs-test-La<br/>sciencedeptlabs-Prod-La |
| Klass rums labb | Innehåller en eller flera virtuella datorer |\<klass namn\>-\<tidsram\>-\<lärare-ID\><ul><li>**Klass namn** identifierar namnet på klassen som labbet stöder.</li><li>**Tidsram** identifierar den tidsram i vilken klassen erbjuds.</li>**Utbildnings identifierare** identifierar den lärare som äger labbet.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe | 
| Galleri för delad avbildning | Innehåller en eller flera versioner av virtuella dator avbildningar | \>Galleri för \<organisationens kort namn | contosouniversitylabsgallery |

Mer information om hur du namnger andra Azure-resurser finns i [namngivnings konventioner för Azure-resurser](/azure/architecture/best-practices/naming-conventions).

## <a name="regions-or-locations"></a>Regioner eller platser
När du konfigurerar Azure Lab Services resurser måste du ange en region eller plats för det data Center som ska vara värd för resursen. Här är mer information om hur region\location påverkar var och en av följande resurser som används i din labb tjänst distribution:

- **Resursgrupp**

    Regionen anger data centret där information om resurs gruppen lagras. Azure-resurser som ingår i resurs gruppen kan finnas i olika regioner från deras överordnade platser.
- **Labb konto eller klass rums labb**

    Labb kontots plats anger regionen för den här resursen. Klass rums labb som skapats i labb kontot kan distribueras till vilken region som helst inom samma geografi. Den speciella region som Labbets virtuella datorer distribueras till väljs automatiskt baserat på tillgänglig kapacitet i regionen vid denna tidpunkt.  
    Om en administratör gör det möjligt för labb skapare att välja deras plats för klass rums labb, baseras de platser som är tillgängliga för val av tillgänglig regional kapacitet när du skapar labbet.

    Platsen för klass rums labbet avgör också vilka beräknings storlekar för virtuella datorer som kan väljas. Vissa beräknings storlekar är bara tillgängliga på specifika platser.
- **Galleri för delad avbildning**

    Regionen anger käll regionen där den första avbildnings versionen lagras innan den replikeras automatiskt till mål regionerna.
    
En allmän regel är att ange en resurs region\location till en som är närmast dess användare. För klass rums labb innebär det att skapa klass rums labbet närmast dina studenter. För onlinekurser där studenterna är placerade över hela världen, måste du använda det bästa omdömet för att skapa ett klass rums labb som finns centralt. Eller dela upp en klass i flera klass rums labb som baseras på din studenters region.

## <a name="vm-sizing"></a>Storlek på virtuell dator
När administratörer eller labb skapare skapar ett klass rums labb, kan de välja mellan följande VM-storlekar baserat på behoven i deras klass rum. Kom ihåg att de tillgängliga beräknings storlekarna är beroende av den region där ditt labb konto finns:

| Storlek | Specifikationer | Föreslagen användning |
| ---- | ----- | ------------- |
| Liten| <ul><li>2 kärnor</li><li>3.5 GB RAM-minne</li></ul> | Den här storleken passar bäst för kommando rad, öppna webbläsare, webb servrar med låg trafik, små till medel stora databaser. |
| Medium | <ul><li>4 kärnor</li><li>7 GB RAM-minne</li></ul> | Den här storleken passar bäst för Relations databaser, minnes intern cachelagring och analys. |
| Medium (kapslad virtualisering) | <ul><li>4 kärnor</li><li>16 GB RAM</li></ul> | Den här storleken passar bäst för Relations databaser, minnes intern cachelagring och analys.  Den här storleken stöder även kapslad virtualisering. |
| Stor | <ul><li>8 kärnor</li><li>32 GB RAM</li></ul> | Den här storleken lämpar sig bäst för program som behöver snabbare processorer, bättre prestanda för lokala diskar, stora databaser, stora cacheminnen.  Den här storleken stöder även kapslad virtualisering. |
| Liten GPU (visualisering) | <ul><li>6 kärnor</li><li>56 GB RAM</li> | Den här storleken passar bäst för fjärrvisualisering, strömning, spel, kodning med hjälp av ramverk som OpenGL och DirectX. |
| Liten GPU (Compute) | <ul><li>6 kärnor</li><li>56 GB RAM</li></ul> |Den här storleken passar bäst för dator intensiva program som artificiell intelligens och djup inlärning. |
| Medelhög GPU (visualisering) | <ul><li>12 kärnor</li><li>112 GB RAM</li></ul> | Den här storleken passar bäst för fjärrvisualisering, strömning, spel, kodning med hjälp av ramverk som OpenGL och DirectX. |

## <a name="manage-identity"></a>Hantera identitet
Det finns två typer av roller som en labb konto administratör kan ha:

- **Ägare**

    En administratör som har tilldelats **ägar** rollen har fullständig åtkomst till labb kontot, inklusive rätten att ge andra användare åtkomst till labb kontot och lägga till labb skapare. Administratören som skapar labb kontot som standard läggs till som ägare.
- **Deltagare**

    En administratör som har tilldelats rollen deltagare kan ändra labb konto inställningar, men de kan inte bevilja åtkomst till andra användare. eller kan de lägga till labb skapare.

När du ansluter ett delat avbildnings galleri till ett labb konto beviljas åtkomst automatiskt till både administratören och Labbets skapare så att de kan visa och spara bilder i galleriet. 

## <a name="pricing"></a>Prissättning

### <a name="azure-lab-services"></a>Azure Lab Services
Prissättningen för Azure Lab Services beskrivs i följande artikel: [Azure Lab Services prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

Du måste också beakta priserna för galleriet för delad avbildning om du planerar att använda det för att lagra och hantera avbildnings versioner. 

### <a name="shared-image-gallery"></a>Galleri för delad avbildning
Det kostar inget att skapa ett delat avbildnings galleri och koppla det till ditt labb konto. Kostnaderna uppstår tills du sparar en avbildnings version i galleriet. Normalt är prissättningen för att använda ett delat avbildnings Galleri ganska försumbar, men det är viktigt att förstå hur det beräknas eftersom det inte ingår i prissättningen för Azure Lab Services.  

### <a name="storage-charges"></a>Lagrings kostnader
Om du vill lagra avbildnings versioner använder ett delat avbildnings Galleri standard diskar som hanteras av hård diskar. Storleken på den HDD-hanterade disk som används beror på storleken på avbildnings versionen som lagras. Se följande artikel för att se prissättningen priser: [Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).


### <a name="replication-and-network-egress-charges"></a>Utgående kostnader för replikering och nätverk
När du sparar en avbildnings version med hjälp av en virtuell dator i klass rummet i ett klass rum, lagrar Lab-tjänster den först i en käll region och replikerar sedan automatiskt käll avbildnings versionen till en eller flera mål regioner. Det är viktigt att Observera att Azure Lab Services automatiskt replikerar käll avbildnings versionen till alla mål regioner inom den plats där klass rummets labb finns. Om ditt klass rums labb till exempel finns på den amerikanska platsen, replikeras en avbildnings version till var och en av de åtta regionerna i USA

Nätverks utgående belastning sker när en avbildnings version replikeras från käll regionen till ytterligare mål regioner. Det debiterade beloppet baseras på avbildnings versionens storlek när avbildningens data först överförs utgående från käll regionen.  Pris information finns i följande artikel: [pris information för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/).

Kunder med [utbildnings lösningar](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) kan avstå från att betala utgående kostnader. Prata med din konto ansvarige om du vill veta mer.  Mer information finns i avsnittet **vanliga frågor och svar** i det länkade dokumentet, särskilt frågan "vilka data överförings program finns för akademiska kunder och hur kvalificerar jag?").

### <a name="pricing-example"></a>Prisexempel
För att Sammanfattning de priser som beskrivs ovan ska vi titta på ett exempel på hur du sparar vår VM-avbildning för mallen i det delade avbildnings galleriet. Antag följande scenarier:

- Du har en anpassad VM-avbildning.
- Du håller på att spara två versioner av avbildningen.
- Ditt labb är i USA, som har totalt åtta regioner.
- Varje avbildnings version är 32 GB i storlek. Därför är hård disk-Managed disk pris $1,54 per månad.

Den totala kostnaden beräknas som:

Antal avbildningar × antal versioner × antalet repliker × Managed disk pris

I det här exemplet är kostnaden:

1 anpassad bild (32 GB) x 2 versioner x 8 amerikanska regioner x $1,54 = $24,64 per månad

### <a name="cost-management"></a>Kostnadshantering
Det är viktigt för labb konto administratören att hantera kostnader genom att rutinmässigt ta bort onödiga bild versioner från galleriet. 

Du bör inte ta bort replikeringen till vissa regioner som ett sätt att minska kostnaderna (det här alternativet finns i delade avbildnings galleriet). Ändringar i replikeringen kan ha skadliga effekter på Azure Lab Services möjlighet att publicera virtuella datorer från avbildningar som sparats i ett delat avbildnings Galleri.

## <a name="next-steps"></a>Nästa steg
I självstudien finns stegvisa anvisningar för att skapa ett labb konto och ett labb: [Självstudier: Konfigurera ett labb konto](tutorial-setup-lab-account.md)
