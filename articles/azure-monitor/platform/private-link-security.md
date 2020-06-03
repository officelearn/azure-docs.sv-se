---
title: Använd Azures privata länk för att på ett säkert sätt ansluta nätverk till Azure Monitor
description: Använd Azures privata länk för att på ett säkert sätt ansluta nätverk till Azure Monitor
author: nkiest
ms.author: nikiest
ms.topic: conceptual
ms.date: 05/20/2020
ms.subservice: ''
ms.openlocfilehash: 95345ba864d498190186e1a366c8551be97c33f5
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84299721"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Använd Azures privata länk för att på ett säkert sätt ansluta nätverk till Azure Monitor

> [!IMPORTANT]
> För tillfället måste du **begära åtkomst** för att använda den här funktionen. Du kan använda för åtkomst med hjälp av [registrerings formuläret](https://aka.ms/AzMonPrivateLinkSignup).


Med [Azures privata länk](../../private-link/private-link-overview.md) kan du på ett säkert sätt länka Azure PaaS-tjänster till ditt virtuella nätverk med hjälp av privata slut punkter. För många tjänster skapar du bara en slut punkt per resurs. Azure Monitor är dock en Constellation av olika sammankopplade tjänster som arbetar tillsammans för att övervaka dina arbets belastningar. Därför har vi skapat en resurs som kallas en Azure Monitor Private Link-omfånget (AMPLS) som gör att du kan definiera gränserna för ditt övervaknings nätverk och ansluta till ditt virtuella nätverk. Den här artikeln beskriver när du ska använda och hur du konfigurerar ett Azure Monitor privat länk omfång.

## <a name="advantages"></a>Fördelar

Med privat länk kan du:

- Anslut privat till Azure Monitor utan att öppna någon offentlig nätverks åtkomst
- Se till att dina övervaknings data endast nås via auktoriserade privata nätverk
- Förhindra data exfiltrering från dina privata nätverk genom att definiera vissa Azure Monitor resurser som ansluter via din privata slut punkt
- Anslut ett privat lokalt nätverk till Azure Monitor med ExpressRoute och privat länk
- Behåll all trafik i Microsoft Azure stamnät nätverket

Mer information finns i [viktiga fördelar med privat länk](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Hur det fungerar

Azure Monitor privat länk omfång är en grupperings resurs för att ansluta en eller flera privata slut punkter (och därmed de virtuella nätverken de finns i) till en eller flera Azure Monitor resurser. Resurserna omfattar Log Analytics arbets ytor och Application Insights komponenter.

![Diagram över resurs sto pol Ogin](./media/private-link-security/private-link-topology-1.png)

> [!NOTE]
> En enskild Azure Monitor resurs kan tillhöra flera AMPLSs, men du kan inte ansluta ett enskilt VNet till fler än en AMPLS. 

## <a name="planning-based-on-your-network"></a>Planera baserat på ditt nätverk

Innan du konfigurerar dina AMPLS-resurser bör du ta hänsyn till kraven på nätverks isolering. Utvärdera dina virtuella nätverks åtkomst till offentligt Internet och åtkomst begränsningarna för var och en av dina Azure Monitor resurser (det vill säga Application Insights komponenter och Log Analytics arbets ytor).

### <a name="evaluate-which-virtual-networks-should-connect-to-a-private-link"></a>Utvärdera vilka virtuella nätverk som ska anslutas till en privat länk

Börja med att utvärdera vilka av dina virtuella nätverk (virtuella nätverk) som har begränsad åtkomst till Internet. Virtuella nätverk som har gratis internet kanske inte behöver en privat länk för att få åtkomst till dina Azure Monitor-resurser. De övervaknings resurser som din virtuella nätverk ansluter till kan begränsa inkommande trafik och kräva en anslutning för privat anslutning (antingen för logg inmatning eller fråga). I sådana fall måste även ett VNet som har åtkomst till det offentliga Internet måste ansluta till dessa resurser via en privat länk och via en AMPLS.

### <a name="evaluate-which-azure-monitor-resources-should-have-a-private-link"></a>Utvärdera vilka Azure Monitor-resurser som har en privat länk

Granska var och en av dina Azure Monitor resurser:

- Bör resursen bara tillåta inmatning av loggar från resurser som finns på vissa virtuella nätverk?
- Ska resursen endast frågas av klienter som finns på vissa virtuella nätverk?

Om svaret på någon av dessa frågor är Ja anger du de begränsningar som beskrivs i [konfigurera Log Analytics](#configure-log-analytics) arbets ytor och [konfigurerar Application Insights komponenter](#configure-application-insights) och kopplar dessa resurser till en eller flera AMPLS. Virtuella nätverk som ska ha åtkomst till dessa övervaknings resurser måste ha en privat slut punkt som ansluter till relevanta AMPLS.
Kom ihåg – du kan ansluta samma arbets ytor eller program till flera AMPLS för att tillåta att de nås av olika nätverk.

### <a name="group-together-monitoring-resources-by-network-accessibility"></a>Gruppera samman övervakning av resurser efter nätverks tillgänglighet

Eftersom varje VNet bara kan ansluta till en AMPLS-resurs, måste du gruppera de övervaknings resurser som ska vara tillgängliga i samma nätverk. Det enklaste sättet att hantera den här grupperingen är att skapa en AMPLS per VNet och välja de resurser som ska anslutas till nätverket. Men för att minska resurserna och förbättra hanterbarheten kanske du vill återanvända en AMPLS över nätverk.

Om dina interna virtuella nätverk till exempel VNet1 och VNet2 ska ansluta till arbets ytorna Workspace1 och Workspace2 och Application Insights komponent Application Insights 3, associerar du alla tre resurserna till samma AMPLS. Om VNet3 endast ska ha åtkomst till Workspace1 skapar du en annan AMPLS-resurs, associerar Workspace1 till den och ansluter VNet3 som visas i följande diagram:

![Diagram över AMPLS en topologi](./media/private-link-security/ampls-topology-a-1.png)

![Diagram över AMPLS B-topologi](./media/private-link-security/ampls-topology-b-1.png)

## <a name="example-connection"></a>Exempel anslutning

Börja med att skapa en Azure Monitor privat länk omfångs resurs.

1. Gå till **skapa en resurs** i Azure Portal och sök efter **Azure Monitor privat länk omfång**.

   ![Sök omfång för Azure Monitor privat länk](./media/private-link-security/ampls-find-1c.png)

2. Klicka på **skapa**.
3. Välj en prenumeration och en resurs grupp.
4. Ge AMPLS ett namn. Det är bäst att använda ett namn som är tydligt vilket syfte och vilken säkerhets gräns som området ska användas för, så att ingen av misstag kan bryta nätverks säkerhets gränser. Till exempel "AppServerProdTelem".
5. Klicka på **Granska + skapa**. 

   ![Skapa Azure Monitor privat länk omfång](./media/private-link-security/ampls-create-1d.png)

6. Låt validerings passet ta en titt och klicka sedan på **skapa**.

## <a name="connect-azure-monitor-resources"></a>Anslut Azure Monitor resurser

Du kan ansluta din AMPLS först till privata slut punkter och sedan Azure Monitor resurser eller vice versa, men anslutnings processen går snabbare om du börjar med dina Azure Monitor-resurser. Så här ansluter vi Azure Monitor Log Analytics arbets ytor och Application Insights komponenter till en AMPLS

1. I sökområdet Azure Monitor privat länk klickar du på **Azure Monitor resurser** i den vänstra menyn. Klicka på knappen **Lägg till**.
2. Lägg till arbets ytan eller komponenten. Om du klickar på knappen **Lägg till** visas en dialog ruta där du kan välja Azure Monitor resurser. Du kan bläddra igenom dina prenumerationer och resurs grupper, eller så kan du skriva in namnet för att filtrera ned dem. Välj arbets ytan eller komponenten och klicka på **tillämpa** för att lägga till dem i ditt omfång.

    ![Skärm bild av Välj ett omfångs-UX](./media/private-link-security/ampls-select-2.png)

### <a name="connect-to-a-private-endpoint"></a>Anslut till en privat slut punkt

Nu när du har resurser som är anslutna till din AMPLS skapar du en privat slut punkt för att ansluta vårt nätverk. Du kan göra det här för att utföra den här uppgiften i det [Azure Portal privata länk centret](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)eller inuti Azure Monitor privata länk omfång, som du gjort i det här exemplet.

1. I din omfattnings resurs klickar du på **anslutningar för privata slut punkter** i den vänstra resurs menyn. Klicka på **privat slut punkt** för att starta processen för att skapa slut punkten. Du kan också godkänna anslutningar som startades i det privata länk centret här genom att markera dem och klicka på **Godkänn**.

    ![Skärm bild av UX för privata slut punkts anslutningar](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Välj prenumeration, resurs grupp och namn för slut punkten och regionen som den ska leva i. Regionen måste vara samma region som det virtuella nätverk som du ska ansluta det till.

3. Klicka på **Nästa: resurs**. 

4. På resurs skärmen

   a. Välj den **prenumeration** som innehåller din Azure Monitor privata scope-resurs. 

   b. För **resurs typ**väljer du **Microsoft. Insights/privateLinkScopes**. 

   c. I list rutan **resurs** väljer du det definitions område för privata länkar som du skapade tidigare. 

   d. Klicka på **Nästa: konfiguration >**.
      ![Skärm bild av Välj Skapa privat slut punkt](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. I konfigurations fönstret,

   a.    Välj det **virtuella nätverk** och **undernät** som du vill ansluta till dina Azure Monitor-resurser. 
 
   b.    Välj **Ja** för **integrering med privat DNS-zon**och låt den automatiskt skapa en ny privat DNS zon. 
 
   c.    Klicka på **Granska + skapa**.
 
   d.    Tillåt validerings pass. 
 
   e.    Klicka på **Skapa**. 

    ![Skärm bild av Välj Skapa privat Endpoint2](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Nu har du skapat en ny privat slut punkt som är ansluten till den här Azure Monitor privata länk omfånget.

## <a name="configure-log-analytics"></a>Konfigurera Log Analytics

Gå till Azure Portal. I din Azure Monitor Log Analytics arbets ytans resurs är ett meny objekts **nätverks isolering** på den vänstra sidan. Du kan styra två olika tillstånd från den här menyn. 

![LA nätverks isolering](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

Först kan du ansluta den här Log Analytics resursen till alla Azure Monitor privata länk omfattningar som du har åtkomst till. Klicka på **Lägg till** och välj omfånget Azure Monitor privat länk.  Klicka på **Använd** för att ansluta. Alla anslutna omfattningar visas på den här skärmen. Genom att göra den här anslutningen kan nätverks trafiken i de anslutna virtuella nätverken komma åt den här arbets ytan. Att göra anslutningen har samma resultat som att ansluta den från omfånget som vi gjorde när vi [anslöt Azure Monitor-resurser](#connect-azure-monitor-resources).  

För det andra kan du styra hur den här resursen kan nås utanför de privata länk definitionerna som anges ovan. Om du ställer in **Tillåt offentligt nätverks åtkomst för** inmatning till **Nej**kan inte datorer utanför de anslutna omfattningarna Ladda upp data till den här arbets ytan. Om du ställer in **Tillåt offentlig nätverks åtkomst för frågor** till **Nej**, kan datorer utanför omfattningarna inte komma åt data på den här arbets ytan. Dessa data omfattar åtkomst till arbets böcker, instrument paneler, API-baserade klient upplevelser, insikter i Azure Portal och mycket annat. Upplevelser som körs utanför Azure Portal som använder Log Analytics data måste också köras i det privata, länkade VNET.

Att begränsa åtkomsten på det här sättet gäller endast data i arbets ytan. Konfigurations ändringar, inklusive aktivering av de här åtkomst inställningarna på eller av, hanteras av Azure Resource Manager. Begränsa åtkomsten till Resource Manager med lämpliga roller, behörigheter, nätverks kontroller och granskning. Mer information finns i [Azure Monitor roller, behörigheter och säkerhet](roles-permissions-security.md).

> [!NOTE]
> Loggar och mått som överförs till en arbets yta via [diagnostikinställningar](diagnostic-settings.md) går via en säker privat Microsoft-kanal och styrs inte av de här inställningarna.

## <a name="configure-application-insights"></a>Konfigurera Application Insights

Gå till Azure Portal. I din Azure Monitor Application Insights komponent resurs ett meny objekts **nätverks isolering** på den vänstra sidan. Du kan styra två olika tillstånd från den här menyn.

![AI-nätverks isolering](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Först kan du ansluta den här Application Insights resursen till Azure Monitor de privata länk omfattningar som du har åtkomst till. Klicka på **Lägg till** och välj **omfånget Azure Monitor privat länk**. Klicka på Använd för att ansluta. Alla anslutna omfattningar visas på den här skärmen. Genom att göra den här anslutningen kan nätverks trafiken i de anslutna virtuella nätverken komma åt den här komponenten. Att göra anslutningen har samma resultat som att ansluta den från omfånget som vi gjorde när vi [anslöt Azure Monitor-resurser](#connect-azure-monitor-resources). 

För det andra kan du styra hur den här resursen kan nås utanför de privata länk omfattningar som anges ovan. Om du ställer in **Tillåt offentligt nätverks åtkomst för** inmatning till **Nej**kan inte datorer eller SDK: er utanför de anslutna omfattningarna Ladda upp data till den här komponenten. Om du ställer in **Tillåt offentlig nätverks åtkomst för frågor** till **Nej**, kommer datorer utanför omfattningarna inte att kunna komma åt data i den här Application Insights resursen. Dessa data omfattar till gång till APM-loggar, Mät värden och Live Metrics-dataströmmen, samt erfarenheter som bygger på de flesta arbets böcker, instrument paneler, API-baserade klient upplevelser, insikter i Azure Portal och mycket mer. 

Observera att användnings upplevelser som inte är Portal måste köras i det privata, länkade VNET som innehåller de övervakade arbets belastningarna. 

Du måste lägga till resurser som är värdar för de övervakade arbets belastningarna till den privata länken. Här är en [dokumentation](https://docs.microsoft.com/azure/app-service/networking/private-endpoint) om hur du gör detta för app Services.

Att begränsa åtkomsten på det här sättet gäller endast för data i Application Insights-resursen. Konfigurations ändringar, inklusive aktivering av de här åtkomst inställningarna på eller av, hanteras av Azure Resource Manager. Begränsa i stället åtkomst till Resource Manager med lämpliga roller, behörigheter, nätverks kontroller och granskning. Mer information finns i [Azure Monitor roller, behörigheter och säkerhet](roles-permissions-security.md).

> [!NOTE]
> För att helt skydda arbets ytans baserade Application Insights måste du låsa både åtkomst till Application Insights resurs och den underliggande Log Analytics arbets ytan.

> [!NOTE]
> Kod på kod nivå (profiler/fel sökare) stöder för närvarande inte privat länk.

## <a name="use-apis-and-command-line"></a>Använda API: er och kommando rad

Du kan automatisera processen som beskrivs tidigare med Azure Resource Manager mallar och kommando rads gränssnitt.

Om du vill skapa och hantera privata länk omfattningar använder du [AZ Monitor Private-Link-scope](https://docs.microsoft.com/cli/azure/monitor/private-link-scope?view=azure-cli-latest). Med det här kommandot kan du skapa omfattningar, associera Log Analytics arbets ytor och Application Insights komponenter och lägga till/ta bort/godkänna privata slut punkter.

Om du vill hantera nätverks åtkomst använder du flaggorna `[--ingestion-access {Disabled, Enabled}]` och `[--query-access {Disabled, Enabled}]` på [Log Analytics arbets ytor](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest) eller [Application Insights komponenter](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest).

## <a name="collect-custom-logs-over-private-link"></a>Samla in anpassade loggar över privat länk

Lagrings konton används i inmatnings processen för anpassade loggar. Som standard används tjänst hanterade lagrings konton. Men för att mata in anpassade loggar på privata länkar måste du använda dina egna lagrings konton och koppla dem till Log Analytics arbets ytor. Se mer information om hur du konfigurerar sådana konton med hjälp av [kommando raden](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace/linked-storage?view=azure-cli-latest).

Mer information om hur du ansluter ditt eget lagrings konto finns i [kundägda lagrings konton för logg](private-storage.md) inmatning

## <a name="restrictions-and-limitations"></a>Begränsningar och begränsningar

### <a name="agents"></a>Agenter

De senaste versionerna av Windows-och Linux-agenterna måste användas i privata nätverk för att möjliggöra säker telemetri av telemetri till Log Analytics arbets ytor. Äldre versioner kan inte överföra övervaknings data i ett privat nätverk.

**Windows-agenten för Log Analytics**

Använd Log Analytics agent version 10.20.18038.0 eller senare.

**Log Analytics-agent för Linux**

Använd agent version 1.12.25 eller senare. Om du inte kan köra kör du följande kommandon på den virtuella datorn.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure Portal

Om du vill använda Azure Monitor Portal upplevelser som Application Insights och Log Analytics måste du tillåta att Azure Portal-och Azure Monitor-tillägg kan nås i privata nätverk. Lägg till **AzureActiveDirectory**, **AzureResourceManager**, **AzureFrontDoor. FirstParty**och **AzureFrontDoor. frontend** [service-Taggar](../../firewall/service-tags.md) i brand väggen.

### <a name="programmatic-access"></a>Programmässig åtkomst

Om du vill använda REST API, [CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest) eller PowerShell med Azure Monitor i privata nätverk lägger du till [service tag-](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)  **AzureActiveDirectory** och **AzureResourceManager** i brand väggen.

Genom att lägga till dessa taggar kan du utföra åtgärder som att fråga logg data, skapa och hantera Log Analytics arbets ytor och AI-komponenter.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Application Insights SDK-nedladdningar från ett Content Delivery Network

Paketera JavaScript-koden i skriptet så att webbläsaren inte försöker hämta kod från ett CDN. Ett exempel finns i [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="log-analytics-solution-download"></a>Hämtning av Log Analyticss lösning

Om du vill tillåta att Log Analytics agent laddar ned lösnings paket lägger du till rätt fullständigt kvalificerade domän namn i listan över tillåtna domän namn för brand väggen. 


| Moln miljö | Agentresurs | Portar | Riktning |
|:--|:--|:--|:--|
|Azure, offentlig     | scadvisor.blob.core.windows.net         | 443 | Utgående
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Utgående
|Azure Kina 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Utgående

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [privat lagring](private-storage.md)
