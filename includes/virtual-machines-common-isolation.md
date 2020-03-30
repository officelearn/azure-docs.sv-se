---
title: ta med fil
description: ta med fil
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 3aeb0369ee4a04dbbe89a4d0684b2a6c97378d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123238"
---
Azure Compute erbjuder storlekar för virtuella datorer som är isolerade till en viss maskinvarutyp och dedikerade till en enskild kund.  Dessa storlekar för virtuella datorer är bäst lämpade för arbetsbelastningar som kräver en hög grad av isolering från andra kunder för arbetsbelastningar som involverar element som efterlevnad och lagstadgade krav.  Kunder kan också välja att ytterligare dela upp resurserna för dessa isolerade virtuella datorer med hjälp av [Azure-stöd för kapslade virtuella datorer](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Om du använder en isolerad storlek garanteras att den virtuella datorn är den enda som körs på den specifika serverinstansen.  De aktuella isolerade virtuella datorerbjudandena inkluderar:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Du kan läsa mer om varje tillgänglig isolerad storlek [här](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>Pensionering D15_v2/DS15_v2 isolering den 15 maj 2020
**Uppdatering den 10 februari 2020: "isolering" pensionering tidslinje har förlängts till 15 maj 2020"**

Azure Dedicated Host är nu GA, vilket gör att du kan köra organisationens virtuella Linux- och Windows-datorer på fysiska servrar med en klient. Vi planerar att helt ersätta isolerade virtuella Azure-datorer med Azure Dedicated Host. Efter **den 15 maj 2020** kommer D15_v2/DS15_v2 virtuella Azure-datorer inte längre att vara maskinvaru isolerade.

## <a name="how-does-this-affect-me"></a>Hur påverkar det här mig?
Efter den 15 maj 2020 tillhandahåller vi inte längre någon isoleringsgaranti för dina D15_v2/DS15_v2 virtuella Azure-datorer. 

## <a name="what-actions-should-i-take"></a>Vilka åtgärder ska jag vidta?
Om maskinvaruisolering inte krävs för dig behöver du inte vidta någon åtgärd. 

Om isolering krävs för dig, före den 15 maj 2020, skulle du behöva antingen:

• [Migrera](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) din arbetsbelastning till Azure Dedicated Host.

• [Begär åtkomst](https://aka.ms/D15iRequestAccess) till en D15i_v2 och DS15i_v2 Azure VM för att få samma prisprestanda. Det här alternativet är endast tillgängligt för scenarier med sparade förekomster per användning och ett år.    

• [Migrera](https://azure.microsoft.com/blog/resize-virtual-machines/) din arbetsbelastning till en annan Azure-isolerad virtuell dator. 

För mer information se nedan:

## <a name="timeline"></a>Tidslinje
| Datum | Åtgärd | 
| --- | --- |
| Den 18 november 2019 | Tillgång till D/DS15i_v2 (PAYG, 1-års RI) |
| Den 14 maj 2020  | Sista dagen att köpa D / DS15i_v2 1-års RI | 
| Den 15 maj 2020   | D/DS15_v2 isoleringsgaranti borttagen | 
| den 15 maj 2021  | Pensionera D/DS15i_v2 (alla kunder utom som köpt 3-års RI D/DS15_v2 före den 18 november 2019)| 
| Den 17 november 2022  | Gå i pension D/DS15i_v2 när 3-åriga RU:er gjort (för kunder som köpt 3-års RI D/DS15_v2 före den 18 november 2019) | 

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>F: Är storleken D / DS15_v2 kommer att få pension?
**A**: Nej, bara "isolering" funktionen kommer att få pensionerade. Om du inte behöver isolering behöver du inte vidta några åtgärder.

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>F: Är storleken D / DS15i_v2 kommer att få pension?
**S**: Ja, storleken är endast tillgänglig fram till 15 maj 2021. För kunder som har köpt 3-åriga RIs på D/DS15_v2 före den 18 november 2019 kommer att ha tillgång till D/DS15i_v2 fram till den 17 november 2022.

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>F: Varför ser jag inte de nya D/DS15i_v2 storlekarna i portalen?
**A**: Om du är en aktuell D/DS15_v2 kund och vill använda de nya D/DS15i_v2 storlekarna fyller du i det här [formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>F: Varför ser jag ingen kvot för de nya D/DS15i_v2 storlekarna?
**A**: Om du är en aktuell D/DS15_v2 kund och vill använda de nya D/DS15i_v2 storlekarna fyller du i det här [formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>F: När går de andra isolerade storlekarna i pension?
**A**: Vi kommer att ge påminnelser 12 månader före den officiella avvecklingen av storlekarna.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>F: Finns det ett driftstopp när min vm landar på en icke-isolerad maskinvara?
**S:** Om du inte behöver isolering behöver du inte vidta några åtgärder och du skulle inte se några driftstopp.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>F: Finns det några kostnadsändringar för att flytta till en icke-isolerad virtuell dator?
**A**: Nej 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>F: Jag har redan köpt en eller tre års reserverad instans för D15_v2 eller Ds15_v2. Hur tillämpas rabatten på min vm-användning?
**A**: RIs som köpts före den 18 november 2019 utökar automatiskt täckningen till den nya isolerade VM-serien. 

| RI |  Flexibilitet för instansstorlek | Berättigande till förmån |   
| --- | --- | --- |
|   D15_v2  |   Av     |   D15_v2 och D15i_v2 |    
|   D15_v2  |   På  |   D15_v2 serien och D15i_v2 kommer alla att få RI-förmånen. |    
|   D14_v2  |   På  |   D15_v2 serien och D15i_v2 kommer alla att få RI-förmånen. |    
 
Likaså för Dsv2-serien.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>F: Jag vill köpa ytterligare reserverade instanser för Dv2. Vilken ska jag välja?
**A**: Alla RIs som köpts efter 18 november 2019 har följande beteende. 

| RI |  Flexibilitet för instansstorlek | Berättigande till förmån |   
| --- | --- | --- |
| D15_v2 |  Av |   endast D15_v2  
| D15_v2 |  På |    D15_v2-serien kommer att få RI-förmånen. Den nya D15i_v2 kommer inte att vara berättigade till RI dra nytta av denna RI typ. | 
| D15i_v2 |     Av | endast D15i_v2 |  
| D15i_v2 |     På  | endast D15i_v2 | 
 
Det går inte att använda instansstorleksflexibilitet för att använda andra storlekar, till exempel D2_v2, D4_v2 eller D15_v2. Likaså för Dsv2-serien.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>F: Kan jag köpa en ny 3-årig RI för D15i_v2 och DS15i_v2?
**A**: Tyvärr nej, endast 1-åriga RI finns att köpa.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>F: Kan jag flytta min befintliga D15_v2/DS15_v2-reservinstansen till en reserverad instans av en isolerad storlek?
**S**: Denna åtgärd är inte nödvändig eftersom förmånen kommer att gälla för både isolerade och icke-isolerade storlekar. Men Azure stöder att ändra befintliga D15_v2/DS15_v2 reserverade instanser till D15i_v2/DS15i_v2. För alla andra reserverade instanser i Dv2/Dsv2 använder du den befintliga reserverade instansen eller köper nya reserverade instanser för de isolerade storlekarna.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>F: Jag är en Azure Service Fabric-kund som förlitar sig på silver- eller guldhållbarhetsnivåerna. Påverkar den här förändringen mig?
**A**: Nej. Garantierna från Service Fabric's [Durability Tiers](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) kommer att fortsätta att fungera även efter den här ändringen. Om du kräver fysisk maskinvaruisolering av andra skäl kan du fortfarande behöva vidta någon av de åtgärder som beskrivs ovan. 
