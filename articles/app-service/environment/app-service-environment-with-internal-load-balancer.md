---
title: Skapa och använda intern belastningsutjämnare med App Service environment - Azure | Microsoft Docs
description: Skapa och använda en ASE med en ILB
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 88f100bc780d8df0202cfcce9b390085a71fc905
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130610"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Med hjälp av en intern belastningsutjämnare med en App Service Environment

> [!NOTE] 
> Den här artikeln handlar om App Service Environment v1. Det finns en nyare version av App Service Environment som är enklare att använda och körs på kraftfullare infrastruktur. Mer information om den nya versionen början med den [introduktion till App Service Environment](intro.md).
>

Funktionen App Service Environment (ASE) är ett Premium-tjänsten i Azure App Service som ger en förbättrad configuration-funktion som inte är tillgänglig i stämplarna för flera innehavare. ASE-funktionen i stort sett distribuerar Azure App Service i dina Azure virtuellt nätverk (vnet). Att få bättre förståelse av de funktioner som erbjuds av App Service-miljöer läsa den [vad är en App Service Environment] [ WhatisASE] dokumentation. Om du inte vet fördelarna med verksamma i ett virtuellt nätverk Läs den [virtuella nätverk vanliga frågor om Azure][virtualnetwork]. 

## <a name="overview"></a>Översikt
En ASE kan distribueras med en internet-tillgänglig slutpunkt eller med en IP-adress i ditt virtuella nätverk. Du behöver distribuera din ASE med en intern belastning Balancer(ILB) för att ange IP-adress till en VNet-adress. När din ASE konfigurerad med en ILB, anger du:

* din egen domän eller underdomän. Om du vill göra det enklare, det här dokumentet förutsätts underdomän men du kan konfigurera det i båda fallen. 
* certifikatet som används för HTTPS
* DNS management for your subdomain. 

I utbyte kan du göra saker om att:

* vara värd för intranätprogram som line-of-business-program på ett säkert sätt i molnet som du kommer åt via en plats till plats eller ExpressRoute VPN
* vara värd för appar i molnet som inte listas i offentliga DNS-servrar
* Skapa internet-isolerade serverdel appar som dina appar för klientdelar säkert kan integrera

#### <a name="disabled-functionality"></a>Inaktiverad funktion
Det finns några saker som du inte kan göra när du använder en ILB ASE. Dessa saker är:

* med hjälp av IPSSL
* tilldela IP-adresser till specifika appar
* köpa och använda ett certifikat med en app via portalen. Du kan naturligtvis fortfarande hämta certifikat direkt med en certifikatutfärdare och använda den med dina appar, men inte via Azure portal.

## <a name="creating-an-ilb-ase"></a>Skapa en ILB ASE
Skapa en ILB ASE skiljer sig inte mycket från att skapa en ASE vanligtvis. En djupare diskussion om hur du skapar en ASE finns i [så här skapar du en App Service Environment][HowtoCreateASE]. Processen för att skapa en ILB ASE är samma mellan skapar ett virtuellt nätverk under ASE-generering eller välja ett befintliga virtuellt nätverk. Så här skapar du en intern belastningsutjämnare i apptjänstmiljö: 

1. I Azure-portalen väljer du **skapa en resurs -> webb + mobil -> App Service Environment**.
2. Välj din prenumeration.
3. Välj eller skapa en Resursgrupp.
4. Välj eller skapa ett virtuellt nätverk (VNet).
5. Skapa ett undernät om att välja ett virtuellt nätverk.
6. Välj **virtuellt nätverk/plats-> konfiguration av virtuellt nätverk** och ange VIP-typ till internt.
7. Ange namn på en underdomän (det här namnet är underdomänen används för appar som skapats i denna ASE).
8. Välj **OK** och sedan **skapa**.

![][1]

I fönstret virtuellt nätverk har en konfiguration av virtuellt nätverk som alternativet som låter dig välja mellan en extern eller intern VIP. Standardvärdet är extern. Om du har satts till extern använder din ASE en internet-åtkomlig VIP. Om du väljer intern har ASE konfigurerats med en ILB på en IP-adress inom det virtuella nätverket. 

När du har valt internt, du kan lägga till fler IP-adresser till din ASE tas bort och i stället måste du ange underdomänen för ASE. I en ASE med extern VIP används namnet på ASE i underdomänen för appar som skapats i denna ASE. Om din ASE heter ***contosotest*** och din app i denna ASE heter ***mytest***, underdomänen är i formatet ***contosotest.p.azurewebsites.net*** och URL för appen är ***mytest.contosotest.p.azurewebsites.net***. Om du anger VIP-typ till interna används inte ditt ASE-namn i underdomänen för ASE. Du ange uttryckligen underdomänen. Om din underdomän är ***contoso.corp.net*** och du har gjort en app i den ASE med namnet ***timereporting***, URL: en för appen är ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Appar i en ILB ASE
Skapa en app i en ILB ASE är samma som du skapar en app i en ASE vanligtvis. 

1. I Azure-portalen väljer du **skapa en resurs -> webb + mobil -> webb** eller **Mobile** eller **API-App**.
2. Ange appens namn.
3. Välj din prenumeration.
4. Välj eller skapa en Resursgrupp.
5. Välj eller skapa en App Service-Plan(ASP). Om du skapar en ny ASP, väljer du ASE som plats och välj den arbetarpool som du vill att ASP ska skapas i. När du skapar ASP kan välja du din ASE som plats och arbetarpoolen. När du anger namnet på appen visas att underdomänen under ditt appnamn ersätts av underdomänen för din ASE. 
6. Välj **Skapa**. Se till att välja den **fäst på instrumentpanelen** kryssrutan om du vill att appen ska visas på instrumentpanelen. 

![][2]

Under namnet på uppdateras cdnverify för att återspegla underdomänen för din ASE. 

## <a name="post-ilb-ase-creation-validation"></a>Efter ILB ASE skapas verifiering
En ILB ASE skiljer sig något från en icke-ILB ASE. Som redan anges du behöver hantera din egen DNS och du måste också tillhandahålla ditt eget certifikat för HTTPS-anslutningar. 

När du har skapat din ASE, ser du att underdomänen visar underdomänen du angav och det finns ett nytt objekt i den **inställningen** menyn kallas **ILB-certifikat**. ASE skapas med ett självsignerat certifikat som gör det lättare att testa HTTPS. Portalen meddelar att du måste ange ett eget certifikat för HTTPS, men detta är att du har ett certifikat som följer med din underdomän. 

![][3]

Om du helt enkelt provar saker och vet inte hur du skapar ett certifikat, kan du använda IIS MMC-konsolprogram för att skapa ett självsignerat certifikat. När den har skapats kan du exportera det som en .pfx-fil och ladda upp den i Användargränssnittet för ILB-certifikat. När du har åtkomst till en plats som skyddas med ett självsignerat certifikat visas webbläsaren en varning om att den plats som du ansluter till inte är säker på grund av att det inte går att verifiera certifikatet. Om du vill undvika att varning måste ett felaktigt signerade certifikat som matchar din underdomän och har en certifikatkedja som kan identifieras av webbläsaren.

![][6]

Om du vill testa flödet med dina egna certifikat och testa både HTTP och HTTPS-åtkomst till din ASE:

1. Gå till ASE UI när ASE har skapats **ASE -> Inställningar -> ILB-certifikat**.
2. Ställ in ILB-certifikat genom att välja certifikatets pfx-fil och ange lösenord. Det här steget tar lite tid att bearbeta och visas meddelandet som en skalning åtgärd pågår.
3. Hämta ILB-adressen för din ASE (**ASE -> Egenskaper -> virtuella IP-adressen**).
4. Skapa en webbapp i ASE när du har skapat. 
5. Skapa en virtuell dator om du inte har något i det virtuella nätverket (inte i samma undernät som ASE eller saker break).
6. Ange DNS för din underdomän. Du kan använda jokertecken med din underdomän i ditt DNS eller om du vill göra några enkla tester Redigera värdfilen på den virtuella datorn att ange namnet på webbappen till VIP IP-adress. Om din ASE hade underdomännamn. ilbase.com och du gjort web app mytestapp så att det skulle åtgärdas på mytestapp.ilbase.com, anger du som i hosts-filen. (På Windows, i värdfilen _c på C:\Windows\System32\drivers\etc\)
7. Använd en webbläsare på den virtuella datorn och gå till https://mytestapp.ilbase.com (eller oavsett webbappens namn med din underdomän).
8. Använd en webbläsare på den virtuella datorn och gå till https://mytestapp.ilbase.com. Om du använder ett självsignerat certifikat måste du acceptera bristen på säkerhet. 

IP-adressen för din ILB anges i dina egenskaper som den virtuella IP-adressen.

![][4]

## <a name="using-an-ilb-ase"></a>Med intern belastningsutjämnare i apptjänstmiljö
#### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
En ILB ASE gör det möjligt för isolering av nätverk för dina appar. Apparna är inte tillgänglig eller är även känt som internet. Den här metoden passar utmärkt för som är värd för intranätplatser, till exempel line-of-business-program. Du kan fortfarande använda Network Security Groups(NSGs) för åtkomstkontroll på nätverksnivå när du vill begränsa åtkomsten ytterligare. 

Om du vill använda Nätverkssäkerhetsgrupper för att ytterligare begränsa åtkomst måste du kontrollera du inte dela den kommunikation som ASE behöver för att kunna fungera. Även om HTTP/HTTPS-åtkomst är bara via den interna Belastningsutjämnaren som används av ASE, beroende ASE fortfarande resurser utanför det virtuella nätverket. Vilka nätverksåtkomst krävs fortfarande finns i [Kontrollera inkommande trafik till en App Service Environment] [ ControlInbound] och [information om nätverkskonfiguration för App Service-miljöer med ExpressRoute][ExpressRoute]. 

Om du vill konfigurera dina NSG: er, måste du känna till IP-adressen som används av Azure för att hantera din ASE. IP-adress är också utgående IP-adressen från din ASE om det gör att internet-begäranden. Utgående IP-adressen för din ASE är statiskt för resten av din ASE. Om du tar bort och återskapa din ASE, får du en ny IP-adress. Du hittar IP-adressen genom att gå till **Inställningar -> Egenskaper** och hitta den **utgående IP-adress**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Hantering av allmänna ILB ASE
Hantera en ILB ASE är i stort sett densamma som hanterar en ASE vanligtvis. Du måste skala upp din arbetarpooler vara värd för flera ASP-instanser och skala upp din frontservrar hantera ökad mängder HTTP/HTTPS-trafik. Allmän information om hur du hanterar konfigurationen av en ASE finns i [konfigurera en App Service Environment][ASEConfig]. 

De ytterligare objekten är certifikathantering och DNS-hantering. Du måste hämta och ladda upp det certifikat som används för HTTPS efter ILB ASE-generering och Ersätt den innan den upphör. Eftersom Azure äger grundläggande domän kan ge det certifikat för ase med extern VIP. Eftersom underdomänen som används av en ILB ASE kan vara vad som helst, måste du ange ditt eget certifikat för HTTPS. 

#### <a name="dns-configuration"></a>DNS-konfiguration
När du använder en extern VIP hanteras DNS av Azure. Appar som skapas i din ASE läggs till automatiskt till Azure DNS, som är en offentlig DNS. I en ILB ASE måste du hantera din egen DNS. För en viss underdomän, till exempel contoso.corp.net, måste du skapa DNS A-poster som pekar på din ILB-adress för:

    * 
    publicera *.SCM ftp 


## <a name="getting-started"></a>Komma igång
Kom igång med App Service-miljöer, se [introduktion till App Service-miljöer][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
