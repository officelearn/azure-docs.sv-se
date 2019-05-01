---
title: Migrera befintliga standard Azure Service Bus-namnområde till premium-nivån | Microsoft Docs
description: Guide för att tillåta migrering av befintliga standard Azure Service Bus-namnområde till premium
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: darosa
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2019
ms.author: aschhab
ms.openlocfilehash: 65f89e234317c5a17e4443e767528fb9be9a8b72
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687066"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Migrera befintliga standard Azure Service Bus-namnområde till premium-nivån
Azure Service Bus erbjuds tidigare namnområden endast på standard-nivån. Namnområden är inställningar för flera innehavare som är optimerade för utvecklarmiljöer och lågt dataflöde. Premium-nivån erbjuder dedikerade resurser per namnområde för förutsägbar latens och ökat genomflöde till ett fast pris. Premium-nivån är optimerat för stora dataflöden och produktionsmiljöer som kräver ytterligare företagsfunktioner.

Den här artikeln beskriver hur du migrerar befintliga namnområden på standard-nivå till premium-nivån.

>[!WARNING]
> Migreringen är avsedd för Service Bus standard-namnområden som ska uppgraderas till premium-nivån. Migreringsverktyget stöder inte nedgradering.

Några av de saker att Observera: 
- Den här migreringen är avsedd att inträffa på plats, vilket innebär att befintliga program för avsändaren och mottagaren **inte kräver ändringar i koden eller konfigurationen**. Den befintliga anslutningssträngen kommer automatiskt att peka mot det nya premium-namnområdet.
- Den **premium** namnområdet ska ha **inga entiteter** i den för migreringen ska lyckas. 
- Alla **entiteter** i standard-namnområdet är **kopieras** premium-namnområdet under migreringsprocessen. 
- Migrering stöder **1 000 entiteter per meddelandeenhet** på premium-nivån. Börja med antalet enheter som du har i din aktuella standardnamnområde för att identifiera hur många meddelanden enheter som du behöver. 

## <a name="migration-steps"></a>Migreringssteg
Vissa villkor är associerade med migreringen. Bekanta dig med följande steg för att minska risken för fel. Dessa steg beskriver migreringsprocessen och stegvis information finns i avsnitten som följer.

1. Skapa ett nytt premium-namnområde.
1. Koppla standard och premium-namnområden till varandra.
1. Synkronisering (kopiera över) enheter från standard till premium-namnområde.
1. Checka in migreringen.
1. Töm entiteter i standard-namnområdet med hjälp av namnet på namnområdet efter migreringen.
1. Ta bort standard-namnområdet.

>[!IMPORTANT]
> När migreringen har bekräftats kan komma åt den gamla standardnamnområde och tömma köer och prenumerationer. När meddelandena har varit tömda, kan de skickas till det nya premium-namnområdet som ska bearbetas av mottagare-program. När du har varit tömda köer och prenumerationer, rekommenderar vi att du tar bort gamla standard-namnområdet.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migrera med hjälp av Azure CLI eller PowerShell

Följ dessa steg för att migrera standard Service Bus-namnområdet till premium med hjälp av Azure CLI eller PowerShell-verktyg.

1. Skapa en ny Service Bus premium-namnområde. Du kan referera till den [Azure Resource Manager-mallar](service-bus-resource-manager-namespace.md) eller [använder Azure-portalen](service-bus-create-namespace-portal.md). Se till att välja **premium** för den **serviceBusSku** parametern.

1. Ange följande miljövariabler för att förenkla migreringen-kommandon.
   ```azurecli
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > Uppgifter efter migrering/aliasnamnet (post_migration_dns_name) används för att få åtkomst till den gamla standardnamnområde efter migreringen. Används för att tömma köer och prenumerationer och sedan ta bort namnområdet.

1. Koppla standard och premium-namnområden och starta synkroniseringen med hjälp av följande kommando:

    ```azurecli
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


1. Kontrollera status för migreringen med hjälp av följande kommando:
    ```azurecli
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    Migreringen anses slutförd när du ser följande värden:
    * MigrationState = ”aktiv”
    * pendingReplicationsOperationsCount = 0
    * provisioningState = ”Succeeded”

    Det här kommandot visar även konfigurationen för migrering. Kontrollera värdena är korrekta. Kontrollera också premium-namnområde i portalen för att se till att alla köer och ämnen har skapats och att de matchar vad var tillgängliga i standard-namnområdet.

1. Checka in migreringen genom att köra kommandot slutförts:
   ```azurecli
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migrera med hjälp av Azure-portalen

Migrering med hjälp av Azure portal har det samma logiska flödet som att migrera med hjälp av kommandon. Följ dessa steg om du vill migrera med hjälp av Azure portal.

1. På den **navigering** menyn till vänster, Välj **migrera till premium**. Klicka på den **börjar** för att fortsätta till nästa sida.
    ![Landningssida för migrering][]

1. Fullständig **installationsprogrammet**.
   ![Konfigurera namnområdet][]
   1. Skapa och tilldela premium-namnområdet för att migrera befintliga standard-namnområdet till.
        ![Konfigurera namnområdet – skapa premium-namnområde][]
   1. Välj en **Postmigrering namn**. Du använder det här namnet för att få åtkomst till standard-namnområdet när migreringen är klar.
        ![Konfigurera namnområdet – Välj efter migreringen namn][]
   1. Välj **”nästa”** att fortsätta.
1. Synkronisera enheter mellan standard och premium-namnområden.
    ![Konfigurera namnområdet - sync-entiteter – start][]

   1. Välj **Starta synkronisering** att börja synkronisera entiteter.
   1. Välj **Ja** i dialogrutan för att bekräfta och starta synkroniseringen.
   1. Vänta tills synkroniseringen är klar. Statusen är tillgängliga i statusfältet.
        ![Namnområde – synkronisera entiteter - Installationsförlopp][]
        >[!IMPORTANT]
        > Om du vill avbryta migreringen av någon anledning kan granska att avbryta flödet i avsnittet vanliga frågor och svar i det här dokumentet.
   1. När synkroniseringen är klar, väljer **nästa** längst ned på sidan.

1. Granska ändringar på sammanfattningssidan. Välj **fullständig migrering** att växla namnområden och slutföra migreringen.
    ![Växla namnområde - växel menyn][] bekräftelsesidan som visas när migreringen är klar.
    ![Växeln namnområde – lyckades][]

## <a name="faqs"></a>Vanliga frågor och svar

### <a name="what-happens-when-the-migration-is-committed"></a>Vad händer när migreringen är allokerade?

När migreringen genomförs pekar anslutningssträngen som pekar på standard-namnområdet till premium-namnområde.

Avsändaren och mottagaren program kommer att koppla från standard Namespace och automatiskt återansluta till premium-namnområde.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Vad gör jag när standard till premium-migreringen är klar?

Standard till premium-migreringen säkerställer att entitetens metadata, till exempel ämnen, prenumerationer och filter kopieras från standard-namnområdet till premium-namnområde. Meddelandedata som har utförts till standard-namnområdet kopieras inte från standard-namnområdet till premium-namnområde.

Standard-namnområdet kan ha några meddelanden som skickades och allokerat under migreringen pågår. Manuellt tömma meddelandena från standard Namespace och skicka dem manuellt till premium Namespace. Om du vill manuellt Rensa meddelanden, använder du en konsolapp eller ett skript som tömmer standardnamnområde entiteter genom att använda efter migreringen DNS-namnet som du angav i kommandona migrering. Skicka dessa meddelanden till premium-namnområde så att de kan bearbetas av mottagarna.

När meddelandena har varit tömda, kan du ta bort standard-namnområdet.

>[!IMPORTANT]
> När meddelanden från standard-namnområdet har tagits tömda, kan du ta bort standard-namnområdet. Detta är viktigt eftersom den anslutningssträng som ursprungligen avses standard-namnområdet nu refererar till premium-namnområde. Du behöver inte standard Namespace längre. Tar bort standard-namnområdet som du har migrerat bidrar till att minska senare förvirring.

### <a name="how-much-downtime-do-i-expect"></a>Hur stora driftstopp förväntar jag?
Migreringsprocessen är avsedd att minska den förväntade stilleståndstiden för program. Avbrott minskar med hjälp av den anslutningssträng som programmen avsändaren och mottagaren använder för att peka på det nya premium-namnområdet.

Nedtid som uppstår av programmet är begränsad till den tid det tar för att uppdatera DNS-posten ska peka på premium-namnområde. Avbrottstid är cirka 5 minuter.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Måste jag göra några konfigurationsändringar när du migrerar?
Nej, det finns inga kodning eller ändringar som behövs för att göra migreringen. Anslutningssträngen som avsändaren och mottagaren program använda för att få åtkomst till standard Namespace mappas automatiskt för att fungera som ett alias för premium-namnområde.

### <a name="what-happens-when-i-abort-the-migration"></a>Vad händer när jag avbryta migreringen?
Migreringen kan avbrytas antingen med hjälp av den `Abort` kommandot eller med hjälp av Azure-portalen. 

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure Portal

![Avbryt flow – Avbryt synkronisering][]
![avbryta flödet – avbryta klar][]

När migreringen har avbrutits, avbryter processen att kopiera entiteter (ämnen, prenumerationer och filter) från standard till premium-namnområde och bryts kopplingen.

Anslutningssträngen är inte uppdaterats för att peka på premium-namnområde. Dina befintliga program fortsätta att fungera som de gjorde innan du börjar migreringen.

Dock inte det ta bort entiteter på premium-namnområde eller ta bort premium-namnområde. Ta bort entiteter manuellt om du inte valt att gå vidare med migreringen.

>[!IMPORTANT]
> Ta bort premium Namespace som du har etablerat för migreringen så att du inte debiteras för resurserna om du vill avbryta migreringen.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Jag vill inte behöva tömma meddelanden. Vad gör jag nu?

Det kan finnas meddelanden som skickas av avsändaren program och paket för lagring på standard Namespace medan migreringen sker och precis innan strävar efter migreringen.

Under migreringen kopieras inte den faktiska data/meddelandenyttolast från standard till premium-namnområde. Meddelanden måste tömda manuellt och sedan skickas till premium-namnområde.

Men om du kan migrera under ett planerat underhåll/Underhåll-fönster och du inte vill att tömma manuellt och skicka meddelanden, Följ dessa steg:

1. Stoppa avsändar-program. Mottagare program kommer att bearbeta meddelanden som finns för närvarande i standard-namnområdet och kommer att tömma kön.
1. När köer och prenumerationer i standard Namespace är tom, följer du proceduren som beskrivs tidigare för att köra migreringen från standard till premium-namnområde.
1. När migreringen är klar kan du starta om avsändaren-program.
1. Avsändarna och mottagarna nu ansluter automatiskt till premium-namnområde.

    >[!NOTE]
    > Du behöver inte stoppa mottagar-program för migreringen.
    >
    > När migreringen är klar, koppla från standard-namnområdet mottagar-program och ansluter automatiskt till premium-namnområde.

## <a name="next-steps"></a>Nästa steg

* Läs mer om den [skillnaderna mellan standard och premium Messaging](./service-bus-premium-messaging.md).
* Lär dig mer om den [hög tillgänglighet och Geo-Disaster recovery-aspekter för Service Bus premium](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

[Landningssida för migrering]: ./media/service-bus-standard-premium-migration/1.png
[Konfigurera namnområdet]: ./media/service-bus-standard-premium-migration/2.png
[Konfigurera namnområdet – skapa premium-namnområde]: ./media/service-bus-standard-premium-migration/3.png
[Konfigurera namnområdet – Välj efter migreringen namn]: ./media/service-bus-standard-premium-migration/4.png
[Konfigurera namnområdet - sync-entiteter – start]: ./media/service-bus-standard-premium-migration/5.png
[Namnområde – synkronisera entiteter - Installationsförlopp]: ./media/service-bus-standard-premium-migration/8.png
[Växla namnområde - switch-menyn]: ./media/service-bus-standard-premium-migration/9.png
[Växeln namnområde – lyckades]: ./media/service-bus-standard-premium-migration/12.png

[Avbryt flow – Avbryt synkronisering]: ./media/service-bus-standard-premium-migration/abort1.png
[Avbryt flow – avbryta klar]: ./media/service-bus-standard-premium-migration/abort3.png
