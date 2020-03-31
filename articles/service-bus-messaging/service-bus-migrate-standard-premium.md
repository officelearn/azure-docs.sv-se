---
title: Migrera Azure Service Bus-namnområden – standard till premium
description: Guide för att tillåta migrering av befintliga Azure Service Bus-standardnamnområden till premium
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 27e3260b91bebee14ff12188a7dbd6c7cf76355c
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385035"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Migrera befintliga standardnamnområden för Azure Service Bus till premiumnivån

Tidigare erbjöd Azure Service Bus endast namnområden på standardnivån. Namnområden är inställningar för flera innehavare som är optimerade för miljöer med lågt dataflöde och utvecklare. Premium-nivån erbjuder dedikerade resurser per namnområde för förutsägbar svarstid och ökat dataflöde till ett fast pris. Premiumnivån är optimerad för miljöer med hög genomströmning och produktion som kräver ytterligare företagsfunktioner.

I den här artikeln beskrivs hur du migrerar befintliga standardnivånamnområden till premiumnivån.  

>[!WARNING]
> Migreringen är avsedd för standardnamnområden för Service Bus som ska uppgraderas till premiumnivån. Migreringsverktyget stöder inte nedgradering.

Några av de punkter att notera:

- Den här migreringen är tänkt att ske på plats, vilket innebär att befintliga avsändande- och mottagarprogram **inte kräver några ändringar**i kod eller konfiguration . Den befintliga anslutningssträngen pekar automatiskt på det nya premiumnamnområdet.
- **Premiumnamnområdet** bör inte ha **några entiteter** i det för att migreringen ska lyckas.
- Alla **entiteter** i standardnamnområdet **kopieras** till premiumnamnområdet under migreringsprocessen.
- Migrering stöder **1 000 entiteter per meddelandeenhet** på premiumnivån. Om du vill identifiera hur många meddelandeenheter du behöver börjar du med antalet entiteter som du har på det aktuella standardnamnområdet.
- Du kan inte migrera direkt från **grundläggande nivå** till **premiumnivå,** men du kan göra det indirekt genom att migrera från grundläggande till standard först och sedan från standard till premium i nästa steg.

## <a name="migration-steps"></a>Migreringsanvisningar

Vissa villkor är associerade med migreringsprocessen. Bekanta dig med följande steg för att minska risken för fel. De här stegen beskriver migreringsprocessen och information steg för steg visas i de avsnitt som följer.

1. Skapa ett nytt premiumnamnområde.
1. Para ihop standard- och premiumnamnområdena till varandra.
1. Synkronisera (copy-over) entiteter från standarden till premiumnamnområdet.
1. Begå migreringen.
1. Töm entiteter i standardnamnområdet med hjälp av namnområdets namnområdes namnområdes namnområdes namnområdes namnområdes namnområdes namnområdes namnområdes namnområde.
1. Ta bort standardnamnområdet.

>[!IMPORTANT]
> När migreringen har bekräftats öppnar du det gamla standardnamnområdet och tömmer köerna och prenumerationerna. När meddelandena har tömts kan de skickas till det nya premiumnamnområdet som ska bearbetas av mottagarprogrammen. När köerna och prenumerationerna har tömts rekommenderar vi att du tar bort det gamla standardnamnområdet.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migrera med hjälp av Azure CLI eller PowerShell

Så här migrerar du standardnamnområdet för Service Bus till premium med hjälp av Azure CLI- eller PowerShell-verktyget.

1. Skapa ett nytt servicebusspremienamnområde. Du kan referera till [Azure Resource Manager-mallarna](service-bus-resource-manager-namespace.md) eller [använda Azure-portalen](service-bus-create-namespace-portal.md). Var noga med att välja **premium** för parametern **serviceBusSku.**

1. Ange följande miljövariabler för att förenkla migreringskommandona.

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > Aliasa/namnet efter migreringen (post_migration_dns_name) används för att komma åt den gamla standardnamnområdespostenmigrering. Använd detta för att tömma köerna och prenumerationerna och sedan ta bort namnområdet.

1. Para ihop standard- och premiumnamnområdena och starta synkroniseringen med hjälp av följande kommando:

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. Kontrollera status för migreringen med hjälp av följande kommando:

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    Migreringen anses vara fullständig när du ser följande värden:

    * MigrationState = "Aktiv"
    * väntaNdeReplicationsOperationsCount = 0
    * etableringState = "Lyckades"

    Det här kommandot visar också migreringskonfigurationen. Kontrollera att värdena är korrekt inställda. Kontrollera också premiumnamnområdet i portalen för att säkerställa att alla köer och ämnen har skapats och att de matchar det som fanns i standardnamnområdet.

1. Utför migreringen genom att köra följande fullständiga kommando:

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migrera med hjälp av Azure-portalen

Migrering med hjälp av Azure-portalen har samma logiska flöde som migrerar med hjälp av kommandona. Följ dessa steg för att migrera med hjälp av Azure-portalen.

1. Välj **Migrera till premium**på menyn **Navigering** i den vänstra rutan . Klicka på knappen **Kom igång** för att fortsätta till nästa sida.
    ![Målsida för migrering][]

1. Slutför **installationen**.
   ![Namnområde för inställningar][]
   1. Skapa och tilldela premiumnamnområdet att migrera det befintliga standardnamnområdet till.
        ![Namnområde för inställningar - skapa premiumnamnområde][]
   1. Välj ett **inläggsmigreringsnamn**. Du ska använda det här namnet för att komma åt standardnamnområdet när migreringen är klar.
        ![Namnområde för inställningar - välj namn för postmigrering][]
   1. Välj **Nästa för** att fortsätta.
1. Synkronisera entiteter mellan standard- och premiumnamnområden.
    ![Namnområde för inställningar - synkroniseringsentiteter - start][]

   1. Välj **Starta synkronisering** om du vill börja synkronisera entiteterna.
   1. Välj **Ja** i dialogrutan för att bekräfta och starta synkroniseringen.
   1. Vänta tills synkroniseringen är klar. Statusen är tillgänglig i statusfältet.
        ![Namnområde för inställningar - synkroniseringsentiteter - förlopp][]
        >[!IMPORTANT]
        > Om du av någon anledning behöver avbryta migreringen läser du avsnittet avbryt i avsnittet Vanliga frågor och svar i det här dokumentet.
   1. När synkroniseringen är klar väljer du **Nästa** längst ned på sidan.

1. Granska ändringar på sammanfattningssidan. Välj **Fullständig migrering** om du vill byta namnområden och slutföra migreringen.
    ![Växla namnområde - switch-menyn][]  
    Bekräftelsesidan visas när migreringen är klar.
    ![Växla namnområde - framgång][]

## <a name="caveats"></a>Varningar

Vissa av de funktioner som tillhandahålls av Azure Service Bus Standard-nivån stöds inte av Azure Service Bus Premium-nivån. Dessa är avsiktligt eftersom premiumnivån erbjuder dedikerade resurser för förutsägbart dataflöde och svarstid.

Här är en lista över funktioner som inte stöds av Premium och deras begränsning -

### <a name="express-entities"></a>Expressenheter

   Expressentiteter som inte arkiverar några meddelandedata till lagring stöds inte i Premium. Dedikerade resurser gav betydande förbättring av dataflödet samtidigt som data bevaras, vilket förväntas av alla företagsmeddelanden.

   Under migreringen skapas alla expressentiteter i standardnamnområdet på Premium-namnområdet som en icke-expressentitet.

   Om du använder ARM-mallar (Azure Resource Manager) ska du se till att du tar bort flaggan enableExpress från distributionskonfigurationen så att dina automatiska arbetsflöden körs utan fel.

### <a name="partitioned-entities"></a>Partitionerade enheter

   Partitionerade entiteter stöddes på standardnivån för att ge bättre tillgänglighet i en konfiguration med flera innehavare. Med tillhandahållandet av dedikerade resurser som är tillgängliga per namnområde på Premium-nivån behövs inte längre detta.

   Under migreringen skapas alla partitionerade entiteter i standardnamnområdet på Premium-namnområdet som en icke-partitionerad entitet.

   Om arm-mallen anger "enablePartitioning" till 'true' för en viss kö eller ett visst ämne ignoreras den av mäklaren.

## <a name="faqs"></a>Vanliga frågor och svar

### <a name="what-happens-when-the-migration-is-committed"></a>Vad händer när migreringen begås?

När migreringen har bekräftats pekar anslutningssträngen som pekade på standardnamnområdet på premiumnamnområdet.

Avsändare- och mottagarprogrammen kopplas från standardnamnområdet och återansluter automatiskt till premiumnamnområdet.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Vad gör jag när standarden för premiummigrering är klar?

Standarden till premiummigrering säkerställer att entitetsmetadata som ämnen, prenumerationer och filter kopieras från standardnamnområdet till premiumnamnområdet. Meddelandedata som har bekräftats till standardnamnområdet kopieras inte från standardnamnområdet till premiumnamnområdet.

Standardnamnområdet kan ha vissa meddelanden som har skickats och bekräftats medan migreringen pågick. Töm dessa meddelanden manuellt från standardnamnområdet och skicka dem manuellt till premiumnamnområdet. Om du vill tömma meddelandena manuellt använder du en konsolapp eller ett skript som tömmer standardnamnområdesentiteterna med hjälp av DNS-namnet Post Migration som du angav i migreringskommandona. Skicka dessa meddelanden till premiumnamnområdet så att de kan bearbetas av mottagarna.

När meddelandena har tömts tar du bort standardnamnområdet.

>[!IMPORTANT]
> När meddelandena från standardnamnområdet har tömts tar du bort standardnamnområdet. Detta är viktigt eftersom anslutningssträngen som ursprungligen refererade till standardnamnområdet nu refererar till premiumnamnområdet. Du behöver inte standardnamnområdet längre. Om du tar bort det standardnamnområde som du har migrerat minskar förvirringen senare.

### <a name="how-much-downtime-do-i-expect"></a>Hur mycket driftstopp förväntar jag mig?

Migreringsprocessen är avsedd att minska den förväntade stilleståndstiden för programmen. Driftstopp minskar med hjälp av anslutningssträngen som avsändaren och mottagarprogrammen använder för att peka på det nya premiumnamnområdet.

Driftstopp som upplevs av programmet är begränsad till den tid det tar att uppdatera DNS-posten för att peka på premiumnamnområdet. Driftstopp är cirka 5 minuter.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Måste jag göra några konfigurationsändringar när jag gör migreringen?

Nej, det finns inga kod- eller konfigurationsändringar som behövs för att utföra migreringen. Anslutningssträngen som avsändare och mottagarprogram använder för att komma åt standardnamnområdet mappas automatiskt för att fungera som ett alias för premiumnamnområdet.

### <a name="what-happens-when-i-abort-the-migration"></a>Vad händer när jag avbryter migreringen?

Migreringen kan avbrytas antingen `Abort` med hjälp av kommandot eller med hjälp av Azure-portalen.

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure Portal

![Avbryt flöde -][]
![avbryt synkronisering Avbryt flöde - avbryt dig][]

När migreringsprocessen avbryts avbryts processen för att kopiera entiteterna (ämnen, prenumerationer och filter) från standarden till premiumnamnområdet och bryter ihopkopplingen.

Anslutningssträngen uppdateras inte för att peka på premiumnamnområdet. Dina befintliga program fortsätter att fungera som de gjorde innan du startade migreringen.

Den tas dock inte bort entiteterna på premiumnamnområdet eller tar bort premiumnamnområdet. Ta bort entiteterna manuellt om du har bestämt dig för att inte gå vidare med migreringen.

>[!IMPORTANT]
> Om du bestämmer dig för att avbryta migreringen tar du bort det premiumnamnområde som du har etablerat för migreringen så att du inte debiteras för resurserna.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Jag vill inte behöva tömma meddelandena. Vad gör jag nu?

Det kan finnas meddelanden som skickas av avsända program och som har gjorts i lagringen på standardnamnområdet medan migreringen äger rum och strax innan migreringen har begåtts.

Under migreringen kopieras inte den faktiska meddelandedata/nyttolasten från standard till premiumnamnområdet. Meddelandena måste tömmas manuellt och sedan skickas till premiumnamnområdet.

Men om du kan migrera under ett planerat underhålls-/städfönster och du inte vill tömma och skicka meddelanden manuellt gör du så här:

1. Stoppa avsändarna. Mottagarprogrammen bearbetar de meddelanden som för närvarande finns i standardnamnområdet och tömmer kön.
1. När köerna och prenumerationerna i standardnamnområdet är tomma följer du proceduren som beskrivs tidigare för att köra migreringen från standarden till premiumnamnområdet.
1. När migreringen är klar kan du starta om avsändningsprogrammen.
1. Avsändare och mottagare kommer nu automatiskt att ansluta till premiumnamnområdet.

    >[!NOTE]
    > Du behöver inte stoppa mottagarprogrammen för migreringen.
    >
    > När migreringen är klar kopplas mottagarprogrammen från standardnamnområdet och ansluter automatiskt till premiumnamnområdet.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [skillnaderna mellan standard- och premiummeddelanden](./service-bus-premium-messaging.md).
* Lär dig mer om aspekterna för [hög tillgänglighet och geokatastrofåterställning för Service Bus premium.](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)

[Målsida för migrering]: ./media/service-bus-standard-premium-migration/1.png
[Namnområde för inställningar]: ./media/service-bus-standard-premium-migration/2.png
[Namnområde för inställningar - skapa premiumnamnområde]: ./media/service-bus-standard-premium-migration/3.png
[Namnområde för inställningar - välj namn för postmigrering]: ./media/service-bus-standard-premium-migration/4.png
[Namnområde för inställningar - synkroniseringsentiteter - start]: ./media/service-bus-standard-premium-migration/5.png
[Namnområde för inställningar - synkroniseringsentiteter - förlopp]: ./media/service-bus-standard-premium-migration/8.png
[Växla namnområde - switch-menyn]: ./media/service-bus-standard-premium-migration/9.png
[Växla namnområde - framgång]: ./media/service-bus-standard-premium-migration/12.png

[Avbryt flöde - avbryt synkronisering]: ./media/service-bus-standard-premium-migration/abort1.png
[Avbryt flödet - avbryt dig]: ./media/service-bus-standard-premium-migration/abort3.png
