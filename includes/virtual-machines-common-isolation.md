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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77123238"
---
Azure Compute erbjuder storlekar för virtuella datorer som är isolerade till en viss maskin varu typ och som är dedikerad till en enda kund.  Dessa storlekar på virtuella datorer lämpar sig bäst för arbets belastningar som kräver en hög grad av isolering från andra kunder för arbets belastningar som inbegriper element som efterlevnad och myndighets krav.  Kunder kan också välja att ytterligare dela upp resurserna i de isolerade virtuella datorerna med hjälp av [Azure-stöd för kapslade virtuella datorer](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Användning av en isolerad storlek garanterar att den virtuella datorn är den enda som körs på den aktuella Server instansen.  De aktuella isolerade virtuella dator erbjudandena är:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Du kan lära dig mer om var och en av de tillgängliga isolerade storlekarna [här](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>Ta bort D15_v2/DS15_v2-isolering den 15 maj 2020
**Uppdatering den 10 februari 2020: tids linjen "isolering" har utökats till 15 maj 2020 "**

Den dedikerade Azure-värden är nu GA, som gör att du kan köra din organisations virtuella Linux-och Windows-datorer på fysiska servrar för enskild klient. Vi planerar att helt ersätta isolerade virtuella Azure-datorer med dedikerad Azure-värd. Efter den **15 maj 2020** kommer de virtuella Azure-datorerna för D15_v2/DS15_v2 inte längre vara isolerade.

## <a name="how-does-this-affect-me"></a>Hur påverkar det här mig?
Efter den 15 maj 2020 kommer vi inte längre att tillhandahålla en isolerings garanti för dina D15_v2/DS15_v2 Azure Virtual Machines. 

## <a name="what-actions-should-i-take"></a>Vilka åtgärder ska jag göra?
Om maskin varu isolering inte krävs för dig behöver du inte göra något. 

Om isolering krävs till dig före den 15 maj 2020 behöver du antingen:

• [Migrera](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) din arbets belastning till Azure-dedikerad värd.

• [Begär åtkomst](https://aka.ms/D15iRequestAccess) till en D15i_v2 och DS15I_V2 Azure VM för att få samma pris prestanda. Det här alternativet är endast tillgängligt för scenarier med betala per användning och ett år med reserverade instanser.    

• [Migrera](https://azure.microsoft.com/blog/resize-virtual-machines/) din arbets belastning till en annan virtuell Azure-dator. 

Mer information finns nedan:

## <a name="timeline"></a>Tidslinje
| Date | Action | 
| --- | --- |
| Den 18 november 2019 | Tillgänglighet för D/DS15i_v2 (PAYG, 1 – år, RI) |
| 14 maj, 2020  | Senaste dagen för att köpa D/DS15i_v2 1 års RI | 
| 15 maj, 2020   | D/DS15_v2 isolerings garanti borttagen | 
| 15 maj, 2021  | Dra tillbaka D/DS15i_v2 (alla kunder, förutom de som köpt 3-års RI-D/DS15_v2 före 18 november 2019)| 
| 17 november 2022  | Dra tillbaka D/DS15i_v2 när 3 års RIs har slutförts (för kunder som köpte 3-års RI för D/DS15_v2 före 18 november 2019) | 

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>F: är storleken D/DS15_v2 att bli tillbakadragen?
**A**: Nej, funktionen "isolering" kommer att tas ur bruk. Om du inte behöver isolera behöver du inte vidta några åtgärder.

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>F: är storleken D/DS15i_v2 att bli tillbakadragen?
**A**: Ja, storleken är bara tillgänglig förrän 15 maj 2021. För kunder som har köpt tre års RIs på D/DS15_v2 före 18 november 2019 får du till gång till D/DS15i_v2 fram till den 17 november 2022.

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>F: Varför ser jag inte de nya D/DS15i_v2-storlekarna i portalen?
S **: om**du är aktuell d/DS15_v2-kund och vill använda de nya d/DS15i_v2-storlekarna fyller du i det här [formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>F: Varför ser jag inte någon kvot för de nya D/DS15i_v2-storlekarna?
S **: om**du är aktuell d/DS15_v2-kund och vill använda de nya d/DS15i_v2-storlekarna fyller du i det här [formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>F: När är de andra isolerade storlekarna som ska tas ur bruk?
S **: vi**kommer att lämna påminnelser 12 månader i förväg om den officiella inställningen av storlekarna.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>F: finns det en nedtid när min virtuella dator hamnar på en icke isolerad maskin vara?
**A**: om du inte behöver isolera behöver du inte utföra några åtgärder och du kan inte se några stillestånds tider.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>F: kostar några kostnads ändringar att flytta till en icke isolerad virtuell dator?
**A**: Nej 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>F: Jag har redan köpt 1-eller 3-års reserverad instans för D15_v2 eller Ds15_v2. Hur kommer rabatten att tillämpas på min VM-användning?
**A**: Fjärrinstallationstjänster som köpts före 18 november 2019 kommer automatiskt att utöka täckningen till den nya ISOLERAde VM-serien. 

| RI |  Flexibilitet för instans storlek | Förmåns berättigande |   
| --- | --- | --- |
|   D15_v2  |   Av     |   D15_v2 och D15i_v2 |    
|   D15_v2  |   På  |   D15_v2 serien och D15i_v2 får alla en RI-förmån. |    
|   D14_v2  |   På  |   D15_v2 serien och D15i_v2 får alla en RI-förmån. |    
 
På samma sätt för Dsv2-serier.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>F: Jag vill köpa ytterligare reserverade instanser för Dv2. Vilken av dem ska jag välja?
**A**: alla köpta licenser efter 18 november 2019 har följande funktion. 

| RI |  Flexibilitet för instans storlek | Förmåns berättigande |   
| --- | --- | --- |
| D15_v2 |  Av |   Endast D15_v2  
| D15_v2 |  På |    D15_v2 serien får RI-förmånen. Nya D15i_v2 är inte berättigade till RI-förmån från den här typen av RI. | 
| D15i_v2 |     Av | Endast D15i_v2 |  
| D15i_v2 |     På  | Endast D15i_v2 | 
 
Flexibilitet för instans storlek kan inte användas för andra storlekar, till exempel D2_v2, D4_v2 eller D15_v2. På samma sätt för Dsv2-serier.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>F: kan jag köpa en ny 3-års RI för D15i_v2 och DS15i_v2?
**A**: Tyvärr Nej, endast 1-års RI är tillgänglig för nytt köp.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>F: kan jag flytta min befintliga D15_v2/DS15_v2 reserv instans till en reserverad instans för isolerad storlek?
**A**: den här åtgärden är inte nödvändig eftersom förmånen gäller både isolerade och icke-isolerade storlekar. Men Azure har stöd för att ändra befintliga D15_v2/DS15_v2 reserverade instanser till D15i_v2/DS15i_v2. För alla andra Dv2/Dsv2-reserverade instanser använder du den befintliga reserverade instansen eller köper nya reserverade instanser för de isolerade storlekarna.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>F: Jag är en Azure Service Fabric-kund som förlitar sig på silver-eller Gold-nivåerna. Påverkar den här ändringen mig?
**A**: Nej. De garantier som tillhandahålls av Service Fabrics [hållbarhets nivåer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) kommer att fortsätta att fungera även efter den här ändringen. Om du behöver en fysisk maskin varu isolering av andra orsaker kan du fortfarande behöva vidta en av de åtgärder som beskrivs ovan. 
