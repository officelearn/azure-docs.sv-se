---
title: Köra uppgifter under användarkonton - Azure Batch
description: Det är användbart att kunna konfigurera användarkontot som du vill att en aktivitet ska köras under. Lär dig vilka typer av användarkonton och hur du konfigurerar dem.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/18/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: fee3dc764d2052185160a4ba6b3d70854c54eeac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252277"
---
> [!NOTE] 
> Användarkontona som beskrivs i den här artikeln skiljer sig från användarkonton som används för RDP (Remote Desktop Protocol) eller Secure Shell (SSH) av säkerhetsskäl. 
>
> Information om hur du ansluter till en nod som kör Linux-konfigurationen för virtuella datorer via SSH finns i [Använda fjärrskrivbord till en Virtuell Linux-dator i Azure](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). Om du vill ansluta till noder som kör Windows via RDP läser du [Anslut till en Virtuell Windows Server](../virtual-machines/windows/connect-logon.md).<br /><br />
> Information om hur du ansluter till en nod som kör molntjänstkonfigurationen via RDP finns [i Aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>


# <a name="run-tasks-under-user-accounts-in-batch"></a>Köra aktiviteter under användarkonton i Batch

En uppgift i Azure Batch körs alltid under ett användarkonto. Som standard körs uppgifter under vanliga användarkonton, utan administratörsbehörighet. Dessa standardinställningar för användarkonton är vanligtvis tillräckliga. För vissa scenarier är det dock användbart att kunna konfigurera användarkontot som du vill att en aktivitet ska köras under. I den här artikeln beskrivs vilka typer av användarkonton som finns och hur du kan konfigurera dem för ditt scenario.

## <a name="types-of-user-accounts"></a>Typer av användarkonton

Azure Batch innehåller två typer av användarkonton för att köra uppgifter:

- **Automatiska användarkonton.** Automatiska användarkonton är inbyggda användarkonton som skapas automatiskt av batch-tjänsten. Som standard körs uppgifter under ett automatiskt användarkonto. Du kan konfigurera specifikationen för automatisk användare för en uppgift så att den anger under vilket automatiskt användarkonto en uppgift ska köras. Med specifikationen för automatisk användare kan du ange höjdnivån och omfattningen för det automatiska användarkonto som ska köra uppgiften. 

- **Ett namngivet användarkonto.** Du kan ange ett eller flera namngivna användarkonton för en pool när du skapar poolen. Varje användarkonto skapas på varje nod i poolen. Förutom kontonamnet anger du lösenordet för användarkontot, höjdnivån och, för Linux-pooler, den privata SSH-nyckeln. När du lägger till en aktivitet kan du ange det namngivna användarkonto som aktiviteten ska köras under.

> [!IMPORTANT] 
> Batch-tjänstversionen 2017-01-01.4.0 introducerar en brytningsändring som kräver att du uppdaterar koden för att anropa den versionen. Om du migrerar kod från en äldre version av Batch, observera att egenskapen **runElevated** inte längre stöds i REST API- eller Batch-klientbiblioteken. Använd egenskapen new **userIdentity för** en aktivitet för att ange höjdnivå. Se avsnittet [Uppdatera koden till det senaste batchklientbiblioteket](#update-your-code-to-the-latest-batch-client-library) för snabba riktlinjer för uppdatering av batchkoden om du använder något av klientbiblioteken.
>
>

## <a name="user-account-access-to-files-and-directories"></a>Åtkomst till användarkonto till filer och kataloger

Både ett automatiskt användarkonto och ett namngivet användarkonto har läs-/skrivåtkomst till uppgiftens arbetskatalog, delade katalog och uppgiftskatalog med flera instanser. Båda typerna av konton har läsbehörighet till start- och jobbförberedelsekataloger.

Om en aktivitet körs under samma konto som användes för att köra en startuppgift har aktiviteten läs-skrivåtkomst till startaktivitetskatalogen. Om en aktivitet körs under samma konto som användes för att köra en jobbförberedelseaktivitet har aktiviteten läs-skrivbehörighet till uppgiftskatalogen för jobbförberedelse. Om en aktivitet körs under ett annat konto än startaktiviteten eller jobbförberedelseaktiviteten har aktiviteten bara läsbehörighet till respektive katalog.

Mer information om hur du kommer åt filer och kataloger från en uppgift finns i [Utveckla storskaliga parallella beräkningslösningar med Batch](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Förhöjd åtkomst för uppgifter 

Användarkontots höjdnivå anger om en aktivitet körs med förhöjd åtkomst. Både ett automatiskt användarkonto och ett namngivet användarkonto kan köras med förhöjd åtkomst. De två alternativen för höjdnivå är:

- **Icke-Amina:** Aktiviteten körs som en standardanvändare utan förhöjd åtkomst. Standardhöjdnivån för ett batch-användarkonto är alltid **NonAdmin**.
- **Admin:** Aktiviteten körs som en användare med förhöjd åtkomst och fungerar med fullständiga administratörsbehörigheter. 

## <a name="auto-user-accounts"></a>Konton för automatisk användare

Som standard körs aktiviteter i Batch under ett automatiskt användarkonto, som en standardanvändare utan förhöjd åtkomst och med aktivitetsomfattning. När specifikationen för automatisk användare har konfigurerats för uppgiftsomfång skapar tjänsten Batch endast ett automatiskt användarkonto för den uppgiften.

Alternativet till aktivitetsomfånget är poolomfattning. När specifikationen för automatisk användare för en uppgift har konfigurerats för poolomfång körs aktiviteten under ett automatiskt användarkonto som är tillgängligt för alla aktiviteter i poolen. Mer information om poolomfattning finns i avsnittet Kör en uppgift som automatisk användare med poolomfattning.   

Standardomfånget är olika på Windows- och Linux-noder:

- På Windows-noder körs aktiviteter som standard under aktivitetsomfattning.
- Linux-noder körs alltid under poolomfattning.

Det finns fyra möjliga konfigurationer för specifikationen för automatisk användare, som var och en motsvarar ett unikt automatiskt användarkonto:

- Åtkomst som inte är administratör med uppgiftsomfattning (standardspecifikationen för automatisk användare)
- Admin (förhöjd) åtkomst med uppgiftsomfång
- Åtkomst som inte är administratör med poolomfattning
- Administratörsåtkomst med poolomfattning

> [!IMPORTANT] 
> Aktiviteter som körs under aktivitetsomfång har inte faktisk åtkomst till andra aktiviteter på en nod. En obehörig användare med åtkomst till kontot kan dock kringgå den här begränsningen genom att skicka en uppgift som körs med administratörsbehörighet och komma åt andra aktivitetskataloger. En obehörig användare kan också använda RDP eller SSH för att ansluta till en nod. Det är viktigt att skydda åtkomsten till dina batch-kontonycklar för att förhindra ett sådant scenario. Om du misstänker att ditt konto kan ha komprometterats måste du återskapa nycklarna.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Köra en uppgift som en automatisk användare med förhöjd åtkomst

Du kan konfigurera specifikationen för automatisk användare för administratörsbehörighet när du behöver köra en uppgift med förhöjd åtkomst. En startuppgift kan till exempel behöva förhöjd åtkomst för att installera programvara på noden.

> [!NOTE] 
> I allmänhet är det bäst att använda förhöjd åtkomst endast när det behövs. Bästa praxis rekommenderar att ge det minsta privilegium som krävs för att uppnå önskat resultat. Om en startuppgift till exempel installerar programvara för den aktuella användaren, i stället för för alla användare, kan du undvika att bevilja förhöjd åtkomst till uppgifter. Du kan konfigurera specifikationen för automatisk användare för poolomfattning och icke-administratörsåtkomst för alla uppgifter som behöver köras under samma konto, inklusive startuppgiften. 
>
>

Följande kodavsnitt visar hur du konfigurerar specifikationen för automatisk användare. Exemplen anger höjdnivån `Admin` till och `Task`omfånget till . Aktivitetsomfattning är standardinställningen, men inkluderas här för exempel.

#### <a name="batch-net"></a>.NET för Batch

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Batch Java

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");                        
```

#### <a name="batch-python"></a>Python för Batch

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Köra en uppgift som en automatisk användare med poolomfattning

När en nod etableras skapas två poolomfattande automatiska användarkonton på varje nod i poolen, ett med förhöjd åtkomst och ett utan förhöjd åtkomst. Om du anger den automatiska användarens omfattning till poolomfång för en viss aktivitet körs aktiviteten under ett av dessa två poolomfattande automatiska användarkonton. 

När du anger poolomfång för den automatiska användaren körs alla uppgifter som körs med administratörsåtkomst under samma poolomfattande autoanvändarkonto. På samma sätt körs uppgifter som körs utan administratörsbehörighet även under ett enda poolomfattande automatiskt användarkonto. 

> [!NOTE] 
> De två automatiska användarkontona för hela poolen är separata konton. Uppgifter som körs under det poolomfattande administrativa kontot kan inte dela data med uppgifter som körs under standardkontot och vice versa. 
>
>

Fördelen med att köra under samma autoanvändarkonto är att uppgifter kan dela data med andra uppgifter som körs på samma nod.

Att dela hemligheter mellan aktiviteter är ett scenario där det är användbart att köra aktiviteter under ett av de två automatiska användarkontona för hela poolen. Anta till exempel att en startuppgift måste etablera en hemlighet på den nod som andra uppgifter kan använda. Du kan använda DPAPI (Windows Data Protection API), men det kräver administratörsbehörighet. I stället kan du skydda hemligheten på användarnivå. Uppgifter som körs under samma användarkonto kan komma åt hemligheten utan förhöjd åtkomst.

Ett annat scenario där du kanske vill köra uppgifter under ett automatiskt användarkonto med poolomfattning är en MPI-filresurs (Message Passing Interface). En MPI-filresurs är användbar när noderna i MPI-uppgiften måste arbeta med samma fildata. Huvudnoden skapar en filresurs som de underordnade noderna kan komma åt om de körs under samma autoanvändarkonto. 

Följande kodavsnitt anger att den automatiska användarens omfång till poolomfång för en aktivitet i Batch .NET. Höjdnivån utelämnas, så aktiviteten körs under det vanliga poolomfattande autoanvändarkontot.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Namngivna användarkonton

Du kan definiera namngivna användarkonton när du skapar en pool. Ett namngivet användarkonto har ett namn och lösenord som du anger. Du kan ange höjdnivån för ett namngivet användarkonto. För Linux-noder kan du också ange en SSH-privat nyckel.

Ett namngivet användarkonto finns på alla noder i poolen och är tillgängligt för alla aktiviteter som körs på dessa noder. Du kan definiera valfritt antal namngivna användare för en pool. När du lägger till en uppgift eller aktivitetssamling kan du ange att aktiviteten körs under ett av de namngivna användarkonton som definierats i poolen.

Ett namngivet användarkonto är användbart när du vill köra alla aktiviteter i ett jobb under samma användarkonto, men isolera dem från uppgifter som körs i andra jobb samtidigt. Du kan till exempel skapa en namngiven användare för varje jobb och köra varje jobbs uppgifter under det namngivna användarkontot. Varje jobb kan sedan dela en hemlighet med sina egna uppgifter, men inte med uppgifter som körs i andra jobb.

Du kan också använda ett namngivet användarkonto för att köra en aktivitet som anger behörigheter för externa resurser, till exempel filresurser. Med ett namngivet användarkonto styr du användaridentiteten och kan använda den användaridentiteten för att ange behörigheter.  

Namngivna användarkonton aktiverar lösenordslös SSH mellan Linux-noder. Du kan använda ett namngivet användarkonto med Linux-noder som behöver köra uppgifter med flera instanser. Varje nod i poolen kan köra uppgifter under ett användarkonto som definierats i hela poolen. Mer information om multi-instance-uppgifter finns i [Använda multiinstansuppgifter\-för att köra MPI-program](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Skapa namngivna användarkonton

Om du vill skapa namngivna användarkonton i Batch lägger du till en samling användarkonton i poolen. Följande kodavsnitt visar hur du skapar namngivna användarkonton i .NET, Java och Python. Dessa kodavsnitt visar hur du skapar både administratörs- och icke-administratörsnamnskonton i en pool. Exemplen skapar pooler med molntjänstkonfigurationen, men du använder samma metod när du skapar en Windows- eller Linux-pool med hjälp av konfigurationen för den virtuella datorn.

#### <a name="batch-net-example-windows"></a>Batch .NET-exempel (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));   

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Exempel på batch .NET (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                             
    virtualMachineSize: "Standard_A1",                                      
    virtualMachineConfiguration: virtualMachineConfiguration);                  

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```


#### <a name="batch-java-example"></a>Exempel på Batch Java

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Exempel på Batch Python

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.non_admin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Köra en uppgift under ett namngivet användarkonto med förhöjd åtkomst

Om du vill köra en uppgift som en upphöjd användare anger du uppgiftens **UserIdentity-egenskap** till `Admin`ett namngivet användarkonto som skapades med egenskapen **ElevationLevel** inställd på .

Det här kodavsnittet anger att aktiviteten ska köras under ett namngivet användarkonto. Det här namngivna användarkontot definierades i poolen när poolen skapades. I det här fallet skapades det namngivna användarkontot med administratörsbehörighet:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Uppdatera koden till det senaste batchklientbiblioteket

Batch-tjänstversionen 2017-01-01.4.0 introducerar en brytningsändring som ersätter egenskapen **runElevated** som finns i tidigare versioner med egenskapen **userIdentity.** Följande tabeller innehåller en enkel mappning som du kan använda för att uppdatera koden från tidigare versioner av klientbiblioteken.

### <a name="batch-net"></a>.NET för Batch

| Om din kod använder...                  | Uppdatera den till ....                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated`har inte angetts | Ingen uppdatering krävs                                                                                               |

### <a name="batch-java"></a>Batch Java

| Om din kod använder...                      | Uppdatera den till ....                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated`har inte angetts | Ingen uppdatering krävs                                                                                                                     |

### <a name="batch-python"></a>Python för Batch

| Om din kod använder...                      | Uppdatera den till ....                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`Där <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`Där <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.non_admin))`             |
| `run_elevated`har inte angetts | Ingen uppdatering krävs                                                                                                                                  |


## <a name="next-steps"></a>Nästa steg

* En djupgående översikt över Batch finns i [Utveckla storskaliga parallella beräkningslösningar med Batch](batch-api-basics.md).
