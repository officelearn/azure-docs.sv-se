---
title: "Köra uppgifter under användarkonton i Azure Batch | Microsoft Docs"
description: "Konfigurera användarkonton för att köra uppgifter i Azure Batch"
services: batch
author: tamram
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: tamram
ms.openlocfilehash: d408c0565c0ed81fc97cc2b3976a4fc233e31302
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Köra uppgifter under användarkonton i Batch

En aktivitet i Azure Batch körs alltid under ett användarkonto. Som standard körs uppgifter under standardanvändarkonton utan administratörsbehörighet. Dessa standardinställningarna räcker oftast. För vissa scenarier, men är det användbart för att kunna konfigurera det användarkonto som du vill att aktiviteten ska köras. Den här artikeln beskrivs vilka typer av användarkonton och hur du kan konfigurera dem för ditt scenario.

## <a name="types-of-user-accounts"></a>Typer av användarkonton

Azure Batch tillhandahåller två typer av användarkonton för pågående aktiviteter:

- **Automatisk användarkonton.** Auto-användarkonton är inbyggda användarkonton som skapas automatiskt av Batch-tjänsten. Som standard körs aktiviteter under en automatisk användarkonto. Du kan konfigurera automatisk användaren specifikationen för en aktivitet att indikera en aktivitet ska köras under vilket auto-användarkonto. Specifikationen auto-användare kan du ange höjning nivå och omfattningen av automatisk-användarkonto som ska köra uppgiften. 

- **En namngiven användarkonto.** Du kan ange en eller flera namngivna användarkonton för en pool när du skapar poolen. Varje användarkonto skapas på varje nod i poolen. Förutom kontonamnet ange användarens kontolösenord höjning nivå och, för Linux pooler privata SSH-nyckeln. När du lägger till en aktivitet kan du ange namngivna användarkontot som aktiviteten ska köras under.

> [!IMPORTANT] 
> Batch-tjänstversion 2017-01-01.4.0 introducerar en ny ändring som kräver att du uppdaterar din kod för att anropa den här versionen. Om du migrerar kod från en äldre version av Batch, Observera att den **runElevated** egenskapen stöds inte längre i klientbibliotek REST API eller Batch. Använd den nya **userIdentity** -egenskapen för en aktivitet för att ange nivån för höjning. Se avsnittet [uppdatera din kod till det senaste batchen klientbiblioteket](#update-your-code-to-the-latest-batch-client-library) snabb riktlinjer för uppdatering av Batch-kod om du använder en av klientbiblioteken.
>
>

> [!NOTE] 
> Användarkonton som beskrivs i den här artikeln stöder inte Remote Desktop Protocol (RDP) eller SSH (Secure Shell), av säkerhetsskäl. 
>
> För att ansluta till en nod som kör Linux virtuella datorkonfigurationen via SSH finns [Använd Fjärrskrivbord till en Linux-VM i Azure](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). Om du vill ansluta till noder som kör Windows via RDP finns [Anslut till en Windows Server-VM](../virtual-machines/windows/connect-logon.md).<br /><br />
> För att ansluta till en nod som kör tjänsten molnkonfigurationen via RDP finns [aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>

## <a name="user-account-access-to-files-and-directories"></a>Användarkontoåtkomst till filer och kataloger

Både ett automatiskt användarkonto och ett namngivet användarkonto har läsning och skrivning åtkomst till aktivitetens arbetskatalogen delad katalog och flera instanser uppgifter directory. Båda typer av konton har läsbehörighet till Start- och förberedelse av kataloger.

Om en aktivitet ska köras under samma konto som användes för att köra Startuppgiften, har skrivskyddad åtkomst till katalogen för aktiviteten starta uppgiften. På samma sätt, om en aktivitet ska köras under samma konto som användes för att köra en projektaktivitet förberedelse, aktiviteten har skrivskyddad åtkomst till katalogen jobbet förberedelse av aktiviteten. Om en aktivitet körs under ett annat konto än startuppgift eller förberedelse projektaktivitet har aktiviteten bara läsbehörighet till katalogen för respektive.

Mer information om hur du använder filer och kataloger från en aktivitet finns [utveckla storskaliga parallell compute lösningar med Batch](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Utökad åtkomst för aktiviteter 

Användarkontots höjning nivå anger om en aktivitet körs med förhöjd åtkomst. Både ett automatiskt användarkonto och ett konto för namngiven användare kan köra med högre behörighet. De två alternativen för höjning nivå är:

- **NonAdmin:** aktiviteten körs som en vanlig användare utan utökad åtkomst. Standardnivån för höjning för ett användarkonto för Batch är alltid **NonAdmin**.
- **Admin:** uppgiften körs som en användare med högre behörighet och fungerar med fullständig administratörsbehörighet. 

## <a name="auto-user-accounts"></a>Auto-användarkonton

Som standard köra uppgifter i Batch under en auto-användarkonto som en vanlig användare utan utökad åtkomst och med aktiviteten omfattning. När specifikationen auto-användare konfigureras för omfånget för aktiviteten skapar Batch-tjänsten en automatisk användarkontot för aktiviteten endast.

Alternativ till aktiviteten scope är poolen omfång. När automatisk användaren specifikationen för en aktivitet är konfigurerad för poolen omfång, körs aktiviteten under en auto-användarkonto som är tillgänglig för alla aktiviteter i poolen. Mer information om poolen scope finns i avsnittet [köra en aktivitet som automatiskt-användare med poolen scope](#run-a-task-as-the-autouser-with-pool-scope).   

Standardvärde skiljer sig på Windows- och Linux-noder:

- På Windows-noder körs aktiviteter under scope på uppgift som standard.
- Linux-noder kan alltid köras under poolen omfång.

Det finns fyra konfigurationer för automatisk användar-specifikationen som motsvarar ett unikt auto-konto:

- Icke-administratörsåtkomst med uppgiften omfattning (standardtypen användare automatiskt)
- Administratörsåtkomst (förhöjd) med uppgiften scope
- Icke-administratörsåtkomst med poolen scope
- Administratörsåtkomst med poolen scope

> [!IMPORTANT] 
> Aktiviteter som körs under aktiviteten scope har inte faktisk åtkomst till andra aktiviteter på en nod. Men kan en obehörig användare med åtkomst till kontot undvika den här begränsningen genom att skicka en aktivitet som körs med administratörsbehörighet och har åtkomst till andra kataloger som aktiviteten. En obehörig användare kan också använda RDP eller SSH för att ansluta till en nod. Det är viktigt att skydda åtkomst till dina nycklar med Batch-konto att förhindra att ett sådant scenario. Om du misstänker att ditt konto komprometteras, måste du återskapa dina nycklar.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Köra en aktivitet som automatiskt-användare med högre behörighet

Du kan konfigurera automatisk användaren specifikationen för administratörsbehörighet när du behöver köra en aktivitet med högre behörighet. Till exempel behöva Startuppgiften utökad åtkomst till installera programvara på noden.

> [!NOTE] 
> I allmänhet är det bäst att använda utökad åtkomst bara när det behövs. Bästa praxis rekommenderar bevilja det minsta privilegiet som krävs för att uppnå önskat utfall. Till exempel om Startuppgiften installerar programvara för den aktuella användaren, i stället för för alla användare, du kan undvika utökade åtkomst beviljas till aktiviteter. Du kan konfigurera automatisk användaren specifikationen för poolen omfång och icke-administratörer åtkomst för alla aktiviteter som måste köras under samma konto, inklusive Startuppgiften. 
>
>

Följande kodavsnitt visar hur du konfigurerar automatisk användar-specifikationen. Exemplen anger höjningen till `Admin` och omfång till `Task`. Uppgiften scope är standardinställningen, men finns här för enkelhetens exempel.

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

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Köra en aktivitet som automatiskt-användare med poolen scope

När en nod har etablerats skapas två poolen hela auto-användarkonton på varje nod i poolen, en med högre behörighet och en utan utökad åtkomst. Ställa in automatisk-användarens omfång till poolen omfattningen för en viss aktivitet kör aktiviteten under någon av dessa två poolen hela auto-användarkonton. 

När du anger poolen omfång för automatisk-användaren alla aktiviteter som körs med administratörsåtkomst köras under samma pool hela auto-användarkonto. Aktiviteter som körs utan administratörsbehörighet körs på samma sätt även under samma pool hela auto-konto. 

> [!NOTE] 
> Två poolen hela auto-användarkontona är separata konton. Aktiviteter som körs under kontot poolen hela administrativa kan inte dela data med aktiviteter som körs under kontot standard och vice versa. 
>
>

Fördelen med att köras under samma auto-användarkontot är att aktiviteter kan dela data med andra aktiviteter som körs på samma nod.

Dela hemligheter mellan aktiviteter är ett scenario där pågående aktiviteter under en av två poolen hela auto-användarkontona är användbart. Anta exempelvis att Startuppgiften måste tillhandahålla en hemlighet på den nod som andra uppgifter kan använda. Du kan använda Windows Data Protection API (DPAPI), men det krävs administratörsbehörighet. I stället kan du skydda hemligheten på användarnivå. Aktiviteter som körs under samma användarkonto kan komma åt hemligheten utan utökad åtkomst.

Ett annat scenario där du kanske vill köra uppgifter under en automatisk användarkonto med poolen scope är en filresurs för Message Passing Interface (MPI). En filresurs för MPI är användbart när noderna i aktiviteten MPI behöver arbeta på samma fildata. Huvudnoden skapar en filresurs som underordnade noder har åtkomst till om de körs under samma auto-användarkonto. 

Följande kodavsnitt anger auto-användarens omfång till poolen omfattningen för en aktivitet i Batch .NET. Nivån höjning utelämnas så aktiviteten körs under kontot poolen hela auto-standardanvändare.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Namngiven användare

Du kan definiera namngivna användarkonton när du skapar en pool. En namngiven användarkonto har ett namn och lösenord som du anger. Du kan ange höjning-nivå för en namngiven användarkontot. Du kan också tillhandahålla en privata SSH-nyckel för Linux-noder.

En namngiven användarkontot finns på alla noder i poolen och är tillgänglig för alla aktiviteter som körs på noderna. Du kan definiera valfritt antal namngivna användare för en pool. När du lägger till en aktivitet eller en uppgift samling kan du ange att aktiviteten körs under ett konto för namngiven användare som definierats i poolen.

Ett namngivet användarkonto är användbart när du vill köra alla uppgifter i ett jobb under samma användarkonto, men kan isolera dem från aktiviteter som körs i andra jobb på samma gång. Du kan till exempel skapa en namngiven användare för varje jobb och köra varje jobb uppgifter under det namngivna användarkontot. Varje jobb kan sedan delar en hemlighet med egna aktiviteter, men inte med aktiviteter som körs i andra jobb.

Du kan också använda ett med namnet konto för att köra en aktivitet som anger behörigheter på externa resurser, till exempel filresurser. Med en namngiven användarkonto styra användarnas identitet och användarens identitet kan använda för att ange behörigheter.  

Namngiven användare aktivera lösenord mindre SSH mellan Linux-noder. Du kan använda en namngiven användarkonto med Linux-noder som behöver för att köra flera instanser uppgifter. Varje nod i poolen kan köra uppgifter under ett användarkonto som har definierats för hela poolen. Mer information om flera instanser uppgifter finns [använder flera\-instansen aktiviteter att köra MPI program](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Skapa namngiven användarkonton

Om du vill skapa namngiven användarkonton i Batch, lägger du till en samling av användarkonton i poolen. Följande kodavsnitt visar hur du skapar namngivna användarkonton i .NET, Java och Python. Dessa kodstycken visar hur du skapar både admin och icke-administratörer med namnet konton i en pool. Exemplen skapa pooler med tjänstkonfigurationen moln, men du kan använda samma metod när du skapar en Windows- eller Linux-pool med konfigurationen av virtuella datorn.

#### <a name="batch-net-example-windows"></a>Batch .NET-exempel (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                                         
    virtualMachineSize: "small",                                                
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

#### <a name="batch-net-example-linux"></a>Batch .NET-exempel (Linux)

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


#### <a name="batch-java-example"></a>Batch Java-exempel

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

#### <a name="batch-python-example"></a>Batch Python-exempel

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.nonadmin)
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

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Köra en aktivitet för en namngiven användarkonto med högre behörighet

Ange om du vill köra en aktivitet som en förhöjd aktivitetens **UserIdentity** egenskapen till ett namngivet användarkonto som har skapats med dess **ElevationLevel** egenskapen `Admin`.

Det här kodstycket anger att aktiviteten ska köras under en namngiven användarkonto. Namngivna användarkontot har definierats för poolen när poolen har skapats. I det här fallet, har namngiven användarkontot skapats med administratörsbehörigheter:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Uppdatera din kod till det senaste Batch-klientbiblioteket

Batch-tjänstversion 2017-01-01.4.0 introducerar en ny ändring ersätter den **runElevated** egenskap som är tillgänglig i tidigare versioner med den **userIdentity** egenskapen. Följande tabeller innehåller en enkel mappning som du kan använda för att uppdatera din kod från tidigare versioner av klientbiblioteken.

### <a name="batch-net"></a>.NET för Batch

| Om din kod använder...                  | Uppdatera det till...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated`inte angetts | Ingen uppdatering krävs                                                                                               |

### <a name="batch-java"></a>Batch Java

| Om din kod använder...                      | Uppdatera det till...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated`inte angetts | Ingen uppdatering krävs                                                                                                                     |

### <a name="batch-python"></a>Python för Batch

| Om din kod använder...                      | Uppdatera det till...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, där <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin)) `                |
| `run_elevated=False`                      | `user_identity=user`, där <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.nonadmin)) `             |
| `run_elevated`inte angetts | Ingen uppdatering krävs                                                                                                                                  |


## <a name="next-steps"></a>Nästa steg

### <a name="batch-forum"></a>Batch-Forum

Den [Azure Batch-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch) på MSDN är det bra att diskutera Batch och ställa frågor om tjänsten. HEAD på över för användbara Fäst inlägg och publicera dina frågor när de uppstår när du skapar Batch-lösningar.