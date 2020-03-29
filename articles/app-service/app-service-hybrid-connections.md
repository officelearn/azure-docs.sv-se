---
title: Hybridanslutningar
description: Lär dig hur du skapar och använder hybridanslutningar i Azure App Service för att komma åt resurser i olika nätverk.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: ec842530f3cae26b869a649617f279d204b98fcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047780"
---
# <a name="azure-app-service-hybrid-connections"></a>Hybridanslutningar för Azure App-tjänst

Hybridanslutningar är både en tjänst i Azure och en funktion i Azure App Service. Som en tjänst har den användningsområden och funktioner utöver de som används i App Service. Mer information om hybridanslutningar och deras användning utanför App Service finns i [Azure Relay Hybrid Connections][HCService].

Inom App Service kan hybridanslutningar användas för att komma åt programresurser i andra nätverk. Den ger åtkomst från din app till en programslutpunkt. Det gör det inte möjligt för en alternativ möjlighet att komma åt ditt program. Som används i App Service korrelerar varje hybridanslutning till en enda TCP-värd- och portkombination. Det innebär att slutpunkten för hybridanslutning kan finnas på alla operativsystem och alla program, förutsatt att du använder en TCP-lyssningsport. Funktionen Hybridanslutningar vet inte eller bryr sig om vad programprotokollet är eller vad du har åtkomst till. Det är helt enkelt att ge tillgång till nätverk.  


## <a name="how-it-works"></a>Hur det fungerar ##
Funktionen Hybridanslutningar består av två utgående samtal till Azure Service Bus Relay. Det finns en anslutning från ett bibliotek på värden där appen körs i App Service. Det finns också en anslutning från Hybrid Connection Manager (HCM) till Service Bus Relay. HCM är en relätjänst som du distribuerar inom nätverket som är värd för den resurs du försöker komma åt. 

Genom de två kopplade anslutningarna har appen en TCP-tunnel till en fast värd:portkombination på andra sidan HCM. Anslutningen använder TLS 1.2 för säkerhets- och SAS-signature-nycklar (Shared Access Signature) för autentisering och auktorisering.    

![Diagram över hybridanslutningsflöde på hög nivå][1]

När din app gör en DNS-begäran som matchar en konfigurerad slutpunkt för hybridanslutning omdirigeras den utgående TCP-trafiken via hybridanslutningen.  

> [!NOTE]
> Det innebär att du alltid bör försöka använda ett DNS-namn för hybridanslutningen. Vissa klientprogram gör ingen DNS-sökning om slutpunkten använder en IP-adress i stället. 
>

### <a name="app-service-hybrid-connection-benefits"></a>Fördelar för Hybridanslutning för App Service ###

Det finns ett antal fördelar med hybridanslutningsfunktionen, bland annat:

- Appar kan komma åt lokala system och tjänster på ett säkert sätt.
- Funktionen kräver ingen internettillgänglig slutpunkt.
- Det är snabbt och enkelt att ställa in. 
- Varje hybridanslutning matchar till en enda värd:portkombination som är användbar för säkerhet.
- Det kräver normalt inte brandväggshål. Anslutningarna är alla utgående över vanliga webbportar.
- Eftersom funktionen är nätverksnivå är den agnostisk till det språk som används av din app och den teknik som används av slutpunkten.
- Den kan användas för att ge åtkomst i flera nätverk från en enda app. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Saker du inte kan göra med hybridanslutningar ###

Saker du inte kan göra med hybridanslutningar är:

- Montera en enhet.
- Använd UDP.
- Få åtkomst till TCP-baserade tjänster som använder dynamiska portar, till exempel FTP-passivt läge eller utökat passivt läge.
- Stöd LDAP, eftersom det kan kräva UDP.
- Stöd Active Directory eftersom du inte kan domän ansluta till en App Service-arbetare.

### <a name="prerequisites"></a>Krav ###
 - Windows App-tjänsten krävs. Den är endast tillgänglig i Windows.  

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Lägga till och skapa hybridanslutningar i appen ##

Om du vill skapa en hybridanslutning går du till [Azure-portalen][portal] och väljer din app. Välj **Nätverk** > **Konfigurera slutpunkterna hybridanslutning**. Här kan du se hybridanslutningarna som är konfigurerade för din app.  

![Skärmbild av listan Hybridanslutning][2]

Om du vill lägga till en ny hybridanslutning väljer du **[+] Lägg till hybridanslutning**.  Du ser en lista över de hybridanslutningar som du redan har skapat. Om du vill lägga till en eller flera av dem i appen markerar du de du vill ha och väljer sedan **Lägg till vald hybridanslutning**.  

![Skärmbild av hybridanslutningsportalen][3]

Om du vill skapa en ny hybridanslutning väljer du **Skapa ny hybridanslutning**. Ange: 

- Namn på hybridanslutning.
- Slutpunktsvärdnamn.
- Slutpunktsport.
- Service Bus namnområde som du vill använda.

![Skärmbild av dialogrutan Skapa ny hybridanslutning][4]

Varje hybridanslutning är knuten till ett servicebussnamnområde och varje servicebussnamnområde finns i en Azure-region. Det är viktigt att du försöker använda ett servicebussnamnområde i samma region som appen för att undvika nätverksinducerad svarstid.

Om du vill ta bort hybridanslutningen från appen högerklickar du på den och väljer **Koppla från**.  

När en hybridanslutning läggs till i din app kan du se information om den genom att helt enkelt välja den. 

![Skärmbild av information om hybridanslutningar][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Skapa en hybridanslutning i Azure Relay-portalen ###

Förutom portalupplevelsen inifrån din app kan du skapa hybridanslutningar inifrån Azure Relay-portalen. För att en hybridanslutning ska kunna användas av App Service måste den:

* Kräv klientauktorisering.
* Har ett metadataobjekt med namnet slutpunkt som innehåller kombinationen host:port som värde.

## <a name="hybrid-connections-and-app-service-plans"></a>Hybridanslutningar och apptjänstplaner ##

Hybridanslutningar för apptjänst är endast tillgängliga i SKU:er för grundläggande priser, standard, premium och isolerade priser. Det finns gränser knutna till prisplanen.  

| Prisplan | Antal hybridanslutningar som kan kunna anslutas i planen |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolerat | 200 |

Användargränssnittet för App Service-abonnemang visar hur många hybridanslutningar som används och vilka appar.  

![Skärmbild av apptjänstplanegenskaper][6]

Välj hybridanslutningen om du vill visa information. Du kan se all information som du såg i appvyn. Du kan också se hur många andra appar i samma abonnemang som använder hybridanslutningen.

Det finns en gräns för antalet slutpunkter för hybridanslutning som kan användas i en App Service-plan. Varje hybridanslutning som används kan dock användas i valfritt antal appar i planen. En enda hybridanslutning som används i fem separata appar i en App Service-plan räknas till exempel som en hybridanslutning.

### <a name="pricing"></a>Prissättning ###

Förutom att det finns ett SKU-krav för App Service-plan, finns det en extra kostnad för att använda hybridanslutningar. Det tillkommer en avgift för varje lyssnare som används av en hybridanslutning. Lyssnaren är Hybrid Connection Manager. Om du hade fem hybridanslutningar som stöds av två hybridanslutningshanterare skulle det vara 10 lyssnare. Mer information finns i [Service Bus-priser][sbpricing].

## <a name="hybrid-connection-manager"></a>Hybridanslutningshanteraren ##

Funktionen Hybridanslutningar kräver en reläagent i nätverket som är värd för slutpunkten för Hybrid-anslutning. Det reläagenten kallas Hybrid Connection Manager (HCM). Om du vill hämta HCM väljer du **Nätverk konfigurera** > **slutpunkterna För**att hämta HCM från din app i [Azure-portalen][portal].  

Det här verktyget körs på Windows Server 2012 och senare. HCM körs som en tjänst och ansluter utgående till Azure Relay på port 443.  

När du har installerat HCM kan du köra HybridConnectionManagerUi.exe om du vill använda verktygets användargränssnitt. Den här filen finns i installationskatalogen för Hybrid-anslutningshanteraren. I Windows 10 kan du också bara söka efter *Hybrid Connection Manager-användargränssnittet* i sökrutan.  

![Skärmbild av Hybrid Connection Manager][7]

När du startar HCM-användargränssnittet visas en tabell som visar alla hybridanslutningar som är konfigurerade med den här instansen av HCM. Om du vill göra några ändringar autentiseras du först med Azure. 

Så här lägger du till en eller flera hybridanslutningar i HCM:

1. Starta HCM-användargränssnittet.
2. Välj **Konfigurera en annan hybridanslutning**.
![Skärmbild av Konfigurera nya hybridanslutningar][8]

1. Logga in med ditt Azure-konto för att få dina Hybrid-anslutningar tillgängliga med dina prenumerationer. HCM fortsätter inte att använda ditt Azure-konto utöver det. 
1. Välj en prenumeration.
1. Välj de hybridanslutningar som du vill att HCM ska vidarebefordra.
![Skärmbild av hybridanslutningar][9]

1. Välj **Spara**.

Nu kan du se de hybridanslutningar som du har lagt till. Du kan också välja den konfigurerade hybridanslutningen för att se information.

![Skärmbild av information om hybridanslutning][10]

HCM kräver för att stödja hybridanslutningarna som den är konfigurerad med:

- TCP-åtkomst till Azure via port 443.
- TCP-åtkomst till slutpunkten för hybridanslutning.
- Möjligheten att göra DNS-uppslag på slutpunktsvärden och servicebussnamnområdet.

> [!NOTE]
> Azure Relay är beroende av webbuttag för anslutning. Den här funktionen är endast tillgänglig på Windows Server 2012 eller senare. På grund av detta stöds HCM inte på något tidigare än Windows Server 2012.
>

### <a name="redundancy"></a>Redundans ###

Varje HCM kan ha stöd för flera hybridanslutningar. Dessutom kan en viss hybridanslutning stödjas av flera HCM:er. Standardbeteendet är att dirigera trafik över de konfigurerade HCM:erna för en viss slutpunkt. Om du vill ha hög tillgänglighet i hybridanslutningarna från nätverket kör du flera HCM:er på separata datorer. Den algoritm för belastningsfördelning som används av relay-tjänsten för att distribuera trafik till HCM:erna är slumpmässig tilldelning. 

### <a name="manually-add-a-hybrid-connection"></a>Lägg till en hybridanslutning manuellt ###

Om du vill att någon utanför din prenumeration ska vara värd för en HCM-instans för en viss hybridanslutning delar du gatewayanslutningssträngen för hybridanslutningen med dem. Du kan se gateway-anslutningssträngen i egenskaperna hybridanslutning i [Azure-portalen][portal]. Om du vill använda strängen väljer du **Ange manuellt** i HCM och klistrar in gatewayanslutningssträngen.

![Lägg till en hybridanslutning manuellt][11]

### <a name="upgrade"></a>Uppgradera ###

Det finns regelbundna uppdateringar av Hybrid Connection Manager för att åtgärda problem eller ge förbättringar. När uppgraderingar släpps visas en popup i HCM-användargränssnittet. Om du använder uppgraderingen tillämpas ändringarna och HCM startas om. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Lägga till en hybridanslutning i appen programmässigt ##

Api:erna nedan kan användas direkt för att hantera hybridanslutningar som är anslutna till dina appar. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

JSON-objektet som är associerat med en hybridanslutning ser ut som:

    {
      "name": "[hybrid connection name]",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "[location]",
      "properties": {
        "serviceBusNamespace": "[namespace name]",
        "relayName": "[hybrid connection name]",
        "relayArmUri": "/subscriptions/[subscription id]/resourceGroups/[resource group name]/providers/Microsoft.Relay/namespaces/[namespace name]/hybridconnections/[hybrid connection name]",
        "hostName": "[endpoint host name]",
        "port": [port],
        "sendKeyName": "defaultSender",
        "sendKeyValue": "[send key]"
      }
    }

Ett sätt att använda den här informationen är med armklienten, som du kan få från [ARMClient][armclient] GitHub-projektet. Här är ett exempel på hur du bifogar en befintlig hybridanslutning till din app. Skapa en JSON-fil enligt schemat ovan som:

    {
      "name": "relay-demo-hc",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "North Central US",
      "properties": {
        "serviceBusNamespace": "demo-relay",
        "relayName": "relay-demo-hc",
        "relayArmUri": "/subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myrelay-rg/providers/Microsoft.Relay/namespaces/demo-relay/hybridconnections/relay-demo-hc",
        "hostName": "my-wkstn.home",
        "port": 1433,
        "sendKeyName": "defaultSender",
        "sendKeyValue": "Th9is3is8a82lot93of3774stu887ff122235="
      }
    }

Om du vill använda det här API:et behöver du send-tangenten och reläresurs-ID. Om du har sparat informationen med filnamnet hctest.json utfärdar du det här kommandot för att bifoga hybridanslutningen till appen: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="secure-your-hybrid-connections"></a>Säkra dina hybridanslutningar ##

En befintlig hybridanslutning kan läggas till i andra App Service Web Apps av alla användare som har tillräcklig behörighet för det underliggande Azure Service Bus Relay. Det innebär att om du måste hindra andra från att återanvända samma hybridanslutning (till exempel när målresursen är en tjänst som inte har några ytterligare säkerhetsåtgärder för att förhindra obehörig åtkomst), måste du låsa åtkomsten till Azure Service bussrelä.

Alla `Reader` som har åtkomst till reläet kan _se_ hybridanslutningen när de försöker lägga till den i sin webbapp i Azure Portal, men de kan inte _lägga_ till den eftersom de saknar behörighet att hämta anslutningssträngen som används för att upprätta relay-anslutningen. För att kunna lägga till hybridanslutningen `listKeys` måste`Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action`de ha behörighet ( ). Rollen `Contributor` eller någon annan roll som innehåller den här behörigheten på Relay gör det möjligt för användare att använda hybridanslutningen och lägga till den i sina egna webbappar.

## <a name="troubleshooting"></a>Felsökning ##

Statusen för "Ansluten" innebär att minst en HCM är konfigurerad med den hybridanslutningen och kan nå Azure. Om statusen för hybridanslutningen inte säger **Ansluten**är hybridanslutningen inte konfigurerad på någon HCM som har åtkomst till Azure.

Den främsta orsaken till att klienter inte kan ansluta till slutpunkten beror på att slutpunkten angavs med hjälp av en IP-adress i stället för ett DNS-namn. Om din app inte kan nå önskad slutpunkt och du använde en IP-adress växlar du till att använda ett DNS-namn som är giltigt på värden där HCM körs. Kontrollera också att DNS-namnet matchas korrekt på värden där HCM körs. Bekräfta att det finns anslutning från värden där HCM körs till slutpunkten för hybridanslutning.  

I App Service kan kommandoradsverktyget **för tcpping** anropas från konsolen Avancerade verktyg (Kudu). Det här verktyget kan tala om för dig om du har tillgång till en TCP-slutpunkt, men det talar inte om för dig om du har tillgång till en slutpunkt för hybridanslutning. När du använder verktyget i konsolen mot en slutpunkt för hybridanslutning bekräftar du bara att det använder en kombination av värd:port.  

Om du har en kommandoradsklient för slutpunkten kan du testa anslutningen från appkonsolen. Du kan till exempel testa åtkomsten till webbserverslutpunkter med hjälp av curl.

## <a name="biztalk-hybrid-connections"></a>BizTalk hybridanslutningar ##

Den tidiga formen av den här funktionen kallades BizTalk Hybrid Connections. Denna förmåga gick End of Life den 31 maj 2018 och upphörde med verksamheten. BizTalk-hybridanslutningar har tagits bort från alla appar och är inte tillgängliga via portalen eller API:et. Om du fortfarande har konfigurerat dessa äldre anslutningar i Hybrid Connection Manager visas statusen Utgått och en end of life-sats längst ned.

![BizTalk hybridanslutningar i HCM][12]


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: https://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: https://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
