---
title: Migrera befintliga Azure Service Bus Standard-namnområden till Premium-nivån | Microsoft Docs
description: Guide för att tillåta migrering av befintliga Azure Service Bus Standard-namnområden till Premium
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
ms.openlocfilehash: 7b153c36e10f1d4e2be2a0cf42f998c31cb6473a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896588"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-premium-tier"></a>Migrera befintliga Azure Service Bus Standard-namnområden till Premium-nivån

Azure Service Bus erbjuds tidigare namnområden endast på Standard-nivån. Dessa var inställningar för flera klienter som har optimerats för utvecklarmiljöer och lågt dataflöde.

Tidigare, har Azure Service Bus utökats för att erbjuda Premium-nivån som erbjuder dedikerade resurser per namnområde för förutsägbar latens och ökat genomflöde till ett fast pris som är optimerade för stora dataflöden och produktionsmiljöer kräver ytterligare företagsfunktioner.

Den nedan verktyg kan befintliga standardnivån namnområden för att migrera till Premium-nivån.

>[!WARNING]
> Migreringen är avsedd för Service Bus Standard namnområde som ***uppgraderas*** till Premium-nivån.
>
> Verktyg för migrering ***inte*** stöder nedgradering.
>[!NOTE]
> Den här migreringen är avsedd att inträffa ***på plats***.
>
> Detta innebär att befintliga avsändaren och mottagaren program inte kräver någon kodning eller ändra.
>
> Den befintliga anslutningssträngen kommer automatiskt att peka mot det nya premium-namnområdet.
>
> Dessutom alla entiteter i Standard-namnområdet är **kopieras över** i Premium-namnområde under migreringsprocessen.
>
>
> Vi stöder ***1000 enheter per Meddelandefunktionsenhet*** på Premium, så du behöver för att identifiera hur många Meddelandefunktionsenheter, starta med antalet enheter som du har i din aktuella Standard-namnområdet.

## <a name="migration-steps"></a>Migreringssteg

>[!IMPORTANT]
> Det finns vissa varningar som är associerade med migreringen. Vi begär du att fullständigt bekanta dig med steg som ingår att minska möjligheter i fel.

Migreringsprocessen för konkreta steg för steg beskrivs i guiderna nedan.

Logiska stegen är-

1. Skapa ett nytt Premium-namnområde.
2. Koppla Standard och Premium-namnområde till varandra.
3. Synkronisera (kopiera över) enheter från Standard till Premium-namnområde
4. Checka in migreringen
5. Tömma entiteter i Standard-namnområdet med hjälp av namnet på namnområdet efter migrering
6. Ta bort Standard-namnområdet

>[!NOTE]
> När migreringen har begåtts, är det mycket viktigt att komma åt den gamla standardnamnområde och tömma köer och prenumerationer.
>
> När meddelandena har varit tömda kan de skickas till det nya premium-namnområdet som ska bearbetas av mottagare-program.
>
> När du har varit tömda köer och prenumerationer, rekommenderar vi tar bort gamla Standard-namnområdet. Du behöver inte den!

### <a name="migrate-using-azure-cli-or-powershell"></a>Migrera med hjälp av Azure CLI eller PowerShell

Om du vill migrera namnområdet för Service Bus Standard till Premium med hjälp av verktyget Azure CLI eller PowerShell, referera till den nedan guide.

1. Skapa ett nytt Service Bus Premium-namnområde. Du kan referera till den [Azure Resource Manager-mallar](service-bus-resource-manager-namespace.md) eller [använder Azure-portalen](service-bus-create-namespace-portal.md). Se till att välja ”Premium” för den **serviceBusSku** parametern.

2. Ange den nedan miljövariabler för att förenkla migreringen-kommandon.
   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the Premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the Standard namespace>
   ```

    >[!IMPORTANT]
    > Uppgifter efter migrering namn (post_migration_dns_name) används för att få åtkomst till den gamla standardnamnområde efter migreringen. Du måste använda tömma köer och prenumerationer och sedan ta bort namnområdet.

3. **Par** Standard och Premium-namnområden och **Starta synkronisering** med hjälp av den nedanstående kommando -

    ```
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


4. Kontrollera status för en migrering med hjälp av den nedanstående kommando -
    ```
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    Migreringen anses slutförd när
    * MigrationState = ”aktiv”
    * pendingReplicationsOperationsCount = 0
    * provisioningState = ”Succeeded”

    Det här kommandot visar även konfigurationen för migrering. . Dubbelkolla att kontrollera att värden är inställda som tidigare deklarerats.

    Dessutom kan också kontrollera Premium-namnområde i portalen för att se till att alla köer och ämnen har skapats och att de matchar vad fanns på Standard-namnområdet.

5. Checka in migreringen genom att köra kommandot nedan
   ```
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-using-azure-portal"></a>Migrera med hjälp av Azure-portalen

Migrering via Azure portal har det samma logiska flödet som att migrera med hjälp av kommandona. Referera till den nedan guide för steg-för-steg-processen att migrera med hjälp av portalen.

1. Välj den **”migrera till Premium-** menyalternativet i navigationsmenyn till vänster. Klicka på den **Kom igång** för att gå vidare till nästa sida.
    ![Landningssida för migrering][]

2. Fullständig **installationsprogrammet**.
   ![Konfigurera namnområdet][]
   1. Skapa och tilldela Premium-namnområdet för att migrera befintliga Standard-namnområdet till.
        ![Konfigurera namnområdet – skapa premium-namnområde][]
   2. Välj den **”Postmigrering namn”** komma åt standardnamnområde genom när migreringen är klar.
        ![Konfigurera namnområdet – Välj efter migreringen namn][]
   3. Klicka på **”nästa”** att fortsätta.
3. **Synkronisera** enheter mellan Standard och Premium-namnområde.
    ![Konfigurera namnområdet - sync-entiteter – start][]

   1. Klicka på **Starta synkronisering** att börja synkronisera entiteter.
   2. Klicka på **”Ja”** på popup-fönstret bekräftar för att starta synkroniseringen.
   3. Vänta tills den **synkronisering** har slutförts. Statusen är tillgänglig i statusfältet.
        ![Namnområde – synkronisera entiteter - Installationsförlopp][]
        >[!IMPORTANT]
        > Om du behöver **Avbryt** av någon anledning, kontrollera att avbryta flödet i avsnittet vanliga frågor och svar i det här dokumentet.
   4. När synkroniseringen är klar klickar du på den **”nästa”** längst ned på sidan.

4. Granska ändringar på sammanfattningssidan.
    ![Växla namnområde - switch-menyn][]

5. Klicka på **'Fullständig migrering'** att växla namnområden och slutföra migreringen.
    ![Växeln namnområde – lyckades][]

## <a name="faqs"></a>Vanliga frågor och svar

### <a name="what-happens-when-the-migration-is-committed"></a>Vad händer när migreringen är allokerade?

När migreringen genomförs pekar anslutningssträngen som pekar på Standard-namnområdet till Premium-namnområde.

Avsändaren och mottagaren program kommer att koppla från Standard Namespace och automatiskt återansluta till Premium-namnområde.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Vad gör jag när Standard till Premium-migreringen är klar?

Standard till Premium-migreringen innebär att metadata för entitet (ämnen, prenumerationer, filter, et al.) kopieras från Standard till Premium-namnområde. Meddelandedata som har utförts till Standard-namnområdet kopieras inte från Standard till Premium-namnområde.

På grund av detta, kan Standard-namnområdet har några meddelanden som skickades och allokerat under migreringen pågår. Dessa meddelanden måste tömda från Standard Namespace manuellt och skickas över till Premium-Namespace manuellt.

Gör du ***måste*** använder en konsolapp eller skript som tömma standardnamnområde-entiteter med hjälp av den **efter migreringen DNS-namnet** att du angett i kommandona migrering och skicka dessa meddelanden på Premium-Namespace så att de kan bearbetas av mottagarna.

När meddelandena har varit tömda, Fortsätt att ta bort Standard-namnområdet.

>[!IMPORTANT]
> Tänk på att när meddelanden från Standard-namnområdet har tagits tömda, du **måste** ta bort Standard-namnområdet.
>
> Detta är viktigt eftersom den anslutningssträng som ursprungligen avses Standard-namnområdet nu faktiskt refererar till Premium-namnområde. Du kommer inte längre att behöva den här Standard Namespace.
>
> Tar bort Standard-namnområdet du migrerat hjälper tydligare vid ett senare tillfälle. 

### <a name="how-much-downtime-do-i-expect"></a>Hur stora driftstopp förväntar jag?
Processen som beskrivs ovan är avsedd att minska den förväntade stilleståndstiden för program. Detta görs genom att använda den anslutningssträng som programmen avsändaren och mottagaren använder för att peka på det nya Premium-namnområdet.

Erfarna av programmet avbrottstiden är begränsad till hur lång tid det tar för att uppdatera DNS-posten så att den pekar till Premium-namnområde.

Detta kan antas vara ***cirka 5 minuter***.

### <a name="do-i-have-to-make-any-configuration-changes-while-performing-the-migration"></a>Måste jag göra några konfigurationsändringar när du utför migreringen?
Nej, det finns ingen kod/konfigurationsändringar som krävs för att utföra migreringen. Anslutningssträngen som avsändaren och mottagaren program använda för att få åtkomst till Standard Namespace mappas automatiskt för att fungera som en **alias** för Premium-Namespace.

### <a name="what-happens-when-i-abort-the-migration"></a>Vad händer när jag avbryta migreringen?
Migrering kan avbrytas med hjälp av kommandot 'Avbryt' eller via Azure portal. 

#### <a name="azure-cli-or-powershell"></a>Azure CLI eller PowerShell

    az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace

#### <a name="azure-portal"></a>Azure Portal

![Avbryt flow – Avbryt synkronisering][]
![avbryta flödet – avbryta klar][]

När migreringen har avbrutits, den faktiskt avbryter kopieras över entiteter (ämnen, prenumerationer och filter) från Standard till Premium-namnområde och bryts kopplingen.

Anslutningssträngen **är inte** uppdateras för att peka på Premium-namnområde. Dina befintliga program fortsätta att fungera som de gjorde innan du börjar migreringen.

Men det **inte** ta bort entiteter på Premium-namnområde eller ta bort det Premium-namnområdet. Detta måste göras manuellt om du har valt att inte gå vidare med migreringen när alla.

>[!IMPORTANT]
> Om du vill avbryta migreringen kan ta bort Premium-Namespace som du hade etablerats för migreringen, så att du inte debiteras för resurser.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Jag vill inte behöva tömma meddelanden. Vad gör jag?

Det kan finnas meddelanden som skickas av avsändaren program och paket för lagring på Standard Namespace medan migreringen sker och strävar efter precis innan migreringen.

Med tanke på att under migreringen, den faktiska data/meddelandenyttolast inte kopieras från Standard till Premium, måste dessa stämma tömda manuellt och sedan skickas till Premium-namnområde.

Men om du kan migrera under ett planerat underhåll/Underhåll-fönster och inte vill att tömma manuellt och skicka meddelanden, kan du följa den nedanstående steg -

1. Stoppa avsändar-program och Tillåt mottagarna att bearbeta meddelanden som finns för närvarande i Standard-namnområdet och tömma kön.
2. När köer och prenumerationer i Standard Namespace är tom, följer du proceduren som beskrivs ovan för att köra migreringen från Standard till Premium-namnområde.
3. När migreringen är klar, startar du om avsändaren-program.
4. Avsändarna och mottagarna nu ansluter automatiskt till Premium-namnområde.

    >[!NOTE]
    > Mottagaren behöver inte stoppas för migreringen.
    >
    > När migreringen är klar, kommer mottagarna koppla från Standard-namnområdet och ansluter automatiskt till Premium-namnområde.

## <a name="next-steps"></a>Nästa steg

* Läs mer om den [skillnaderna mellan Standard och Premium-meddelanden](./service-bus-premium-messaging.md)
* Läs mer om hög tillgänglighet och Geo-Diaster recovery aspekter för Service Bus Premium [här](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)

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
