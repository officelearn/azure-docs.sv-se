---
title: Affärskontinuitet och haveriberedskap
description: Utforma din strategi för att skydda data, snabbt återhämta dig från störande händelser, återställa resurser som krävs av kritiska affärsfunktioner och upprätthålla affärskontinuitet för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 7bf71ce7c44229ccf19022e9cfb0162f9d77cd97
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437701"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Kontinuitet i verksamheten och haveriberedskap för Azure Logic Apps

För att minska påverkan och effekterna som oförutsägbara händelser har på ditt företag och kunder, se till att du har en [ *KATASTROFÅTERSTÄLLNING* (DR)](https://en.wikipedia.org/wiki/Disaster_recovery) lösning på plats så att du kan skydda data, snabbt återställa de resurser som stöder kritiska affärsfunktioner och hålla verksamheten igång för att upprätthålla [ *kontinuitet* (BC)](https://en.wikipedia.org/wiki/Business_continuity_planning). Störningar kan till exempel omfatta avbrott, förluster i underliggande infrastruktur eller komponenter som lagring, nätverk eller beräkningsresurser, oåterkalleliga programfel eller till och med en fullständig datacenterförlust. Genom att ha en lösning för affärskontinuitet och haveriberedskap (BCDR) redo kan ditt företag eller din organisation svara snabbare på avbrott, planerade eller oplanerade och minska stilleståndstiden för dina kunder.

Den här artikeln innehåller BCDR-vägledning och strategier som du kan använda när du skapar automatiserade arbetsflöden med hjälp av [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Logikapparbetsflöden hjälper dig att lättare integrera och dirigera data mellan appar, molntjänster och lokala system genom att minska hur mycket kod du har att skriva. När du planerar för BCDR ska du se till att du inte bara tar hänsyn till dina logikappar utan även de här Azure-resurserna som du använder med dina logikappar:

* [Anslutningar](../connectors/apis-list.md) som du skapar från logikappar till andra appar, tjänster och system. Mer information finns i [Anslutningar till resurser](#resource-connections) senare i det här avsnittet.

* [Lokala datagateways](../logic-apps/logic-apps-gateway-connection.md) som är Azure-resurser som du skapar och använder i dina logikappar för att komma åt data i lokala system. Varje gatewayresurs representerar en separat [datagatewayinstallation](../logic-apps/logic-apps-gateway-install.md) på en lokal dator. Mer information finns i [Lokala datagateways](#on-premises-data-gateways) senare i det här avsnittet.

* [Integrationskonton](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) där du definierar och lagrar de artefakter som logikappar använder för [B2B-scenarier (Business-to-Business).](../logic-apps/logic-apps-enterprise-integration-overview.md) Du kan till exempel [ställa in haveriberedskap mellan regioner för integrationskonton](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md).

* [Integrationstjänstmiljöer (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) där du skapar logikappar som körs i en isolerad Logic Apps-körningsinstans i ett virtuellt Azure-nätverk. Dessa logikappar kan sedan komma åt resurser som är skyddade bakom en brandvägg i det virtuella nätverket.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Primära och sekundära platser

Varje logikapp måste ange den plats som du vill använda för distribution. Den här platsen är antingen en offentlig region i globala Azure med flera innehavare, till exempel "Västra USA", eller en integrationstjänstmiljö (ISE) som du tidigare har skapat och distribuerat till ett virtuellt Azure-nätverk. Att köra logikappar i en ISE liknar att köra logikappar i en global Azure-region, vilket innebär att din katastrofåterställningsstrategi kan tillämpas på båda scenarierna. Internet-företag har dock andra överväganden, till exempel konfigurera åtkomst till resurser som endast är tillgängliga för IS-företag.

> [!NOTE]
> Om logikappen även fungerar med B2B-artefakter, till exempel handelspartner, avtal, scheman, kartor och certifikat, som lagras i ett integrationskonto, måste både ditt integrationskonto och logikappar ange samma plats.

Den här katastrofåterställningsstrategin fokuserar på att konfigurera din primära logikapp för att [*växla över*](https://en.wikipedia.org/wiki/Failover) till en logikapp för vänteläge eller säkerhetskopiering på en annan plats där Azure Logic Apps också är tillgängligt. På så sätt, om den primära lider förluster, störningar eller misslyckanden, kan den sekundära ta på arbetet. Den här strategin kräver att din sekundära logikapp och beroende resurser redan har distribuerats och är klara på den alternativa platsen.

Om du följer bra DevOps-metoder använder du redan [Azure Resource Manager-mallar](../azure-resource-manager/management/overview.md) för att definiera och distribuera dina logikappar och deras beroende resurser. Resource Manager-mallar ger dig möjlighet att använda en enda distributionsdefinition och sedan använda parameterfiler för att ange de konfigurationsvärden som ska användas för varje distributionsmål. Den här funktionen innebär att du kan distribuera samma logikapp till olika miljöer, till exempel utveckling, test och produktion. Du kan också distribuera samma logikapp till olika [Azure-regioner](../best-practices-availability-paired-regions.md)eller ISE,som stöder strategier för haveriberedskap som använder parade regioner .

För redundansstrategin måste dina logikappar och platser uppfylla dessa krav:

* Den sekundära logikappinstansen har åtkomst till samma appar, tjänster och system som den primära logikappinstansen.

* Båda logikappinstanserna har samma värdtyp. Så antingen distribueras båda instanserna till regioner i globala Azure med flera innehavare, eller så distribueras båda instanserna till ISE, vilket gör att dina logikappar direkt kan komma åt resurser i ett virtuellt Azure-nätverk. Bästa praxis och mer information om parade regioner för BCDR finns i [Business Continuity and disaster recovery (BCDR): Azure paired regions](../best-practices-availability-paired-regions.md).

  Till exempel måste både de primära och sekundära platserna vara ISE när den primära logikappen körs i en ISE och använder [ISE-versionskopplingar , HTTP-åtgärder](../connectors/apis-list.md#ise-connectors)för att anropa resurser i det virtuella Azure-nätverket eller båda. I det här fallet måste din sekundära logikapp också ha en liknande inställning på den sekundära platsen som den primära logikappen.

  > [!NOTE]
  > För mer avancerade scenarier kan du blanda både Azure med flera innehavare och en ISE som platser. Se dock till att du tar hänsyn till och förstår [skillnaderna mellan hur logikappar körs i en ISE jämfört med Azure med flera innehavare](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

* Om du använder ISEs [kontrollerar du att de skalas ut eller har tillräcklig kapacitet](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) för att hantera belastningen.

#### <a name="example-multi-tenant-azure"></a>Exempel: Azure med flera innehavare

Det här exemplet visar primära och sekundära logikappinstanser som distribueras till separata regioner i den globala Azure med flera innehavare för det här scenariot. En enda [Resource Manager-mall](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) definierar både logikappinstanser och de beroende resurser som krävs av dessa logikappar. Separata parameterfiler anger de konfigurationsvärden som ska användas för varje distributionsplats:

![Primära och sekundära logikappinstanser på separata platser](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Exempel: Integrationstjänstmiljö

I det här exemplet visas tidigare primära och sekundära logikappinstanser men distribueras till separata ISE-företag. En enda Resource Manager-mall definierar både logikappinstanser, de beroende resurser som krävs av dessa logikappar och IS-företagen som distributionsplatser. Separata parameterfiler definierar de konfigurationsvärden som ska användas för distribution på varje plats:

![Primära och sekundära logikappar på olika platser](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Anslutningar till resurser

Azure Logic Apps tillhandahåller [inbyggda utlösare och åtgärder samt hundratals hanterade anslutningsappar](../connectors/apis-list.md) som logikappen kan använda för att arbeta med andra appar, tjänster, system och andra resurser, till exempel Azure Storage-konton, SQL Server-databaser, Office 365 Outlook-e-postkonton och så vidare. Om logikappen behöver åtkomst till dessa resurser skapar du anslutningar som autentiserar åtkomst till dessa resurser. Varje anslutning är en separat Azure-resurs som finns på en viss plats och kan inte användas av resurser på andra platser.

För din strategi för haveriberedskap bör du tänka på de platser där beroende resurser finns i förhållande till dina logikappinstanser:

* Din primära instans och beroende resurser finns på olika platser. I det här fallet kan den sekundära instansen ansluta till samma beroende resurser eller slutpunkter. Du bör dock skapa anslutningar specifikt för den sekundära instansen. På så sätt påverkas inte dina sekundära anslutningar om din primära plats blir otillgänglig.

  Anta till exempel att din primära logikapp ansluter till en extern tjänst som Salesforce. Vanligtvis är den externa tjänstens tillgänglighet och plats oberoende av logikappens tillgänglighet. I det här fallet kan din sekundära instans ansluta till samma tjänst men ha en egen anslutning.

* Både din primära instans och beroende resurser finns på samma plats. I det här fallet bör beroende resurser ha säkerhetskopior eller replikerade versioner på en annan plats så att den sekundära instansen fortfarande kan komma åt dessa resurser.

  Anta till exempel att din primära logikapp ansluter till en tjänst som finns på samma plats eller region, till exempel Azure SQL Database. Om hela den här regionen blir otillgänglig är azure SQL Database-tjänsten i den regionen sannolikt inte tillgänglig. I det här fallet vill du att den sekundära instansen ska använda en replikerad databas eller säkerhetskopia tillsammans med en separat anslutning till databasen.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Lokala datagatewayer

Om logikappen körs i Azure med flera innehavare och behöver åtkomst till lokala resurser som SQL Server-databaser måste du installera den [lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md) på en lokal dator. Du kan sedan skapa en datagatewayresurs i Azure-portalen så att logikappen kan använda gatewayen när du skapar en anslutning till resursen.

Datagateway-resursen är associerad med en plats eller Azure-region, precis som din logikappresurs. I din strategi för haveriberedskap kontrollerar du att datagatewayen förblir tillgänglig för logikappen. Du kan [aktivera hög tillgänglighet för din gateway](../logic-apps/logic-apps-gateway-install.md#high-availability) när du har flera gatewayinstallationer.

> [!NOTE]
> Om logikappen körs i en integrationstjänstmiljö (ISE) och endast använder ISE-versionskopplingar för lokala datakällor behöver du inte datagatewayen eftersom ISE-kopplingar ger direkt åtkomst till den lokala resursen.
>
> Om ingen ISE-versionerad anslutning är tillgänglig för den lokala resurs som du vill ha, kan logikappen fortfarande skapa anslutningen med hjälp av en icke-ISE-anslutning, som körs i den globala Azure med flera innehavare, inte din ISE. Den här anslutningen kräver dock den lokala datagatewayen.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Aktiva och aktiva-passiva roller

Du kan ställa in dina primära och sekundära platser så att dina logikappinstanser på dessa platser kan spela upp följande roller:

| Primär-sekundär roll | Beskrivning |
|------------------------|-------------|
| *Aktiv-aktiv* | De primära och sekundära logikappinstanserna på båda platserna hanterar aktivt begäranden genom att följa något av dessa mönster: <p><p>- *Belastningsfördelning:* Du kan låta båda instanserna lyssna på en slutpunkt och belastningsbalanstrafik till varje instans efter behov. <p>- *Konkurrerande konsumenter*: Du kan låta båda instanserna fungera som konkurrerande konsumenter så att instanserna konkurrerar om meddelanden från en kö. Om en instans misslyckas tar den andra instansen över arbetsbelastningen. |
| *Aktiv-passiv* | Den primära logikappinstansen hanterar aktivt hela arbetsbelastningen, medan den sekundära instansen är passiv (inaktiverad eller inaktiv). Den sekundära väntar på en signal om att den primära inte är tillgänglig eller inte fungerar på grund av avbrott eller fel och tar över arbetsbelastningen som aktiv instans. |
| Kombination | Vissa logikappar spelar en aktiv roll, medan andra logikappar spelar en aktiv-passiv roll. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Aktiva-aktiva exempel

De här exemplen visar den aktiva aktiva installationen där både logikappinstanser aktivt hanterar begäranden eller meddelanden. Något annat system eller tjänst distribuerar begäranden eller meddelanden mellan instanser, till exempel något av följande alternativ:

* En "fysisk" belastningsutjämnare, till exempel en maskinvara som dirigerar trafik

* En "mjuk" belastningsutjämnare som [Azure Load Balancer](../load-balancer/load-balancer-overview.md) eller [Azure API Management](../api-management/api-management-key-concepts.md). Med API Management kan du ange principer som avgör hur inkommande trafik ska belastningsutjämnas. Du kan också använda en tjänst som stöder tillståndsspårning, till exempel [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

  Även om det här exemplet främst visar Azure Load Balancer kan du använda det alternativ som bäst passar ditt scenarios behov:

  !["Aktiv-aktiv" inställning som använder en belastningsutjämnare eller tillståndskänslig tjänst](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Varje logikappinstans fungerar som konsument och har båda instanserna konkurrerar om meddelanden från en kö:

  !["Aktiv-aktiv" setup som använder "konkurrerande konsumenter"](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Aktiv-passiva exempel

I det här exemplet visas den aktiva passiva inställningen där den primära logikappinstansen är aktiv på en plats, medan den sekundära instansen förblir inaktiv på en annan plats. Om den primära uppstår ett avbrott eller ett fel kan du låta en operatör köra ett skript som aktiverar den sekundära för att ta på arbetsbelastningen.

!["Aktiv-passiv" setup som använder "konkurrerande konsumenter"](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Kombination med aktiv-aktiv och aktiv-passiv

Det här exemplet visar en kombinerad inställning där den primära platsen har både aktiva logikappinstanser, medan den sekundära platsen har aktiv-passiv logikappinstanser. Om den primära platsen uppstår ett avbrott eller fel kan den aktiva logikappen på den sekundära platsen, som redan hanterar en partiell arbetsbelastning, ta över hela arbetsbelastningen.

* På den primära platsen lyssnar en aktiv logikapp på en Azure Service Bus-kö efter meddelanden, medan en annan aktiv logikapp söker efter e-postmeddelanden med hjälp av en Office 365 Outlook-avsökningsutlösare.

* På den sekundära platsen fungerar en aktiv logikapp med logikappen på den primära platsen genom att lyssna och konkurrera om meddelanden från samma Service Bus-kö. Under tiden väntar en passiv inaktiv logikapp i vänteläge för att söka efter e-postmeddelanden när den primära platsen blir otillgänglig men *är inaktiverad* för att undvika att läsa e-postmeddelanden.

!["Aktiv-passiv" och "aktiv-passiv" kombination som använder återkommande triggers](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Logikapptillstånd och historik

När logikappen utlöses och börjar köras lagras appens tillstånd på samma plats där appen startade och inte kan överföras till en annan plats. Om ett fel eller avbrott inträffar avbryts alla pågående arbetsflödesinstanser. När du har konfigurerat en primär och sekundär plats börjar nya arbetsflödesinstanser köras på den sekundära platsen.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Minska övergivna pågående instanser

Om du vill minimera antalet övergivna arbetsflödesinstanser på förlopp kan du välja mellan olika meddelandemönster som du kan implementera, till exempel:

* [Mönster för fast operationsföljdsglidning](https://docs.microsoft.com/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Det här företagsmeddelandemönstret som delar upp en affärsprocess i mindre steg. För varje fas ställer du in en logikapp som hanterar arbetsbelastningen för den fasen. För att kommunicera med varandra använder dina logikappar ett asynkront meddelandeprotokoll som Azure Service Bus-köer eller ämnen. När du delar upp en process i mindre steg minskar du antalet affärsprocesser som kan fastna i en misslyckad logikappinstans. Mer allmän information om det här mönstret finns i [Enterprise integrationsmönster - Routningsblankett](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html).

  Det här exemplet visar ett routningssedelmönster där varje logikapp representerar en fas och använder en Service Bus-kö för att kommunicera med nästa logikapp i processen.

  ![Dela upp en affärsprocess i faser som representeras av logikappar, som kommunicerar med varandra med hjälp av Azure Service Bus-köer](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Om både primära och sekundära logikappinstanser följer samma routningsglidningsmönster på sina platser kan du implementera det [konkurrerande konsumentmönstret](https://docs.microsoft.com/azure/architecture/patterns/competing-consumers) genom att konfigurera [aktiva aktiva roller](#roles) för dessa instanser.

* [Processhanterare (mäklare) mönster](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Peek-lock utan timeout-mönster](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Tillgång till utlösande och kör historik

Om du vill ha mer information om logikappens tidigare arbetsflödeskörningar kan du granska appens utlösare och köra historik. En logikapps historik för körning lagras på samma plats eller region där logikappen kördes, vilket innebär att du inte kan migrera den här historiken till en annan plats. Om din primära instans växlar över till en sekundär instans kan du bara komma åt varje instanss utlösare och kör historik på respektive platser där dessa instanser kördes. Du kan dock få platsaberoende information om logikappens historik genom att konfigurera dina logikappar för att skicka diagnostikhändelser till en Azure Log Analytics-arbetsyta. Du kan sedan granska hälsotillstånd och historik över logikappar som körs på flera platser.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Vägledning för utlösartyp

Den utlösartyp som du använder i logikapparna avgör vilka alternativ du kan konfigurera logikappar på olika platser i din strategi för katastrofåterställning. Här är de tillgängliga utlösartyperna som du kan använda i logikappar:

* [Utlösare för återkommande](#recurrence-trigger)
* [Utlösare av avsökning](#polling-trigger)
* [Utlösare för begäran](#request-trigger)
* [Webhook-utlösare](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Utlösare för återkommande

Den **återkommande** utlösaren är oberoende av en specifik tjänst eller slutpunkt och utlöses endast baserat på ett angivet schema och inga andra kriterier, till exempel:

* En fast frekvens och ett visst intervall, till exempel var 10:e minut
* Ett mer avancerat schema, till exempel den sista måndagen i varje månad kl 17:00

När logikappen börjar med en återkommande utlösare måste du konfigurera dina primära och sekundära logikappinstanser med [aktiv-passiva roller](#roles). Om du vill minska *återställningstidens mål* (RTO), som refererar till måltiden för att återställa en affärsprocess efter ett avbrott eller en katastrof, kan du ställa in logikappinstanserna med en kombination av [aktiv-passiva roller](#roles) och [passiv-aktiva roller](#roles). I den här inställningen delar du schemat mellan platser.

Anta till exempel att du har en logikapp som måste köras var tionde minut. Du kan ställa in logikappar och platser så att den sekundära platsen kan ta över arbetet om den primära platsen blir otillgänglig:

!["Aktiv-passiv" och "passiv-aktiv" kombination som använder återkommande triggers](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* På den primära platsen ställer du in [aktiv-passiva roller](#roles) för dessa logikappar:

  * För den *aktiva* aktiverade logikappen ställer du in upprepningsutlösaren så att den startar högst upp i timmen och upprepar var 20:e minut, till exempel 09:00, 09:20 och så vidare.

  * För den *passiva* logikappen med inaktiverad anger du upprepningsutlösaren till samma schema men börjar klockan 10 minuter efter timmen och upprepas var 20:e minut, till exempel 09:10, 09:30 och så vidare.

* På den sekundära platsen konfigurerar du [passivt aktiv](#roles) för dessa logikappar:

  * För den *passiva* logikappen med inaktiverad anger du upprepningsutlösaren till samma schema som den aktiva logikappen på den primära platsen, som är högst upp i timmen och upprepar var 20:e minut, till exempel 09:00, 09:10 och så vidare.

  * För den *aktiva* aktiverade logikappen ställer du in upprepningsutlösaren på samma schema som den passiva logikappen på den primära platsen, som ska börja klockan 10 minuter efter timmen och upprepas var 20:e minut, till exempel 09:10, 09:20 och så vidare.

Om en störande händelse inträffar på den primära platsen aktiverar du den passiva logikappen på den alternativa platsen. På så sätt, om det tar tid att hitta felet, begränsar den här inställningen antalet missade upprepningar under den fördröjningen.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Utlösare av avsökning

Om du regelbundet vill kontrollera om nya data för bearbetning är tillgängliga från en viss tjänst eller slutpunkt kan logikappen använda en *avsökningsutlösare* som upprepade gånger anropar tjänsten eller slutpunkten baserat på ett fast schema för återkommande. De data som tjänsten eller slutpunkten tillhandahåller kan ha någon av dessa typer:

* Statiska data, som beskriver data som alltid är tillgängliga för läsning
* Flyktiga data, som beskriver data som inte längre är tillgängliga efter läsning

Om du vill undvika att flera gånger läsa samma data måste logikappen komma ihåg vilka data som tidigare lästs genom att behålla tillståndet antingen på klientsidan eller på server-, tjänst- eller systemsidan.

* Logikappar som fungerar med klienttillstånd använder utlösare som kan upprätthålla tillstånd.

  En utlösare som läser ett nytt meddelande från en e-post inkorg kräver till exempel att utlösaren kommer ihåg det senast lästa meddelandet. På så sätt startar utlösaren logikappen endast när nästa olästa meddelande anländer.

* Logikappar som fungerar med server-, tjänst- eller systemtillstånd använder egenskapsvärden eller inställningar som finns på server-, tjänst- eller systemsidan.

  En frågebaserad utlösare som läser en rad från en databas `isRead` kräver till exempel `FALSE`att raden har en kolumn som är inställd på . Varje gång utlösaren läser en rad uppdaterar logikappen `isRead` den `FALSE` `TRUE`raden genom att ändra kolumnen från till .

  Den här servermetoden fungerar på samma sätt för Service Bus-köer eller ämnen som har köande semantik där en utlösare kan läsa och låsa ett meddelande medan logikappen bearbetar meddelandet. När logikappen är klar med bearbetningen tar utlösaren bort meddelandet från kön eller ämnet.

Från ett katastrofåterställningsperspektiv, när du ställer in logikappens primära och sekundära instanser, ska du ta hänsyn till dessa beteenden baserat på om logikappen spårar tillståndet på klientsidan eller på serversidan:

* För en logikapp som fungerar med klientsidan kontrollerar du att logikappen inte läser samma meddelande mer än en gång. Endast en plats kan ha en aktiv logikappinstans vid en viss tidpunkt. Kontrollera att logikappinstansen på den alternativa platsen är inaktiv eller inaktiverad tills den primära instansen växlar över till den alternativa platsen.

  Office 365 Outlook-utlösaren behåller till exempel klienttillståndet och spårar tidsstämpeln för det senast lästa e-postmeddelandet för att undvika att läsa en dubblett.

* För en logikapp som fungerar med serverläge kan du ställa in logikappinstanserna så att de spelar antingen [aktiva aktiva roller](#roles) där de fungerar som konkurrerande konsumenter eller [aktiv-passiva roller](#roles) där den alternativa instansen väntar tills den primära instansen växlar över till den alternativa platsen.

  Om du läser från en meddelandekö, till exempel en Azure Service Bus-kö, används servertillstånd eftersom kötjänsten underhåller lås på meddelanden för att förhindra att andra klienter läser samma meddelanden.

  > [!NOTE]
  > Om logikappen behöver läsa meddelanden i en viss ordning, till exempel från en servicebusskö, kan du använda det konkurrerande konsumentmönstret, men bara i kombination med Service Bus-sessioner, som också kallas [ *sekventiellt konvojmönster* ](https://docs.microsoft.com/azure/architecture/patterns/sequential-convoy). Annars måste du ställa in logikappinstanserna med aktiv-passiva roller.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Utlösare för begäran

Utlösaren **För begäran** gör din logikapp uppringbar från andra appar, tjänster och system och används vanligtvis för att tillhandahålla följande funktioner:

* Ett direkt REST API för din logikapp som andra kan ringa

  Använd till exempel utlösaren Begär för att starta logikappen så att andra logikappar kan anropa utlösaren med hjälp av åtgärden **Samtalsarbetsflöde - Logikappar.**

* En [webhook-](#webhook-trigger) eller motringningsmekanism för logikappen

* Ett sätt att manuellt köra användaråtgärder eller rutiner för att anropa logikappen, till exempel genom att använda ett PowerShell-skript som utför en viss uppgift

Från ett katastrofåterställningsperspektiv är utlösaren Begäran en passiv mottagare eftersom logikappen inte gör något arbete och väntar tills någon annan tjänst eller något annat system uttryckligen anropar utlösaren. Som en passiv slutpunkt kan du ställa in primära och sekundära instanser på följande sätt:

* [Aktiv aktiv](#roles): Båda instanserna hanterar aktivt begäranden eller anrop. Anroparen eller routern balanserar eller distribuerar trafik mellan dessa instanser.

* [Aktiv-passiv](#roles): Endast den primära instansen är aktiv och hanterar allt arbete, medan den sekundära instansen väntar tills den primära erfar störningar eller fel. Anroparen eller routern bestämmer när den sekundära instansen ska anropas.

Som en rekommenderad arkitektur kan du använda Azure API Management som en proxy för logikappar som använder Begäran triggers. API Management ger [inbyggd korsregional återhämtning och möjlighet att dirigera trafik över flera slutpunkter](https://docs.microsoft.com/azure/api-management/api-management-howto-deploy-multi-region).

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Webhook-utlösare

En *webhook-utlösare* ger möjligheten för logikappen att prenumerera på en tjänst genom att skicka en *motringnings-URL* till den tjänsten. Logikappen kan sedan lyssna och vänta på att en viss händelse ska inträffa vid den tjänstens slutpunkt. När händelsen inträffar anropar tjänsten webhook-utlösaren med hjälp av motringnings-URL:en, som sedan kör logikappen. När den är aktiverad prenumererar webhook-utlösaren på tjänsten. När den är inaktiverad avregistrerar utlösaren prenumerationen från tjänsten.

Ur ett katastrofåterställningsperspektiv ställer du in primära och sekundära instanser som använder webhook-utlösare för att spela aktiva passiva roller eftersom endast en instans ska ta emot händelser eller meddelanden från den prenumererade slutpunkten.

## <a name="assess-primary-instance-health"></a>Bedöma primärinstanshälsa

För att din strategi för katastrofåterställning ska fungera behöver din lösning sätt att utföra dessa uppgifter:

* [Kontrollera den primära instansens tillgänglighet](#check-primary-availability)
* [Övervaka primärinstansens hälsa](#monitor-primary-health)
* [Aktivera den sekundära instansen](#activate-secondary)

I det här avsnittet beskrivs en lösning som du kan använda direkt eller som en grund för din egen design. Här är en visuell översikt på hög nivå för den här lösningen:

![Skapa logikapp för watchdog som övervakar en logikapp för hälsokontroll på den primära platsen](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Kontrollera primär instanstillgänglighet

Om du vill ta reda på om den primära instansen är tillgänglig, körs och kan fungera kan du skapa en logikapp för hälsokontroll som finns på samma plats som den primära instansen. Du kan sedan ringa den här hälsokontrollappen från en annan plats. Om hälsokontrollappen svarar är den underliggande infrastrukturen för Azure Logic Apps-tjänsten i den regionen tillgänglig och fungerar. Om hälsokontrollappen inte svarar kan du anta att platsen inte längre är felfri.

För den här uppgiften skapar du en grundläggande logikapp för hälsokontroll som utför dessa uppgifter:

1. Tar emot ett samtal från watchdog-appen med hjälp av utlösaren Begäran.

1. Svara med en status som anger om den markerade logikappen fortfarande fungerar med hjälp av svarsåtgärden.

   > [!IMPORTANT]
   > Logikappen för hälsokontroll måste använda en svarsåtgärd så att appen svarar synkront, inte asynkront.

1. Om du vill ta ytterligare reda på om den primära platsen är felfri kan du ta hänsyn till hälsotillståndet för andra tjänster som interagerar med mållogikappen på den här platsen. Bara utöka hälsokontroll logik app för att bedöma hälsotillståndet för dessa andra tjänster också.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Skapa en logikapp för watchdog

Om du vill övervaka den primära instansens hälsotillstånd och anropa logikappen för hälsokontroll skapar du en logikapp för "watchdog" på en *alternativ plats*. Du kan till exempel ställa in logikappen för watchdog så att watchdog-logiken misslyckas, så kan watchdogn skicka en avisering till ditt operationsteam så att de kan undersöka felet och varför den primära instansen inte svarar.

> [!IMPORTANT]
> Kontrollera att din watchdog logic app är på en *plats som skiljer sig från den primära platsen*. Om logic apps-tjänsten på den primära platsen har problem kanske din logikapp för watchdog inte körs.

För den här uppgiften, på den sekundära platsen, skapa en watchdog logic app som utför dessa uppgifter:

1. Kör baserat på en fast eller schemalagd upprepning med hjälp av utlösaren Återkommande.

   Du kan ställa in upprepningen till ett värde som ligger under toleransnivån för återställningstidens mål (RTO).

1. Anropa logikappen för hälsokontroll på den primära platsen med hjälp av HTTP-åtgärden, till exempel:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Aktivera din sekundära instans

Om du vill aktivera den sekundära instansen automatiskt kan du skapa en logikapp som anropar hanterings-API:et, till exempel [Azure Resource Manager-kopplingen,](https://docs.microsoft.com/connectors/arm/) för att aktivera lämpliga logikappar på den sekundära platsen. Du kan expandera din watchdog-app för att anropa den här aktiveringslogiken app efter att ett visst antal fel inträffar.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Samla in diagnostikdata

Du kan konfigurera loggning för dina logikappkörningar och skicka de resulterande diagnostikdata till tjänster som Azure Storage, Azure Event Hubs och Azure Log Analytics för vidare hantering och bearbetning.

* Om du vill använda dessa data med Azure Log Analytics kan du göra data tillgängliga för både primära och sekundära platser genom att konfigurera logikappens **diagnostikinställningar** och skicka data till flera Log Analytics-arbetsytor. Mer information finns i [Konfigurera Azure Monitor-loggar och samla in diagnostikdata för Azure Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md).

* Om du vill skicka data till Azure Storage eller Azure Event Hubs kan du göra data tillgängliga för både primära och sekundära platser genom att konfigurera georedundans. Mer information finns i dessa artiklar:<p>

  * [Azure Blob Storage haveriberedskap och konto redundans](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure Event Hubs geo-katastrofåterställning](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Nästa steg

* [Översikt över återhämtning för Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
* [Checklista för återhämtning för specifika Azure-tjänster](https://docs.microsoft.com/azure/architecture/checklist/resiliency-per-service)
* [Datahantering för återhämtning i Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/data-management)
* [Säkerhetskopiering och haveriberedskap för Azure-program](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)
* [Återställa från regionomfattande tjänststörningar](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
* [Microsoft Service Level Agreements (SLA) för Azure-tjänster](https://azure.microsoft.com/support/legal/sla/)
