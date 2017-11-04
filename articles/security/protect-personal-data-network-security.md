---
title: "Skydda dina personliga data med Azure funktioner för nätverkssäkerhet | Microsoft Docs"
description: "Skydda personliga data med Azure funktioner för nätverkssäkerhet"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: d61b29f1327f57bc32b2c53de3fe58e53fcf3cac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="protect-personal-data-with-network-security-features-azure-application-gateway-and-network-security-groups"></a>Skydda dina personliga data med funktioner för nätverkssäkerhet: Azure Application Gateway och Nätverkssäkerhetsgrupper

Den här artikeln innehåller information och procedurer som hjälper dig att använda Azure Application Gateway och Nätverkssäkerhetsgrupper för att skydda personliga data.

En viktig del i en flera lager säkerhetsstrategi integritetsskydd personliga data är skydd mot vanliga säkerhetsproblem kryphål, till exempel SQL injection eller globala webbplatsskript. Att hålla oönskad trafik utanför din Azure virtuellt nätverk skyddar mot potentiella hot av känsliga data och Microsoft Azure ger dig verktyg för att skydda dina data mot angripare.

## <a name="scenario"></a>Scenario

Ett stort kryssning företag, sitt säte i USA utökar åtgärderna för att erbjuda färdvägar i Medelhavet, Adriatiska havet, baltiska havet samt Förenta staterna. För att främja dessa åtgärder, har det fått flera mindre kryssning rader i Italien, Tyskland, Danmark och Storbritannien

Företaget använder Microsoft Azure för att lagra företagets data i molnet och köra program på virtuella datorer som bearbetar och komma åt dessa data. Dessa data innehåller personligt identifierbar information, till exempel namn, adresser, telefonnummer och kreditkortsinformation av dess globala kundbas. Den innehåller också traditionella personal information, till exempel adresser, telefonnummer, skatt ID-nummer och annan information om företagets anställda på alla platser. Raden kryssning har också en stor databas med medlemmar av trafik och förmåner som innehåller personuppgifter för att spåra relationer med aktuella och tidigare kunder.

Företagets anställda åtkomst till nätverket från företagets fjärranslutna kontor och resa agenter finns runtom i världen har åtkomst till vissa företagsresurser och använda webbaserade program som finns i virtuella Azure-datorer kan interagera med den.

## <a name="problem-statement"></a>Problembeskrivning

Företaget måste skydda kundernas och anställdas personliga data från angripare som utnyttjar sårbarheter programvara för att köra skadlig kod som kan uttsätta personliga data lagras eller används av företagets molnbaserade program.

## <a name="company-goal"></a>Företagets mål

Företagets mål så att obehöriga inte kommer åt företagets virtuella Azure-nätverk och program och data som finns där genom att utnyttja vanliga säkerhetsproblem. 

## <a name="solutions"></a>Lösningar

Microsoft Azure tillhandahåller säkerhetsmekanismer som förhindrar oönskad trafik från att ange virtuella Azure-nätverk. Kontroll av inkommande och utgående trafik utförs traditionellt genom brandväggar. Du kan använda Application Gateway med Brandvägg för webbaserade program och Nätverkssäkerhetsgrupp grupper (NSG), som fungerar som en enkel distribuerade brandväggen i Azure. Dessa verktyg kan du identifiera och blockera oönskad trafik.

### <a name="application-gatewayweb-application-firewall"></a>Brandvägg för programmet Gateway/webbaserade program

Den [Brandvägg för webbaserade program](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (Brandvägg) komponent i den [Azure Programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) skyddar webbprogram som är mer och mer riktad mot angrepp som drar nytta av vanliga kända säkerhetsproblem. En centraliserad Brandvägg skyddar mot webbattacker och säkerhetshanteringen utan några ändringar i programmet.

Azure Brandvägg adresser olika attack kategorier inklusive SQL injection, över flera webbplatser, HTTP-protokollet överträdelser och avvikelser, robotar, robotar, skannrar, vanliga felinställningar för programmet, http-Denial of Service och andra vanliga attacker som kommandot injection HTTP-begäran smuggling HTTP-svar dela och Fjärrlagring inkludering attacker. 

Du kan skapa en Programgateway med Brandvägg eller Lägg till Brandvägg i en befintlig gateway för programmet. I båda fallen kräver Azure Programgateway sitt eget undernät.

#### <a name="how-do-i-create-an-application-gateway-with-waf"></a>Hur skapar jag en Programgateway med Brandvägg? 

Om du vill skapa en ny Programgateway med Brandvägg är aktiverad, gör du följande:

1. Logga in på Azure-portalen och i den **Favoriter** i portalen klickar du på **ny**

2. Klicka på **Nätverk** i bladet **Nytt**.

3. Klicka på **Programgateway**.

4. Gå till Azure-portalen **klickar du på nytt \> nätverk \> Application Gateway.**

   ![Skapa programgatewayer](media/protect-netsec/app-gateway-01.png)

5. I den **grunderna** bladet som visas, ange värden för följande fält: namn, tjänstnivån (Standard eller Brandvägg) SKU-storleken (små, medelstora eller stora) instansen antalet (2 för hög tillgänglighet), prenumeration, resursgrupp och plats.

6. I den **inställningar** bladet som visas under **för virtuella nätverk**, klickar du på **Välj ett virtuellt nätverk**. Det här steget anger du öppnas bladet välj virtuella nätverk.

7. Klicka på **Skapa nytt** att öppna den **skapa virtuellt nätverk** bladet.

8. Ange följande värden: namn, adressutrymme, undernätsnamn, adressintervallet i undernätet. Klicka på **OK**.

9. På den **inställningar** bladet under **Frontend-IP-konfiguration**, Välj typ av IP-adress.

10. Klicka på **Välj en offentlig IP-adress** sedan **Skapa nytt.**

11. Acceptera standardvärdet och på **OK.**

12. På den **inställningar** bladet under **Lyssnarkonfigurationen**väljer att använda HTTP eller HTTPS enligt **protokollet**. Ett certifikat krävs för att använda HTTPS.

13. Konfigurera de specifika inställningarna för Brandvägg: **brandväggen status** (**aktiverad**) och **brandväggsläge** (**förebyggande**). Om du väljer **identifiering** som läge, loggas endast trafik.

14. Granska de **sammanfattning** och klickar på **OK**. Programgatewayen är nu i kö och skapas.

När programgatewayen har skapats kan du navigera till den i portalen och fortsätta konfigurationen av programgatewayen.

![skapade Programgateway](media/protect-netsec/adatum-app-gateway.png)

#### <a name="how-do-i-add-waf-to-an-existing-application"></a>Hur lägger jag till Brandvägg till ett befintligt program?

Om du vill uppdatera en befintlig Programgateway för att stödja Brandvägg i förebyggande läge, gör du följande:

1. Klicka på **Alla resurser** i rutan **Favoriter** i Azure Portal.

2. Klicka på den befintliga gatewayen program i den **alla resurser** bladet. 
>[!NOTE]
Obs: Om den prenumeration som du har valt redan har flera resurser i den, du kan ange namnet i filtret efter namn... när du ska hitta din DNS-zon.
3. Klicka på **Brandvägg för webbaserade program** och uppdatera gatewayen programinställningar: **uppgradera Brandvägg nivån** (markerad) **brandväggen status** (aktiverat)  **Brandväggsläge** (förhindra). Du måste också konfigurera regelinställningen och konfigurera inaktiverade regler.

Mer detaljerad information om hur du skapar en ny Programgateway med Brandvägg och hur du lägger till en befintlig Programgateway Brandvägg finns [skapa en Programgateway med Brandvägg för webbaserade program med hjälp av portalen.](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

En [nätverkssäkerhetsgruppen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) innehåller en lista över säkerhetsregler som tillåter eller nekar nätverkstrafik till resurser som är anslutna till [Azure Virtual Networks](https://docs.microsoft.com/azure/virtual-network/) (VNet). NSG: er kan vara kopplad till undernät eller individuella virtuella datorer. När en nätverkssäkerhetsgrupp är kopplad till ett undernät gäller reglerna för alla resurser som är anslutna till undernätet. Trafiken kan begränsas ytterligare genom att en nätverkssäkerhetsgrupp associeras med en virtuell dator eller ett nätverkskort.

NSG: er innehåller fyra egenskaper: namn, Region, resursgruppen och regler.
>[!Note]
Även om en nätverkssäkerhetsgrupp finns i en viss resursgrupp kan den vara kopplad till resurser i valfri resursgrupp, förutsatt att resursen tillhör samma Azure-region som nätverkssäkerhetsgruppen.

NSG-regler innehåller nio egenskaper: namn, protokoll (TCP, UDP eller \*, som inkluderar ICMP samt UDP och TCP), källa portintervall, Målportintervall, källadress-prefix, måladress-prefix, riktning (inkommande eller utgående), prioritet ( mellan 100 och 4096) och komma åt typen (Tillåt eller neka). Alla NSG: er innehåller en uppsättning standardregler som kan tas bort eller åsidosätts av regler som du skapar.

#### <a name="how-do-i-implement-nsgs"></a>Hur jag implementera NSG: er?

Implementera NSG: er kräver planering och det finns flera designöverväganden som du behöver ta hänsyn till. Dessa omfattar begränsningar för antalet NSG: er per prenumeration och regler per NSG; Utformning av VNet och undernät, särskilda regler, ICMP-trafik, isolering av nivåer med undernät och belastningsutjämnare.

Mer information i Planera och implementera NSG: er och ett exempelscenario för distribution finns [filtrera nätverkstrafik med nätverkssäkerhetsgrupper.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)

#### <a name="how-do-i-create-rules-in-an-nsg"></a>Hur skapar regler i en NSG?

För att skapa regler för inkommande trafik i en befintlig NSG, gör du följande:

1. Klicka på **Bläddra**, och sedan **Nätverkssäkerhetsgrupper**.

2. I listan över NSG: er, klickar du på **NSG-klientdel**, och sedan **inkommande säkerhetsregler.**

3. I listan över ingående säkerhetsregler, klickar du på **Lägg till.**

4. Ange värden i följande fält: namn, prioritet, källa, protokoll, källa intervallet, mål, mål portintervall och åtgärden.

Den nya regeln visas i NSG: N efter några sekunder.

![Nätverkssäkerhetsregler](media/protect-netsec/inbound-security.png)

Mer information om hur du skapar NSG: er i undernät, skapa regler och koppla en NSG till ett frontend och backend-undernät, finns [skapa nätverkssäkerhetsgrupper med Azure-portalen.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)

## <a name="next-steps"></a>Nästa steg

[Azure nätverkssäkerhet](https://azure.microsoft.com/blog/azure-network-security/)

[Säkerhetsmetoder för Azure-nätverk](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)

[Hämta information om en nätverkssäkerhetsgrupp](https://docs.microsoft.com/rest/api/network/virtualnetwork/get-information-about-a-network-security-group)

[Brandvägg för webbaserade program (Brandvägg)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)
