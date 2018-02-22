---
title: "Skapa och använda en intern belastningsutjämnare med en Apptjänst-miljö | Microsoft Docs"
description: "Skapa och använda en ASE med en ILB"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: f7c94b790c6aa7c75c62fd05671f016b7185b2a2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Med hjälp av en intern belastningsutjämnare med en Apptjänst-miljö

> [!NOTE] 
> Den här artikeln handlar om Apptjänstmiljö v1. Det finns en nyare version av Apptjänst-miljön som är enklare att använda och körs på mer kraftfulla infrastruktur. Mer information om den nya versionen start med den [introduktion till Apptjänst-miljön](intro.md).
>

Funktionen App Service miljö (ASE) är ett Premium-tjänstealternativ i Azure App Service som levererar en utökad konfiguration-funktion som inte är tillgänglig i flera stämplar. Funktionen ASE i stort sett distribuerar Azure App Service i ditt virtuella Azure-Network(VNet). Att få en bättre förståelse av funktionerna som erbjuds av Apptjänstmiljöer att läsa den [vad är en Apptjänstmiljö] [ WhatisASE] dokumentation. Om du inte vet fördelarna med att du arbetar i ett virtuellt nätverk Läs den [Azure Virtual Network vanliga frågor och svar][virtualnetwork]. 

## <a name="overview"></a>Översikt
En ASE kan distribueras med en tillgänglig slutpunkt för internet eller en IP-adress på ditt VNet. För att ange IP-adressen till en adress för virtuella nätverk måste du distribuera din ASE med en intern Balancer(ILB) belastningen. När din ASE konfigureras med ett ILB kan ange du:

* en egen domän eller underdomän. Om du vill göra det enklare, det här dokumentet förutsätts underdomän men du kan konfigurera den alls. 
* certifikatet som används för HTTPS
* DNS-hantering för din underdomänen. 

I utbyte kan du göra saker om att:

* värden intranätsprogram som line-of-business-program på ett säkert sätt i molnet som du kommer åt via en plats till plats eller ExpressRoute VPN
* värd-appar i molnet som inte listas i offentliga DNS-servrar
* Skapa internet isolerade backend-appar som din frontend appar på ett säkert sätt kan integrera

#### <a name="disabled-functionality"></a>Inaktiverad funktion
Det finns vissa saker som du kan göra när du använder en ILB ASE. Detta omfattar:

* med hjälp av IPSSL
* tilldela IP-adresser till specifika appar
* köpa och använder ett certifikat med en app på portalen. Du kan, självklart fortfarande erhålla certifikat direkt med en certifikatutfärdare och använda den med dina appar, men inte via Azure-portalen.

## <a name="creating-an-ilb-ase"></a>Skapa en ILB ASE
Skapa en ILB ASE skiljer sig inte mycket från att skapa en ASE normalt. En ingående diskussion om hur du skapar en ASE finns [så här skapar du en Apptjänst-miljö][HowtoCreateASE]. Processen för att skapa en ILB ASE är samma mellan skapa ett virtuellt nätverk under skapande av ASE eller välja ett befintligt virtuellt nätverk. Så här skapar du en intern belastningsutjämnare i apptjänstmiljö: 

1. Välj i Azure-portalen **skapa en resurs -> webb + mobil -> Apptjänstmiljö**.
2. Välj din prenumeration.
3. Välj eller skapa en Resursgrupp.
4. Välj eller skapa ett virtuellt nätverk (VNet).
5. Skapa ett undernät om du väljer ett virtuellt nätverk.
6. Välj **virtuell nätverksplats konfiguration av virtuellt nätverk ->** och ange VIP till internt.
7. Ange ett underdomännamn på (det här namnet är underdomänen används för appar som har skapats i den här ASE).
8. Välj **OK** och sedan **skapa**.

![][1]

I fönstret virtuellt nätverk har ett alternativ som gör att du kan välja mellan en extern VIP eller interna VIP konfiguration av virtuellt nätverk. Standardvärdet är extern. Om du har satts till externa använder din ASE en tillgänglig VIP för internet. Om du väljer internt har din ASE konfigurerats med en ILB på en IP-adress inom ditt VNet. 

Möjligheten att lägga till flera IP-adresser till din ASE tas bort när du har valt internt, och i stället måste du ange underdomänen i ASE. I en ASE med en extern VIP används namnet på ASE i underdomänen för appar som har skapats i den ASE. Om din ASE heter ***contosotest*** och din app i den ASE heter ***MinTest***, underdomänen är i formatet ***contosotest.p.azurewebsites.net*** och webbadressen för appen ***mytest.contosotest.p.azurewebsites.net***. Om du anger vilken VIP till interna används ASE-namnet inte i underdomänen för ASE. Du ange uttryckligen underdomänen. Om din underdomän ***contoso.corp.net*** och du har gjort en app i den ASE med namnet ***timereporting***, URL: en för appen är ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Appar i en ILB ASE
Skapa en app i en ASE ILB är detsamma som att skapa en app i en ASE normalt. 

1. Välj i Azure-portalen **skapa en resurs -> webb + mobil -> webb** eller **Mobile** eller **API-App**.
2. Ange appens namn.
3. Välj din prenumeration.
4. Välj eller skapa en Resursgrupp.
5. Välj eller skapa en App Service Plan(ASP). Om du skapar en ny ASP, Välj din ASE som och välj arbetspool som du vill att ASP ska skapas i. När du skapar ASP väljer du din ASE som platsen och worker-poolen. När du anger namnet på appen visas underdomänen under appens namn ersättas av underdomänen för din ASE. 
6. Välj **Skapa**. Se till att välja den **fäst på instrumentpanelen** kryssrutan om du vill att appen visas på instrumentpanelen. 

![][2]

Underdomännamnet uppdateras för att återspegla underdomänen i din ASE under namnet på appen. 

## <a name="post-ilb-ase-creation-validation"></a>Bokför ILB ASE skapa validering
En ILB ASE skiljer sig något från en icke-ILB ASE. Redan anges måste du hantera din egen DNS och du måste även ange ditt eget certifikat för HTTPS-anslutningar. 

När du har skapat din ASE ser du att underdomänen visar underdomänen du angav och det finns ett nytt objekt i den **inställningen** menyn kallas **ILB certifikat**. ASE skapas med ett självsignerat certifikat som gör det enklare att testa HTTPS. Portalen talar om att du måste ange ditt eget certifikat för HTTPS, men detta är att rekommenderar att du har ett certifikat som går med din underdomänen. 

![][3]

Om du försöker bara saker och kan inte skapa ett certifikat, kan du använda konsolprogram IIS MMC för att skapa ett självsignerat certifikat. När den har skapats kan du exportera det som en .pfx-fil och överför den i Användargränssnittet för ILB-certifikat. När du har åtkomst till en plats som skyddas med ett självsignerat certifikat visas webbläsaren en varning att platsen du ansluter till inte är säker på grund av att det inte går att verifiera certifikatet. Om du vill undvika att varning måste ett felaktigt signerade certifikat som matchar din underdomän och har en förtroendekedja för som identifieras i din webbläsare.

![][6]

Om du vill prova flödet med dina egna certifikat och testa både HTTP och HTTPS åtkomst till din ASE:

1. Gå till ASE UI efter ASE skapas **ASE -> Inställningar -> ILB certifikat**.
2. Ange ILB certifikat genom att välja certifikatets pfx-fil och ange lösenord. Det här steget tar en stund att bearbeta och visas meddelandet skalning åtgärd pågår.
3. Hämta ILB-adressen för din ASE (**ASE -> Egenskaper för virtuell IP-adress ->**).
4. Skapa en webbapp i ASE när den har skapats. 
5. Skapa en virtuell dator om du inte har något i det virtuella nätverket (inte i samma undernät som ASE eller saker break).
6. Set DNS for your subdomain. Du kan använda jokertecken med din underdomän i din DNS eller om du vill göra några enkla tester Redigera värdfilen på den virtuella datorn att ange webbprogrammets namn till VIP-IP-adress. Om din ASE hade underdomännamnet. ilbase.com du gjort web app mytestapp så att det skulle riktas mytestapp.ilbase.com, ställa in och som i hosts-filen. (På Windows, hosts-filen är på C:\Windows\System32\drivers\etc\)
7. Använda en webbläsare på den virtuella datorn och gå till http://mytestapp.ilbase.com (eller vad din webbprogrammets namn är med ditt underdomän).
8. Använd en webbläsare på den virtuella datorn och gå till https://mytestapp.ilbase.com. Du måste acceptera bristen på säkerhet om du använder ett självsignerat certifikat. 

IP-adressen för din ILB anges i dina egenskaper som den virtuella IP-adressen.

![][4]

## <a name="using-an-ilb-ase"></a>Med intern belastningsutjämnare i apptjänstmiljö
#### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
En ILB ASE gör det möjligt för isolering av nätverk för dina appar. Appar är inte tillgänglig eller även känt som internet. Den här metoden är utmärkt för intranätplatser, till exempel av branschspecifika program. Du kan använda Network Security Groups(NSGs) för åtkomstkontroll på nätverksnivån även när du vill begränsa åtkomsten ytterligare. 

Om du vill använda NSG: er för att begränsa åtkomsten ytterligare måste du kontrollera du inte dela den kommunikation som ASE som behövs för att fungera. Även om HTTP/HTTPS-åtkomst endast via ILB som används av ASE, beroende ASE fortfarande resurser utanför VNet. Vilka nätverksåtkomst krävs fortfarande finns [styra inkommande trafik till en Apptjänst-miljö] [ ControlInbound] och [nätverk konfigurationsinformation för Apptjänstmiljöer med ExpressRoute][ExpressRoute]. 

Om du vill konfigurera dina NSG: er, vet du IP-adressen som används av Azure för att hantera dina ASE. IP-adressen är också utgående IP-adress från din ASE om den gör det internet-begäranden. Utgående IP-adressen för din ASE är statiskt för din ASE livslängd. Om du tar bort och återskapa din ASE får du en ny IP-adress. Om du vill hitta IP-adress, gå till **Inställningar -> Egenskaper** och Sök efter den **utgående IP-adress**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Hantering av allmänna ILB ASE
Hantera en ILB ASE är i stort sett densamma som hanterar en ASE normalt. Du måste skala upp din arbetarpooler att vara värd för flera instanser av ASP och skala upp din frontservrar att hantera ökad mängder HTTP/HTTPS-trafik. Allmän information om hur du hanterar konfigurationen av en ASE finns [konfigurerar en Apptjänstmiljö][ASEConfig]. 

Ytterligare management-objekt är certifikathantering och DNS-hantering. Du måste hämta och ladda upp certifikatet som används för HTTPS efter ILB ASE har skapats och Ersätt den innan den upphör. Eftersom Azure äger domänen bas, kan det ge certifikat för ASEs med en extern VIP. Eftersom underdomänen används av en ILB ASE kan vara något, måste du ange ett eget certifikat för HTTPS. 

#### <a name="dns-configuration"></a>DNS-konfiguration
När du använder en extern VIP hanteras DNS av Azure. Appar som skapas i din ASE läggs till automatiskt till Azure DNS, som är en offentlig DNS. I en ILB ASE måste du hantera din egen DNS. För en given underdomän, till exempel contoso.corp.net, måste du skapa DNS A-poster som pekar på ILB-adress för:

    * 
    publicera *.SCM ftp 


## <a name="getting-started"></a>Komma igång
Kom igång med Apptjänstmiljöer finns [introduktion till Apptjänstmiljöer][WhatisASE]

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
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
