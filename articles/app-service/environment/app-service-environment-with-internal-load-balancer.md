---
title: Skapa en ILB ASE v1
description: Skapa och använda en ASE med en ILB. Det här dokumentet tillhandahålls endast för kunder som använder den äldre v1 ASE.
author: ccompy
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0c03905017629e28e41cce2adaa65eac347b8185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294718"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Använda en intern belastningsutjämnare med en apptjänstmiljö

> [!NOTE] 
> Den här artikeln handlar om App Service Environment v1. Det finns en nyare version av App Service Environment som är enklare att använda och körs på mer kraftfull infrastruktur. Om du vill veta mer om den nya versionen börjar med [introduktionen till App Service Environment](intro.md).
>

Funktionen För App Service Environment (ASE) är ett Premium-tjänstalternativ för Azure App Service som ger en förbättrad konfigurationsfunktion som inte är tillgänglig i stämplarna för flera innehavare. ASE-funktionen distribuerar i huvudsak Azure App Service i ditt Virtuella Azure-nätverk(VNet). För att få en större förståelse för de funktioner som erbjuds av App Service-miljöer, läs dokumentationen [Vad är en App Service Environment-dokumentation.][WhatisASE] Om du inte känner till fördelarna med att arbeta i ett virtuellt nätverk kan du läsa [vanliga frågor och svar om Azure Virtual Network][virtualnetwork]. 

## <a name="overview"></a>Översikt
En ASE kan distribueras med en internettillgänglig slutpunkt eller med en IP-adress i ditt virtuella nätverk. För att kunna ange IP-adressen till en VNet-adress måste du distribuera din ASE med en intern belastningsutjämnare (ILB). När din ASE är konfigurerad med en ILB anger du:

* din egen domän eller underdomän. För att göra det enkelt antar det här dokumentet underdomänen, men du kan konfigurera det åt båda hållen. 
* certifikatet som används för HTTPS
* DNS-hantering för din underdomän. 

I utbyte kan du göra saker om att:

* värd intranätprogram, som affärsprogram, säkert i molnet som du kommer åt via en webbplats till plats eller ExpressRoute VPN
* värdappar i molnet som inte finns med i offentliga DNS-servrar
* skapa internet isolerade backend-appar med vilka dina frontend-appar säkert kan integreras

#### <a name="disabled-functionality"></a>Inaktiverad funktion
Det finns vissa saker som du inte kan göra när du använder en ILB ASE. Dessa saker inkluderar:

* använda IPSSL
* tilldela IP-adresser till specifika appar
* köpa och använda ett certifikat med en app via portalen. Du kan naturligtvis fortfarande hämta certifikat direkt med en certifikatutfärdare och använda dem med dina appar, men inte via Azure-portalen.

## <a name="creating-an-ilb-ase"></a>Skapa en ILB ASE
Skapa en ILB ASE är inte mycket annorlunda än att skapa en ASE normalt. En djupare diskussion om hur du skapar en ASE finns i [Så här skapar du en apptjänstmiljö][HowtoCreateASE]. Processen för att skapa en ILB ASE är densamma mellan att skapa ett VNet när ASE skapas eller att välja ett befintligt virtuella nätverk. Så här skapar du en intern belastningsutjämnare i apptjänstmiljö: 

1. I Azure-portalen väljer du **Skapa en resurs -> Webb + Mobil -> App Service Environment**.
2. Välj din prenumeration.
3. Välj eller skapa en Resursgrupp.
4. Välj eller skapa ett virtuellt nätverk (VNet).
5. Skapa ett undernät om du väljer ett virtuella nätverk.
6. Välj **Virtuellt nätverk/plats -> VNet-konfiguration** och ange VIP-typen till Intern.
7. Ange ett underdomännamn (det här namnet är den underdomän som används för de appar som skapats i den här ASE:n).
8. Välj **OK** och **skapa**sedan .

![][1]

I fönstret Virtuellt nätverk finns det ett VNet-konfigurationsalternativ som gör att du kan välja mellan en extern VIP eller intern VIP. Standardinställningen är Extern. Om du har ställt in den på Extern använder din ASE en internetanpassad VIP. Om du väljer Intern är din ASE konfigurerad med en ILB på en IP-adress inom ditt VNet. 

När du har valt Internt tas möjligheten att lägga till fler IP-adresser till DIN ASE bort och i stället måste du ange underdomänen för ASE. I en ASE med en extern VIP används namnet på ASE i underdomänen för appar som skapats i ase. Om din ASE heter ***contosotest*** och din app i att ASE heter ***mytest,*** är underdomänen av formatet ***contosotest.p.azurewebsites.net*** och webbadressen för den appen ***är mytest.contosotest.p.azurewebsites.net***. Om du ställer in VIP-typen på Internt används inte ditt ASE-namn i underdomänen för ASE. Du anger uttryckligen underdomänen. Om din underdomän är ***contoso.corp.net*** och du har gjort en app i den ASE-namngivna ***tidsrapporteringen***är webbadressen för den appen ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Appar i en ILB ASE
Att skapa en app i en ILB ASE är samma sak som att skapa en app i en ASE normalt. 

1. I Azure-portalen väljer du **Skapa en resurs -> Webb + Mobil -> Webb-** eller **Mobil-** eller **API-app**.
2. Ange appens namn.
3. Välj din prenumeration.
4. Välj eller skapa en Resursgrupp.
5. Markera eller skapa en appserviceplan(ASP). Om du skapar en ny ASP väljer du ASE som plats och väljer den arbetarpool som du vill att ASP ska skapas i. När du skapar ASP väljer du ASE som plats och arbetarpool. När du anger namnet på appen ser du att underdomänen under appnamnet ersätts av underdomänen för DIN ASE. 
6. Välj **Skapa**. Var noga med att markera kryssrutan **Fäst på instrumentpanelen** om du vill att appen ska visas på instrumentpanelen. 

![][2]

Under appnamnet uppdateras underdomännamnet för att återspegla underdomänen i DIN ASE. 

## <a name="post-ilb-ase-creation-validation"></a>Validering av skapande av ILB ASE
En ILB ASE skiljer sig något från en icke-ILB ASE. Som redan nämnts måste du hantera din egen DNS och du måste också ange ditt eget certifikat för HTTPS-anslutningar. 

När du har skapat ASE kommer du att märka att underdomänen visar underdomänen du angav och att det finns ett nytt objekt på **inställningsmenyn** som kallas **ILB-certifikat**. ASE skapas med ett självsignerat certifikat som gör det enklare att testa HTTPS. Portalen talar om för dig att du måste ange ett eget certifikat för HTTPS, men det är för att uppmuntra dig att ha ett certifikat som går med din underdomän. 

![][3]

Om du bara provar saker och inte vet hur du skapar ett certifikat kan du använda IIS MMC-konsolprogrammet för att skapa ett självsignerat certifikat. När den har skapats kan du exportera den som en PFX-fil och sedan ladda upp den i ILB-certifikatgränssnittet. När du öppnar en webbplats som är skyddad med ett självsignerat certifikat ger webbläsaren dig en varning om att webbplatsen du använder inte är säker på grund av oförmågan att validera certifikatet. Om du vill undvika den varningen behöver du ett korrekt signerat certifikat som matchar din underdomän och har en förtroendekedja som känns igen av din webbläsare.

![][6]

Om du vill prova flödet med dina egna certifikat och testa både HTTP- och HTTPS-åtkomst till DIN ASE:

1. Gå till ASE UI när ASE har **skapats ASE ->-inställningar -> ILB-certifikat**.
2. Ange ILB-certifikat genom att välja certifikat pfx-fil och ange lösenord. Det här steget tar en liten stund att bearbeta och meddelandet om att en skalningsåtgärd pågår visas.
3. Hämta ILB-adressen för din ASE **(ASE -> Properties -> Virtual IP-address).**
4. Skapa en webbapp i ASE när du har skapat den. 
5. Skapa en virtuell dator om du inte har en i det virtuella nätverket (Inte i samma undernät som ASE eller saker bryts).
6. Ange DNS för underdomänen. Du kan använda ett jokertecken med din underdomän i DIN DNS eller om du vill göra några enkla tester, redigera hosts-filen på din virtuella dator för att ställa in webbappnamn till VIP IP-adress. Om din ASE hade underdomännamnet .ilbase.com och du gjorde webbappen mytestapp så att den skulle tas upp på mytestapp.ilbase.com, ange det i din hosts-fil. (I Windows finns hosts-filen på C:\Windows\System32\drivers\etc.\)
7. Använd en webbläsare på den `https://mytestapp.ilbase.com` virtuella datorn och gå till (eller vad ditt webbappnamn är med din underdomän).
8. Använd en webbläsare på den virtuella datorn och gå till `https://mytestapp.ilbase.com`. Du måste acceptera bristen på säkerhet om du använder ett självsignerat certifikat. 

IP-adressen för din ILB visas i dina egenskaper som virtuell IP-adress.

![][4]

## <a name="using-an-ilb-ase"></a>Använda en ILB ASE
#### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
En ILB ASE möjliggör nätverksisolering för dina appar. Apparna är inte tillgängliga eller ens kända via internet. Detta tillvägagångssätt är utmärkt för att vara värd för intranätplatser, till exempel affärsspecifika program. När du behöver begränsa åtkomsten ytterligare kan du fortfarande använda Nätverkssäkerhetsgrupper (NSG) för att styra åtkomsten på nätverksnivå. 

Om du vill använda NSG för att ytterligare begränsa åtkomsten måste du se till att du inte bryter den kommunikation som ASE behöver för att fungera. Även om HTTP/HTTPS-åtkomsten endast är via ILB som används av ASE, beror ASE fortfarande på resurser utanför det virtuella nätverket. Information om vilken nätverksåtkomst som fortfarande krävs finns [i Styra inkommande trafik till en apptjänstmiljö][ControlInbound] och information om [nätverkskonfiguration för apptjänstmiljöer med ExpressRoute][ExpressRoute]. 

Om du vill konfigurera nsg:er måste du känna till IP-adressen som används av Azure för att hantera din ASE. Denna IP-adress är också utgående IP-adress från din ASE om den gör internet förfrågningar. Den utgående IP-adressen för din ASE förblir statisk under din ASE:s livstid. Om du tar bort och återskapar din ASE får du en ny IP-adress. Om du vill hitta IP-adressen går du till **Inställningar -> egenskaper** och hittar den **utgående IP-adressen**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Allmän ILB ASE-hantering
Att hantera en ILB ASE är i stort sett detsamma som att hantera ett ASE normalt. Du måste skala upp arbetspoolerna så att de är värdar för fler ASP-instanser och skala upp frontend-servrarna för att hantera ökade mängder HTTP/HTTPS-trafik. Allmän information om hur du hanterar konfigurationen av en ASE finns i [Konfigurera en apptjänstmiljö][ASEConfig]. 

De ytterligare hanteringsobjekten är certifikathantering och DNS-hantering. Du måste hämta och ladda upp certifikatet som används för HTTPS när ILB ASE har skapats och ersätta det innan det upphör att gälla. Eftersom Azure äger basdomänen kan den tillhandahålla certifikat för ASE med en extern VIP. Eftersom underdomänen som används av en ILB ASE kan vara vad som helst, måste du ange ditt eget certifikat för HTTPS. 

#### <a name="dns-configuration"></a>DNS-konfiguration
När du använder en extern VIP hanteras DNS av Azure. Appar som skapas i din ASE läggs till automatiskt till Azure DNS, som är en offentlig DNS. I en ILB ASE måste du hantera din egen DNS. För en viss underdomän, till exempel contoso.corp.net, måste du skapa DNS A-poster som pekar på din ILB-adress för:

    * 
    *.scm ftp publicera 


## <a name="getting-started"></a>Komma igång
Mer om du vill komma igång med App Service-miljöer finns i [Introduktion till apptjänstmiljöer][WhatisASE]

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
